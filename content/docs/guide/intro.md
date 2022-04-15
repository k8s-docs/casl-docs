---
title: 介绍
categories: [guide]
weight: 2
meta:
  keywords: ~
  description: ~
---

[![@casl/ability NPM version](https://badge.fury.io/js/%40casl%2Fability.svg)](https://badge.fury.io/js/%40casl%2Fability)
[![](https://img.shields.io/npm/dm/%40casl%2Fability.svg)](https://www.npmjs.com/package/%40casl%2Fability)
[![CASL Join the chat](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/stalniy-casl/casl)

## 什么是 CASL?

CASL (发音 /ˈkæsəl/, 类似 **castle**) 是一个同构的授权 JavaScript 库，它限制了给定客户端可以访问的资源。
它被设计成可增量采用的，并且可以轻松地在基于简单声明、全功能主题和基于属性的授权之间进行伸缩。
它使得跨 UI 组件、API 服务和数据库查询管理和共享权限变得容易。

> CASL 实现[基于属性的访问控制](https://en.wikipedia.org/wiki/Attribute-based_access_control)

## 入门

尝试 CASL.js 最简单的方法是使用[Hello World 示例][example-hello-casl]。
您可以在另一个选项卡中打开它，并跟随我们一起学习一些基本的例子。
或者你也可以创建 Nodejs 项目，并安装 `@casl/ability` 作为依赖项。

> [安装页面][guide-install]提供了更多安装 CASL 的选项。

[example-hello-casl]: https://codesandbox.io/s/github/stalniy/casl-examples/tree/master/packages/hello-world
[guide-install]: ../install

## 基本

CASL 在能力级别上操作，这是用户在应用程序中实际可以做的事情。
能力本身取决于 4 个参数(后 3 个是可选的):

1.  **User Action**
    描述用户在应用程序中实际可以做什么。
    用户动作是一个词(通常是动词)，它依赖于业务逻辑 (e.g., `prolong`, `read`).
    它通常是来自 CRUD 的单词列表 - `create`, `read`, `update` 和 `delete`.
2.  **Subject**
    要检查用户操作的主题或主题类型。
    通常这是一个业务(或域)实体 (e.g., `Subscription`, `Article`, `User`).
    主题和主题类型之间的关系与对象实例与其类之间的关系相同。
3.  **Fields**
    可以用来限制用户的动作只匹配主题的字段吗 (例如，允许版主更新`Article`的`status`栏位，而不允许更新`description`或`title`栏位。)
4.  **Conditions**
    限制用户动作只针对匹配主题的标准。
    当您需要对特定的主题给予许可时，这是非常有用的 (e.g., 允许用户管理自己的 `Article`)

CASL 的核心是一个系统，它允许我们使用清晰的 javascript 语法声明性地定义和检查用户权限:

```js @{data-filename="defineAbility.js"}
import { defineAbility } from "@casl/ability";

export default defineAbility((can, cannot) => {
  can("manage", "all");
  cannot("delete", "User");
});
```

> CASL 对 TypeScript 有复杂的支持，但在本指南中，我们将使用 JavaScript 来简化。
> 详见[CASL TypeScript](../../advanced/typescript)

在上面的例子中，我们刚刚定义了一个`Ability`实例，它允许在应用程序中做任何事情，除了删除用户。
正如你可能猜到的，`can` and `cannot` 接受相同的参数，但有不同的含义: `can` 允许对指定的主题进行操作，而 `cannot` 禁止。
两者都可以接受最多 4 个参数(顺序与在[concepts section](#basics)中列出的完全相同)。
在本例中，`manage` and `delete`是用户操作，`all` and `User`是主题。

> `manage` and `all` 是 CASL 中的特殊关键字。
> ' manage '表示任何动作，' all '表示任何主体。

现在让我们试着检查权限

```js @{data-filename="app.js"}
import ability from "./defineAbility.js";

ability.can("read", "Post"); // true
ability.can("read", "User"); // true
ability.can("update", "User"); // true
ability.can("delete", "User"); // false
ability.cannot("delete", "User"); // true
```

在上面的例子中，`Ability`实例允许我们以一种非常可读的方式检查权限。
顺便说一下，所有这些示例都演示了基于主题类型(即对象类型或类)的权限检查，但当您需要基于对象的属性(attributes)(即属性(properties))来限制对象时，CASL 真的很出色。

## 条件

对于中等规模的应用程序，最常见的需求是能够限制用户，使他们只能在自己的资源上执行操作。
CASL 允许我们在定义步骤中将一个条件对象作为“can”和“cannot”方法的第三个参数传递给它。

在深入讨论细节之前，让我们首先考虑博客网站的权限要求。
在这样的博客中，一个用户

- 可以 `read` 任何 `Article`
- 可以 `update` 自已 `Article`'s
- 可以 `create` 一个 `Comment` 为任何文章
- 可以 `update` 自己 `Comment`

让我们将其转化为 CASL:

```js @{data-filename="defineAbility.js"}
import { defineAbility } from "@casl/ability";

export default (user) =>
  defineAbility((can) => {
    can("read", "Article");

    if (user.isLoggedIn) {
      can("update", "Article", { authorId: user.id });
      can("create", "Comment");
      can("update", "Comment", { authorId: user.id });
    }
  });
```

您看到实际的业务需求如何容易地转换为代码了吗?现在让我们检查它们!

```js
import defineAbilityFor from './defineAbility';

const user = { id: 1 };
const ability = defineAbilityFor(user);
const article = /* intentionally not defined */;

ability.can('read', article);
```

As you see, you can do checks on the subject the same way you do it on the subject's type! But what does the `article` variable hold inside? How does CASL know the subject type of the object referenced by this variable?

Do you remember that a subject and its type belong to each other in the same way as an object instance and its class? CASL remembers this as well and retrieves `article.constructor.name` as its subject type.

> Classes are natural in backend but not always makes sense in frontend development.
> CASL supports other ways to detect subject type, see [Subject type detection](../subject-type-detection) for details.

Let's get back to our example and define classes for `Article` and `Comment` entities:

```js @{data-filename="entities.js"}
class Entity {
  constructor(attrs) {
    Object.assign(this, attrs);
  }
}

export class Article extends Entity {}
```

And this is how the example with missed `article` variable looks eventually:

```js
import defineAbilityFor from "./defineAbility";
import { Article } from "./entities";

const user = { id: 1 };
const ability = defineAbilityFor(user);
const article = new Article();

ability.can("read", article); // user can read any article
```

And few more examples just to get familiar with:

```js @{data-filename="app.js"}
import defineAbilityFor from "./defineAbility";
import { Article } from "./entities";

const user = { id: 1, isLoggedIn: true };
const ownArticle = new Article({ authorId: user.id });
const anotherArticle = new Article({ authorId: 2 });
const ability = defineAbilityFor(user);

ability.can("read", "Article"); // true
ability.can("update", "Article"); // true
ability.can("update", ownArticle); // true
ability.can("update", anotherArticle); // false, we can't update articles which were not written by us
```

> Despite the fact that `can` and `cannot` functions in `defineAbility` callback are similar to `can` and `cannot` methods of `Ability` class, they have completely different purposes and accept different arguments.
> See [Make `can` API less confusing](../../cookbook/less-confusing-can-api) if it confuses you.

**Pay attention** that conditions object contains the same keys as the entity we want to check.
This is how CASL matches entities by conditions.
In our case, it just checks that `authorId` in `Article` instance equals to `authorId` in conditions object.
Conditions may have several fields, in that case all fields should match (`AND` logic).

But conditions are not restricted to simple equality checks! Thanks to [ucast](https://github.com/stalniy/ucast) `Ability` instances can match objects using [MongoDB query language](http://docs.mongodb.org/manual/reference/operator/query/).

> If you are not familiar with MongoDB query language, see [CASL conditions in depth](../conditions-in-depth) for details

You can define the same pair of action and subject with different conditions multiple times.
For example, let's allow our blog users to share drafts and publish articles:

```js
import { defineAbility } from "@casl/ability";

export default function defineAbilityFor(user) {
  return defineAbility((can) => {
    can("read", "Article", { published: true });
    can("read", "Article", { published: false, sharedWith: user.id });
  });
}
```

In such case, the pair of action/subject rules are combined by logical `OR`.
More formally, this can be translated as "users can read Article if it's published OR users can read Article if it's not published AND shared with them".

But that's not all, if you need more granular permission checks, you can define them on subject's attributes (i.e., fields)!

## Fields

Sometimes you may need to restrict which fields a user can access.
For example, let's allow only moderators to publish `Article`:

```js @{data-filename="defineAbility.js"}
import { defineAbility } from "@casl/ability";

export default (user) =>
  defineAbility((can) => {
    can("read", "Article");
    can("update", "Article", ["title", "description"], { authorId: user.id });

    if (user.isModerator) {
      can("update", "Article", ["published"]);
    }
  });
```

Here we defined that any user can update `title` and `description` fields of their own `Article`s and only moderators can update `published` field.

> If fields are not specified, a user is allowed to access any field.

To check permissions use the same `can` and `cannot` methods of `Ability` instance:

```js
import defineAbilityFor from "./defineAbility";
import { Article } from "./entities";

const moderator = { id: 2, isModerator: true };
const ownArticle = new Article({ authorId: moderator.id });
const foreignArticle = new Article({ authorId: 10 });
const ability = defineAbilityFor(moderator);

ability.can("read", "Article"); // true
ability.can("update", "Article", "published"); // true
ability.can("update", ownArticle, "published"); // true
ability.can("update", foreignArticle, "title"); // false
```

> For more complex cases, you can use nested fields and wildcards, see [Restricting field access](../restricting-fields) for details

## 检查逻辑

Let's consider a simple example where user can publish articles:

```js
import { defineAbility } from "@casl/ability";
import { Article } from "./entities";

const ability = defineAbility((can) => {
  can("read", "Article", { published: true });
});
const article = new Article({ published: true });

ability.can("read", article); // (1)
ability.can("do", "SomethingUndeclared"); // (2)
ability.can("read", "Article"); // (3)
```

Line `(1)` returns `true` as we expected.
Line `(2)` return `false` because for any unknown subject or action CASL returns `false`, by default everything is forbidden (if `manage` and `all` keywords are not used).
But what would you expect line `(3)` to return? The answer may be unexpected for some of you, it returns `true` as well.
**Why?!**

Let's get back to our experience for a while.
Historically, majority of permissions management libs were built on top of roles or flags.
So, a user either has permission or not.
This can be expressed in pseudo code:

```js
allow("read_article");
allow("update_article");
```

This is interpreted as "user can read ALL articles and user can update ALL articles".
So, this is "all or nothing" mindset.

**But CASL is different!** It allows us to ask different questions to our permissions.
So, when you check on a

- subject, you ask "can I read THIS article?"
- subject type, you ask "can I read SOME article?" (i.e., at least one article)

It's very useful when you don't have an instance to check but know its type (for example, during creation), so this allows your app to fail fast.

> If you do checks on a subject type, you need to check permissions one more time on the final subject, right before sending request to API or database.

## 反向规则

This guide talk a lot about allowable permissions but nothing about disallowable ones.
This is for the reason.
The direct logic is much simpler to understand, and we recommend to use it whenever possible.

To define an inverted rule, you need to use the 2nd argument in the callback of `defineAbility`.
Let's give a user a permission to do anything but not delete:

```js
import { defineAbility } from "@casl/ability";

const ability = defineAbility((can, cannot) => {
  can("manage", "all");
  cannot("delete", "all");
});

ability.can("read", "Post"); // true
ability.can("delete", "Post"); // false
```

As you should know direct rules are checked by logical `OR` on the other hand inverted ones are checked by logical `AND`.
So, in the example above user:

- can do anything on all entities
- and cannot delete any entity

When defining direct and inverted rules for the same pair of action and subject the order of rules matters: `cannot` declarations should follow after `can`, otherwise they will be overridden by `can`.
For example, let's disallow to read all private objects (those that have property `private = true`):

```js
const user = { id: 1 };
const ability = defineAbility((can, cannot) => {
  cannot("read", "all", { private: true });
  can("read", "all", { authorId: user.id });
});

ability.can("read", { private: true }); // false
ability.can("read", { authorId: user.id }); // true
ability.can("read", { authorId: user.id, private: true }); // true!
```

Here we got an unexpected result because direct rule is the last one.
To fix the result just revert those rules and **always remember to put inverted rules after the direct one!**

### 禁止的原因

The good point about inverted rules is that they help to explicitly forbid particular actions.
Moreover they allow to add explanation.
Let's see how

```js @{data-filename="defineAbility.js"}
import { defineAbility } from "@casl/ability";

export default defineAbility((can, cannot) => {
  can("read", "all");
  cannot("read", "all", { private: true }).because("You are not allowed to read private information");
});
```

We can get this message back by checking permissions using `ForbiddenError`:

```js
import { ForbiddenError } from "@casl/ability";
import ability from "./defineAbility";

try {
  ForbiddenError.from(ability).throwUnlessCan("read", { private: true });
} catch (error) {
  if (error instanceof ForbiddenError) {
    console.log(error.message); // You are not allowed to read private information
  }

  throw error;
}
```

> To learn more about `ForbiddenError`, see [ForbiddenError API](../../api#forbidden-error)

## 更新规则

Sometimes, especially in frontend application development, we need to update `Ability` instance's rules (e.g., on login or logout).
To do this, we can use `update` method:

```js
import ability from "./defineAbility";

ability.update([]); // forbids everything
ability.update([
  // switch to readonly mode
  { action: "read", subject: "all" },
]);
```

Also we can use `AbilityBuilder` to create rules:

```js
import { Ability, AbilityBuilder } from "@casl/ability";

const ability = new Ability();

const { can, rules } = new AbilityBuilder();
can("read", "all");

ability.update(rules);
```

To track when rules are updated, we can subscribe to `update` (before ability is updated) or `updated` (after ability is updated) events of `Ability` instance:

```js
const unsubscribe = ability.on("update", ({ rules, target }) => {
  // `rules` is an array passed to `update` method
  // `target` is an Ability instance that triggered event
});

unsubscribe(); // removes subscription
```

## 还有什么?

CASL does not have a concept of "a role" and this makes it very powerful! As CASL allows to describe user abilities in your application, you can use it to:

1.  Implement [feature toggles](https://en.wikipedia.org/wiki/Feature_toggle)\
     Hide unfinished feature or show it to beta testers only.
2.  Conduct [A/B testing](https://en.wikipedia.org/wiki/A/B_testing)\
     Based on age, region, country or whatever hide features for some users and show for others
3.  Simple Business Logic\
     Disallow users to watch video if their subscription has been expired

> See [Roles with predefined permissions](../../cookbook/roles-with-static-permissions) for details.

## 准备好了?

我们已经简要介绍了 CASL.js 核心的所有特性——本指南的其余部分将涵盖它们和其他高级特性，并提供更详细的细节，所以请务必通读全文!

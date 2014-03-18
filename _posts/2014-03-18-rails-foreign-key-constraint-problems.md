---
layout: post
title: rails外键约束
tags: [todo rails]
---
> [Ruby on Rails Guides](http://guides.rubyonrails.org/migrations.html#active-record-and-referential-integrity) 是这么描述RoR与[引用完整性](http://en.wikipedia.org/wiki/Referential_integrity)的：

> The Active Record way claims that intelligence belongs in your models, not in the database. As such, features such as triggers or foreign key constraints, which push some of that intelligence back into the database, are not heavily used.

> Validations such as validates :foreign_key, uniqueness: true are one way in which models can enforce data integrity. The :dependent option on associations allows models to automatically destroy child objects when the parent is destroyed. Like anything which operates at the application level, these cannot guarantee referential integrity and so some people augment them with foreign key constraints in the database.

> Although Active Record does not provide any tools for working directly with such features, the execute method can be used to execute arbitrary SQL. You could also use some gem like [foreigner](https://github.com/matthuhiggins/foreigner) which add foreign key support to Active Record (including support for dumping foreign keys in db/schema.rb).

大意是，RoR本身不提供数据库层面的引用完整性，但是提供了很多方式让开发者能够在应用层实现。
如果不满足于此，可以手动添加约束，比如说，利用execute方法，也可以利用foreigner插件。


## TODO

- 两种方式各自的优缺点。

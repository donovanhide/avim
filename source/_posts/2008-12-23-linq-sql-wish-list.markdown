---
layout: post
title: "Linq To SQL Wish List"
date: 2008-12-23 22:44:13
comments: true
categories:
---

I really like using Linq, it is an elegant query language that can be applied to in-memory objects as well as translated into SQL dialects behind the scenes. What this gives is a common means of expressing filters, orderings, groupings and aggregations throughout your code. I have battled with massive reports using the CROSS JOIN with LEFT OUTER JOIN pattern in SQL, passing stacks of parameters into the stored procedure to get the report results out. Admittedly, it could be tweaked to make it run pretty fast, but it was sheer hell to maintain.

Linq allows queries to be composed at runtime and built to arbitrary levels of complexity, as needed by the consumer of the code. This is a win situation compared to versioning strings of SQL that are not type-checked and prone to error. However, if we go down the Linq route we also need an ORM solution to go with it. Currently there are four choices:

* Subsonic
* Linq to SQL
* The Entity Framework V1
* Nhibernate with Linq to Nhibernate

I like Linq to SQL because it is lightweight, the documentation is fairly complete and I've invested time learning it. It has some missing features, though, and now Microsoft are deprecating it in favour of the Entity Framework V2.

Please don't do this! Improve what is already a good product with some simple additions:

* Add a many-to-many association
* Add the two other patterns of inheritance: [Class Table Inheritance](http://martinfowler.com/eaaCatalog/classTableInheritance.html) and [Concrete Class Table Inheritance](http://martinfowler.com/eaaCatalog/concreteTableInheritance.html)
* Improve the CreateDatabase() method and extend the MetaModel to include database default values (newid() for example) and a PostCreateDatabase hook for inserting any custom SQL
* Add a versioning feature to the mapping source which can deal with migrations from one version to another.
Currently Linq to Sql works well for DB-first design, as long as you automate the process as follows:
* Introspect the db with sqlmetal to create a .dbml file
* Patch the dbml file with changes using MSBuild Community Tasks for instance
* Run the second half of the sqlmetal process to generate the code

This means that you don't end up with nice clean POCO code, however. I would prefer, in a DDD way, to start with some POCO's and then create mappings using a fluent interface, and then have some SQL DDL's created. Maybe this is possible now. I just don't want to plough through the horrid NHibernate documentation and the EF looks like a mess that won't be sorted out until VS 2010 hits the streets. Subsonic is an option but Rob Conery seems awfully busy at the moment!
---
layout: post
title: Rails Boundaries from Nested Resources
date: 2019-09-14 23:08 +0100
author: igbanam
category: blog
tag:
- rails
- ddd
headerImage: false
---

With the growth of microservices, and the scaling of many start-ups into medium-sized businesses, apps need to scale in their architecture. A popular way this is done is by creating some sort of grouping in your application. These groupings lead to boundaries. Boundaries could be microservices. The monolith could thus be strangled into microservices along these boundary lines. This is good stuff.

At this point in the life of a Rails app, most developers opt to build more Rails apps as sibling-microservice applications; smaller, more manageable. The process of creating a boundary could start within Rails. I bet you already have some boundaries defined in your application. All you need do is take the boundary splitting to the next step.

### Opinionated Boundary Routes

> Let your routes guide you. 🙏🏾  
> ~ [Master Yasky](https://twitter.com/Yaasky/status/1173634775216742407)

A good pointer to the boundaries you already have in your application is defined in your routes file. If you have any nested resources, scoped routes, or namespaced routes, you have created a boundary. Most times, these boundaries are only respected in our routes. When boundaries are implemented properly, the structure of the application should reflect what is within the routes file.

```ruby
Rails.application.routes.draw do
  resources :customers do
    resources :orders
  end
end
```

This setup tells me that orders is scoped within customers. With our routes setup this way, we know that our dealings in the `orders_controller` would need some parameter from the controller. Sort of speaks to "a customer's order". The file structure which should follow should be thus

{% gist 12cee9d0443c3e3ede2cb9c202d37771 %}

Currently, Rails does not enforce this by default. Rails would only do this when you generate a nested resource. And the routes for that would be namespaced.

I am beginning to think it is better to define the product boundaries in architecture as they show up in the routes. This way there are at least two — possibly many — views into the application architecture; not just a documentation somewhere.

### Things to consider next

Nested resources is the first glaring pointer that there should be some sort of boundary drawn. But this is not the only pointer. Looking into the routes file shows some other keywords which may point to different ways you may draw boundaries in your Rails application.

  1. Scope
  2. Namespace
  3. Constraint

This may be the lingo necessary in differentiating how boundaries are drawn: business concerns, processes, team departments, user roles, and so on.

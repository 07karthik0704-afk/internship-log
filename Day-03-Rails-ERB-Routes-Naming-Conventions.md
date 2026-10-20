# Day 3 — Rails ERB, Routes, and Naming Conventions

These notes cover three Rails fundamentals:

- ERB syntax in Rails views
- `resources` vs `resource` in routes
- Convention over Configuration

---

## 1. ERB in Rails Views

Rails view files often end with `.html.erb`. This means the file can contain HTML and embedded Ruby (ERB).

### `<% %>` — Run Ruby code without displaying its result

Use this for Ruby logic, such as loops and conditions.

Example:

```erb
<% @students.each do |student| %>
  ...
<% end %>
```

Condition example:

```erb
<% if student.age > 18 %>
  <p>Adult</p>
<% else %>
  <p>Minor</p>
<% end %>
```

The Ruby code runs, but the code's result is not inserted into the HTML by the ERB tag.

### `<%= %>` — Run Ruby code and display its result

Use this when you want a value to appear on the webpage.

```erb
<%= student.name %>
<%= student.age %>
<%= notice %>
```

If `student.name` returns `"Karthik"`, the generated HTML contains `Karthik`.

### Golden rule

- **Logic:** `<% %>`
- **Display a result:** `<%= %>`

### Loop example

```erb
<% @students.each do |student| %>
  <p><%= student.name %></p>
<% end %>
```

The loop goes through each student in `@students`. For every iteration, `student` refers to the current student object, and `<%= student.name %>` prints that student's name.

### Common mistake

Incorrect when you want to display the name:

```erb
<% student.name %>
```

Correct:

```erb
<%= student.name %>
```

---

## 2. `resources` vs `resource`

Both are route declarations used in `config/routes.rb`. They generate routes for standard actions, but they represent resources differently.

### `resources` — A collection of records

```ruby
resources :students
```

This is appropriate when the application manages multiple student records. It generates standard RESTful routes, including:

| Action | HTTP method | Example path |
|---|---|---|
| Index | GET | `/students` |
| New | GET | `/students/new` |
| Create | POST | `/students` |
| Show | GET | `/students/1` |
| Edit | GET | `/students/1/edit` |
| Update | PATCH/PUT | `/students/1` |
| Destroy | DELETE | `/students/1` |

The ID in paths such as `/students/1` identifies a particular student.

### `resource` — A singular resource

```ruby
resource :profile
```

This is useful when the application treats a profile as a single resource, such as the current user's profile. Its URLs don't include a profile ID.

Common paths include:

- `/profile` — show or update the profile, or create it
- `/profile/new` — display a form to create it
- `/profile/edit` — display a form to edit it

### Easy rule

- `resources` — a collection, usually with individual record IDs in URLs.
- `resource` — a singular resource without an ID in the URL.

Note: both declarations can generate multiple routes for different actions. The difference is whether the resource is treated as a collection or a single resource.

---

## 3. Convention over Configuration

**Convention over Configuration** means Rails uses standard naming rules and sensible defaults so you don't have to configure every connection manually.

In Day 2, we ran:

```bash
rails generate scaffold Student name:string age:integer
```

Rails generated the model, migration, controller, views, and routes following its conventions.

### Common Rails naming conventions

| Component | Example |
|---|---|
| Model class | `Student` |
| Model file | `app/models/student.rb` |
| Database table | `students` |
| Controller class | `StudentsController` |
| Controller file | `app/controllers/students_controller.rb` |
| Views folder | `app/views/students/` |

General rule:

- Model class names are singular and use CamelCase: `Student`, `Product`.
- Model filenames are singular and use snake_case: `student.rb`, `product.rb`.
- Database table names are usually plural: `students`, `products`.
- Controller class names and view folders are usually plural: `StudentsController`, `students/`.

For example:

```ruby
class Product < ApplicationRecord
end
```

By convention, Rails maps the `Product` model to the `products` database table.

Following these conventions helps Rails connect models, tables, controllers, and views automatically. You can override the defaults when needed.

---

## 4. Quick Revision

- `<% %>` runs Ruby logic without printing the result into the HTML.
- `<%= %>` inserts the expression's result into the HTML.
- `resources :products` represents a collection of products.
- `resource :profile` represents a singular profile resource.
- Rails conventionally maps `Product` to `products`.
- Routes direct requests, controllers handle actions, models interact with data, and views generate HTML.

## 5. Practice Questions

1. What is the difference between `<% student.name %>` and `<%= student.name %>`?
2. Which route declaration would you use for many products, each with its own ID?
3. What is the default table name for a model named `Product`?
4. Which controller class conventionally handles the `resources :students` routes?
5. What does Convention over Configuration mean in Rails?

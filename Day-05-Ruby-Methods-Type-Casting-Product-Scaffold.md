# Day 5 — Ruby Methods, Type Casting & Product Scaffold

These notes cover Ruby methods, comments, user input, type conversion, Rails scaffolding, database inserts, and useful gem commands.

---

## 1. Ruby Methods

A method is a reusable block of code that performs a task.

```ruby
def add(a, b)
  a + b
end

puts add(10, 20) # 30
```

- `def` starts a method definition.
- `add` is the method name.
- `a` and `b` are parameters.
- `a + b` is the final evaluated expression.
- `end` finishes the method definition.

Ruby methods return the value of the last evaluated expression by default. Using `return` is optional when returning the final expression.

```ruby
def add(a, b)
  return a + b
end
```

This method also returns the sum.

---

## 2. Comments in Ruby

Comments help explain code or temporarily disable a line while debugging. Ruby ignores comments during normal execution.

### Single-line comment

```ruby
# This method adds two numbers.
def add(a, b)
  a + b
end
```

### Multi-line comment

```ruby
=begin
This is a multi-line comment.
Ruby ignores this text.
=end
```

The `=begin` and `=end` markers must begin at the start of a line. In everyday Ruby code, `#` comments are more common.

---

## 3. User Input and Type Casting

### Reading input with `gets`

```ruby
puts "Enter your name:"
name = gets
puts name
```

`gets` reads input as a String and normally includes the trailing newline when Enter is pressed.

### Removing the newline with `chomp`

```ruby
name = gets.chomp
```

- `gets` might return `"Karthik\n"`.
- `gets.chomp` returns `"Karthik"`.

### User input is a String

Even when a user types digits, `gets.chomp` returns a String:

```ruby
puts "Enter your age:"
age = gets.chomp

puts age.class # String
```

### Converting between types

```ruby
"10".to_i   # String to Integer: 10
"10.5".to_f # String to Float: 10.5
10.to_s     # Integer to String: "10"
```

For numeric input:

```ruby
puts "Enter your age:"
age = gets.chomp.to_i

puts age.class # Integer
```

Be aware that methods such as `to_i` convert strings permissively; invalid input may become `0`. Validate user input when correctness matters.

---

## 4. Product Scaffold in Rails

From the root folder of your Rails project, a scaffold can generate a Product feature:

```bash
rails generate scaffold Product name:string description:text price:decimal stock:integer is_active:boolean
```

This generates files such as:

- Product model
- Products controller
- Views for standard CRUD actions
- A database migration
- Routes for standard CRUD actions

The fields mean:

| Field | Type | Meaning |
|---|---|---|
| `name` | `string` | Product name |
| `description` | `text` | Longer description |
| `price` | `decimal` | Product price |
| `stock` | `integer` | Quantity in stock |
| `is_active` | `boolean` | Whether the product is active |

### Create the database and apply migrations

If the application's database has not yet been created, run:

```bash
rails db:create
```

Then apply pending migrations:

```bash
rails db:migrate
```

The scaffold command generates the migration; `rails db:migrate` applies it to the database. If your `jarvis` project currently only has the Student scaffold, create the Product scaffold only when you're ready to add it.

---

## 5. Ways to Insert Data into the Database

### Method 1: Rails UI forms

Visit the resource's page in the browser, open its **New** form, enter the data, and submit it. The controller's `create` action processes the submitted parameters and attempts to save a record.

Typical flow:

1. User fills out a form.
2. Browser sends a request to Rails.
3. The controller's `create` action receives the submitted parameters.
4. The model attempts to save the record.
5. Rails redirects after success or re-renders the form if saving fails.

### Method 2: SQL manually

Use a database client such as DBeaver or pgAdmin to run SQL against the correct database. For example, after the `products` table exists:

```sql
INSERT INTO products (name, description, price, stock, is_active, created_at, updated_at)
VALUES ('Laptop', 'Sample laptop', 45000, 5, TRUE, NOW(), NOW());
```

The exact columns depend on your schema.

### Method 3: Seeds file

Write predefined data in `db/seeds.rb`:

```ruby
Product.create!(
  name: "Jana nayagan",
  description: "Sample product description",
  price: 500,
  stock: 10,
  is_active: true
)
```

Run the seed file with:

```bash
rails db:seed
```

`create!` raises an error if the record cannot be saved. Running seeds repeatedly can create duplicate records unless you add logic to avoid duplicates.

### Method 4: Rails console

Start an interactive Rails session:

```bash
rails console
```

Then run Ruby commands, for example:

```ruby
Product.create!(
  name: "Laptop",
  description: "A sample laptop",
  price: 45000,
  stock: 5,
  is_active: true
)

Product.all
```

Exit the console with:

```ruby
exit
```

### Method 5: Faker gem

Faker can generate sample data for testing.

Add this to your `Gemfile`:

```ruby
gem "faker"
```

Install the dependency:

```bash
bundle install
```

If you have a `Customer` model with `name` and `email` fields, you can add this to `db/seeds.rb`:

```ruby
10.times do
  Customer.create!(
    name: Faker::Name.name,
    email: Faker::Internet.unique.email
  )
end
```

Then run:

```bash
rails db:seed
```

The `Customer` model and table must already exist. Faker data is for development/testing, not real customer records.

---

## 6. Gem Commands

A gem is a packaged Ruby library.

```bash
gem list
gem list faker
gem info faker
```

- `gem list` — lists installed gems.
- `gem list faker` — checks for an installed gem named `faker`.
- `gem info faker` — shows information about the installed gem.

In a Rails application, use `Gemfile` to declare dependencies and `bundle install` to install the dependencies for the project. You can explore gem documentation and source code on sites such as GitHub and RubyGems.

---

## 7. Quick Revision

- Ruby methods return the last evaluated expression automatically.
- `#` starts a single-line comment.
- `gets.chomp` reads input and removes its trailing newline.
- User input from `gets` is a String; use conversions such as `to_i` and `to_f` when needed.
- `rails generate scaffold` generates model, controller, views, routes, and a migration.
- `rails db:migrate` applies pending migrations.
- Data can be inserted through Rails forms, SQL, seeds, the Rails console, or generated test data using Faker.
- `Gemfile` declares Ruby dependencies; `bundle install` installs them.

## 8. Practice Questions

1. What value does this method return?

   ```ruby
   def multiply(a, b)
     a * b
   end

   multiply(4, 5)
   ```

2. What is the difference between `gets` and `gets.chomp`?
3. What does `"25".to_i` return?
4. Which command applies a generated migration?
5. Which file contains Rails seed instructions?
6. What is the difference between `rails db:seed` and `rails console`?
7. What is Faker used for?

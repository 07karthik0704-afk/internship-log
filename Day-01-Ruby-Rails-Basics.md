# Day 1 — Ruby & Rails Basics

## Learning goal

Understand what Ruby and Ruby on Rails are, how they relate to each other, and the basic commands used to create and run a Rails application.

---

## 1. Ruby and Rails: the difference

### Ruby

Ruby is an open-source, dynamically typed programming language. It is designed to be readable and productive for developers.

### Ruby on Rails

Ruby on Rails (Rails) is a web application framework built using Ruby. It provides conventions, tools, and a standard project structure so developers can build web applications without creating everything from scratch.

**Remember:**
- **Ruby** is the programming language.
- **Rails** is the framework built on Ruby.
- **The application** is what we build using Ruby and Rails.

Ruby can be used without Rails. Rails applications use Ruby.

---

## 2. Ruby basics

### 2.1 Dynamically typed

In Ruby, you usually do not need to declare a variable's type in advance. Ruby determines the type from the value assigned to it.

```ruby
name = "Karthik"
age = 22
salary = 10000
```

- `name` contains a string.
- `age` contains an integer.
- `salary` contains an integer.

A variable can be reassigned a value of a different type:

```ruby
price = 500
price = "Five hundred"
```

Ruby allows this because it is dynamically typed.

### 2.2 Everything is an object

In Ruby, values such as strings and numbers are objects. Objects can have methods that perform actions or return information.

```ruby
name = "Karthik"
puts name.upcase
```

Output:

```text
KARTHIK
```

Explanation:
- `"Karthik"` is a string object.
- `upcase` is a method called on that string.
- The method returns the uppercase version.
- `puts` prints the result.

Another example:

```ruby
number = 10
puts number.even?
```

Output:

```text
true
```

The `even?` method checks whether the number is even. In Ruby, methods ending in `?` commonly return a boolean value (`true` or `false`).

### 2.3 Open-source

Ruby's source code is publicly available, and the language is developed by a community. Ruby can be used without paying a language licence fee.

---

## 3. Installing Ruby and Rails

The original setup notes use Windows. Installation steps can differ by operating system.

### Install Ruby

1. Download Ruby from [RubyInstaller](https://rubyinstaller.org/) on Windows.
2. Verify the installation:

```bash
ruby -v
```

This prints the installed Ruby version.

### Install Rails

Install Rails as a Ruby gem:

```bash
gem install rails
```

Then check the installed Rails version:

```bash
rails -v
```

**What is a gem?** A gem is a packaged Ruby library or tool. Rails is distributed as a gem.

### Ruby version managers

Tools such as **RVM** and **rbenv** help developers install and switch between Ruby versions. Which tool to use depends on the operating system and development environment.

---

## 4. Create a new Rails project

Example commands from the original Windows notes:

```bat
cd C:\Users\Sudha
mkdir rails_projects
cd rails_projects
rails new my_first_app
cd my_first_app
```

What each command does:

- `cd` changes the current directory.
- `mkdir rails_projects` creates a folder named `rails_projects`.
- `rails new my_first_app` generates a new Rails application and its starting files.
- `cd my_first_app` moves into the application folder.

The example path `C:\Users\Sudha` is specific to the original setup. Use a folder path that exists on your own computer.

### Other ways to create an application

**Standard Rails app:**

```bash
rails new project_name
```

**API-focused Rails app:**

```bash
rails new project_name --api
```

The `--api` option creates an application configured mainly for serving API responses, with a reduced web interface stack.

**Scaffolding:** Rails scaffolding can generate a starting set of components for a resource, such as products. It is different from `rails new`, which creates the overall application.

---

## 5. Run the Rails server

From inside the Rails project directory, run:

```bash
rails server
```

Then open this address in your browser:

[http://localhost:3000](http://localhost:3000)

- `localhost` refers to your own computer.
- `3000` is the default development server port.
- The server lets you view and test the Rails app locally.

Stop the server with `Ctrl + C` in the terminal.

---

## 6. Important Rails concepts to learn next

Rails commonly organises application code using **MVC**:

- **Model:** represents data and handles data-related logic.
- **View:** presents information, often as HTML.
- **Controller:** receives a request and coordinates the response.
- **Routes:** connect incoming URLs and HTTP methods to controller actions.
- **Migrations:** describe changes to the database structure.

These are introductory definitions. Learn each one in detail in later lessons.

---

## 7. Commands to remember

| Command | Purpose |
|---|---|
| `ruby -v` or `ruby --version` | Check the Ruby version |
| `gem install rails` | Install Rails |
| `rails -v` or `rails --version` | Check the Rails version |
| `rails new my_first_app` | Create a Rails application |
| `cd my_first_app` | Enter the project directory |
| `rails server` | Start the local development server |

---

## 8. Quick revision

1. **What is Ruby?** An open-source, dynamically typed programming language.
2. **What is Rails?** A web application framework built on Ruby.
3. **What does dynamically typed mean?** Ruby determines a value's type at runtime; a variable can later be assigned a value of another type.
4. **What does it mean that everything is an object?** Values such as strings and numbers are objects that can respond to methods.
5. **What is a gem?** A packaged Ruby library or tool.
6. **What does `rails new` do?** Generates a new Rails application.
7. **What does `rails server` do?** Starts the app's local development server.
8. **What is `localhost:3000`?** The default local address used to access the development server.

## 9. Practice questions

Try answering these without looking at the notes:

1. What is the difference between Ruby and Rails?
2. What will this code print?

   ```ruby
   name = "Karthik"
   puts name.upcase
   ```

3. Will Ruby allow this reassignment? Why?

   ```ruby
   price = 500
   price = "Five hundred"
   ```

4. Which command checks the Rails version?
5. Which command starts the Rails server?
6. What is the purpose of `rails new my_first_app`?

---

**Day 1 takeaway:** Ruby is the language, Rails is the framework, and the basic commands let you create and run a Rails application. The next step is to understand the Rails project structure and how a request moves through the app.

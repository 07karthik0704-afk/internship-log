# Day 4 — Ruby Data Types, Variables & Basic Practice

These notes cover common Ruby data types, data structures, variable scopes, and a basic loop.

---

## 1. Ruby Data Types

Ruby values are objects. Common data types include:

| Type | Example | Meaning |
|---|---|---|
| String | `"Karthik"` | Text |
| Integer | `21` | Whole number |
| Float | `21.5` | Decimal number |
| Symbol | `:name` | Identifier, often used as a key |
| Boolean | `true` or `false` | Truth value |
| `nil` | `nil` | Represents the absence of a value |

You can check the class of a value with `.class`:

```ruby
puts "Karthik".class # String
puts 21.class        # Integer
puts 21.5.class      # Float
puts true.class      # TrueClass
puts nil.class       # NilClass
```

**Note about strings:** Ruby strings are mutable by default, which means their contents can be changed. A string can be frozen to prevent modification.

---

## 2. Ruby Data Structures

### Array — an ordered collection

An array stores multiple values in order. Array indexes start at `0`.

```ruby
students = ["Karthik", "Anisha", "Devi"]

puts students[0] # Karthik
puts students[1] # Anisha
puts students[2] # Devi
```

### Hash — key-value pairs

A hash stores values associated with keys.

```ruby
student = { name: "Karthik", age: 21 }

puts student[:name] # Karthik
puts student[:age]  # 21
```

Here, `:name` and `:age` are symbol keys.

### Range — a sequence of values

A range represents a sequence, often used with numbers.

```ruby
puts (1..5).to_a  # [1, 2, 3, 4, 5]
puts (1...5).to_a # [1, 2, 3, 4]
```

- `1..5` includes `5`.
- `1...5` excludes `5`.

---

## 3. Variables and Scope

Ruby variable types use different naming patterns:

| Type | Example | General meaning |
|---|---|---|
| Local variable | `name` | Available within its local scope |
| Global variable | `$name` | Accessible globally; use sparingly |
| Instance variable | `@name` | Belongs to an object |
| Class variable | `@@name` | Shared within a class and its inheritance hierarchy; use carefully |
| Constant | `NAME` | Constant-style name; Ruby allows reassignment but warns in typical cases |

### Local variable example

```ruby
def greet
  name = "Karthik"
  puts name
end

greet # Karthik
```

The local variable `name` is available inside the method where it is defined. It cannot be directly accessed outside that method.

### Connection to Rails

In the Rails controller:

```ruby
def index
  @students = Student.all
end
```

`@students` is an instance variable. In the view, Rails can access it:

```erb
<% @students.each do |student| %>
  <%= student.name %>
<% end %>
```

Here, `student` is the block variable representing the current record during each iteration. `student.name` calls the name accessor on that Student object.

---

## 4. Basic Ruby Loop

A `for` loop can iterate through a range:

```ruby
for i in 1..3
  puts i
end
```

Output:

```text
1
2
3
```

The range `1..3` includes both `1` and `3`.

### String interpolation

Ruby uses `#{...}` inside a double-quoted string to insert an expression's value:

```ruby
for i in 1..3
  puts "Iteration number: #{i}"
end
```

Output:

```text
Iteration number: 1
Iteration number: 2
Iteration number: 3
```

### Inclusive and exclusive ranges

```ruby
(1..5).to_a  # [1, 2, 3, 4, 5]
(1...5).to_a # [1, 2, 3, 4]
```

- `..` includes the endpoint.
- `...` excludes the endpoint.

---

## 5. Quick Revision

- Ruby values are objects.
- Strings store text, integers store whole numbers, and floats store decimal numbers.
- Arrays store ordered values; hashes store key-value pairs; ranges represent sequences.
- Variable prefixes and naming conventions help indicate scope.
- `@students` is an instance variable in the Rails controller.
- `@students.each` iterates through the student records.
- `#{i}` inserts the value of `i` into a double-quoted string.

## 6. Practice Questions

1. What does `25.class` return?
2. What does `students[1]` return for `students = ["Karthik", "Anisha", "Devi"]`?
3. What is the difference between `{ name: "Karthik" }` and `["Karthik", "Anisha"]`?
4. What is the difference between `1..5` and `1...5`?
5. In a Rails controller, what does `@students` indicate?
6. What will this code print?

   ```ruby
   for i in 1..3
     puts "Number: #{i}"
   end
   ```

# Day 8 — Ruby Conditional Statements & Access Modifiers

## Learning goals

By the end of these notes, you should be able to:
- Use `if` and `else` for basic decisions.
- Write a simple ternary expression.
- Check multiple conditions with `elsif`.
- Use `case` / `when`, including ranges and condition-style cases.
- Understand `unless`.
- Check for `nil` using `nil?`.
- Explain the difference between `public`, `protected`, and `private` methods.

---

## 1. `if` / `else` — Basic Conditions

Use `if` to run code when a condition is true, and `else` to run another block when it is false.

```ruby
stock = 5

if stock > 0
  puts "Item available in stock"
else
  puts "Item not available"
end
```

Output:

```text
Item available in stock
```

How it works:
- `stock > 0` is the condition.
- If it is `true`, Ruby runs the `if` block.
- If it is `false`, Ruby runs the `else` block.
- `end` closes the conditional statement.

For `stock = 0`, the condition `stock > 0` is false, so the output is `Item not available`.

---

## 2. Ternary Operator (`? :`)

The ternary operator is a compact way to write a simple `if` / `else`.

General structure:

```ruby
condition ? value_if_true : value_if_false
```

Example:

```ruby
is_active = false
result = is_active ? "Product Active" : "Product Inactive"
puts result
```

Output:

```text
Product Inactive
```

Another example:

```ruby
stock = 5
message = stock > 0 ? "Available" : "Out of stock"
puts message
```

Use a ternary operator for short, simple decisions. For complicated logic, regular `if` / `else` is often easier to read.

---

## 3. `if` / `elsif` / `else` — Multiple Conditions

Use `elsif` to check additional conditions in order. Ruby runs the first matching branch and skips the rest.

```ruby
price = 450
discount = 0

if price >= 100 && price < 200
  discount = 10
elsif price >= 200 && price < 500
  discount = 20
elsif price >= 500 && price < 1000
  discount = 35
else
  discount = 0
end

puts "Discount: #{discount}%"
```

Output:

```text
Discount: 20%
```

### Important operators

- `&&` means **AND**. Both conditions must be true.
- `>=` means greater than or equal to.
- `<` means less than.
- `#{discount}` inserts the variable's value into the string. This is called **string interpolation**.

For `price = 450`, the first condition is false, the second is true, so the discount becomes `20`.

---

## 4. `case` / `when` with Ranges

`case` / `when` is another way to choose between multiple possible values.

```ruby
price = 450

case price
when 100..200
  discount = 10
when 201..500
  discount = 20
when 501..1000
  discount = 35
else
  discount = 0
end

puts "Discount: #{discount}%"
```

Output:

```text
Discount: 20%
```

### Inclusive and exclusive ranges

```ruby
100..200   # Includes 100 and 200
100...200  # Includes 100 but excludes 200
```

The ranges in the example cover whole-number price bands without overlapping. For decimal prices, design ranges carefully so values at the boundaries are handled as intended.

---

## 5. `case` Without a Variable

You can write `case` without placing a value after it. Each `when` can then test a Boolean condition.

```ruby
price = 450
discount = 0

case
when price >= 100 && price < 200
  discount = 10
when price >= 200 && price < 500
  discount = 20
when price >= 500 && price < 1000
  discount = 35
else
  discount = 0
end

puts "Discount: #{discount}%"
```

Output:

```text
Discount: 20%
```

Ruby checks the `when` conditions from top to bottom and executes the first matching branch.

### Compare the two `case` styles

| Style | Example | Useful when |
|---|---|---|
| With a value | `case price` / `when 201..500` | Matching a value against ranges or choices |
| Without a value | `case` / `when price >= 200 && price < 500` | Checking several Boolean conditions |

---

## 6. `unless`

`unless` is the opposite of `if`: its main block runs when the condition is false.

```ruby
stock = 0

unless stock > 0
  puts "No stock available"
else
  puts "Stock available"
end
```

Output:

```text
No stock available
```

Because `stock > 0` is false, Ruby runs the main `unless` block.

The same logic can be written with `if` / `else`:

```ruby
if stock > 0
  puts "Stock available"
else
  puts "No stock available"
end
```

Tip: use `unless` for simple negative conditions. Complicated `unless` statements, especially those with `else`, can be harder to read.

---

## 7. Checking `nil` with `nil?`

In Ruby, `nil` represents the absence of a value. The `nil?` method checks whether a value is `nil`.

```ruby
email = nil

if email.nil?
  puts "Email is missing"
else
  puts "Email is present"
end
```

Output:

```text
Email is missing
```

Compare:

```ruby
nil.nil?      # true
false.nil?    # false
"".nil?       # false
```

An empty string (`""`) is not `nil`; it is a string containing zero characters.

### Rails note

Rails provides `blank?`, which treats `nil`, an empty string, and whitespace-only strings as blank.

```ruby
nil.blank?    # true
"".blank?     # true
"   ".blank?  # true
"hello".blank? # false
```

`blank?` is a Rails/Active Support method, not a standard Ruby `Object` method available in every plain Ruby program.

---

## 8. Access Modifiers: `public`, `protected`, and `private`

Access modifiers control how methods can be called. They help define which methods are part of a class's public interface and which are intended for internal use.

### 8.1 Public methods

Public methods can generally be called from outside the object.

```ruby
class Animal
  def eat
    puts "Animal is eating"
  end
end

animal = Animal.new
animal.eat
```

Output:

```text
Animal is eating
```

### 8.2 Private methods

Private methods are intended for internal use. In ordinary Ruby calls, you cannot call a private method with an explicit receiver such as `dog.sleep`.

```ruby
class Dog
  def eat
    puts "Dog is eating"
    sleep
  end

  private

  def sleep
    puts "Dog is sleeping"
  end
end

dog = Dog.new
dog.eat
```

Output:

```text
Dog is eating
Dog is sleeping
```

This normally raises `NoMethodError`:

```ruby
dog.sleep
```

The method `eat` can call `sleep` internally without an explicit receiver.

### 8.3 Protected methods

Protected methods can be called within the class and subclasses, subject to Ruby's protected-method receiver rules. They are useful for behavior shared by related classes but not intended as public methods.

```ruby
class Animal
  def eat
    puts "Animal is eating"
    run
  end

  protected

  def run
    puts "Animal is running"
  end
end

animal = Animal.new
animal.eat
```

Output:

```text
Animal is eating
Animal is running
```

This normally raises `NoMethodError`:

```ruby
animal.run
```

A child class can also use inherited protected behavior:

```ruby
class Animal
  protected

  def run
    puts "Animal is running"
  end
end

class Dog < Animal
  def eat
    puts "Dog is eating"
    run
  end
end

Dog.new.eat
```

Output:

```text
Dog is eating
Animal is running
```

### Quick comparison

| Modifier | Main idea | Can you usually call `object.method` from outside? |
|---|---|---|
| `public` | Public interface | Yes |
| `protected` | Related-class/subclass behavior | No |
| `private` | Internal implementation | No |

### Syntax to remember

```ruby
class Example
  def visible_method
    puts "Public by default"
  end

  protected

  def related_class_method
    puts "Protected method"
  end

  private

  def internal_method
    puts "Private method"
  end
end
```

In Ruby, instance methods are public by default until a visibility modifier changes the visibility of subsequent methods.

### Rails connection

Rails models and controllers often use private helper methods for internal logic—for example, preparing data or checking a condition. Keeping internal helpers private helps make the class's intended public interface clearer.

---

## 9. Quick Comparison of Conditional Statements

| Statement | What it does | Example |
|---|---|---|
| `if` / `else` | Chooses between two paths | `if stock > 0` |
| Ternary | Short form of a simple `if` / `else` | `active ? "Yes" : "No"` |
| `elsif` | Adds another condition | `elsif price < 500` |
| `case` / `when` | Selects a matching branch | `case price` |
| `unless` | Runs the main block when a condition is false | `unless stock > 0` |
| `nil?` | Checks whether a value is `nil` | `email.nil?` |

---

## 10. Practice Exercises

Try to answer before checking the answer key.

### Exercise 1 — `if` / `else`

```ruby
stock = 0

if stock > 0
  puts "Available"
else
  puts "Unavailable"
end
```

What is printed?

### Exercise 2 — Ternary

```ruby
is_active = true
puts is_active ? "Active" : "Inactive"
```

What is printed?

### Exercise 3 — Multiple conditions

```ruby
price = 750

if price < 200
  discount = 10
elsif price < 500
  discount = 20
elsif price < 1000
  discount = 35
else
  discount = 0
end

puts discount
```

What number is printed?

### Exercise 4 — Range

```ruby
price = 200

case price
when 100..200
  puts "First range"
when 201..500
  puts "Second range"
else
  puts "Other"
end
```

Which message is printed? Pay attention to the range endpoints.

### Exercise 5 — `unless`

```ruby
is_logged_in = true

unless is_logged_in
  puts "Log in"
else
  puts "Welcome"
end
```

What is printed?

### Exercise 6 — `nil?`

```ruby
phone = ""

puts phone.nil?
```

What Boolean value is printed, and why?

### Exercise 7 — Access modifiers

```ruby
class Animal
  def eat
    run
  end

  protected

  def run
    puts "Running"
  end
end

animal = Animal.new
animal.eat
```

Does this print `Running` or raise an error? Explain briefly.

### Exercise 8 — Private method

```ruby
class Dog
  def make_sound
    bark
  end

  private

  def bark
    puts "Woof!"
  end
end

dog = Dog.new
dog.make_sound
```

What is printed? Would `dog.bark` work as an ordinary call from outside the class?

---

## 11. Answer Key

1. `Unavailable`
2. `Active`
3. `35`
4. `First range`, because `100..200` includes `200`.
5. `Welcome`
6. `false`; an empty string is not `nil`.
7. It prints `Running`; the public `eat` method calls the protected method internally.
8. It prints `Woof!`; `dog.bark` normally raises `NoMethodError` because `bark` is private.

---

## 12. Revision Checklist

- [ ] I can explain when `if` and `else` run.
- [ ] I can read and write a ternary expression.
- [ ] I know Ruby checks `elsif` conditions in order.
- [ ] I understand `case` / `when` and inclusive ranges.
- [ ] I know `unless` runs its main block when the condition is false.
- [ ] I know `nil`, `false`, and `""` are different.
- [ ] I can explain public, protected, and private methods.
- [ ] I understand why a private helper method can be called inside its class but not normally through an outside object call.

**Practical tip:** Run these examples in a Ruby file or `irb`. Before running each one, predict the output and then explain why Ruby chose that branch.

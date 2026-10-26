# Day 9 — Rails Model Validations & Business Logic

## Learning goals
- Explain why Rails models use validations.
- Validate presence, uniqueness, length, numericality, and format.
- Run validations only when a condition is true.
- Write custom validation methods and add errors.
- Understand gem-based profanity checking.
- Implement business rules that depend on product activity and stock.

---

## 1. What Are Model Validations?

Model validations check whether model data meets the application's rules before Rails saves a record through normal Active Record operations.

```ruby
class Customer < ApplicationRecord
  validates :name, presence: true
end
```

- `Customer` is the model class.
- `ApplicationRecord` provides Active Record functionality.
- `validates` declares a validation rule.
- `:name` is the attribute being checked.
- `presence: true` means the value must be present.

### `new`, `valid?`, and saving

```ruby
customer = Customer.new(name: "")
customer.valid? # false
customer.save   # false, if the validation still fails
```

`new` creates an object in memory; it does not save it. `valid?` runs validations and returns `true` or `false`. A failed validation prevents a normal `save` from succeeding.

Inspect errors with:

```ruby
customer.errors.full_messages
```

---

## 2. Presence Validation

```ruby
class Customer < ApplicationRecord
  validates :name, presence: true
end
```

The name must be present. A missing value or empty string fails this validation.

```ruby
customer = Customer.new(name: "")
puts customer.valid? # false
```

This rule does not automatically fill in a missing name.

---

## 3. Uniqueness Validation

```ruby
class Customer < ApplicationRecord
  validates :email, uniqueness: true
end
```

Rails checks whether another record already has the same email. If a matching record exists, the validation normally fails.

```ruby
customer = Customer.new(email: "arun@example.com")
customer.valid? # false if another customer already uses this email
```

A common error message is `Email has already been taken`.

**Important database detail:** A model-level uniqueness validation alone cannot guarantee uniqueness when two requests arrive at nearly the same time. For important values such as email, also add a **unique database index**. The model validation gives useful feedback; the index enforces uniqueness at the database level.

---

## 4. Length Validation

```ruby
class Product < ApplicationRecord
  validates :description, length: { minimum: 3, maximum: 20 }
end
```

The description must have at least 3 characters and no more than 20 characters. Both endpoints are allowed.

- `"TV"` — invalid because it has 2 characters.
- `"Laptop"` — passes this length rule because it has 6 characters.
- A description with 21 characters — invalid.

A record can still fail other validations even when its description passes.

---

## 5. Numericality Validation

```ruby
class Product < ApplicationRecord
  validates :price, numericality: { greater_than_or_equal_to: 1 }
end
```

The price must be numeric and at least `1`.

| Value | Passes this validation? | Reason |
|---|---|---|
| `100` | Yes | Numeric and at least 1 |
| `1` | Yes | Meets the minimum |
| `0` | No | Below the minimum |
| `0.5` | No | Below the minimum |
| `-10` | No | Below the minimum |
| `"cheap"` | No | Not numeric |

This validation does not automatically change a low price to `1`.

---

## 6. Format Validation with a Regular Expression

```ruby
class Product < ApplicationRecord
  validates :name, format: { with: /\A[a-zA-Z0-9 ]+\z/ }
end
```

This regular expression allows English letters, digits, and spaces.

| Pattern | Meaning |
|---|---|
| `\A` | Beginning of the string |
| `a-z` | Lowercase English letters |
| `A-Z` | Uppercase English letters |
| `0-9` | Digits |
| Space | A literal space |
| `+` | One or more allowed characters |
| `\z` | End of the string |

Examples:
- `"Laptop 123"` — valid.
- `"Smart TV"` — valid.
- `"Phone-Case"` — invalid because `-` isn't allowed.
- `"Laptop@"` — invalid because `@` isn't allowed.

If a name is required, add a presence validation as well:

```ruby
validates :name, presence: true,
                 format: { with: /\A[a-zA-Z0-9 ]+\z/ }
```

The regex rejects an empty string, but `presence: true` clearly communicates the requirement.

---

## 7. Conditional Validation

You can run a validation only when a condition is true.

```ruby
class Product < ApplicationRecord
  validates :stock, presence: true, if: :is_active?
end
```

Rails calls `is_active?` to decide whether to run the stock validation.

- If `is_active?` returns `true`, stock must be present.
- If `is_active?` returns `false`, this particular stock validation is skipped.
- Rails does not automatically set stock to zero or change the product's active state.

The method or attribute that determines activity must exist in your actual model.

| Active? | Stock | This validation |
|---|---|---|
| Yes | `10` | Passes |
| Yes | `nil` | Fails |
| No | `nil` | Skipped |

---

## 8. Custom Validation with `validate`

Use a custom validation when a built-in rule doesn't cover the business requirement.

```ruby
class Product < ApplicationRecord
  validate :deny_profanity

  def deny_profanity
    bad_words = ["idiot", "stupid", "nonsense", "fool", "lopper"]

    if bad_words.any? { |word| name.to_s.match?(/#{Regexp.escape(word)}/i) }
      errors.add(:name, "contains inappropriate words")
    end
  end
end
```

How it works:
- `validate :deny_profanity` tells Rails to run the method during validation.
- `bad_words` contains the words to check.
- `any?` returns `true` if at least one word matches.
- `name.to_s` turns `nil` into an empty string for this check.
- `Regexp.escape(word)` treats each word literally in the regex.
- `/i` makes matching case-insensitive.
- `errors.add(:name, "...")` adds a validation error to `name`.

If a custom validation adds an error, `valid?` returns `false`.

**Caution:** This is a basic learning example, not a complete production profanity filter. It can match a listed word inside a larger word. Production checks should define word-boundary behavior and test false positives and false negatives.

### Built-in vs custom

```ruby
validates :name, presence: true  # Built-in validation
validate :deny_profanity         # Custom validation method
```

Use `validates` for common rules. Use `validate` when you need your own logic.

---

## 9. Profanity Validation Using a Gem

A gem may provide ready-made profanity detection.

Example based on the API in these learning notes:

```ruby
class Product < ApplicationRecord
  validate :block_bad_words

  def block_bad_words
    if ProfanityFilter::Base.profane?(name)
      errors.add(:name, "contains profanity")
    end
  end
end
```

If the gem's checker returns `true`, Rails adds an error. If it returns `false`, this method adds no error.

**Important:** This assumes the gem is installed and supports `ProfanityFilter::Base.profane?`. Check the chosen gem's documentation for the correct API. Detection can make mistakes, so test it for your application's needs.

| Custom word list | Gem |
|---|---|
| You maintain the words and logic | The gem supplies detection logic |
| Simple for a small example | May handle more cases |
| Full control over the list | Behavior depends on the gem and configuration |

---

## 10. Custom Stock Dependency Validation

Suppose an active product must have stock, but an inactive product may omit it.

```ruby
class Product < ApplicationRecord
  validate :check_stock_for_active

  def check_stock_for_active
    if is_active? && stock.blank?
      errors.add(:stock, "must be present when product is active")
    end
  end
end
```

The condition `is_active? && stock.blank?` uses `&&` (AND). Both parts must be true before an error is added.

- `is_active?` checks whether the product is active.
- `stock.blank?` checks whether stock is blank. In Rails, `blank?` includes `nil`, an empty string, and whitespace-only strings.
- `errors.add` records a validation error.

| Active? | Stock | This custom validation |
|---|---|---|
| Yes | `10` | Passes |
| Yes | `nil` | Fails |
| Yes | `""` | Fails |
| No | `nil` | Passes this check |

Other validations can still fail even if this custom check passes.

### Built-in conditional vs custom validation

Built-in conditional version:

```ruby
validates :stock, presence: true, if: :is_active?
```

Custom version:

```ruby
validate :check_stock_for_active

def check_stock_for_active
  if is_active? && stock.blank?
    errors.add(:stock, "must be present when product is active")
  end
end
```

Both express the same basic rule. The built-in version is shorter; the custom version is useful when the logic or error behavior needs more control.

---

## 11. Quick Comparison

| Syntax | Type | Purpose |
|---|---|---|
| `validates :name, presence: true` | Built-in | Require a value |
| `validates :email, uniqueness: true` | Built-in | Reject duplicates at model-validation time |
| `validates :description, length: {...}` | Built-in | Restrict character length |
| `validates :price, numericality: {...}` | Built-in | Require a numeric value and limits |
| `validates :name, format: {...}` | Built-in | Require a text pattern |
| `validates :stock, presence: true, if: :is_active?` | Conditional built-in | Run a rule only when a condition is true |
| `validate :method_name` | Custom | Run your own validation method |
| `errors.add(:attribute, "message")` | Custom helper | Attach an error to an attribute |

---

## 12. Practice Exercises

Try answering these before checking the answer key.

### Exercise 1 — Presence

```ruby
validates :name, presence: true
customer = Customer.new(name: "")
puts customer.valid?
```

What is printed?

### Exercise 2 — Uniqueness

A customer already exists with `email: "a@example.com"`.

```ruby
validates :email, uniqueness: true
customer = Customer.new(email: "a@example.com")
puts customer.valid?
```

Assuming the database contains that existing customer, what does the validation return?

### Exercise 3 — Length

```ruby
validates :description, length: { minimum: 3, maximum: 20 }
product = Product.new(description: "TV")
puts product.valid?
```

What is printed, assuming other validations pass?

### Exercise 4 — Numericality

```ruby
validates :price, numericality: { greater_than_or_equal_to: 1 }
product = Product.new(price: 0.5)
puts product.valid?
```

What is printed, assuming other validations pass?

### Exercise 5 — Format

```ruby
validates :name, format: { with: /\A[a-zA-Z0-9 ]+\z/ }
product = Product.new(name: "Phone-Case")
puts product.valid?
```

What is printed, assuming other validations pass?

### Exercise 6 — Conditional validation

```ruby
validates :stock, presence: true, if: :is_active?
```

The product is inactive and stock is `nil`. Is this stock validation run?

### Exercise 7 — Custom validation

```ruby
def check_name
  errors.add(:name, "is not allowed") if name == "blocked"
end
```

What happens when this method runs and `name` is `"blocked"`?

### Exercise 8 — Business logic

An active product has `stock = nil`:

```ruby
if is_active? && stock.blank?
  errors.add(:stock, "must be present when product is active")
end
```

Are both parts of the condition true, and is an error added?

---

## 13. Answer Key

1. `false` — the name is blank.
2. `false` — another customer already uses the email, assuming the model validation sees that record.
3. `false` — `"TV"` is shorter than 3 characters.
4. `false` — `0.5` is below the minimum of 1.
5. `false` — the hyphen is not allowed by the regex.
6. No. The validation is skipped because the product is inactive.
7. An error is added to `name`; the record fails validation if the error remains.
8. Yes. Both conditions are true, so the stock error is added.

---

## 14. Revision Checklist

- [ ] I can explain what model validations do.
- [ ] I know the difference between `valid?` and saving a record.
- [ ] I understand `presence` and `uniqueness`.
- [ ] I can use `length`, `numericality`, and `format`.
- [ ] I understand `if: :is_active?`.
- [ ] I know the difference between `validates` and `validate`.
- [ ] I understand how `errors.add` makes a custom validation fail.
- [ ] I can explain the stock dependency business rule.
- [ ] I know model-level uniqueness should be backed by a unique database index when required.

**Practical tip:** In a Rails console, create a model object with `Model.new(...)`, call `valid?`, and inspect `errors.full_messages`. This lets you test validations without saving test data to the database.

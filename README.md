# jlogic_ruby

> **This is a maintained fork** of [bhgames/json-logic-ruby](https://github.com/bhgames/json-logic-ruby), published as the [`jlogic_ruby`](https://rubygems.org/gems/jlogic_ruby) gem (the `json_logic` name is owned by the upstream project). Most of these fixes have since been merged upstream too — thanks @bhgames — this fork stays around for the couple still pending and for the `jlogic_ruby` gem name itself. Fixes: `==`/`!=` nil handling, `if`, `reduce`, `in`, `===` range matching, and a global core-class monkey-patch — see [CHANGELOG.md](CHANGELOG.md) for details. Verified against the full official [jsonlogic.com test suite](http://jsonlogic.com/tests.json) (297/297 passing).

Build complex rules, serialize them as JSON, and execute them in ruby.

**jlogic_ruby** is a ruby parser for [JsonLogic](http://jsonlogic.com). Other libraries are available for parsing this logic for Python and JavaScript at that link!

## DANGER WILL ROBINSON!

This Ruby version of [jwadhams/json-logic](https://github.com/jwadhams/json-logic)
does not have the same functionality as the original Javascript version.  In
particular, *method* is not implemented at all and *add_operation* can only define
operations with **_exactly_** one parameter.  There are probably other deficiencies
as well.

This does not mean that JsonLogic is not useful... IMHO, it is.  However, the class of
problems it can solve is a subset of the class of problems that 
[jwadhams/json-logic](https://github.com/jwadhams/json-logic) can solve.

Contributions of expertise, time and energy are welcome to correct these deficiencies
and bring the Ruby version up to parity with the Javascript version.  Inquire within
if you are interested.

## Installation

`gem install jlogic_ruby`

Or in your `Gemfile`:

```ruby
gem 'jlogic_ruby', require: 'json_logic'
```

The gem name is `jlogic_ruby`, but it's a drop-in replacement — you still `require 'json_logic'` and use the `JSONLogic` module exactly as documented below.

## Why use JsonLogic?

If you're looking for a way to share logic between front-end and back-end code, and even store it in a database, JsonLogic might be a fit for you.

JsonLogic isn't a full programming language. It's a small, safe way to delegate one decision. You could store a rule in a database to decide later. You could send that rule from back-end to front-end so the decision is made immediately from user input. Because the rule is data, you can even build it dynamically from user actions or GUI input.

JsonLogic has no setters, no loops, no functions or gotos. One rule leads to one decision, with no side effects and deterministic computation time.

## Virtues
1. Terse.
2. Consistent. {"operator" : ["values" ... ]} Always.
3. Secure. We never eval(). Rules only have read access to data you provide, and no write access to anything.
4. Flexible. Easy to add new operators, easy to build complex structures.

## Examples

### simple

```ruby
JSONLogic.apply({ "==" => [1, 1] }, {})
# => true
```

This is a simple rule, equivalent to 1 == 1. A few things about the format:

1. The operator is always in the 「key」 position. There is only one key per JsonLogic rule.
2. The values are typically an array.
3. Each value can be a string, number, boolean, array (non-associative), or null

### Compound

Here we're beginning to nest rules.

```ruby
JSONLogic.apply(
{ "and" => [
  { ">" => [3,1] },
  { "<" => [1,3] }]
}, {})

# => true
```

In an infix language (like JavaScript) this could be written as:

```
( (3 > 1) && (1 < 3) )
```

### Data-Driven

Obviously these rules aren't very interesting if they can only take static literal data. Typically jsonLogic will be called with a rule object and a data object. You can use the var operator to get attributes of the data object:

```ruby
 JSONLogic.apply(
  { "var" => ["a"] }, # Rule
  { "a" => 1, "b" => 2 }  # Data
 )
 # => 1
```

If you like, we support syntactic sugar on unary operators to skip the array around values:


```ruby
JSONLogic.apply(
  { "var" => "a" },
  { "a" => 1, "b" => 2 }
 )
# => 1
```

You can also use the `var` operator to access an array by numeric index:

```ruby
JSONLogic.apply(
  { "var" => 1 },
  ["apple", "banana", "carrot"]
)
# => "banana"
```

Here's a complex rule that mixes literals and data. The pie isn't ready to eat unless it's cooler than 110 degrees, and filled with apples.

```ruby
rules = JSON.parse(%Q|{ "and" : [
  {"<" : [ { "var" : "temp" }, 110 ]},
  {"==" : [ { "var" : "pie.filling" }, "apple" ] }
] }|)

data = JSON.parse(%Q|{ "temp" : 100, "pie" : { "filling" : "apple" } }|)

JSONLogic.apply(rules, data)

# => true
```

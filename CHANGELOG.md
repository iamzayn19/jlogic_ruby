# Changelog

## 0.5.0

Maintained fork of [bhgames/json-logic-ruby](https://github.com/bhgames/json-logic-ruby), published as `json-logic-ruby` since the `json_logic` gem name is owned by the upstream project.

- Fix `==`/`!=` treating a missing/nil `var` lookup as equal to `""`, `0`, or `false` (rewrote as proper loose equality per the JsonLogic spec)
- Fix `reduce` not resolving a logic expression (e.g. `{"var": "..."}`) passed as the initial accumulator value
- Fix `if` returning the last condition or the full argument list instead of `nil` when nothing matches
- Fix `in` raising on a `nil` array instead of returning `false`
- Fix `===` to use Ruby's native `===` instead of `==`, adding `Range`/`Regexp`/`Class` matching (e.g. date-in-range checks) while staying strict for plain values
- Remove the global `Hash#transform_keys` monkey-patch, which broke the native mapping-hash form (`transform_keys(hash)`) for every other gem in the same process
- Fix `add_operation` polluting `Object`/`Class` via unsafe metaprogramming

All fixes verified against the full official [jsonlogic.com test suite](http://jsonlogic.com/tests.json) (297/297 passing).

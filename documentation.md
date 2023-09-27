# Documentation for JOSM's MapCSS Validator

The validator in JOSM is a potent tool underpinned by the MapCSS language, offering a robust framework for data quality assurance within the OpenStreetMap (OSM) ecosystem. With MapCSS as its foundation, this feature in the Java OpenStreetMap Editor (JOSM) empowers you to create and execute custom validation rules, providing meticulous control over the evaluation of OSM map data. I've tried to distill the most important parts of the MapCSS validation documentation here. If you need more functionality, you can check if it is available on the [JOSM Wiki](https://josm.openstreetmap.de/wiki/Help/Validator/MapCSSTagChecker).

Lastly, using the [MapCSS Syntax Highlighter](https://marketplace.visualstudio.com/items?itemName=whammo.mapcss-syntax) VS Code extension can help visualize the complicated JOSM validation syntax.

## Table of Contents

- [Selectors](#selectors)
  - [Types](#types)
  - [Blocks](#blocks)
  - [Classes](#classes)
  - [Pseudo-classes](#pseudo-classes)
  - [Negation](#negation)
- [Function](#function)
  - [Commands](#commands)
  - [Functions](#functions)

# Selectors

Selectors are how rules pick which objects they target. Selectors are chained, refining the objects that will be returned to your functions as you add additional parameters, as if adding `AND` boolean operators. Order does not matter, as long as a type comes first.

## Types

Every selector must begin with one of the following types.

| type       | description                                                          |
| ---------- | -------------------------------------------------------------------- |
| `*`        | Select any object.                                                   |
| `node`     | Select any node.                                                     |
| `way`      | Select any way.                                                      |
| `relation` | Select any relation.                                                 |
| `area`     | Select any area: either a closed way or a set of ways in a relation. |

## Blocks

Selector blocks are always contained within brackets, and filter through an object's OSM tags. Regex patterns with escape characters for both the key and values need only be single-escaped.

### Key evaluation within blocks

| type      | description                                                       | example         |
| --------- | ----------------------------------------------------------------- | --------------- |
| presence  | Key does exist on object.                                         | `["shop"]`      |
| absence   | Key does not exist on object.                                     | `[!"amenity"]`  |
| regex     | Regular expression matching to key, contained within slashes.     | `[/max[a-z]+/]` |
| not regex | Regular expression non-matching to key, contained within slashes. | `[!/addr:.*/]`  |

### Value evaluation within blocks

These value evaluations can be combined with any of the above key evaluations. More-niche operators are explained [here](https://josm.openstreetmap.de/wiki/Help/Styles/MapCSSImplementation#Conditionselector).

| operator             | description                                                | example                         |
| -------------------- | ---------------------------------------------------------- | ------------------------------- |
| `=`                  | Simple equivalence.                                        | `["shop"="gift"]`               |
| `!=`                 | Not equal.                                                 | `["amenity"!="fuel"]`           |
| `>`, `<`, `>=`, `<=` | Comparison of numerical values.                            | `["maxspeed">40]`               |
| `^=`                 | Value starts with this string.                             | `["name"^="St."]`               |
| `$=`                 | Value ends with this string.                               | `["name"$="Hwy"]`               |
| `=~`                 | Regular expression matching, contained within slashes.     | `["addr:postcode"=~/[0-9]{5}/]` |
| `!~`                 | Regular expression non-matching, contained within slashes. | `["addr:postcode"!~/[0-9]{5}/]` |

## Classes

You can name selection groups so that they can be reused, mixed, or matched as needed elsewhere. For example:

```js
*["name"] {
  set name;
}

way.name {
  /* do something */
}
```

is equivalent to:

```js
way["name"] {
  /* do something */
}
```

Note that unlike the functions below, the `set` operator does not take a colon.

## Pseudo-classes

Additional selectors that describe the state of an object. Always joined to previous selector elements with a `:`. Most common to use for validation rules is probably `:modified`, which will make the rule only apply to already-modified objects. You can read about the rest of these pseudo-classes [here](https://josm.openstreetmap.de/wiki/Help/Styles/MapCSSImplementation#PseudoClasses).

## Negation

MapCSS negates any following selectors with a `!` exclamation point after the last affirmative selector. Every selector thereafter will be negated like a `NOT` boolean operator.

```js
*["name"] {
  set name;
}

way!.name {
  /* selects all ways that do not belong to the 'name' class */
}

way:modified!.name {
  /* selects modified ways that do not belong to the 'name' class */
}

way!.name:modified {
  /* selects unmodified ways that do not belong to the 'name' class */
}
```

## Other

Other capabilities, like a zoom selector, parent/child object selector, and layer identifier are more advanced or seldom used for validator rules. More information is available in the full MapCSS documentation.

# Function

Each line in the body of the rule represents a different operation for the rule to perform, always in the form of `command: string;`.

```js
*["key"] {
  command: string;
  command: string;
  command: string;
}
```

## Commands

### `throw*`

You can only use one `throw*` function per rule.

| name           | description              |
| -------------- | ------------------------ |
| `throwError`   | Raise an error message.  |
| `throwWarning` | Raise a warning message. |
| `throwOther`   | Raise an info message.   |

### `fix*`

You can use as many `fix*` functions per rule as needed.

| name              | description                                                                                    | example              |
| ----------------- | ---------------------------------------------------------------------------------------------- | -------------------- |
| `fixAdd`          | Add or overwrite the given key and value. Requires both the key and value, separated by a `=`. | `"key=value"`        |
| `fixRemove`       | Remove the given key.                                                                          | `"key"`              |
| `fixChangeKey`    | Change the key.                                                                                | `"old_key=>new_key"` |
| `fixDeleteObject` | Delete the object. Rarely used!                                                                |                      |

### `suggestAlternative`

Provide an alternative string without activating the <kbd>Fix</kbd> button.

### `group`

Show a message under which other, more specific `throwError`, `throwWarning`, or `throwOther` messages can be grouped.

```js
*["name"],
*["phone"] {
  throwWarning: "{0.key}";
  group: "Key is unexpected";
}
/*
Would appear in JOSM validation panel as:
Key is unexpected [+]
   phone [+]
   name [+]
*/
```

### `assert*`

Unit tests example keys and values to see if the selectors are working. Does not check the rule's commands, just the selection. The `validator.check_assert_local_rules` setting must be set to `true`.

| name            | description                                             | example       |
| --------------- | ------------------------------------------------------- | ------------- |
| `assertMatch`   | Check that the selector matches the given string.       | `"key=value"` |
| `assertNoMatch` | Check that the selector doesn't match the given string. | `"key"`       |

## Strings

The second part of each line must always evaluate to a string. The most basic of these possibilities is a simple `"string"`. However, plenty of operations can be done to transform or manipulate tags and values before being passed to the command.

### String functions

I've listed the most common functions for use with the validator below. The full list of functions supported by JOSM are available [here](https://josm.openstreetmap.de/wiki/Help/Styles/MapCSSImplementation#Evalexpressions).

#### `tr(placeholder, *args) -> str`

Returns a string with other strings inserted. The first string is the placeholder pattern, with later strings representing the arguments that match to the pattern, starting with `0`. For example:

```js
tr("{0} {1}-{2}@@{3}", "abc", "qwe", upper("xyz"), lower("Foo"));
/* would yield "abc qwe-XYZ@@foo" */
```

#### `concat(*args)`

Joins strings together.

```js
concat("phone=", replace("123-456-7890", "-", ""));
/* would yield "phone=1234567890" */
```

#### `regexp_match(regex_pattern, string, regex_flags?) -> list`

Returns a list of regex matches to the `regex_pattern` given the input `string`. First item of the list is always the full match, followed by any regex capture groups in the pattern string. The `regex_flags` argument is optional.

❗Escape characters in `regex_pattern` need to be double-escaped! ❗

#### `get(list, item) -> str`

Retrieve a string from a given `list` the `item`, provided as an integer starting at `0`. Useful for retrieving `regexp_match` values.

#### `replace(string, old, new) -> str`

Returns the string with instances of the `old` string replaced by the `new`.

#### `tag(key) -> str`

Returns the value of a given key, provided as a `str`, if one exists. Otherwise, returns `null`.

#### `b ? fst : snd`

Conditional operator that can be used to add some simple logic for what to pass to the command. You can use any of the following common comparison operators in the conditional: `==`, `!=`, `<`, `>`, `<=`, `>=`, `||`, `&&`, or `!`. For example, the following rule checks if the values for `website` and `url` are the same, and if so, deletes `url`:

```js
*["website"]["url"] {
  throwWarning: "Duplicate tag: {0.key}";
  fixRemove: tag("{0.key}") == tag("{1.key}") ? tr("{0}", "{1.key}") : "";
}
```

#### Other

There are lots of other useful string functions, like `join`, `split`, `upper`, `lower`, `title`, `length`, `count`, and many more in the [JOSM Wiki](https://josm.openstreetmap.de/wiki/Help/Styles/MapCSSImplementation#Evalexpressions).

### Placeholders

Placeholders look up the selector, helping to reduce rule the need to rewrite the same code for different keys or values. The placeholder uses the index of the selector, starting at `0` with the first non-type filter. They are similar in syntax to Python f-strings, going between brackets in a string. They require one of the following selector attributes:

| name    | output                                                                | syntax        |
| ------- | --------------------------------------------------------------------- | ------------- |
| `key`   | String of the indicated selector's key.                               | `"{i.key}"`   |
| `value` | String of the indicated selector's value.                             | `"{i.value}"` |
| `tag`   | String of the indicated selector's key and value, separated by a `=`. | `"{i.tag}"`   |

For example, this:

```js
way["name"]["shop"] {
  fixRemove: "name";
}

way["phone"]["shop"] {
  fixRemove: "phone";
}
```

has the same effect as this:

```js
way["name"]["shop"],
way["phone"]["shop"] {
  fixRemove: "{0.key}";
}
```

Note that placeholders don't work while also using [classes](#classes).

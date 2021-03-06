# Pipe operator for JavaScript

## Authors
* David Rhys White (@davidrhyswhite)

## Overview and motivation

Piping is useful when composing many small pure functions together, one problem with this is the evaluation needs to be read from the inner most expression value in reverse to the outer most function call. Given the following statement we can see that it would be read as 'call math round then call math square then pass in PI', whereas the order of execution is in reverse.

```javascript
const myNumber = Math.round(Math.sqrt(Math.PI));
```

Unix has long provided a piping mechanism to achieve that, for example: `ps aux | grep node` as well as F# and the Elixir programming language, both provide the `|>` pipe greater-than operator which is very readable.

## Syntax

My proposal would be to use the slightly more verbose `|>` pipe-greater than syntax, it provides a convenient direction of travel for the expression on the left side, into the expression on the right.

```javascript
const myNumber = Math.PI |> Math.sqrt |> Math.round;
```

The left side of the operator should always be a primitive data type or data structure, these could be any of the following: `Boolean`, `Null`, `undefined`, `Number`, `String`, `Symbol` or `Object`. Since functions are standard objects they can be passed in as the initial value, as long as the function on the right handles the calling on that function.

It also provides a way to either log, process, or do anything with the data from the last expression on the left at various stages of the execution without adding additional brackets in and around the calls, for example.

```javascript
function logger (callback) {
  return function (value) {
    callback(value);
    return value;
  };
}

Math.PI
  |> logger(console.log);
  |> Math.sqrt
  |> logger(console.log);
  |> Math.round
  |> logger(console.log);
```

While a slightly contrived example as we certainly wouldn't write code that looks like:

```javascript
logger(Math.round(logger(Math.sqrt(logger(Math.PI)))));
```

We would instead assign to variables at each stage of execution:

```javascript
const PI = Math.PI;
logger(PI);

const squaredPI = Math.sqrt(PI);
logger(squaredPI);

const roundedSquaredPI = Math.round(squaredPI);
logger(roundedSquaredPI);
```

However with this clarity we have unfortunately had to create additional constants within this lexical block, whereas simple value passing between pure functions provides a very clean and readable approach and allows easier updates in the future if we wanted to add additional processing within the chain.

### Notes
* A single `|` operator would be harder to implement given we already have the `||` double pipe operator in JavaScript, parsers would need to look forward to see if the next operator is not a second `|` pipe operator which makes a better case for the `|>` pipe greater-than operator. It would be new to JavaScript, though has already been adopted by at least 1 other language.   

## External References
* [F# pipe operator](https://docs.microsoft.com/en-gb/dotnet/fsharp/language-reference/functions/index#function-composition-and-pipelining)
* [Elixir pipe operator](https://elixir-lang.org/getting-started/enumerables-and-streams.html#the-pipe-operator)
* [Unix pipeline](https://en.wikipedia.org/wiki/Pipeline_(Unix))

## Scoping in practice

A good way to determine your scope, and understand how your code will be read by the JavaScript Engine, is to ask the following questions:

> [!NOTE]
> - Does this line generate a block scope?
> - If so, where does it starts and ends?
> - Define it's:
> 1. Variables;
> 2. Constants; 
> 3. Functions;
> 4. Statements. 
> - What is its lexical scope? Or, what other identifiers can it access?


Below, we will see some examples considering the following code:

``` js
const myName = 'Isa'; 

function first() {
const age = 30;

if (age >= 30) {
const decade = 3;
var millennial = true;
}

function second() {
const job = 'developer';
console.log(`${myName} is a ${age}-old ${job}`)
}
second();
}

first();
```

What we have above is a **nested structure of scopes.**

### Global scope in practice
The global scope comprehends everything on a JS source code that is not within a block scope.

Global scope: 
``` js
myName = 'Isa';
first = <function>
```

- Variables: none
- Constants: myName
- Functions: first();
- Statements: none.

Note that the `second` function is not part of the global scope.

In this code, the `second` function is declared inside the `first` function. This means that `second()` is only accessible within the `first` function's local scope and any nested scopes within it.


### First() local/function/scope block:
``` js
age = 30;
age >= 30
millennial = true;
second = <function>
```

- Variables: millennial;
- Constants: decade;
- Functions: second();
- Statements: age >= 30.

When we say that the if statement is part of the first function's scope, we mean that the entire block of code within the if statement is part of the function's scope. This includes the condition `age >= 30` as well as any variables or functions defined within the if block.

### Lexical scope of First():

In the given code, the lexical scope created by the function `first()` includes:

1.  The global scope, which includes the variable `myName`.
2.  The local scope of the `first()` function, which includes the variables `age`, `decade`, and the nested function `second()`.
3. The `millennial` variable, because it was declared with "var" so it is not block scoped.

``` js
myName = 'Isa'; 
age = 30;
age >= 30
millennial = true;
second = <function>;
```

This means that, after the JavaScript engine searches for the identifiers within the variable environment, functions and statements of first(), it will search for which other identifiers can be accessed within this execution context.

These other variables are defined outside of the function scope, but within its lexical scope. The search goes from the inner to outer scopes until the engine reaches the global scope and then it stops, whether it found them or not. Just like the building we saw on the previous page:

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%2020230406121730.png)

### Second() local/function/block scope:
``` js

job = 'developer';
console.log(`${myName} is a ${age}-old ${job}`);
```

- Variables: none;
- Constants: `job`;
- Functions: none;
- Statements: `console.log(${myName} is a ${age}-old ${job});`

### Second() lexical scope:

In the given code, the lexical scope created by the function `second()` includes:

1.  The global scope, which includes the variable `myName`.
2.  The local scope of the `second()` function, which includes the variable `job`, and the statement function `console.log(...)`.
3. The `millennial` variable, because it was declared with "var".
4.  The variable environment of the `first()` function, because the engine does a look-up on the code to find the value of the mentioned identifiers. As we saw above: *the outer scopes are always accessible from the inner scopes.*

``` js
job = 'developer';
console.log(`${myName} is a ${age}-old ${job}`)
myName = 'Isa';
age = 30;
age >= 30
millennial = true;
 ```
5. However, as you could notice above, the opposite is not true.

Not only you **cannot access block-scoped variables from outside of its block if they are declared with let or const**, but you can use this information to "hide" certain identifiers.

In this case:
- The constant `decade` from the if statement cannot be accessed from the `second()` function, because the "if statement" creates a block scope and its identifiers cannot be accessed from outer scopes, only from its own inner nested scopes (if it had any).
- If I declare a let or const variable inside of the `second()` function block scope, I'll not be able to access it from the `first()` function:

```js

  
function first() {  

 function second() {  
  let test = 3;  
 }  
 second();  
console.log(test});  //referenceError: 'test' is not defined.
}  
  
first();
```

**Because JavaScript looks up, from the inner to the outer scopes. Not the other way around.**

### How does scoping works within if() statements?

In modern JavaScript, code blocks like `if` statements and `for` loops create block-scoped variables using the `let` and `const` keywords. Block-scoped variables are only accessible within the block in which they are declared, and their scope is determined by the location of the declaration within the code block.

If() statements are not functions, but as we learned above, they create what we call their own "block scope".

If () scope:
``` js
decade = 3;
millennial = true;
```

- Variables: millennial;
- Constants: decade;
- Functions: none
- Statements: none.

The `age >= 30` expression is evaluated within the context of the `first` function's local scope, and the result of the expression determines whether the code block within the `if` statement is executed or not.

In other words, the `if` statement executes the code block within it only if the condition `age >= 30` evaluates to `true`. **The condition is not a part of the `if` block's scope, but it affects which parts of the code within the block is executed.**

The `age >= 30` expression is actually part of the `first` function's local scope, rather than being part of the `if` block's scope. And it's part of the `second()` function's lexical scope, because it is nested within the `first()` function.

So the `age >= 30` expression is not part of the `if` block's scope **because it is evaluated within the `first` function's local scope, not within the `if` block's scope.** 

The `if` block's scope includes only the variables and functions declared within the block itself, and its lexical scope includes the identifiers declared on its parent scopes (the local scope of the first() function, in this case.

On the upcoming next page, we will discuss the "this" keyword.
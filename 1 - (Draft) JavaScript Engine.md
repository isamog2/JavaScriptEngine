### Intro
JavaScript is not an interpreted language.

JavaScript Engines are programs that execute JS code. Browsers, for example, have their own JavaScript Engine.

Each browser has its own JS Engine and V8 is the engine that powers Google Chrome and Node.js.

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230327142157.png)

Every JavaScript Engine is composed of a CallStack and a Heap:
![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230327133411.png)

The memory heap is an unstructured memory pool, where the objects are stored.
The call stack is where the code is executed considering its execution context.

## How is the code executed within the Call stack?
Machine code: Computers only understand binary. 0's and 1's. 
Source code: the fun stuff you write on your IDE.

Your source code needs to be translated into machine code, for the computer to understand it.
This translation happens through compilation or interpretation.

## Parsing
Once the sourcecode enters the engine, the first step is to parse the code. 
Parsing means the engine is reading the source code. During this process, the code is parsed into a data structure called "Abstract Syntax Tree" or AST. 
Each line of code is split down into pieces that are meaningful to the language like "const" or function keywords, and then the pieces are saved into the tree in a structured way.

This process also checks if there are any syntax errors. The resulting tree will later be used to generate the machine code.

Example:

``` js
const x = 23;
```

Huge AST is generated for this smol constant declaration:

```js
VariableDeclaration {  
 start: 0
 end: 13  
 declarations: [  
 VariableDeclarator {  
 start: 6 
 end: 12 
 id: Identifier { 
 start 6  
 end: 7  
 name: "x" 
 } 
 init: Literal 
 start: 10 
 end: 12 
   value: 23 
   raw: "23"
   }  
 }  
 }  
  kind: const 
```

So that simple "const = 23" needs to become all of the above, just so it can become machine code, just so it can be executed.

The next step is the compilation. 

### Translation into machine code:

#### Compilation
The compilation is a form of translation where the entire AST code is converted into machine code at once. The source code becomes a portable binary file that can be executed by a computer. The execution of the machine code file can happen way after compilation.

#### Interpretation
Through interpretation, the AST code is read and executed at the same time. This means that on each line, the interpreter reads it, translates and executes it, and then it moves to the next one. The translation happens right before execution on each line. That results in slow and low performance, and it's how JS used to work.

#### Just in time compilation
JavaScript Engines now run through Just-in-time Compilation (JIT):
The entire AST code is converted into machine code and doesn't get stored in a portable machine code file because it's immediately executed. 
It's not fully translated at once and only executed later on, but it's also not read and executed line by line. It's fully translated at once and then immediately executed.

During Just in time compilation, JavaScript first creates an unoptimized machine language to start executing as soon as possible. In the background, this "bad machine code" is being optimised and recompiled during the program execution.
![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230327140534.png)

This last part happens completely separated from the CallStack, in some special threads inside the engine that we cannot access through our code, and it's the reason why V8 is so fast.

Once the code is compiled, the execution context starts to be created.

## 1. Execution contexts

An execution context is an environment in which a piece of JavaScript is executed.

First, the Top-level code is executed, and only when it's finished, the engine starts to execute functions and wait for callbacks.

The global execution context is created when a JavaScript script first starts to run, and it represents the global scope in JavaScript.

 
## Runtime

Runtime is a big container for all the things that we need to run JavaScript somewhere. 
In the context of a browser, this is what the JS Runtime environment is made of:

- JavaScript Engine (made of the heap and call stack);
- Web APIs
- The callback queue
- The event loop

### Web APIs
API -> Application Programming Interface.

APIs are functionalities provided to the engine, who are not part of the JS engine itself. JavaScript gets access to APIs through the global window object. They include everything regarding the DOM, Timers, Fetch APIs, etc. They are also part of the runtime because are also required for JS to run.

Example: you create an App and would like it to connect to other apps. It would be a huge problem if you had to create custom integrations for each possible App. 
You can build an API into your application: a doorway where third-party applications can get access to the data and features of our App without the need to provide any custom code whatsoever.

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230327143422.png)

For this other App the access a feature that we built for example, it will send an API request. 

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230327143704.png)

The API request is an HTTP message. It can be a "get" request, some of these most common requests:
- Post
- Get 
- Put
- Patch
- Delete
Or other uncommon ones.

These methods correspond to CRUD operations:
- Create
- Read
- Update
- Update
- Delete

Basically, Post requests create something on the application; Get reads something on our application; Put updates something on our application; Patch also updates but put will replace, while patch will modify the information. Delete will delete the application.

On Node.js, the JS runtime has C++ bindings and thread pools instead of Web APIs. The WEB APIs, as. the name says, are for browsers.

### The callback queue

A data structure that contains all the callback functions that are ready to be executed.

Example: 

``` js
document.querySelector('.check').addEventListener('click', handleClick);
```

We attach event handler functions to DOM elements like a button to react to some events. These event handler functions are also called "callback functions".
As the event happens (like the "click" above), the callback function will be called -> put on top of the "callback queue" (through the event loop).

Once the call stack is empty, the callback function from the callback queue is passed to be executed.

### The event loop
The event loop is when the callback function is taken from the callback queue to the call stack, to be executed.

## Part 2: Execution contexts
Global executed context is created for top-level code
Top-level code is code that is not inside any function.
Only code outside functions are executed. Since functions should only be executed when they are called.

Example:
``` js
const name = 'Isa';
```

This first line "const name" declaration, is top-level code and it will be executed in the global execution context.

The code inside the functions below will only be executed when the functions are called

```js
 const first = () => {
  	let a = 1;
const b = second();
	a = a + b;
 	return a;
 };
 function second() {
 	var c = 2;
 	return c;
 }
 ```

Compilation -> Creation of global execution context (top-level code) -> execution of top-level code inside global Execution Context.

Once top-level code is finished, functions start execution as well.

One execution context is created per function:
For each function call, a new execution context is created.

All of these Execution Contexts together, create the Call Stack.

### What is inside the execution context?
1. Variable environment: 
- `Let`, `const` and `var` declarations
- Functions
- Arguments

2. Scope chain 

3. This Keyword

The Variable Environment is created during Creation Phase, right before Execution.
The first thing within the variable environment =>  Let, const, and var declarations;

Then we have Functions. Each function has its own execution context.

Last but not least, the Arguments object -> all the arguments that were passed inside the function inside each execution context.
Any argument inside of this function will be inside of the variable environment of this specific execution context.
  
A function can also access variables outside the function, thanks to the scope chain.

Scope chain is made by references to variables that are located outside of the current function.  
Each context also has a special variable called the "this" keyword.
  
Once the creation phase is finished, we have Execution. **Execution contexts belonging to arrow functions don't get their own arguments keyword. nor do they get the "This" keyword.** 

Arrow functions don't have an arguments object or this keyword but they can use the arguments and this from their closest regular function parent.  
  
So we have a global execution context;  
An execution context for the first function;
Another one is for the second function.
  
Global execution context contains variable declarations. Example:  

  ```js
const name = 'Isa';  
const first = () => {  
 let a = 1;  
 const b = second(7, 9);  
 a = a + b;  
 return a;  
}  
function second(x, y) {  
 var c = 2;  
 return c;  
}  
const x = first();  
```

Global Variable environment would be composed of:  
``` js
name = 'Isa',  
first = <function>  
second = <function>  
x = <unknown>;  
```

Because ''Isa'' is top-level code, we can access its content from the start.
First has its own EC and its EC has its own Variable Environment. So from here, we can only see it's a function, but it was not called yet.  
Second also has its own  Execution Context.
const X will only be known after "first()" is executed, so is unknown at this point.  
  
For the functions, their variable environment (inside their execution context) will literally contain all the code of that particular function.  
  
Technically, values only become known during execution. 
  
First() Variable Environment:

``` js
a = 1
b = <unknown>  
```  

The value of b is unknown at this point because it needs the value of second to be executed.

Second() Variable Environment:  
``` js
c = 2
arguments = [7, 9]
```

On a big function with multiple variable environments and execution contexts, it could be hard for JS Engine to keep track. For this reason, it has the Call Stack.

## Stacking calls
Let's consider this code again:


  ```js
const name = 'Isa';  
const first = () => {  
 let a = 1;  
 const b = second(7, 9);  
 a = a + b;  
 return a;  
}  
function second(x, y) {  
 var c = 2;  
 return c;  
}  
const x = first();  
```


When the compiled code starts execution, the top-level code will start execution. Once this starts, the Global execution context will be created. This EC then gets moved to the Call Stack.

This is the global execution context that was created with the top-level code:

``` js
name = 'Isa',  
first = <function>  
second = <function>  
const x = first();  
```

Since it's the only one there at that point, it's on top of the call stack, so it's executed first:

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328103440.png)

On the last line, we declare `const x = first();` so the function `first();` is called.
Top-level code gets executed without being called, as it's part of the Global context. **Functions, on the other hand, need to be called** in order to create an execution context and be executed.

Once this function is called, it gets its own execution context so it can run the code that is inside its body.

At this point, the Execution Context of first() gets moved on top of the global context within the Call Stack. Since the first() is now on top, global context execution pauses for the first() EC to be executed:

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328104207.png)

On the second line of first(), second() gets called with the arguments (7, 9). 
Once this is done, it creates an execution context for second(). The execution of first() pauses and second() gets moved to the top of the stack for its execution.

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328104520.png)

On the last line of function second() we have a "return c;". The "return" means that we are ceasing the execution of this function. So the function second() execution context will be popped off the call stack. 

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328104706.png)

The previous execution context is now back to being the active execution context.

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328104811.png)

We're back to the first function but now on the third line (a = a + b;). After this calculation, we have a "return a", this means that first() will also be popped off the call stack.

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328104929.png)

Then the global context will be the first in line again. We are back at the Global Execution Context, but now at the last line. This time, the return value is finally assigned to X, and the execution is finished. 

![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328105225.png)

The program will stay in this state forever, at least until we close the browser. The global execution context is only popped off the call stack when we close the browser.
![](https://github.com/isamog2/StudiesMadeVisual/blob/main/ObsidianSharing/Obsidian%20Vault/Images/Pasted%20image%2020230328105225.png)

# 8 JavaScript optimizations Closure Compiler can do conventional minifiers just can’t

Closure Compiler is a JavaScript type checker and optimizer. But just what can these optimizations do to shrink that oh-so-important bundle size?

Disclaimer: This is not a completely fair test. Please don’t flame me. But feel free to contribute more tests.

* Smaller, simpler programs tend be easier to statically analyze and can be optimized more aggressively
* The ES6 polyfils from Babel tend to be quite heavy compared to Closure which are a one time cost and look bad in small examples
* Closure and Babel/Uglify do not make the same assumptions. Closure optimizations break code that abuses the dynamic nature of JS. See full restrictions
* I am not a Babel/Uglify expert. Please alert me if I am using these tools incorrectly or ineffectively

### 1) Inline Functions

[Closure 17b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dfunction%2520my_function()%2520%257B%250A%2520%2520console.log(%2522f%2522)%253B%250A%257D%250Amy_function()%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function(s)%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26DISAMBIGUATE_PROPERTIES%3Dtrue%26AMBIGUATE_PROPERTIES%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 56b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=GYVwdgxgLglg9mABAWwJ4H1SVggFASkQG8AoRRCBAZzgBsBTAOlrgHNcAiYD_AbhIC-JNJnDR4YAryA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&prettier=false&targets=&version=6.26.0&envVersion=)

```js
// input.js
function my_function() {
  console.log("f");
}
my_function();
```

```js
// output.js
console.log("f");
```


### 2) Strip Dead Code

[Closure 24b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dclass%2520A%2520%257B%250A%2520%2520method%28%29%2520%257B%250A%2520%2520%2520%2520console.log%28%2522A.method%2522%29%253B%250A%2520%2520%257D%250A%257D%250A%250Aclass%2520B%2520%257B%250A%2520%2520deadCode%28%29%2520%257B%250A%2520%2520%2520%2520console.log%28%2522this%2520is%2520dead%2520code!%2522%29%253B%250A%2520%2520%257D%250A%257D%250A%250Anew%2520A%28%29.method%28%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 436b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=GYVwdgxgLglg9mABAWwJ4H1SVggFASkQG8AoRRCBAZzgBsBTAOlrgHNcAiYD_AbhIC-JNJnDR4YAryA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&prettier=false&targets=&version=6.26.0&envVersion=)

```js
// input.js
var name = "closure ";
function a() { return "compiler" }
function myScript(){ console.log('hello ' + name + a());}
myScript();
```

```js
// output.js
console.log("A.method");
```

### 3)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

### 4)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

### 5)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

### 6)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

### 7)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

### 8)

[Closure xxb]()
[Babel xxb]()

```js
// input.js
```

```js
// output.js
```

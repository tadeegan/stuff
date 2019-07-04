# 8 JavaScript optimizations Closure Compiler can do conventional minifiers just can’t

Closure Compiler is a JavaScript type checker and optimizer. But just what can these optimizations do to shrink that oh-so-important bundle size?

Disclaimer: This is not a completely fair test.

* Smaller, simpler programs tend be easier to statically analyze and can be optimized more aggressively
* The ES6 polyfils from Babel tend to be quite heavy compared to Closure which are a one time cost and look bad in small examples
* Closure and Babel/Uglify do not make the same assumptions. Closure optimizations break code that abuses the dynamic nature of JS. See full restrictions
* I am not a Babel/Uglify expert. Please alert me if I am using these tools incorrectly or ineffectively. Feel Free to give counter examples.

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

[Closure 24b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3D%252F%252F%2520input.js%250Avar%2520name%2520%253D%2520%2522closure%2520%2522%253B%250Afunction%2520a()%2520%257B%2520return%2520%2522compiler%2522%2520%257D%250Afunction%2520myScript()%257B%2520console.log('hello%2520'%2520%252B%2520name%2520%252B%2520a())%253B%257D%250AmyScript()%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function(s)%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_TYPES%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26INLINE_VARIABLES%3Dtrue%26LABEL_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26CLOSURE_PASS%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 436b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=GYVwdgxgLglg9mABAWwJ4H1SVggFASkQG8AoRRCBAZzgBsBTAOlrgHNcAiYD_AbhIC-JNJnDR4YAryA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&prettier=false&targets=&version=6.26.0&envVersion=)

ES6 polyfils are heavy here (and elsewhere in these tests).

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

### 3) Rename Prototype properties

[Closure 56b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dclass%2520A%2520%257B%250A%2520%2520constructor%28%29%2520%257B%250A%2520%2520%2520%2520this.prop%2520%253D%2520100%253B%250A%2520%2520%257D%250A%257D%250A%250Alet%2520a%2520%253D%2520new%2520A%28%29%253B%250A%250Aa.prop%252B%252B%253B%250Aconsole.log%28a.prop%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26MISSING_PROPERTIES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26INLINE_PROPERTIES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26EXTRACT_PROTOTYPE_MEMBER_DECLARATIONS%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26USE_TYPES_FOR_LOCAL_OPTIMIZATION%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26DISAMBIGUATE_PROPERTIES%3Dtrue%26AMBIGUATE_PROPERTIES%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 216b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=MYGwhgzhAECC0G8BQ1rAPYDsIBcBOArsDungBQCUiKq0OAFgJYQB0ADnum9ALzQCMABkEBuGgF8kkpCACmOaGF7RMsgO5xKYpGHac2AagNiM2dHJYh0AczK6OXCiKA&debug=false&circleciRepo=&evaluate=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&targets=&version=6.26.0)

```js
// input.js
class A {
  constructor() {
    this.prop = 100;
  }
}

let a = new A();

a.prop++;
console.log(a.prop);
```

```js
// output.js
var a=new function(){this.a=100};a.a++;console.log(a.a);
```

### 4) Inline Variables

[Closure 35b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dfunction%2520f%28x%29%2520%257B%250A%2520%2520console.log%28%2522got%253A%2520%2522%2520%252B%2520x%29%253B%250A%257D%250Avar%2520someVar%2520%253D%25209001%253B%250Af%28someVar%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 78b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=GYVwdgxgLglg9mABMAFADwJSIN4ChGIQIDOcANgKYB0ZcA5igER1xQBcijiA1IpgNy4AvrgBuAQwBOiUgFsKANSmIAvIgCcABk0BGQajmKpGfkA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&prettier=false&targets=&version=6.26.0&envVersion=)

```js
// input.js
function f(x) {
  console.log("got: " + x);
}
var someVar = 9001;
f(someVar);
```

```js
// output.js
console.log("got: 9001");
```

### 5) Statically infer call graph

[Closure 34b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dclass%2520A%2520%257B%250A%2520%2520method()%2520%257B%250A%2520%2520%2520%2520console.log(%2522A%2522)%253B%250A%2520%2520%257D%250A%257D%250A%250Aclass%2520B%2520%257B%250A%2520%2520method()%2520%257B%250A%2520%2520%2520%2520console.log(%2522B%2522)%253B%250A%2520%2520%257D%250A%257D%250A%250A%252F**%2520%2540return%2520%257BA%257D%2520*%252F%250Afunction%2520getA()%2520%257B%250A%2520%2520return%2520new%2520A()%253B%250A%257D%250A%250Alet%2520a%2520%253D%2520getA()%253B%250Alet%2520b%2520%253D%2520new%2520B()%253B%250A%250A%252F%252F%2520Statically%2520infer%2520A%2523method%2520or%2520B%2523method%2520in%2520a%2520dynamic%2520language%250Aa.method()%253B%250Ab.method()%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function(s)%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_TYPES%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26AMBIGUATE_PROPERTIES%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26DEVIRTUALIZE_METHODS%3Dtrue%26DISAMBIGUATE_PROPERTIES%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26INLINE_VARIABLES%3Dtrue%26LABEL_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26CLOSURE_PASS%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 727b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=MYGwhgzhAECC0G8BQ1oFsCmAXAFgewBMAKASkRVWmDwDsI8QMA6EPAcyICJZOSBuCgF8kwpKEgwAQuVSZchUjMrU6DZqw6dJvAamGiA9ACoj0AAIAnbAFcLNRLEHQjBpADNrNYFgCWtaGzYsIrIqFZYtvY0GADucKQCooxY0GDQALwBQQlIydAARhnQ0XGSOUgGBtAAylhgvsBgICAAntA-NG4YFnAAxHL4BNB4PZL92IPt9mkELTRgaD7A0OA0bNZggUhgTAMK_Ej5uxP7fEA&debug=false&circleciRepo=&evaluate=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&targets=&version=6.26.0)

```js
// input.js

class A {
  method() {
    console.log("A");
  }
}

class B {
  method() {
    console.log("B");
  }
}

/** @return {A} */
function getA() {
  return new A();
}

let a = getA();
let b = new B();

// Statically infer A#method or B#method in a dynamic language
a.method();
b.method();
```

```js
// output.js
console.log("A");console.log("B");
```

### 6) Determine function side effects for accurate code pruning


[Closure 108b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dvar%2520global%2520%253D%25200%253B%250Afunction%2520sideEffects%28a%29%2520%257B%250A%2520%2520global%252B%253Da%253B%250A%2520%2520console.log%28%2522added%253A%2520%2522%2520%252B%2520a%29%253B%250A%2520%2520return%2520a%253B%250A%257D%250A%250Afunction%2520pure%28a%252C%2520b%29%2520%257B%250A%2520%2520return%2520a%2520%252B%2520b%253B%250A%257D%250A%250A%252F%252F%2520var%2520is%2520unused%2520and%2520can%2520be%2520droped%2520but%2520value%2520computation%2520has%2520side%2520effects.%250Alet%2520unused1%2520%253D%2520sideEffects%281%29%253B%250Alet%2520unused2%2520%253D%2520sideEffects%282%29%253B%250Alet%2520unused3%2520%253D%2520sideEffects%283%29%253B%250Alet%2520unused4%2520%253D%2520sideEffects%284%29%253B%250Alet%2520unused5%2520%253D%2520sideEffects%285%29%253B%250Alet%2520unused6%2520%253D%2520sideEffects%286%29%253B%250Alet%2520sum%2520%253D%2520pure%284%252C%25204%29%253B%250A%250Aconsole.log%28%2522global%253A%2520%2522%2520%252B%2520global%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 311b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=G4QwTgBA5gNg9gIxDCBeCAGA3AKAGYCuAdgMYAuAlnERAM4UAmApgKJ55Pm0AUIAlBADeOCNHhIYAalQhcoktVpwYTAHTwo3AEQgGzBgC4IWiJIj85EMEzIEwNWTgC-OfMXJUaABztNeAGggEAWFRa1t7c1Mg3BccAHp4iFBICloIYgJaJgZzIlySEBoEJggGMDgvHKCCMmTkAlKFAFsfMhBKaggACxB0-mYIJnZOMlpVHBU6zOyGAEY0OkZWEa5uOb5cKYyiLJyAJkWBlY41_c3Jmx29hgBmI-W2U7HuW4vtmZyAFgfmJ9GeF93ldPgwAKy_E4A7hg4HTXazABskP-a0RcLoBGaix81m4X0CQNwOAURCUKnUcE0WlgiGQRhMZlpEk2QA&debug=false&circleciRepo=&evaluate=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&targets=&version=6.26.0)

```js
// input.js
var global = 0;
function sideEffects(a) {
  global+=a;
  console.log("added: " + a);
  return a;
}

function pure(a, b) {
  return a + b;
}

// var is unused and can be droped but value computation has side effects.
let unused1 = sideEffects(1);
let unused2 = sideEffects(2);
let unused3 = sideEffects(3);
let unused4 = sideEffects(4);
let unused5 = sideEffects(5);
let unused6 = sideEffects(6);
// var is unused and function has no side effects so we can remove all the code
let sum = pure(4, 4);

console.log("global: " + global);
```

```js
// output.js
var a = 0;
function b(c) {
  a += c;
  console.log("added: " + c);
}
b(1);
b(2);
b(3);
b(4);
b(5);
b(6);
console.log("global: " + a);
```

### 7) Collapse object namespaces / static functions


[Closure 257b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dlet%2520namespace%2520%253D%2520%257B%257D%253B%250Anamespace.foo%2520%253D%2520%257B%257D%253B%250Anamespace.foo.bar%2520%253D%2520function%28%29%2520%257B%250A%2520%2520console.log%28%2522body%2520needs%2522%29%253B%250A%2520%2520console.log%28%2522to%2520be%2520big%2520enough%2522%29%253B%250A%2520%2520console.log%28%2522to%2520prevent%2520inlining%2522%29%253B%250A%257D%250A%250Aclass%2520C%2520%257B%250A%2520%2520static%2520baz%28%29%2520%257B%250A%2520%2520%2509console.log%28%2522body%2520needs%2522%29%253B%250A%2520%2520%2509console.log%28%2522to%2520be%2520big%2520enough%2522%29%253B%250A%2520%2520%2509console.log%28%2522to%2520prevent%2520inlining%2522%29%253B%250A%2520%2520%257D%250A%257D%250A%250Alet%2520x%2520%253D%2520namespace.foo%253B%250Ax.bar%28%29%253B%250Ax.bar%28%29%253B%250AC.baz%28%29%253B%250AC.baz%28%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26DISAMBIGUATE_PROPERTIES%3Dtrue%26AMBIGUATE_PROPERTIES%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26PRETTY_PRINT%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 798b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=G4QwTgBA5gNg9gIxDCBeCAGA3AKAGYCuAdgMYAuAlnERAM4UAmApgKJ55Pm0AUIAlBADeOCNHhIYAalQhcoktVpwYTAHTwo3AEQgGzBgC4IWiJIj85EMEzIEwNWTgC-OfMXJUaABztNeAGggEAWFRa1t7c1Mg3BccAHp4iFBICloIYgJaJgZzIlySEBoEJggGMDgvHKCCMmTkAlKFAFsfMhBKaggACxB0-mYIJnZOMlpVHBU6zOyGAEY0OkZWEa5uOb5cKYyiLJyAJkWBlY41_c3Jmx29hgBmI-W2U7HuW4vtmZyAFgfmJ9GeF93ldPgwAKy_E4A7hg4HTXazABskP-a0RcLoBGaix81m4X0CQNwOAURCUKnUcE0WlgiGQRhMZlpEk2QA&debug=false&circleciRepo=&evaluate=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&targets=&version=6.26.0)

```js
// input.js

let namespace = {};
namespace.foo = {};
namespace.foo.bar = function() {
  console.log("body needs");
  console.log("to be big enough");
  console.log("to prevent inlining");
}

class C {
  static baz() {
  	console.log("body needs");
  	console.log("to be big enough");
  	console.log("to prevent inlining");
  }
}

let x = namespace.foo;
x.bar();
x.bar();
C.baz();
C.baz();
```

```js
// output.js
function a() {
  console.log("body needs");
  console.log("to be big enough");
  console.log("to prevent inlining");
}
function b() {
  console.log("body needs");
  console.log("to be big enough");
  console.log("to prevent inlining");
}
a();
a();
b();
b();
```

### 8) Remove unused returns

[Closure 86b](https://closure-compiler-debugger.appspot.com/j2cl_debugger.html#input0%3Dlet%2520global%2520%253D%25200%253B%250Afunction%2520getIncGlobal%28%29%2520%257B%250A%2520%2520if%2520%28global%2520%253E%2520100%29%2520%257B%250A%2520%2520%2520%2520%252F%252F%2520return%2520statement%2520unused!%2520Drop%2520it%252C%2520keep%2520side%2520effect.%250A%2520%2520%2520%2520return%2520%252B%252Bglobal%253B%250A%2520%2520%257D%250A%2520%2520return%2520global%253B%2520%252F%252F%2520return%2520statement%2520unused!%2520Drop%2520it.%250A%257D%250A%250Alet%2520unusedReturn%2520%253D%2520getIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250AgetIncGlobal%28%29%253B%250A%250Aconsole.log%28%2522global%253A%2520%2522%2520%252B%2520global%29%253B%26conformanceConfig%26externs%3Dvar%2520console%2520%253D%2520%257B%257D%253B%250A%252F**%2520%2540param%2520%257Bstring%257D%2520s*%252F%250Aconsole.log%2520%253D%2520function%28s%29%2520%257B%257D%253B%26refasterjs-template%26TRANSPILE%3Dtrue%26CHECK_SYMBOLS%3Dtrue%26CHECK_TYPES%3Dtrue%26MISSING_PROPERTIES%3Dtrue%26COMPUTE_FUNCTION_SIDE_EFFECTS%3Dtrue%26FOLD_CONSTANTS%3Dtrue%26DEAD_ASSIGNMENT_ELIMINATION%3Dtrue%26INLINE_CONSTANTS%3Dtrue%26INLINE_FUNCTIONS%3Dtrue%26COALESCE_VARIABLE_NAMES%3Dtrue%26INLINE_VARIABLES%3Dtrue%26FLOW_SENSITIVE_INLINE_VARIABLES%3Dtrue%26INLINE_PROPERTIES%3Dtrue%26SMART_NAME_REMOVAL%3Dtrue%26REMOVE_DEAD_CODE%3Dtrue%26EXTRACT_PROTOTYPE_MEMBER_DECLARATIONS%3Dtrue%26REMOVE_UNUSED_PROTOTYPE_PROPERTIES%3Dtrue%26REMOVE_UNUSED_CLASS_PROPERTIES%3Dtrue%26REMOVE_UNUSED_VARIABLES%3Dtrue%26COLLAPSE_VARIABLE_DECLARATIONS%3Dtrue%26COLLAPSE_ANONYMOUS_FUNCTIONS%3Dtrue%26CONVERT_TO_DOTTED_PROPERTIES%3Dtrue%26USE_TYPES_FOR_LOCAL_OPTIMIZATION%3Dtrue%26LABEL_RENAMING%3Dtrue%26COLLAPSE_PROPERTIES%3Dtrue%26DEVIRTUALIZE_PROTOTYPE_METHODS%3Dtrue%26DISAMBIGUATE_PROPERTIES%3Dtrue%26AMBIGUATE_PROPERTIES%3Dtrue%26VARIABLE_RENAMING%3Dtrue%26PROPERTY_RENAMING%3Dtrue%26OPTIMIZE_CALLS%3Dtrue%26OPTIMIZE_PARAMETERS%3Dtrue%26OPTIMIZE_RETURNS%3Dtrue%26CLOSURE_PASS%3Dtrue%26CROSS_MODULE_CODE_MOTION%3Dtrue%26CROSS_MODULE_METHOD_MOTION%3Dtrue%26includeDefaultExterns%3Dtrue)
[Babel 237b](https://babeljs.io/repl/#?babili=true&browsers=&build=&builtIns=false&code_lz=DYUwLgBA5sD2BGBDYEC8EAMBuAUAMwFcA7AYzAEtYjpwBJUgcTiWAAoBKCAbxwgnLwRWMBMggA-CAEYMGTjz58ATuAJLqAag0iWuPgF9eEFWDXUdyXIZyhIxAgGcQAEwBKq9Whph6JJqLZ2XCg6RmZkDmDQv3DAqJ8wgMicEISYpKCU6P8WZNTfHIjMnBIqB1hQADo4KFYAIgtgAC4IOogNaFigoA&debug=false&circleciRepo=&evaluate=false&lineWrap=true&presets=es2015%2Creact%2Cstage-2%2Cbabili&targets=&version=6.26.0)

```js
// input.js

let global = 0;
function getIncGlobal() {
  if (global > 100) {
    // return statement unused! Drop it, keep side effect.
    return ++global;
  }
  return global; // return statement unused! Drop it.
}

let unusedReturn = getIncGlobal();
getIncGlobal();
getIncGlobal();
getIncGlobal();
getIncGlobal();
getIncGlobal();
getIncGlobal();

console.log("global: " + global);
```

```js
// output.js
var a=0;function b(){100<a&&++a}b();b();b();b();b();b();b();console.log("global: "+a);
```

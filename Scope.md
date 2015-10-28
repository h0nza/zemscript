# Introduction #

For explanation of scoping see [wikipedia](http://en.wikipedia.org/wiki/Scope_%28programming%29)


# Dynamic Scoping #

The implementation in trunk uses dynamic scoping. When a function is called it makes a copy of the symbol table and sets it as the current symbol table to use. Then when the function exits it restores it to the previous symbol table. Since functions can call other functions it results in a stack of symbol tables.

```
x = 0;
f = function() {
    return x;
};
g = function() {
    x = 1;
    return f();
};
println("x: " ~ g()); // Prints x: 1
```

# Lexical Scoping #

The implementation in lexical\_scope branch uses lexical (static) scoping. When a function is defined the current symbol table is bound to it. Then when a function is called it copies the bounded symbol table and sets it as the current symbol table. When the function exits it restores the previous symbol table.

```
x = 0;
f = function() {
    return x;
};
g = function() {
    x = 1;
    return f();
};
println("x: " ~ g()); // Prints x: 0
```

The lexical\_scope branch also implements the global keyword. This allows for using a global variable inside a function. It works by using the global symbol table for any variable that has been declared as global in the current scope.
```
x = 0;
f = function() {
    global x;
    x = 1;
};
println("x: " ~ f()); // Prints x: 1
```

Defining a variable at the top level as global results in it behaving the same as dynamic scoping.
```
global x;
x = 0;
f = function() {
    return x;
};
g = function() {
    x = 1;
    return f();
};
println("x: " ~ g()); // Prints x: 1
```

Note that function parameters mask top level global variables. For example:
```
global msg;
msg = "Hello World!";
greet = function(msg) {
    return msg;
};
println(greet("Greetings!")); // Prints Greetings!
```

Also as part of the lexical scope implementation, lexical closures are supported. For example:
```
line = function(m, c) {
    return function(x) {
        return m * x + c;
    };
};
y = line(5, 2);
println(y(8)); // Prints 42
```
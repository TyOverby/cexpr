# Cexpr
## A lisp-like frontend on top of C


### Basic Function

A basic syntactic translation.

**basic_func.cexpr**
```scheme
(fn main:int (argc:int argv:char**)
    (* argc 3))
```

**basic_func.c**
```c
int main(int argc, char** argv) {
    return argc * 3;
}
```

### Header Generation
The `export` macro places anything inside into the header file instead

**header_generation.cexpr**
```scheme
(export fn foo:int (a:int b:int) (+ a b))
```

**header_generation.h**
```c
int foo(int a, int b) { return a + b }
```

### Parent Scope Access and Modification
```scheme
(fn read_n:u8* (name:char* n:int) 
    (var mem:u8* = (malloc n))
    (defer-failure (free mem))
    (check (!= mem 0))
    
    (var file:FILE* (fopen name "r"))
    (check (!= file 0))
    (defer (fclose file))
    (check (!= (fread mem 0 n file) 0))
    (return mem))
```

```c
u8* read_n(char* name, int n) {
    u8* mem = malloc(n);
    if (mem == 0) {
      goto: error;
    }
    
    FILE* file = fopen(name, "r");
    if (file == 0) {
      goto error;
    }
    if (fread(mem 0 n file) == 0) {
      goto: error;
    }
    fclose(file);
    return mem;

    error:
      free(mem);
      fclose(file);
}
```

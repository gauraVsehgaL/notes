* Type deduction - calls to function templates, auto, decltype
* It makes C++ software more adaptable. Changing a type at one place automatically propogates through type deduction at all locations.
* **Template Type Deduction**
```C++
template<typename T>
void Foo(paramType param){}

Foo(expr);
```
* case 1 : `paramType` is a pointer or a reference, but not universal reference. `template<typename T> void Foo(T &param)`
    *  If `expr's` type is a reference, ignore the reference part.
    *  Then pattern-match expr's type against `paramType` to determint T
*  case 2 : `paramType` is a universal reference.
`template<typename T> void Foo(T &&param)`
    * If `expr` is an lvalue, both `T` and `paramType` are deduced to  be lvalue references.
    * If `expr` is an rvalue, the *Case 1* rule applies.
* case 3 : `paramType` is neither a pointer, nor a reference. We are dealing with pass by value. `template<typename T> void Foo(T param);` `param` will be a copy of wharever is passed in - a completely new object.
* The ability to declare references to arrays, gives us the possibility to find an array size at compile time.
```c++
template<typename T, size_t N>
constexpr
size_t getSize(T (&)[N]){
    return N;
}
```
* Function type decay to function pointer just like arrays decay to array pointer.
* During template type deduction, arguments that are array or function names decay to pointers, unless they are used to initialize references.

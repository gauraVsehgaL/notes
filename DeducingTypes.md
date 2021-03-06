## Template type deduction
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

## Item 2  : auto type deduction
* `auto` type deduction is template type deduction with one exception. Comparing to template deduction example, `auto` plays the role of `T`, and **the type specifier for the variable acts as the `paramType`**. 
  * `const auto cx = x`, the type specifier is `const auto`
  * `const auto &cx = x`, the type specifier is `const auto&`
* Similar to template type deduction for universal references(case 2)
```c++
auto x = 42; //case 3
const auto cx = x; //case 3, cx is neither, so cx is const int
const auto &rx = x; //case 1, rx is non universal reference.

const auto &&uRef1 = x; //case 2, uRef1 is universal reference, x is lvalue, so uRef1 is lavalue reference i.e. int&
const auto &&uRef2 = cx; //case 2, uRef2 is universal reference, cx is const int and lvalue, so uRef1 is const int&
const auto &&uRef3 = 27; //case 2, uRef3 is universal reference and 27 is rvalue, so case 1 applies, so uRef3 is rvalue reference i.e. int&&
```
* `auto` type deduction assumes that a braced initializer represents a `std::initializer_list` and template type deduction does not.
```C++
auto x = {1,2,3};   //x's type is std::initializer_list
template<typename T>
void Foo(T param){}
Foo({1,2,3});   // error, can't deduce type for T
```
* C++14 further allows `auto` for function return types, and lambdas can use `auto` in parameter declarations. However, these uses of auto employ template type deduction and not auto type deduction. So a function with an auto return type that returns a braced initializer list, won't compile.
```c++
auto getList(){
    return {1,2,3}; //error
}

auto lamb = [](auto param){};
lamb({1,2,3}); //error, can't deduce type.
```

## Item 3 : decltype
* Given a name or expression, `declytype` tells us the name's or expression's type.
```C++
vector<int> v;
decltype(v[0]) // is int&
```
* `decltype` primarily use is for declaring function templates where the funtion's return type depends on its parameter types.
```C++
template<typename Container, typename Index>
auto Foo(Container &c, Index i) -> decltype(c[i]){
    return c[i];
}
```
* A function's trailing return type has the advantage that the function's parameters can be used in the specification of the return type.
* For functions with an auto return type specification, compilers employ template type deduction. Also during template type deduction, the reference-ness of the initializing expression is ignored. So if a function with auto return type, returns a reference, it will be ignored.
```c++
auto Foo()
{
    vector<int> v;
    return v[2];// expr is int &, and & will be dropped and return type will be deduced as int
}

Foo()=5;    // won't compile since lhs is an rvalue.
```
* To solve this problem use `decltype(auto)` as return type. Here `auto` specifies that the type is to be deduced, and `decltype` says that `decltype` rules should be used during the deduction.
* The use of `decltype(auto)` is not limited to function return types. It can also be convenient for declaring variables when you want to apply the `decltype` type deduction rules to the initializing expression.
* For lvalue expressions of type T other than names(x vs (x)), decltype always reports a type of T&.
```C++
decltype(auto) f1(){
    int x;
    return x;//decltype(x) is int, so f1 returns int
}

decltype(auto f2()
{
    int x;
    return (x);//decltype((x)) is int&, so f2 returns int&
})
```
* Functions returning lvalues, always return lvalue references. ???

## Item 4 : Know how to view deduced types
* Use IDE to hover over variables to get types.
* Use type in a way that leads to compilation errors and then see the error.
* `typeid()` yields a `type_info` object, whose `name` member can be used to view the type name. Not always accurate.
* `boost::typeindex::type_id_with_cvr` which yields a `boost::typeindex::type_index` object whose `pretty_name` member function does a better job.
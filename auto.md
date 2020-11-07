## Item 5 : Preder auto to explicit type declaratoins\
* The type of a closure(what lambda yields to) is known only to the compiler, hence we can't do without `auto`. `auto fun = <some lambda>`
  * Well, we can use `std::function` instead of auto above, but *std::function* is not the same as using *auto*. An auto-declared variable holding a closure has the same type as the closure, ans as such it uses only as much memory as the closure requires
  * The type of a `std::function` declared variable holding a closure is an instantiation of the `std::function` template, and that has a fixed size for any given signature. The size may not be adequate for the closure it's asked to store, and in such cases, `std::function` constructor will allocate heap memory to store the closure.
  * Due to implementation details that restrict inlining and yield indirect function calls, invoking a closure via a `std::function` object is almost certain to be slower than calling it via an `auto` declared object.
  * `std::function` is bigger and slower compared to `auto`
* *auto* variables have their type deduced from tehir initializer, so they must be initialized.
```C++
int x1; //potentially uninitialized
auto x2; //error, initializer required
```
```C++
std::unordered_map<string, int> elems;
for (pair<string, int> &elem:elems){} //the type of key is const so it is actually pair<const string, int>. The compiler converts by creating a temporary, so you are not actually having a reference to an element in the map, but to a temporary.
```

## Item 6 : Use the explicitly typed initializer idiom when auto deduces undesired types.
* `vector<bool>::reference` is an invisible proxy class.
* invisible proxy classes don't play weel with `auto`.
* *Where documentation falls short, header files fill the gap*
* Even for proxy objects, we need not abandon `auto` altogether. The solution is to force a different type deduction.
* ***explicityly typed initializer idiom*** - it involves declaring a variable with auto, by casting the initialization expression to the type you want *auto* to deduce.
```C++
double calcEpsilon(); 
float ep = static_cast<float>(calcEpsilon());`
```

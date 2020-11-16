* A useful heuristic to determine whether an expression is an lvalue is to ask if you can take its address. If you can, it typically is. If you can't, it's ususally an rvalue.
* Type of an expression is independent of whether the expression is an lvalue or an rvalue.
    *  In case of a paramter of rvalue reference type, the prameter itself is an lvalue, though it has rvalue reference type. `void func(Foo &&bar)`, `bar` is an lvalue, though it has rvalue reference type.
* Distinction between *parameters* and *arguments* is important since paramters are lvalues, whereas arguments with which they are initialized can be lvalues or rvalues.
* *Perfect forwarding* - an argument is passed to a function such that the original argument's rvalueness is preserved.
*  **Definition** includes provide the storage locations or implementation details.
* **Declarations** intoduce *names* and *types* without giving details, such as where storage is located or how things are implemented.


 

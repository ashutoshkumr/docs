#### Notes
- Prefer using `{}` when initializing variables wherever possible, since it saves you from bugs due to `narrowing conversions`.
  ```c++
  // compilers may report warning for this, but it will succeed anyway
  unsigned int a = -5;
  // this will definitely result in compilation error
  unsigned int b {-5};
  // redundant
  unsigned int c = {-5};
  // not sure what's the use case for this one
  unsigned int d(-5);
  ```
- It's advised to not use `auto` as function return type, since they do not describe an obvious interface.
- Prefer `enum class` over `enum` since former is more strongly typed.
- It's ok for `a` and `b` to be non-constant arguments in `constexpr func(int a, int b)`.
- `const` variables can be initialized at run time, which is what differentiates it from `constexpr` variables.
- Prefer `std::variant` over raw `unions` for tagging type of currently held value.
  ```c++
  #include <variant>
  
  std::variant<int, double, std::string> v;
  v = "hello";
  std::cout << "string ? " << std::holds_alternative<std::string>(v) << std::endl; 
  ```
- Adding `noexcept` at the end of function declration results in calling `std::terminate()` whenever an exception is thrown from within that function.
- Use `static_assert` for compile time assertions.
- Use structured binding to `pack` or `un-pack` only public members.
  ```c++
  struct point {
    int a;
    int b;
  }
  
  point func() {
    return {5, 6};
  }
  
  auto [x, y] = func();
  std::cout << "Pass ? " <<  (x == 5 && y == 6);
  ```
- Use `std::initializer_list` for instantiations (of custom classes) that require following semantics: `std::vector v {1, 2, 3, 4}`
- Methods defined inside class are inlined by default.
- Methods suffixed with `const` indicate that it doesn't modify `this`. Not doing this may result in weird errors when passing object as const referenceZ
- Abstract class should either provide a definition for virtual function or declare them pure.
  ```c++
  class container {
    public:
        virtual double& operator[](unsigned long) = 0;
        virtual unsigned long size() = 0;
        // if braces are not provided, meaning it were a declration, it would've been an error!
        virtual ~container() {};
  };
  ```
- `vtbl` or virtual function table, maintained by each class implementing virtual functions, contains indexed function pointers to all overridden virtual functions. The pointer to `vtbl` itself is stored in the very beginning of the object.
- Add explicit `override` suffix to method declarations to catch any mistakes in function declaration (e.g. spelling mistake), even though its use is purely optional.
- `static_cast` (casting singed to unsigned, etc) vs `dynamic_cast` (returns nullptr if cast is not valid, frequently used to recover type information. i.e. when determining wich derived class object does a base class pointer point to).

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
- Methods suffixed with `const` indicate that it doesn't modify `this`. Not doing this may result in weird errors when passing object as const reference. Typically `error: passing 'const xxx' as 'this' argument discards qualifiers [-fpermissive]`.
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
- Compiler implicitly generates following for classes:
  ```c++
  class container {
    public:
      container();                              // default constructor
      container(const container&);              // copy constructor
      container(container&&);                   // move constructor
      container& operator=(const container&);   // copy assignment
      container& operator=(container&&);        // move assignment
      ~container();                             // destructor
  };
  ```
- Suffix constructor with `= default` or `= delete` to be explicit about whether or not compiler should provide default implementations of various kinds of constructors.
- Prefix constructor with `explicit` if it accepts only one argument to prohibit constructs like following: `MyClass c = 5`.
- `Abstract classes` should almost always `delete` copy constructor and assignment.
- `&&` is an rvalue reference, usually returned by `std::move` or return value of a function.
- Compilers are designed to do `copy elision` upon initialization, hence move constructors may not be invoked as often.
- A move constructor should leave the object to be `moved from` in a state where it can be destroyed.
- Any new type should ideally define following conventional operations:
  - Comparisons: `==`, `!=`, `<`, `<=`, `>`, and `>=`
  - Container operations: `size()`, `begin()`, `end()`
  - Input and output operations: `>>` and `<<`
  - User defined literals
  - `swap()`
  - Hash functions: `hash<>`
- Template type deduction can sometimes cause unexpected results. e.g.
  ```c++
  std::vector<std::string> v1 {"Hello", "There"};   // will initialize with std::strings
  std::vector v1 {"Hello", "There"};                // will initialize with const char*

  ```
- There are cases where type cannot be deducted, use `deduction guide` in such cases:
  ```c++
  template <typename T>
  class container {
    // type deduction not needed for this constructor
    container(T);
    // type deduction needed for this since construtor arguments are not necessarily same as T
    template <typename U>
    container(U, U);
  };
  // deduction guide  
  template <typename U>
  container(U start, U end) -> container<int>;
  ```
- A function template can be a member function, but not a `virtual` member, since the compileer would not know all instantiations of such a template in a program to generate a `vtbl`.
- Example of function object (aka `functor`), `lambda expressions` and how they are used as `policy objects`.
  ```c++
  // functor
  template<typename T>
  class less_than {
      const T val;
      public:
          less_than(const T& v): val{v} {}
          // overload call operator
          bool operator()(const T& x) const { return x < val; }
  };
  // a function that expects functor as a predicate, i.e. a policy object
  template<typename Sequence, typename Callable>
  int count(const Sequence& s, const Callable& c)
  {
      int i = 0;
      for (auto& x: s) {
          if (c(x)) ++i;
      }
      return i;
  }
  
  int x = 3;
  std::cout << "Values less than " << x << ": "
    << count(std::vector {1, 2, 3, 4}, less_than {3}) << std::endl;
  // the lambda expression below will generate function object exactly like the one mentioned above
  std::cout << "Values less than " << x << ": "
    << count(std::vector {1, 2, 3, 4}, [&](int a) { return a < x; }) << std::endl;
  ```
- Use `auto` as type for arguments to writing `generic lambda expressions`, but its support for function arguments was only added in `c++20`.
- `std::string` is an alias for `std::basic_string<char>`.
- `std::string_view` is a read-only view of sequence of characters, designated by a pair of char* and length. i.e. `std::string_view sv = {&"hello"[0], 3};`

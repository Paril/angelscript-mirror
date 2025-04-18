# Fork of AngelScript

I use this repo to stage new features for inclusion into AngelScript. All of these have already been sent as patches for inclusion, and when they are in the real thing I'll merge it up here and remove it from this list.

Contains the following additional features:
* Typed enums. Enumerations on both the host side and script side can be represented by any integral type, similar to C++.
  
  ```cpp
  enum MyEnum : int16
  {
    A,
    B,
    C
  }
  ```

  Mostly backwards compatible API-wise, except for `GetEnumValueByIndex` which now requires a pointer to an asINT64. `GetTypedefTypeId` will return the underlying type ID of an enumeration.
* `nodiscard` support. Very simplistic implementation of `nodiscard` which can detect simple accidental misuses of function calls that return a value that should not be ignored.

  ```cpp
  bool DontIgnoreMe() nodiscard { return true; }

  ...

  DontIgnoreMe(); // warning: Discarded return value of a nodiscard function (DontIgnoreMe)
  ```
* Additional debugging support in `asIScriptFunction` (`GetLineNumber` / `GetLineNumberCount`)
* A function callback on `asIScriptContext`, which is called when a function is entered and left. This can be used for performance timing. It passes an object of type `asSFunctionInfo`.
* A function callback on `asIScriptEngine`, which is called when garbage collection occurs. Can be used for performance timing. It passes an object of type `asSGarbageCollectionInfo`.
* `asETypeModifiers` contains an edditional flag, `asTM_IF_HANDLE_THEN_CONST`, which is set for host functions that are `if_handle_then_const`.
* Value types can now be passed by `inout` reference if the function call determines it is safe to do so. For instance, a parameter or local variable can always safely be passed by reference to another script function instead of throwing an error, without requiring `allowUnsafeReferences`. Eventually I'd like to expand this to in/out too.

  ```cpp
  void RefSample(int &r) { r++; }

  void RefMain()
  {
    int x = 5;
    RefSample(x);
    // x is now 6
  }
  ```
* `AS_DEBUG` bytecode output now includes the function ID in the txt file, to prevent multiple functions with the same name from colliding.
* Better error messages for errors related to functions, such as duplicate named arguments, failed function matching, etc.
* Changes to implicit conversions between enum types and integers, mainly to support other enum types but also to plug some weird behavior.
* Support for digit separators, matching the C++14 implementation (`'` can be used as digit separation in any context).
  
  ```cpp
  auto integer_literal = 1'000'000;
  auto floating_point_literal = 0.000'015'3;
  auto binary_literal = 0b0100'1100'0110;
  auto a_dozen_crores = 12'00'00'000;
  ```

- # Assignment
- ```zig
  const constant: i32 = 5;  // signed 32-bit constant
  var variable: u32 = 5000; // unsigned 32-bit variable
  
  // @as performs an explicit type coercion
  const inferred_constant = @as(i32, 5);
  var inferred_variable = @as(u32, 5000);
  
  const a: i32 = undefined;
  var b: u32 = undefined;
  ```
- # Arrays
- ```zig
  const a = [5]u8{ 'h', 'e', 'l', 'l', 'o' };
  const b = [_]u8{ 'w', 'o', 'r', 'l', 'd' };
  
  const array = [_]u8{ 'h', 'e', 'l', 'l', 'o' };
  const length = array.len; // 5
  ```
- # If
- ```zig
  const expect = @import("std").testing.expect;
  
  test "if statement" {
      const a = true;
      var x: u16 = 0;
      if (a) {
          x += 1;
      } else {
          x += 2;
      }
      try expect(x == 1);
  }
  
  test "if statement expression" {
      const a = true;
      var x: u16 = 0;
      x += if (a) 1 else 2;
      try expect(x == 1);
  }
  ```
- # While
- ```zig
  test "while" {
      var i: u8 = 2;
      while (i < 100) {
          i *= 2;
      }
      try expect(i == 128);
  }
  
  test "while with continue expression" {
      var sum: u8 = 0;
      var i: u8 = 1;
      while (i <= 10) : (i += 1) {
          sum += i;
      }
      try expect(sum == 55);
  }
  
  test "while with continue" {
      var sum: u8 = 0;
      var i: u8 = 0;
      while (i <= 3) : (i += 1) {
          if (i == 2) continue;
          sum += i;
      }
      try expect(sum == 4);
  }
  
  test "while with break" {
      var sum: u8 = 0;
      var i: u8 = 0;
      while (i <= 3) : (i += 1) {
          if (i == 2) break;
          sum += i;
      }
      try expect(sum == 1);
  }
  ```
- # For
- ```zig
  test "for" {
      //character literals are equivalent to integer literals
      const string = [_]u8{ 'a', 'b', 'c' };
  
      for (string) |character, index| {
          _ = character;
          _ = index;
      }
  
      for (string) |character| {
          _ = character;
      }
  
      for (string) |_, index| {
          _ = index;
      }
  
      for (string) |_| {}
  }
  ```
- # Functions
- ```zig
  fn addFive(x: u32) u32 {
      return x + 5;
  }
  
  test "function" {
      const y = addFive(0);
      try expect(@TypeOf(y) == u32);
      try expect(y == 5);
  }
  ```
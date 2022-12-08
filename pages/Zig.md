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
- # Errors
- ```zig
  const FileOpenError = error{
      AccessDenied,
      OutOfMemory,
      FileNotFound,
  };
  
  const AllocationError = error{OutOfMemory};
  
  test "coerce error from a subset to a superset" {
      const err: FileOpenError = AllocationError.OutOfMemory;
      try expect(err == FileOpenError.OutOfMemory);
  }
  
  test "error union" {
      const maybe_error: AllocationError!u16 = 10;
      const no_error = maybe_error catch 0;
  
      try expect(@TypeOf(no_error) == u16);
      try expect(no_error == 10);
  }
  
  fn failingFunction() error{Oops}!void {
      return error.Oops;
  }
  
  test "returning an error" {
      failingFunction() catch |err| {
          try expect(err == error.Oops);
          return;
      };
  }
  
  fn createFile() !void {
      return error.AccessDenied;
  }
  
  test "inferred error set" {
      //type coercion successfully takes place
      const x: error{AccessDenied}!void = createFile();
  
      //Zig does not let us ignore error unions via _ = x;
      //we must unwrap it with "try", "catch", or "if" by any means
      _ = x catch {};
  }
  
  const A = error{ NotDir, PathNotFound };
  const B = error{ OutOfMemory, PathNotFound };
  const C = A || B;
  ```
- # Switch
- ```zig
  test "switch statement" {
      var x: i8 = 10;
      switch (x) {
          -1...1 => {
              x = -x;
          },
          10, 100 => {
              //special considerations must be made
              //when dividing signed integers
              x = @divExact(x, 10);
          },
          else => {},
      }
      try expect(x == 1);
  }
  
  Here is the former, but as a switch expression.
  
  test "switch expression" {
      var x: i8 = 10;
      x = switch (x) {
          -1...1 => -x,
          10, 100 => @divExact(x, 10),
          else => x,
      };
      try expect(x == 1);
  }
  ```
- # Unreachable
- ```zig
  test "unreachable" {
      const x: i32 = 1;
      const y: u32 = if (x == 2) 5 else unreachable;
      _ = y;
  }
  ```
- # Pointers
- ```zig
  fn increment(num: *u8) void {
      num.* += 1;
  }
  
  test "pointers" {
      var x: u8 = 1;
      increment(&x);
      try expect(x == 2);
  }
  
  test "const pointers" {
      const x: u8 = 1;
      var y = &x;
      y.* += 1;
  }
  ```
- # Slices
- ```zig
  fn total(values: []const u8) usize {
      var sum: usize = 0;
      for (values) |v| sum += v;
      return sum;
  }
  
  test "slices" {
      const array = [_]u8{ 1, 2, 3, 4, 5 };
      const slice = array[0..3];
      try expect(total(slice) == 6);
  }
  
  test "slices 2" {
      const array = [_]u8{ 1, 2, 3, 4, 5 };
      const slice = array[0..3];
      try expect(@TypeOf(slice) == *const [3]u8);
  }
  
  test "slices 3" {
      var array = [_]u8{ 1, 2, 3, 4, 5 };
      var slice = array[0..];
      _ = slice;
  }
  ```
- # Enum
- ```zig
  const Direction = enum { north, south, east, west };
  
  const Value = enum(u2) { zero, one, two };
  
  test "enum ordinal value" {
      try expect(@enumToInt(Value.zero) == 0);
      try expect(@enumToInt(Value.one) == 1);
      try expect(@enumToInt(Value.two) == 2);
  }
  
  const Value2 = enum(u32) {
      hundred = 100,
      thousand = 1000,
      million = 1000000,
      next,
  };
  
  test "set enum ordinal value" {
      try expect(@enumToInt(Value2.hundred) == 100);
      try expect(@enumToInt(Value2.thousand) == 1000);
      try expect(@enumToInt(Value2.million) == 1000000);
      try expect(@enumToInt(Value2.next) == 1000001);
  }
  
  const Suit = enum {
      clubs,
      spades,
      diamonds,
      hearts,
      pub fn isClubs(self: Suit) bool {
          return self == Suit.clubs;
      }
  };
  
  test "enum method" {
      try expect(Suit.spades.isClubs() == Suit.isClubs(.spades));
  }
  
  const Mode = enum {
      var count: u32 = 0;
      on,
      off,
  };
  
  test "hmm" {
      Mode.count += 1;
      try expect(Mode.count == 1);
  }
  ```
- # Structs
- ```zig
  const Vec3 = struct {
      x: f32, y: f32, z: f32
  };
  
  test "struct usage" {
      const my_vector = Vec3{
          .x = 0,
          .y = 100,
          .z = 50,
      };
      _ = my_vector;
  }
  
  const Vec4 = struct {
      x: f32, y: f32, z: f32 = 0, w: f32 = undefined
  };
  
  test "struct defaults" {
      const my_vector = Vec4{
          .x = 25,
          .y = -50,
      };
      _ = my_vector;
  }
  
  const Stuff = struct {
      x: i32,
      y: i32,
      fn swap(self: *Stuff) void {
          const tmp = self.x;
          self.x = self.y;
          self.y = tmp;
      }
  };
  
  test "automatic dereference" {
      var thing = Stuff{ .x = 10, .y = 20 };
      thing.swap();
      try expect(thing.x == 20);
      try expect(thing.y == 10);
  }
  ```
- # Unions
- ```zig
  const Result = union {
      int: i64,
      float: f64,
      bool: bool,
  };
  
  test "simple union" {
      var result = Result{ .int = 1234 };
      result.float = 12.34;
  }
  
  const Tag = enum { a, b, c };
  
  const Tagged = union(Tag) { a: u8, b: f32, c: bool };
  
  test "switch on tagged union" {
      var value = Tagged{ .b = 1.5 };
      switch (value) {
          .a => |*byte| byte.* += 1,
          .b => |*float| float.* *= 2,
          .c => |*b| b.* = !b.*,
      }
      try expect(value.b == 3);
  }
  
  const Tagged = union(enum) { a: u8, b: f32, c: bool };
  
  const Tagged2 = union(enum) { a: u8, b: f32, c: bool, none };
  ```
- # Integer Rules
- ```zig
  const decimal_int: i32 = 98222;
  const hex_int: u8 = 0xff;
  const another_hex_int: u8 = 0xFF;
  const octal_int: u16 = 0o755;
  const binary_int: u8 = 0b11110000;
  
  const one_billion: u64 = 1_000_000_000;
  const binary_mask: u64 = 0b1_1111_1111;
  const permissions: u64 = 0o7_5_5;
  const big_address: u64 = 0xFF80_0000_0000_0000;
  
  test "integer widening" {
      const a: u8 = 250;
      const b: u16 = a;
      const c: u32 = b;
      try expect(c == a);
  }
  
  test "@intCast" {
      const x: u64 = 200;
      const y = @intCast(u8, x);
      try expect(@TypeOf(y) == u8);
  }
  
  test "well defined overflow" {
      var a: u8 = 255;
      a +%= 1;
      try expect(a == 0);
  }
  ```\
- # Floats
- LATER ```zig
  test "float widening" {
      const a: f16 = 0;
      const b: f32 = a;
      const c: f128 = b;
      try expect(c == @as(f128, a));
  }
  
  const floating_point: f64 = 123.0E+77;
  const another_float: f64 = 123.0;
  const yet_another: f64 = 123.0e+77;
  
  const hex_floating_point: f64 = 0x103.70p-5;
  const another_hex_float: f64 = 0x103.70;
  const yet_another_hex_float: f64 = 0x103.70P-5;
  
  const lightspeed: f64 = 299_792_458.000_000;
  const nanosecond: f64 = 0.000_000_001;
  const more_hex: f64 = 0x1234_5678.9ABC_CDEFp-10;
  
  test "int-float conversion" {
      const a: i32 = 0;
      const b = @intToFloat(f32, a);
      const c = @floatToInt(i32, b);
      try expect(c == a);
  }
  ```
- # Labelled Blocks
- ```zig
  test "labelled blocks" {
      const count = blk: {
          var sum: u32 = 0;
          var i: u32 = 0;
          while (i < 10) : (i += 1) sum += i;
          break :blk sum;
      };
      try expect(count == 45);
      try expect(@TypeOf(count) == u32);
  }
  
  blk: {
      const tmp = i;
      i += 1;
      break :blk tmp;
  }
  ```
- # Labelled Loops
- ```zig
  test "nested continue" {
      var count: usize = 0;
      outer: for ([_]i32{ 1, 2, 3, 4, 5, 6, 7, 8 }) |_| {
          for ([_]i32{ 1, 2, 3, 4, 5 }) |_| {
              count += 1;
              continue :outer;
          }
      }
      try expect(count == 8);
  }
  ```
- # Loops as Expressions
- ```zig
  fn rangeHasNumber(begin: usize, end: usize, number: usize) bool {
      var i = begin;
      return while (i < end) : (i += 1) {
          if (i == number) {
              break true;
          }
      } else false;
  }
  
  test "while loop expression" {
      try expect(rangeHasNumber(0, 10, 3));
  }
  ```
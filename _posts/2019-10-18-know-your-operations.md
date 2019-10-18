---
layout: post
title: Know Your Operations
date: 2019-10-18 09:51 +0100
author: igbanam
category: blog
tag:
  - ruby
  - performance
headerImage: false
---

I recently saw a new detail of a new release into Ruby 2.7 where the array class would have methods like `intersection`, `union`, and `difference`.

> TL;DR These already exist; as `&`, `|`, and `-` — but they have been SUPERCHARGED 🔥

There are [many cool additions to Ruby 2.7][1]. Through my readings this morning, I saw that the array class would be getting an additional `Array#intersection` alias; yet another alias along the lines of [`Array#union`][3] and [`Array#difference`][4].

While this sounded interesting, I thought "What is the performance benefit of having this method?" So I got the new [Ruby 2.7][2] and gave it a spin.

Sadly, the [patch for `Array#intersection`][5] has not made it into the 2.7 preview build yet.

```sh
✔ ~/Downloads/ruby-2.7.0-preview1/install_here/test_programs
10:43 $ ../bin/ruby -v
ruby 2.7.0preview1 (2019-05-31 trunk c55db6aa271df4a689dc8eb0039c929bf6ed43ff) [x86_64-darwin18]
✔ ~/Downloads/ruby-2.7.0-preview1/install_here/test_programs
10:43 $ ../bin/ruby
puts Array.new.respond_to?(:intersection)
false
```

So I thought to give the other methods a benchmark. This benchmark still uses the Ruby version above, and seeks to compare the timing difference between the method on the object and the operation.

### Array Difference a.k.a "-"

The syntax for this is `left_array.difference(right_array)`.

This returns all the elements in `left_array` which are not present in `right_array`. The difference operation, unlike all other operations considered here, is not commutative. This means `left_array - right_array != right_array - left_array`, unless in a special case where the contents of both arrays allow this.

**Benchmarker**

```
require 'benchmark'

a = (1..8).to_a
b = (2..9).to_a

# a - b = [1]
# b - a = [9]

puts "Time for a - b"
puts Benchmark.measure { 1_000_000.times { a - b } }
puts "Time for a.difference b"
puts Benchmark.measure { 1_000_000.times { a.difference b } }
```

**Results for Difference**

```
Time for a - b
  0.263208   0.000529   0.263737 (  0.264506)
Time for a.difference b
  0.284521   0.002734   0.287255 (  0.289998)
```

The results here show that the **operation is more performant**; albeit slightly than the method call.

Note that the test inputs here are two ordered arrays — which I believe is the simplest case — made to overlap themselves with only one integer.

### Array Union a.k.a "|"

This returns all unique items in two arrays. This operation is commutative. Despite the position of the operands, the result is always the same

**Benchmarker**

```ruby
require 'benchmark'

a = (1..8).to_a
b = (2..9).to_a

# a | b = [1, 2, 3, 4, 5, 6, 7, 8, 9]

puts "Time for a | b"
puts Benchmark.measure { 1_000_000.times { a | b } }
puts "Time for a.union b"
puts Benchmark.measure { 1_000_000.times { a.union b } }
```

**Results for (ordered) Union**

```
Time for a | b
  0.514844   0.003135   0.517979 (  0.521104)
Time for a.union b
  0.520425   0.002118   0.522543 (  0.525702)
```

### Conclusion

From the benchmarks, it seems like there isn't much of a performance hit with the methods on the array object; slightly less performant, but nothing which would bring down your servers in production.

This is still a welcome change; along two lines

  1. Ruby prides itself in fostering developer happiness. With this new additions, code just reads better; the intent of the code is self-evident.
  2. With these new methods, we now have the ability to pass multiple arrays to the operations. e.g. `[1, 2, 3].difference([1], [2]) #=> [3]`. This was not possible with just the operations.


  [1]: https://www.ruby-lang.org/en/news/2019/05/30/ruby-2-7-0-preview1-released/
  [2]: https://cache.ruby-lang.org/pub/ruby/2.7/ruby-2.7.0-preview1.tar.gz
  [3]: https://ruby-doc.org/core-2.6.3/Array.html#method-i-union
  [4]: https://ruby-doc.org/core-2.6.3/Array.html#method-i-difference
  [5]: https://github.com/ruby/ruby/pull/2533/files

---
title: Threading in Ruby
date: "2020-04-05T12:39:00-0500"
description: "Using threads for concurrency in Ruby"
---
The concept of concurrency comes up frequently in software, whether it's in relation to a language like Go, or a web server like Puma. The primary way of implementing concurrency in Ruby is through the use of Threads. Before we get there, we should look at the Ruby ecosystem and the factors that effect the use of concurrency and multithreading.

### The Ecosystem

For the purposes of this post I will be discussing CRuby, which is typically considered the 'default' Ruby. It has gone by other names such as MRI (Matz's Ruby Interpreter) or YARV (Yet Another Ruby VM). This distinction is important since CRuby imposes a Global Interpreter Lock (GIL), whereas other rubies, like JRuby, do not. The GIL essentially limits the program to a single CPU core, meaning that multithreaded programs cannot run in parallel. The distinction here is that multithreaded programs in Ruby can run in overlapping time but will not run at the same time.

Note: Parallelism is possible in Ruby using the [Process](https://ruby-doc.org/core-2.7.0/Process.html) module, which has its own considerations and concerns.

### Using Threads
The core library class [Thread](https://ruby-doc.org/core-2.7.0/Thread.html) describes itself as "the Ruby implementation for a concurrent programming model". Threads can be created by calling `new` and passing it a block to execute. Having created a thread, it is important to call `#join` on it as this blocks the main thread from completing prior to the thread completing. Consider these two cases:

```Ruby
# Case 1
thr1 = Thread.new { sleep 10; puts "thr1 " }
thr2 = Thread.new { sleep 5; puts "thr2 " }
thr2.join

# Case 2
thr1 = Thread.new { sleep 10; puts "thr1 " }
thr2 = Thread.new { sleep 5; puts "thr2 " }
thr1.join
```

Executing case 1 will output "thr2". The main thread finishes prior to `thr1` completing, but will wait for `thr2` to complete since we called `#join` on it. Case 2 will output "thr2 thr1" since `thr2` will (almost certainly) complete prior to `thr1` completing. It is not typically this clear which thread will complete first so it is recommended to call `#join` on every created thread to ensure execution prior to the main thread exiting.

### Why Use Threads?
Threads allow for performance improvements when the Ruby code has periods of inaction. A common use case is for blocking I/O, such as HTTP requests, file system operations, and database operations. Given this example:
```ruby
require 'benchmark'
require 'net/http'

NAMES = ['Robert Downey Jr.', 'Scarlett Johanssen', 'Chris Evans']

puts Benchmark.measure{
  5.times do |i|
    NAMES.each do |name|
      uri = URI("https://en.wikipedia.org/wiki/#{name.split(' ').join('_')}")
      Net::HTTP.get(uri)
    end
  end
}

puts Benchmark.measure{
  threads = []
  5.times do |i|
    NAMES.each do |name|
      threads << Thread.new do
        uri = URI("https://en.wikipedia.org/wiki/#{name.split(' ').join('_')}")
        Net::HTTP.get(uri)
      end
    end
  end
  threads.map(&:join)
}
```
We get the following output:
```
    user     system      total        real
0.150000   0.050000   0.200000 (  4.974252)
0.080000   0.030000   0.110000 (  0.271877)
```
This is a speed increase of ~18x by using threads! We can see from the difference between the total and the real values how much time is just spent inactive. By filling this time waiting on HTTP responses by creating new threads we are able to speed up our program significantly. This is an obviously contrived example but the benefits are clear.

### Wrapping Up
Threads in Ruby provide an easy way to improve the performance of otherwise blocking independent actions by leveraging concurrency. While it is possible to improve performance in this way we also need to be cautious of competition for shared resources. The example above used threads whose work was independent, thus negating the need to manage access to these resources. This concurrency can also be applied to actions sharing a resource, though more careful implementation is required.

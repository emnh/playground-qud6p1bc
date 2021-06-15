# How to randomize bits and integers efficiently in C++

## TODO: This article is work in progress.

This article will tell you some resources for efficient ways to randomize bits and integers in C++.

## Default fast 64 bit RNG for C++

Here's a default quite efficient one called lehmer64 if you're too lazy to benchmark a lot of them.
There might be faster ones if you check the resources, but I haven't done my own benchmarking yet.
This one should still be up there in the top contenders though.

I am not sure if you need to seed it using the functions at
[Lehmer 64 Source Code](https://github.com/lemire/testingRNG/blob/master/source/lehmer64.h)
or just can give any random seed like "1337" below.

```C++ runnable
#include <iostream>

using namespace std;

__uint128_t g_lehmer64_state = 1337;

uint64_t lehmer64() {
  g_lehmer64_state *= 0xda942042e4dd58b5;
  return g_lehmer64_state >> 64;
}

int main() 
{
    cout << "Hello, World: " << lehmer64() << endl;
    cout << "Hello, World: " << lehmer64() << endl;
    cout << "Hello, World: " << lehmer64() << endl;
    return 0;
}
```

Once you have chosen a fast random integer generator, you can AND together a few of them, to get probabilities of 1/2, 1/4, 1/8 and so on, for each bit to be set.

## Resources

### Fastest Random Number Generators
 - [The Fastest Conventional Random Number Generator That Can Pass Big Crush](https://lemire.me/blog/2019/03/19/the-fastest-conventional-random-number-generator-that-can-pass-big-crush/)
 - [StackOverflow: Need a fast random generator for C](https://stackoverflow.com/questions/1640258/need-a-fast-random-generator-for-c)
 - [Random number generators for C performance tested](https://thompsonsed.co.uk/random-number-generators-for-c-performance-tested)
 - [Testing RNGs](https://github.com/lemire/testingRNG)
 - [Hash Function Quality and Speed Tests](https://github.com/rurban/smhasher/)
 - [Presenting XXH3](https://fastcompression.blogspot.com/2019/03/presenting-xxh3.html)

### Selecting a random bit from a bitset 
 - [StackOverflow: Best C++ way to choose randomly position of set bit in bitset](https://stackoverflow.com/questions/37460396/best-c-way-to-choose-randomly-position-of-set-bit-in-bitset)
 - [StackOverflow: Find nth set bit in an int](https://stackoverflow.com/questions/7669057/find-nth-set-bit-in-an-int)


# Advanced usage

If you want a more complex example (external libraries, viewers...), use the [Advanced C++ template](https://tech.io/select-repo/598)

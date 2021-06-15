# How to randomize bits and integers efficiently in C++

## TODO: This article is work in progress.

This article will tell you some resources for efficient ways to randomize bits and integers in C++.

## Default fast 64 bit RNG for C++

Here's a default quite efficient one called lehmer64 if you're too lazy to benchmark a lot of them.
There might be faster ones if you check the resources, but I haven't done my own benchmarking yet.
This one should still be up there in the top contenders though.

I was not sure if you need to seed it using the functions at
[Lehmer 64 Source Code](https://github.com/lemire/testingRNG/blob/master/source/lehmer64.h)
or just can give any random seed like "1337" below. Reading the article at Wikipedia on
[Lehmer RNG](https://en.wikipedia.org/wiki/Lehmer_random_number_generator) we can see that the seed must be
coprime to the modulus. Well then, it seems that any odd number is coprime to the modulus 2^64,
since they are not divisible by 2.

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

If you need an even faster generator, probably go with one of the [Xoroshiro](https://prng.di.unimi.it/).
Both of them can be further optimized though by using 2,3 or 4 generators simultaneously and getting vectorized.

sprkrd: The author of xoshiro also recommends seeding using splitmix64 (which is the same seeding algorithm that lehmer64 used).
In the case of xoshiro it's clear why: states with lots of 0s affect negatively the quality of the produced numbers.
In case of lehmer, i'm not so sure that splitmix is that much of a requirement, it would seem it'd work with any non-zero seed.

derjack: Is int128 that fast? I just use xorshift. So perhaps that's also a valid alternative. Benchmarking on CG needed :) .

Once you have chosen a fast random integer generator, you can AND together a few of them, to get probabilities of 1/2, 1/4, 1/8 and so on, for each bit to be set.

## Faster than Modulo Reduction to constrain range of random number

sprkrd: regarding rng speed... often the bottleneck is not in the RNG itself
it's in the function you use to draw a random sample from an uniform distribution
for instance, for drawing a random int between 0 and a-1, you could do a simple rng() % a
and the modulo operation can be more costly than, say, an addition or a bitwise operation
There are ways to have an uniform number in the (0,a-1) interval that are slightly faster than %.

Here's the [solution](https://lemire.me/blog/2016/06/27/a-fast-alternative-to-the-modulo-reduction/) and here are [benchmarks](https://www.pcg-random.org/posts/bounded-rands.html).

Fastest code from last link included here for convenience:
```C++
uint32_t bounded_rand(rng_t& rng, uint32_t range) {
    uint32_t x = rng();
    uint64_t m = uint64_t(x) * uint64_t(range);
    uint32_t l = uint32_t(m);
    if (l < range) {
        uint32_t t = -range;
        if (t >= range) {
            t -= range;
            if (t >= range) 
                t %= range;
        }
        while (l < t) {
            x = rng();
            m = uint64_t(x) * uint64_t(range);
            l = uint32_t(m);
        }
    }
    return m >> 32;
}
```

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

### Suggestions from CodinGame chat

 - [PCG RNGs](https://www.pcg-random.org/index.html)
 - [Xoshiro / Xoroshiro RNG](https://prng.di.unimi.it/)

# Advanced usage

If you want a more complex example (external libraries, viewers...), use the [Advanced C++ template](https://tech.io/select-repo/598)

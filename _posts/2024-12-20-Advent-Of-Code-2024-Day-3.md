---
title: "Advent Of code Day 3"
date: 2024-12-20 19:00:00 +0800
categories: [Advent Of Code 2024]
tags: [rust]
---

# Advent of Code 2024 - Day 3: Mull It Over

> **Note**: This solution was written while learning Rust, and while it works correctly, there are likely more idiomatic and efficient ways to solve this problem. I welcome suggestions and improvements from more experienced Rust developers!

In today's [Advent of Code challenge](https://adventofcode.com/2024/day/3), we tackled a problem involving parsing corrupted memory containing multiplication instructions. Let's break down the solution for both parts.

## Part 1: Finding Valid Multiplications

The first part required us to find valid multiplication instructions in the form `mul(X,Y)` where X and Y are 1-3 digit numbers. Here's how we solved it:

1. We used a regex pattern `mul\([0-9]{1,3},[0-9]{1,3}\)` to match valid multiplication instructions
2. For each match, we extracted the numbers using another regex `[0-9]+`
3. We multiplied the numbers and summed the results

```rust
let rgx = Regex::new(r"mul\([0-9]{1,3},[0-9]{1,3}\)").unwrap();
let numberRegex = Regex::new("[0-9]+").unwrap();

let mut sum = 0;
for mtch in matches {
    let num_matches: Vec<&str> = numberRegex.find_iter(&mtch)
        .map(|m| m.as_str())
        .collect();
    let num1: u32 = num_matches[0].parse().unwrap();
    let num2: u32 = num_matches[1].parse().unwrap();
    sum = sum + (num1 * num2);
}
```

## Part 2: Conditional Processing

Part 2 introduced two new instructions: `do()` and `don't()` that enable or disable multiplication processing. We modified our solution to:

1. Extended the regex to match both multiplication and control instructions:
   `mul\([0-9]{1,3},[0-9]{1,3}\)|do\(\)|don't\(\)`
2. Added a boolean flag to track whether processing is enabled
3. Only performed multiplications when processing was enabled

```rust
let mut enable_process = true;
let mut sum = 0;

for mtch in matches {
    if enable_process && mtch != "do()" && mtch != "don't()" {
        // Process multiplication
        let num_matches: Vec<&str> = numberRegex.find_iter(&mtch)
            .map(|m| m.as_str())
            .collect();
        let num1: u32 = num_matches[0].parse().unwrap();
        let num2: u32 = num_matches[1].parse().unwrap();
        sum = sum + (num1 * num2);
    }
    else if mtch == "do()" {
        enable_process = true;
    } 
    else if mtch == "don't()" {
        enable_process = false;
    } 
}
```

The key insight for Part 2 was maintaining state through the `enable_process` flag, which allowed us to conditionally process multiplication instructions based on the most recent control instruction.

## Areas for Improvement

As a Rust learner, there are several aspects of this solution that could potentially be improved:
- Error handling could be more robust instead of using `unwrap()`
- The code structure could be more modular with separate functions
- The regex patterns could potentially be optimized
- The state management in Part 2 might be handled more elegantly

## Useful Resources

In developing this solution, the following resources were particularly helpful:

1. [Rust Regex Documentation](https://docs.rs/regex/latest/regex/) - Official documentation for Rust's regex crate
2. [Regular Expression Tutorial](https://www3.ntu.edu.sg/home/ehchua/programming/howto/Regexe.html) - A comprehensive guide to regular expressions
3. [Advent of Code 2024 Day 3](https://adventofcode.com/2024/day/3) - The original problem description

This solution successfully handles both parts of the challenge while maintaining clean, readable code through the use of Rust's powerful regex and string parsing capabilities, though there's certainly room for improvement as I continue learning Rust.

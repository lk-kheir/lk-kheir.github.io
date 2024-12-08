---
title: "Advent Of code Day 1"
date: 2024-12-08 19:00:00 +0800
categories: [Advent Of Code 2024]
tags: [rust]
---

# Reconciling Two Lists of Location IDs: A Practical Example

When working with large datasets, it's common to encounter scenarios where two supposedly similar lists of values don't align perfectly. In this example—originally inspired by an Advent of Code challenge—we have two lists of unique location IDs. We want to measure how different they are by pairing them up and summing the distances between each pair. This provides a quantifiable "difference score." Additionally, in a second part, we calculate a "similarity score" by counting how often each number from one list appears in the other.

## Understanding the Problem

We start with two separate lists of numbers (e.g., location IDs). For example:   
Left List (L): 3, 4, 2, 1, 3, 3  
Right List (R): 4, 3, 5, 3, 9, 3


### Part One: Difference Calculation

1. **Sort both lists** so that their values are in ascending order.  
   - Sorted Left: 1, 2, 3, 3, 3, 4  
   - Sorted Right: 3, 3, 3, 4, 5, 9

2. **Pair the numbers positionally**:  
   - Pair 1: L=1, R=3 → distance = |1 - 3| = 2  
   - Pair 2: L=2, R=3 → distance = |2 - 3| = 1  
   - Pair 3: L=3, R=3 → distance = |3 - 3| = 0  
   - Pair 4: L=3, R=4 → distance = |3 - 4| = 1  
   - Pair 5: L=3, R=5 → distance = |3 - 5| = 2  
   - Pair 6: L=4, R=9 → distance = |4 - 9| = 5

   Adding these all up gives a total difference of `2 + 1 + 0 + 1 + 2 + 5 = 11`.

In a real-world scenario, if your lists are large, calculating this difference helps identify how "far apart" they are in numeric terms. A smaller total difference suggests the lists are more similar.

## The Code Explanation

Below is a Rust program that reads two columns of integers from a file (e.g., `src/input.txt`), sorts each column independently, calculates their total difference, and finally computes the similarity score.

### Key Steps in the Code

1. **Reading lines from a file:**  
   The `read_lines` function uses `std::fs::read_to_string` to load the file content and splits it by line into a `Vec<String>`.

2. **Parsing the input:**  
   Each line is expected to have two integers separated by whitespace. We parse them and store the first in `col1` and the second in `col2`.

3. **Sorting the columns:**  
   Both `col1` and `col2` are sorted in ascending order, preparing them for pairwise difference calculation.

4. **Calculating the total difference:**  
   Once sorted, we pair them index-by-index and sum the absolute differences.


### The Code

```rust

use std::fs::read_to_string;
use std::collections::HashMap;

fn read_lines(filename: &str) -> Vec<String> {
    let mut result = Vec::new();
    for line in read_to_string(filename).unwrap().lines() {
        result.push(line.to_string());
    }
    result
}

fn main() {
    let mut col1: Vec<u32> = Vec::new();
    let mut col2: Vec<u32> = Vec::new();

    let content: Vec<String> = read_lines("src/input.txt");

    for line in content {
        let temp: Vec<&str> = line.split_whitespace().collect();
        
        // Safely parse integers and handle invalid input
        if let (Some(&first), Some(&second)) = (temp.get(0), temp.get(1)) {
            if let (Ok(val1), Ok(val2)) = (first.parse::<u32>(), second.parse::<u32>()) {
                col1.push(val1);
                col2.push(val2);
            } else {
                eprintln!("Failed to parse values: {}", line);
            }
        } else {
            eprintln!("Invalid line format: {}", line);
        }
    }

    col1.sort();
    col2.sort();

    // Calculate the difference
    let diff: Vec<u32> = col1.iter()
        .zip(col2.iter())
        .map(|(&a, &b)| (a as i32 - b as i32).abs() as u32)
        .collect();

    let total_diff: u32 = diff.iter().sum();
    
    println!("Total Difference: {}", total_diff);
}
```

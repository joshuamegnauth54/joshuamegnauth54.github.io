+++
title = "Test post"
date = "2020-06-17"
+++

```rust
use std::ptr::null_mut;

struct AllenbongStrings<const SIZE: usize> {
    // N.b. it doesn't actually make sense to store pointers to Strings in
    // Rust because Strings (as opposed to &str) internally store a pointer
    // to heap allocated data.
    strings: [*mut String; SIZE],
    index: usize,
}

impl<const SIZE: usize> AllenbongStrings<SIZE> {
    pub fn new() -> Self {
        // Fill up the array with null pointers.
        // This is not something we'd do in Rust either.
        let strings = [null_mut::<String>(); SIZE];

        Self { strings, index: 0 }
    }

    pub fn add_string<S>(&mut self, item: S)
    where
        S: Into<String>,
    {
        if self.index < SIZE {
            // Convert item into a Box<String>...
            let item_boxed = Box::new(item.into());
            // And then produce a *mut String. This tells Rust we intend to manage a raw pointer.
            let item_ptr = Box::into_raw(item_boxed);
            // Insert the pointer at a valid location in the array.
            self.strings[self.index] = item_ptr;
            // Increment the index.
            self.index += 1;
        } else {
            eprintln!("You can't add anymore Strings, idiot. Learn to count.");
        }
    }

    pub fn iter_mut<'iter>(&'iter mut self) -> AllenIter<'iter, SIZE> {
        AllenIter::new(self)
    }
}

impl<const SIZE: usize> Drop for AllenbongStrings<SIZE> {
    fn drop(&mut self) {
        for str_ptr in self.iter_mut() {
            // String is dropped and deallocated at the end of this block
            unsafe {
                // Convert the raw pointer back into a Box
                let die_lol = Box::from_raw(str_ptr);
                println!("Dropping {}", die_lol);
                //*str_ptr = std::ptr::null_mut();
            }
        }
        println!("Strings were deleted without a double free.");
    }
}

// An iterator so we don't have to manage indices.
// The 'iter thing is a lifetime so AllenIter can't die before allenbong.
struct AllenIter<'iter, const SIZE: usize> {
    // Borrowing (i.e. a reference) to AllenbongStrings
    allenbong: &'iter AllenbongStrings<SIZE>,
    // Index for the iterator which will always be less than allenbong.index
    index: usize
}

impl<'iter, const SIZE: usize> AllenIter<'iter, SIZE> {
    fn new(allenbong: &'iter mut AllenbongStrings<SIZE>) -> Self {
        Self {
            allenbong,
            index: 0
        }
    }
}

// Implementing Iterator so I don't have to mess with an index.
impl<'iter, const SIZE: usize> Iterator for AllenIter<'iter, SIZE> {
    // Pointers are Copy so I can just return pointers.
    type Item = *mut String;

    fn next(&mut self) -> Option<Self::Item> {
        let current_index = self.index;

        // As long as index < allenbong.index then the data can be accessed
        // safely (i.e. no dangling pointers).
        if current_index < self.allenbong.index {
            self.index += 1;
            Some(self.allenbong.strings[current_index])
        }
        else {
            None
        }
    }
}

fn main() {
    // This allocates an array of 50 in the object but...
    let mut allen = AllenbongStrings::<50>::new();
    // We're only adding 1..2...5! 5! Strings.
    allen.add_string("Meow");
    allen.add_string("Woof");
    allen.add_string("Moo");
    allen.add_string("delete bert pls");
    allen.add_string("Hi kebinlol");

    // allen dies here. I mean the object.
    // But the other 45 null pointers aren't referenced. Success!
    // Also no double frees/segmentation faults.
}
```
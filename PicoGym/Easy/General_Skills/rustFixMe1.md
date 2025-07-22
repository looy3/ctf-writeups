# PicoGym - rustfixme1

- Write-Up Author: looy3 


## Write up  

---

We were given this prompt
```
Have you heard of Rust? Fix the syntax errors in this Rust file to print the flag!
Download the Rust code here.
```
This is the rust script:
```
use xor_cryptor::XORCryptor;

fn main() {
    // Key for decryption
    let key = String::from("CSUCKS") // How do we end statements in Rust?

    // Encrypted flag values
    let hex_values = ["41", "30", "20", "63", "4a", "45", "54", "76", "01", "1c", "7e", "59", "63", "e1", "61", "25", "7f", "5a", "60", "50", "11", "38", "1f", "3a", "60", "e9", "62", "20", "0c", "e6", "50", "d3", "35"];

    // Convert the hexadecimal strings to bytes and collect them into a vector
    let encrypted_buffer: Vec<u8> = hex_values.iter()
        .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
        .collect();

    // Create decrpytion object
    let res = XORCryptor::new(&key);
    if res.is_err() {
        ret; // How do we return in rust?
    }
    let xrc = res.unwrap();

    // Decrypt flag and print it out
    let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);
    println!(
        ":?", // How do we print out a variable in the println function?
        String::from_utf8_lossy(&decrypted_buffer)
    );
}% 
```
Running the script:
```
error: expected `;`, found keyword `let`
 --> main.rs:5:37
  |
5 |     let key = String::from("CSUCKS") // How do we end statements in Rust?
  |                                     ^ help: add `;` here
...
8 |     let hex_values = ["41", "30", "20", "63", "4a", "45", "54", "76", "01", "1c", "7e", "59", "63", "e1", "61", "25", "7f", "5a", "60", "50", "11", "38", "1f", "3a", "60", "e9", "62", "20", "0c", "e6", ...
  |     --- unexpected token

error: argument never used
  --> main.rs:26:9
   |
25 |         ":?", // How do we print out a variable in the println function?
   |         ---- formatting specifier missing
26 |         String::from_utf8_lossy(&decrypted_buffer)
   |         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ argument never used

error[E0432]: unresolved import `xor_cryptor`
 --> main.rs:1:5
  |
1 | use xor_cryptor::XORCryptor;
  |     ^^^^^^^^^^^ maybe a missing crate `xor_cryptor`?
  |
  = help: consider adding `extern crate xor_cryptor` to use the `xor_cryptor` crate

error[E0425]: cannot find value `ret` in this scope
  --> main.rs:18:9
   |
18 |         ret; // How do we return in rust?
   |         ^^^ help: a local variable with a similar name exists: `res`

error: aborting due to 4 previous errors

Some errors have detailed explanations: E0425, E0432.
For more information about an error, try `rustc --explain E0425`.
```
It pretty clearly explains the issues it is having. I fixed these issues and reran with the following script:
```
use xor_cryptor::XORCryptor;

fn main() {
    // Key for decryption
    let key = String::from("CSUCKS"); // How do we end statements in Rust?

    // Encrypted flag values
    let hex_values = ["41", "30", "20", "63", "4a", "45", "54", "76", "01", "1c", "7e", "59", "63", "e1", "61", "25", "7f", "5a", "60", "50", "11", "38", "1f", "3a", "60", "e9", "62", "20", "0c", "e6", "50", "d3", "35"];

    // Convert the hexadecimal strings to bytes and collect them into a vector
    let encrypted_buffer: Vec<u8> = hex_values.iter()
        .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
        .collect();

    // Create decrpytion object
    let res = XORCryptor::new(&key);
    if res.is_err() {
        return; // How do we return in rust?
    }
    let xrc = res.unwrap();

    // Decrypt flag and print it out
    let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);
    println!(
        "{}", // How do we print out a variable in the println function?
        String::from_utf8_lossy(&decrypted_buffer)
    );
}
```

I read the println documentation to get how to print a variable. Everything else was pretty intuitive
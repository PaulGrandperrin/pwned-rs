[![Crates.io](https://img.shields.io/crates/v/pwned.svg)](https://crates.io/crates/pwned)
[![Build Status](https://travis-ci.org/wisespace-io/pwned-rs.png?branch=master)](https://travis-ci.org/wisespace-io/pwned-rs)
[![MIT licensed](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE-MIT)
[![Apache-2.0 licensed](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](./LICENSE-APACHE)

# pwned-rs

Check your passwords against [Have I been pwned?](https://haveibeenpwned.com/)

## Usage

Add this to your Cargo.toml

```toml
[dependencies]
pwned = { git = "https://github.com/wisespace-io/pwned-rs.git" }
```

### Check a password against the API and see the number of occurrences

It uses the range API, so only the first 5 characters of a SHA1 hashed password are sent to Have I been pwned?

```rust
use pwned::api::*;

#[tokio::main]
async fn main() {
    let pwned = PwnedBuilder::default()
        .build().unwrap();

    match pwned.check_password("password").await {
        Ok(pwd) => println!("Pwned? {} - Occurrences {}", pwd.found, pwd.count),
        Err(e) => println!("Error: {}", e),
    }
}
```

### Check all breaches for an account

```rust
use pwned::api::*;
#[tokio::main]
async fn main() {
    
    let pwned = PwnedBuilder::default()
        .user_agent("my_user_agent")
        .api_key(std::env::var("HIBP_API_KEY").expect("You need to give your HIBP API key as the HIBP_API_KEY environment variable"))
        .build().unwrap();

    match pwned.check_email("test@wisespace.io").await {
        Ok(answer) => {
            for breach in answer {
                println!("Service {:?}, breach date {:?} Domain: {:?}", breach.name, breach.breach_date, breach.domain);
            }
        },
        Err(e) => println!("Message: {}", e),
    }

}
```

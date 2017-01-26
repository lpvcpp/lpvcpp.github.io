---
title: "Hello world 0.1"
author: lvpcpp
description: ""
---

~~~~rust
use std::fmt::{Display, Formatter, Result};

struct Me {
    name: String,
    know_as: String,
    likes: Vec<String>,
    location: String,
    link: String,
    relationship: bool,
}
impl Display for Me {
    fn fmt(&self, f: &mut Formatter) -> Result {
        write!(f,
               "Hi, About me \{\{ name: {}, know_as: {}, likes: {:?}, location: {}, link: {}, \
                relationship: {} \}\}",
               self.name,
               self.know_as,
               self.likes,
               self.location,
               self.link,
               self.relationship)
    }
}
fn main() {
    let introduction = Me {
        name: "Le Phong Vu".to_string(),
        know_as: "lpvcpp".to_string(),
        likes: vec!["music".to_string(), "travel".to_string(), "code".to_string()],
        location: "Ho Chi Minh".to_string(),
        link: "https://lpvcpp.github.io".to_string(),
        relationship: true,
    };

    println!("{}", introduction);
}

~~~~

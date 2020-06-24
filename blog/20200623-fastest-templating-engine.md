[<- Blog](README.md)

# Fastest Templating Engine
2020-06-23

Preface: I realize most of the following templating engines are templating libraries, but referring to them all as engines makes things less confusing for readers and they both fulfill similar roles anyway.

I've recently been working on [lemmy-lite](https://github.com/IronOxidizer/lemmy-lite) and I've had a few people ask me why I chose to use [Maud](https://maud.lambda.xyz/) as the templating engine when there are more popular engines even within the Rust community. I referred them to [this dated benchmark](https://lambda.xyz/blog/maud-is-fast/) from 2016 and realized I should make a quick blog post with updated 2020 performance. Here are the results.

## Methodology

Hardware
```
CPU: i7 6700
RAM: 16GB 2133MHz DDR4
Drive: Samsung 860 EVO 480GB
```

Software
```
linux lts 5.4
alpine 3.12
musl 1.1
llvm 3.6.2
cargo 1.45 nightly
rustc 1.45 nightly

serde 1.0.113
serde_json 1.0.55
rustc-serialize 0.3.24

askama 0.9.0
handlebars 3.1.0
horrorshow 0.8.3
liquid 0.20.1
maud 0.22.0
tera 1.3.1
```

Commands
```
git clone https://github.com/lambda-fairy/maud.git
cd maud/benchmarks
cargo bench
```

## Results

Benchmark was done 5 times, values are times in nanoseconds to complete each iteration

|            | Run 1 | Run 2 | Run 3 | Run 4 | Run 5 | Median | Relative |
|------------|------:|------:|------:|------:|------:|-------:|---------:|
| askama     | 899   | 1010  | 923   | 1032  | 1008  | 1008   | 1.47     |
| handlebars | 16652 | 18398 | 17976 | 17997 | 17899 | 17976  | 26.20    |
| horrorshow | 798   | 888   | 893   | 859   | 892   | 888    | 1.29     |
| liquid     | 12823 | 15052 | 14948 | 15143 | 15333 | 15052  | 21.94    |
| maud       | 599   | 700   | 686   | 690   | 659   | 686    | 1.00     |
| tera       | 8115  | 9227  | 9311  | 9346  | 8974  | 9227   | 13.45    |


Producing this median graph

![image](https://user-images.githubusercontent.com/60191958/85488310-16302100-b59c-11ea-9e8e-b35ffdf35e80.png)

## Conclusion

Maud is 23% faster than the next fastest (askama) and **26 times** faster than handlebars; the most popular templating engine in Rust according to crates.io downloads.

I know some of you may be thinking that a few nanoseconds doesn't matter in a world where requests take hundreds of milliseconds anyways, but consider how many more simultaneous client connections could be handled on the same machine given how fast and efficient Maud is. This makes it ideal in situations where processing is limited but bandwidth is not which happens to be the case for systems like the Raspberry Pi 4 with gigabit networking.

That being said, speed isn't everything, and Maud has it shortcomings as well (which I might cover in a future blog post), but for the time being, I'm planning on sticking with Maud for lemmy-lite and future projects where templating is needed. I've been enjoying the library and the confidence it gives me knowing there isn't a faster solution out there that I could otherwise be using.

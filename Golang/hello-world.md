# Hello World in Golang

In this post, we will explore how to write a simple "Hello, World!" program in Golang.

## Prerequisites

Before we start, make sure you have the following installed:

- Go (Golang) [Download here](https://golang.org/dl/)

## Step-by-Step Guide

### Step 1: Create a New Go File

First, create a new file named `main.go` in your project directory.

```bash
mkdir hello-world
cd hello-world
touch main.go
```

### Step 2: Write the Code

Open `main.go` in your text editor and add the following code:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

### Step 3: Run the Program

To run the program, open your terminal, navigate to the directory containing `main.go`, and execute:

```bash
go run main.go
```

You should see the output:

```
Hello, World!
```

## Explanation

- `package main`: Defines the package name. `main` is a special package name that tells the Go compiler that the package should compile as an executable program.
- `import "fmt"`: Imports the `fmt` package, which contains functions for formatting text, including printing to the console.
- `func main()`: The `main` function is the entry point of the program. When the program runs, the code inside `main` will be executed.
- `fmt.Println("Hello, World!")`: Calls the `Println` function from the `fmt` package to print "Hello, World!" to the console.

## Conclusion

Congratulations! You've just written and executed your first Go program.

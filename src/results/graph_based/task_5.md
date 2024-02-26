# Task 87

1 of 4 success

## Attempt 1

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io;
use std::ops::Add;

const PI: f64 = 3.1415926535897932384626433832795;

#[derive(Copy, Clone)]
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Point {
        Point { x, y }
    }

    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }
}

// Implementing the Add trait to use the + operator on Point
impl Add for Point {
    type Output = Self;
    fn add(self, other: Self) -> Self {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    let mut input_string = String::new();
    io::stdin().read_line(&mut input_string).unwrap();
    let inputs: Vec<f64> = input_string
        .split_whitespace()
        .map(|number| number.parse().unwrap())
        .collect();

    let (px, py, vx, vy, a, b, c, d) = (inputs[0], inputs[1], inputs[2], inputs[3], inputs[4], inputs[5], inputs[6], inputs[7]);

    let mut cur = Point::new(px, py) + Point::new(a / 2.0, 0.0);
    let tmp = cur;
    let mut ans = vec![cur];
    ans.push(cur + Point::new(-a / 2.0, b));
    ans.push(cur + Point::new(-a / 2.0, -b));
    cur = cur + Point::new((a - c) / 2.0, 0.0);
    ans.push(cur + Point::new(0.0, -d));
    ans.push(cur + Point::new(c, 0.0));
    ans.push(cur + Point::new(0.0, d));
    ans.remove(0);
    ans.push(tmp);

    let angle = vy.atan2(vx) - PI / 2.0;
    
    for point in ans.iter_mut() {
        *point = *point + Point::new(-px, -py);
        point.rotate(angle);
        *point = *point + Point::new(px, py);
        println!("{:.9} {:.9}", point.x, point.y);
    }
}
```

## Attempt 2

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io;
use std::ops::Add;

const PI: f64 = 3.1415926535897932384626433832795;

#[derive(Copy, Clone)]
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Point {
        Point { x, y }
    }

    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }
}

impl Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let nums: Vec<f64> = input
        .split_whitespace()
        .map(|num| num.parse().unwrap())
        .collect();

    let (px, py, vx, vy, a, b, c, d) = (nums[0], nums[1], nums[2], nums[3], nums[4], nums[5], nums[6], nums[7]);

    let mut cur = Point::new(px, py) + Point::new(a / 2.0, 0.0);
    let mut ans: Vec<Point> = Vec::new();
    let tmp = cur;

    ans.push(cur);
    ans.push(cur + Point::new(-a / 2.0, b));
    ans.push(cur + Point::new(-a / 2.0, -b));
    ans.push(cur + Point::new((a - c) / 2.0, 0.0));
    ans.push(cur + Point::new(0.0, -d));
    ans.push(cur + Point::new(c, 0.0));
    ans.push(cur + Point::new(0.0, d));

    ans.remove(0);
    ans.push(tmp);

    let angle = vy.atan2(vx) - PI / 2.0;

    for i in 0..ans.len() {
        ans[i] = ans[i] + Point::new(-px, -py);
        ans[i].rotate(angle);
        ans[i] = ans[i] + Point::new(px, py);
        println!("{:.10} {:.10}", ans[i].x, ans[i].y);
    }
}
```

## Attempt 3

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io;
use std::ops::Add;

// Defining the constant for PI
const PI: f64 = 3.1415926535897932384626433832795;

// Defining the Point struct
#[derive(Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

// Implementing methods for Point
impl Point {
    // Constructor
    fn new(x: f64, y: f64) -> Self {
        Point { x, y }
    }

    // Rotate method
    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }
}

// Implementing the Add trait for Point
impl Add for Point {
    type Output = Self;

    fn add(self, other: Self) -> Self {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    // Reading input
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");

    let values: Vec<f64> = input
        .split_whitespace()
        .map(|x| x.parse().expect("Failed to parse input"))
        .collect();

    let (px, py, vx, vy, a, b, c, d) = (
        values[0], values[1], values[2], values[3],
        values[4], values[5], values[6], values[7],
    );

    // Initial point
    let mut cur = Point::new(px, py);

    // Vector to store the points of the polygon
    let mut ans = vec![];

    // Constructing the polygon
    cur = cur + Point::new(a / 2.0, 0.0);
    let tmp = cur;
    ans.push(cur);
    ans.push(cur + Point::new(-a / 2.0, b));
    ans.push(cur + Point::new(-a / 2.0, -b));
    ans.push(cur + Point::new((a - c) / 2.0, 0.0));
    ans.push(cur + Point::new(0.0, -d));
    ans.push(cur + Point::new(c, 0.0));
    ans.push(cur + Point::new(0.0, d));
    ans.remove(0);
    ans.push(tmp);

    // Calculating the angle for rotation
    let angle = (vy / vx).atan() - PI / 2.0;

    // Applying rotation and printing the points
    for point in ans {
        let mut rotated_point = point + Point::new(-px, -py);
        rotated_point.rotate(angle);
        rotated_point = rotated_point + Point::new(px, py);
        println!("{:.10} {:.10}", rotated_point.x, rotated_point.y);
    }
}
```

## Attempt 4

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::f64::consts::PI;
use std::io;

#[derive(Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Point {
        Point { x, y }
    }

    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }

    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let parts: Vec<f64> = input
        .split_whitespace()
        .map(|x| x.parse().unwrap())
        .collect();

    let (px, py, vx, vy, a, b, c, d) = (
        parts[0], parts[1], parts[2], parts[3],
        parts[4], parts[5], parts[6], parts[7],
    );

    let mut cur = Point::new(px, py);
    let mut ans: Vec<Point> = Vec::new();

    cur = cur.add(Point::new(a / 2.0, 0.0));
    let tmp = cur;
    ans.push(cur);
    cur = cur.add(Point::new(-a / 2.0, b));
    ans.push(cur);
    cur = cur.add(Point::new(-a / 2.0, -b));
    ans.push(cur);
    cur = cur.add(Point::new((a - c) / 2.0, 0.0));
    ans.push(cur);
    cur = cur.add(Point::new(0.0, -d));
    ans.push(cur);
    cur = cur.add(Point::new(c, 0.0));
    ans.push(cur);
    cur = cur.add(Point::new(0.0, d));
    ans.push(cur);
    
    ans.remove(0);
    ans.push(tmp);

    let angle = vy.atan2(vx) - PI / 2.0;

    for i in 0..ans.len() {
        let mut point = ans[i];
        point = point.add(Point::new(-px, -py));
        point.rotate(angle);
        point = point.add(Point::new(px, py));
        
        println!("{:.9} {:.9}", point.x, point.y);
    }
}
```


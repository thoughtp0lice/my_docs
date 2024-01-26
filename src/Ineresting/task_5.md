# Task 5 - Space Rotation

## C+= Source Code

```c++
using namespace std;
const long double pi = 3.1415926535897932384626433832795;
struct point {
  double x, y;
  point() {}
  point(double _x, double _y) {
    x = _x;
    y = _y;
  }
  void rotate(double angle) {
    double nx = x * cos(angle) - y * sin(angle),
           ny = x * sin(angle) + y * cos(angle);
    x = nx;
    y = ny;
  }
};
point operator+(point x, point y) { return point(x.x + y.x, x.y + y.y); }
int main() {
  double px, py, vx, vy, a, b, c, d;
  cin >> px >> py >> vx >> vy >> a >> b >> c >> d;
  point cur(px, py);
  vector<point> ans;
  cur = cur + point(a / 2.0, 0);
  point tmp = cur;
  ans.push_back(cur);
  cur = cur + point(-a / 2.0, b);
  ans.push_back(cur);
  cur = cur + point(-a / 2.0, -b);
  ans.push_back(cur);
  cur = cur + point((a - c) / 2.0, 0);
  ans.push_back(cur);
  cur = cur + point(0, -d);
  ans.push_back(cur);
  cur = cur + point(c, 0);
  ans.push_back(cur);
  cur = cur + point(0, d);
  ans.push_back(cur);
  ans.erase(ans.begin());
  ans.push_back(tmp);
  double angle = atan2(vy, vx);
  angle -= pi / 2;
  for (int i = 0; i < (int)ans.size(); i++) {
    cout.precision(10);
    ans[i] = ans[i] + point(-px, -py);
    ans[i].rotate(angle);
    ans[i] = ans[i] + point(px, py);
    cout << fixed << ans[i].x << ' ' << ans[i].y << endl;
  }
}
```

## GPT-4 Attempt Example

```rust
use std::f64::consts::PI;
use std::io;
use std::str;

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

impl std::ops::Add for Point {
    type Output = Point;

    fn add(self, other: Point) -> Point {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    let mut buf = String::new();
    io::stdin().read_line(&mut buf).unwrap();
    let parts: Vec<f64> = buf.trim().split(' ').map(|x| x.parse().unwrap()).collect();
    let px = parts[0];
    let py = parts[1];
    let vx = parts[2];
    let vy = parts[3];
    let a = parts[4];
    let b = parts[5];
    let c = parts[6];
    let d = parts[7];
    let mut cur = Point::new(px, py);
    let mut ans = vec![];
    cur = cur + Point::new(a / 2.0, 0.0);
    let tmp = cur;
    ans.push(cur);
    cur = cur + Point::new(-a / 2.0, b);
    ans.push(cur);
    cur = cur + Point::new(-a / 2.0, -b);
    ans.push(cur);
    cur = cur + Point::new((a - c) / 2.0, 0.0);
    ans.push(cur);
    cur = cur + Point::new(0.0, -d);
    ans.push(cur);
    cur = cur + Point::new(c, 0.0);
    ans.push(cur);
    cur = cur + Point::new(0.0, d);
    ans.push(cur);
    ans.remove(0);
    ans.push(tmp);
    let angle = (vy / vx).atan();
    let angle = angle - PI / 2.0;
    for i in 0..ans.len() {
        ans[i] = ans[i] + Point::new(-px, -py);
        ans[i].rotate(angle);
        ans[i] = ans[i] + Point::new(px, py);
        println!("{:.10} {:.10}", ans[i].x, ans[i].y);
    }
}
```

### Note

- Need just one fix: derive Copy and Clone trait for point, but GPT-4 and 3.5 somehow always forget to add that


## IO Description

[IO Description]

Input

The only line of the input contains eight integers px, py, vx, vy ( - 1000 ≤ px, py, vx, vy ≤ 1000, vx2 + vy2 > 0), a, b, c, d (1 ≤ a, b, c, d ≤ 1000, a > c).
Output

Each line should contain two floats.

[IO Examples]

Input

8 8 0 2 8 3 4 5

Output

8.000000000000 11.000000000000 
4.000000000000 8.000000000000 
6.000000000000 8.000000000000 
6.000000000000 3.000000000000 
10.000000000000 3.000000000000 
10.000000000000 8.000000000000 
12.000000000000 8.000000000000


## GPT-4 Summary

The given C++ program is designed to create a geometric figure with specific dimensions and then rotate it around the original point `(px, py)` to align with a given vector `(vx, vy)`. It uses a structure called `point` to represent points in 2D space and provides methods for rotating points around the origin.

Here's a step-by-step explanation of how the code works:

1. It defines a `point` struct to represent a point in 2D space, with an `x` and `y` coordinate, a default constructor, a parameterized constructor, and a `rotate` method that rotates the point by a given angle around the origin.

2. It defines an addition operator for points that returns a new point with coordinates being the sum of the coordinates of the two given points.

3. In the `main` function, it reads the following variables:
   - `(px, py)`: The original point's coordinates.
   - `(vx, vy)`: The components of the vector that will determine the rotation angle.
   - `a`, `b`, `c`, `d`: Parameters that define the dimensions of the geometric figure.

4. It initializes a `point` called `cur` at `(px, py)` and a `vector<point>` called `ans` that will store the vertices of the figure.

5. It creates the figure by adding points to the `ans` vector. The points are defined relative to the original point and the dimensions `a`, `b`, `c`, `d`. The figure's shape is not described, but from the calculations, we can infer that it's potentially a polygon with horizontal and vertical edges.

6. It erases the first point added to `ans` and then adds the point `tmp` back to the end of the `ans` vector, effectively rotating the sequence of points.

7. It calculates the angle of rotation by taking the arctangent of the vector `(vy, vx)` and adjusting by subtracting `pi / 2` to align the figure's base with the vector.

8. It loops over the points in `ans`, adjusts their position relative to the origin by subtracting `(px, py)`, rotates each point by the calculated angle, and then repositions the points back relative to the original point `(px, py)`.

9. It prints out the rotated points to the standard output with fixed precision.

The program assumes that the input vector `(vx, vy)` is not the zero vector (both `vx` and `vy` cannot be zero), as the `atan2` function would otherwise not provide meaningful results. The output of the program will be the vertices of the geometric figure after rotation, printed in the specified format with high precision.

## Description From Dataset

Petya has recently started working as a programmer in the IT city company that develops computer games.

Besides game mechanics implementation to create a game it is necessary to create tool programs that can be used by game designers to create game levels. Petya's first assignment is to create a tool that allows to paint different arrows on the screen.

A user of this tool will choose a point on the screen, specify a vector (the arrow direction) and vary several parameters to get the required graphical effect. In the first version of the program Petya decided to limit parameters of the arrow by the following: a point with coordinates (px, py), a nonzero vector with coordinates (vx, vy), positive scalars a, b, c, d, a > c.

The produced arrow should have the following properties. The arrow consists of a triangle and a rectangle. The triangle is isosceles with base of length a and altitude of length b perpendicular to the base. The rectangle sides lengths are c and d. Point (px, py) is situated in the middle of the triangle base and in the middle of side of rectangle that has length c. Area of intersection of the triangle and the rectangle is zero. The direction from (px, py) point to the triangle vertex opposite to base containing the point coincides with direction of (vx, vy) vector.

Enumerate the arrow points coordinates in counter-clockwise order starting from the tip.


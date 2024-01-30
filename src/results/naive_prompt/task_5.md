# Task 5 Naive Result

## Prompt 

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

## GPT-4-0125-preview Result

Temperature = 0.8 

### Attempt 1

**Result Summary**
- Point need to have Copy trait

```rust
use std::f64::consts::PI;
use std::io;

struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Self {
        Self { x, y }
    }

    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }

    fn add(&self, other: &Point) -> Point {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let values: Vec<f64> = input
        .split_whitespace()
        .map(|x| x.parse::<f64>().unwrap())
        .collect();
    let (px, py, vx, vy, a, b, c, d) = (
        values[0], values[1], values[2], values[3],
        values[4], values[5], values[6], values[7],
    );

    let mut cur = Point::new(px, py).add(&Point::new(a / 2.0, 0.0));
    let mut ans: Vec<Point> = Vec::new();
    let tmp = cur;
    ans.push(cur);
    cur = cur.add(&Point::new(-a / 2.0, b));
    ans.push(cur);
    cur = cur.add(&Point::new(-a / 2.0, -b));
    ans.push(cur);
    cur = cur.add(&Point::new((a - c) / 2.0, 0.0));
    ans.push(cur);
    cur = cur.add(&Point::new(0.0, -d));
    ans.push(cur);
    cur = cur.add(&Point::new(c, 0.0));
    ans.push(cur);
    cur = cur.add(&Point::new(0.0, d));
    ans.push(cur);
    ans.remove(0);
    ans.push(tmp);
    let angle = vy.atan2(vx) - PI / 2.0;
    for point in ans.iter_mut() {
        *point = point.add(&Point::new(-px, -py));
        point.rotate(angle);
        *point = point.add(&Point::new(px, py));
        println!("{:.10} {:.10}", point.x, point.y);
    }
}
```

### Attempt 2

**Result Summary**

- point need to be copy
- centered is not mutable

```rust
use std::f64::consts::PI;
use std::io;

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

    fn add(&self, other: &Point) -> Point {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let values: Vec<f64> = input
        .trim()
        .split_whitespace()
        .map(|x| x.parse().unwrap())
        .collect();
    let (px, py, vx, vy, a, b, c, d) = (
        values[0], values[1], values[2], values[3], values[4], values[5], values[6], values[7],
    );

    let mut cur = Point::new(px, py);
    let mut ans: Vec<Point> = Vec::new();
    cur = cur.add(&Point::new(a / 2.0, 0.0));
    let tmp = cur;
    ans.push(cur);
    cur = cur.add(&Point::new(-a / 2.0, b));
    ans.push(cur);
    cur = cur.add(&Point::new(-a / 2.0, -b));
    ans.push(cur);
    cur = cur.add(&Point::new((a - c) / 2.0, 0.0));
    ans.push(cur);
    cur = cur.add(&Point::new(0.0, -d));
    ans.push(cur);
    cur = cur.add(&Point::new(c, 0.0));
    ans.push(cur);
    cur = cur.add(&Point::new(0.0, d));
    ans.push(cur);
    ans.remove(0);
    ans.push(tmp);

    let angle = vy.atan2(vx) - PI / 2.0;
    for point in ans.iter_mut() {
        let centered = point.add(&Point::new(-px, -py));
        centered.rotate(angle);
        let rotated = centered.add(&Point::new(px, py));
        println!("{:.10} {:.10}", rotated.x, rotated.y);
    }
}
```

### Attempt 3

**Result Summary**
- same issue with the point and push

```rust
use std::f64::consts::PI;
use std::io;

struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Self {
        Self { x, y }
    }

    fn rotate(&mut self, angle: f64) {
        let nx = self.x * angle.cos() - self.y * angle.sin();
        let ny = self.x * angle.sin() + self.y * angle.cos();
        self.x = nx;
        self.y = ny;
    }

    fn add(&self, other: &Point) -> Point {
        Point::new(self.x + other.x, self.y + other.y)
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let values: Vec<f64> = input.split_whitespace().map(|x| x.parse().unwrap()).collect();
    let (px, py, vx, vy, a, b, c, d) = (values[0], values[1], values[2], values[3], values[4], values[5], values[6], values[7]);
    
    let mut cur = Point::new(px, py).add(&Point::new(a / 2.0, 0.0));
    let mut ans = vec![cur];
    ans.push(cur.add(&Point::new(-a / 2.0, b)));
    ans.push(ans.last().unwrap().add(&Point::new(-a / 2.0, -b)));
    ans.push(ans.last().unwrap().add(&Point::new((a - c) / 2.0, 0.0)));
    ans.push(ans.last().unwrap().add(&Point::new(0.0, -d)));
    ans.push(ans.last().unwrap().add(&Point::new(c, 0.0)));
    ans.push(ans.last().unwrap().add(&Point::new(0.0, d)));
    
    let tmp = ans[0];
    ans.remove(0);
    ans.push(tmp);
    
    let angle = vy.atan2(vx) - PI / 2.0;
    
    for point in &mut ans {
        point.x -= px;
        point.y -= py;
        point.rotate(angle);
        point.x += px;
        point.y += py;
        
        println!("{:.10} {:.10}", point.x, point.y);
    }
}

```

### Attempt 4

**Result Summary**

- Point need to have clone

```rust
use std::f64::consts::PI;
use std::io;

struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn new(x: f64, y: f64) -> Self {
        Point { x, y }
    }

    fn rotate(&mut self, angle: f64) {
        let (sin_angle, cos_angle) = angle.sin_cos();
        let nx = self.x * cos_angle - self.y * sin_angle;
        let ny = self.x * sin_angle + self.y * cos_angle;
        self.x = nx;
        self.y = ny;
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let nums: Vec<f64> = input
        .split_whitespace()
        .map(|num| num.parse::<f64>().unwrap())
        .collect();
    let (px, py, vx, vy, a, b, c, d) = (
        nums[0], nums[1], nums[2], nums[3], nums[4], nums[5], nums[6], nums[7],
    );

    let mut cur = Point::new(px, py);
    let mut ans = Vec::new();

    cur = Point::new(cur.x + a / 2.0, cur.y);
    let tmp = cur.clone();
    ans.push(cur.clone());
    cur = Point::new(cur.x - a / 2.0, cur.y + b);
    ans.push(cur.clone());
    cur = Point::new(cur.x - a / 2.0, cur.y - b);
    ans.push(cur.clone());
    cur = Point::new(cur.x + (a - c) / 2.0, cur.y);
    ans.push(cur.clone());
    cur = Point::new(cur.x, cur.y - d);
    ans.push(cur.clone());
    cur = Point::new(cur.x + c, cur.y);
    ans.push(cur.clone());
    cur = Point::new(cur.x, cur.y + d);
    ans.push(cur.clone());
    ans.remove(0);
    ans.push(tmp);

    let angle = vy.atan2(vx) - PI / 2.0;

    for point in ans.iter_mut() {
        point.x -= px;
        point.y -= py;
        point.rotate(angle);
        point.x += px;
        point.y += py;
        println!("{:.10} {:.10}", point.x, point.y);
    }
}
```
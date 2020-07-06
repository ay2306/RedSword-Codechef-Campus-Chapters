# Convex Hull
## 1. What is Convex Hull ?
Convex Hull, for a given set of points, is a polygon which encloses all the points inside it. In simpler words, take a cardboard and pierce nails on it, those will be your points on cartesian plane. Now, take a rubber band and stretch it with your hands to a size such that every nail gets occupied in it. Now, release it. Now what you will see is that the rubber band is supported by some nails and some nails lie inside it. Remember that no nail should lie outside. The convex polygon formed by the rubber band is what we call as CONVEX HULL.

## 2. How to form convex hull in programming ?
There are many algorithms for forming convex hull from a given set of points.
We will be looking at two methods :

### Jarvis's Algorithm
The idea of Jarvis’s Algorithm is simple, we start from the leftmost point (or point with minimum x coordinate value) and we keep wrapping points in counterclockwise direction. The big question is, given a point p as current point, how to find the next point in output? The idea is to use orientation() here. Next point is selected as the point that beats all other points at counterclockwise orientation, i.e., next point is q if for any other point r, we have `orientation(p, q, r) = counterclockwise`

Following is the detailed algorithm :

1) Initialize `p` as leftmost point.
2) Do following while we don’t come back to the first (or leftmost) point.
    a) The next point q is the point such that the triplet (p, q, r) is counterclockwise for any other point r.
    b) next[p] = q (Store q as next of p in the output convex hull).
    c) p = q (Set p as q for next iteration).

Time Complexity : `O(n^2)`

`Code :`

```cpp
#include <bits/stdc++.h> 
using namespace std; 
 
struct Point { 
    int x , y ; 
} ;

int orientation(Point p , Point q , Point r) { 
    int val = (q.y - p.y) * (r.x - q.x) - (q.x - p.x) * (r.y - q.y) ; 
    if (val == 0) return 0 ;
    return (val > 0) ? 1 : 2 ; 
} 

void convexHull(Point points[] , int n) {  
    if (n < 3) return ; 
    vector<Point> hull ;  
    int left_most = 0 ;
    for (int i = 1 ; i < n ; ++i) {
        if (points[i].x < points[left_most].x) {
            left_most = i ;
        }
    } 
    int p = left_most , q ;
    do {
        hull.push_back(points[p]) ;
        q = (p+1) % n ; 
        for (int i = 0; i < n; i++) {
           if (orientation(points[p], points[i], points[q]) == 2) {
               q = i ;
           } 
        }
        p = q ;
    } while (p != left_most) ;
    cout << "Convex Hull : " ;
    for (int i = 0 ; i < (int)hull.size() ; ++i) {
        cout << "(" << hull[i].x << ", " << hull[i].y << ")  " ;
    } 
}

int main() {
    ios_base :: sync_with_stdio(false) ;
    cin.tie(0) ;
    cout.tie(0) ;
    int n ;
    cin >> n ;
    Point pts[n] ;
    for (int i = 0 ; i < n ; ++i) {
        cin >> pts[i].x >> pts[i].y ;
    }
    convexHull(pts , n) ;
    return 0 ;
}
```

### Graham Scan
1) The algorithm first finds the leftmost and rightmost points A and B. In the event multiple such points exist, the lowest among the left (lowest Y-coordinate) is taken as A, and the highest among the right (highest Y-coordinate) is taken as B. Clearly, A and B must both belong to the convex hull as they are the farthest away and they cannot be contained by any line formed by a pair among the given points.

2) Now, draw a line through AB. This divides all the other points into two sets, S1 and S2, where S1 contains all the points above the line connecting A and B, and S2 contains all the points below the line joining A and B. The points that lie on the line joining A and B may belong to either set. The points A and B belong to both sets. Now the algorithm constructs the upper set S1 and the lower set S2 and then combines them to obtain the answer.

3) To get the upper set, we sort all points by the x-coordinate. For each point we check if either - the current point is the last point, (which we defined as B), or if the orientation between the line between A and the current point and the line between the current point and B is clockwise. In those cases the current point belongs to the upper set S1. Checking for clockwise or anticlockwise nature can be done by checking the orientation.

4) If the given point belongs to the upper set, we check the angle made by the line connecting the second last point and the last point in the upper convex hull, with the line connecting the last point in the upper convex hull and the current point. If the angle is not clockwise, we remove the most recent point added to the upper convex hull as the current point will be able to contain the previous point once it is added to the convex hull.

5) The same logic applies for the lower set S2. If either - the current point is B, or the orientation of the lines, formed by A and the current point and the current point and B, is counterclockwise - then it belongs to S2.

6) If the given point belongs to the lower set, we act similarly as for a point on the upper set except we check for a counterclockwise orientation instead of a clockwise orientation. Thus, if the angle made by the line connecting the second last point and the last point in the lower convex hull, with the line connecting the last point in the lower convex hull and the current point is not counterclockwise, we remove the most recent point added to the lower convex hull as the current point will be able to contain the previous point once added to the hull.

The final convex hull is obtained from the union of the upper and lower convex hull.

Time Complexity : `O(n.log(n))`

`Code : `

```cpp
#include<bits/stdc++.h>
using namespace std ;
struct pt {
    int x , y ;
} ;

bool cmp(pt a , pt b) {
    return a.x < b.x || (a.x == b.x && a.y < b.y) ;
}

bool cw(pt a , pt b , pt c) {
    return a.x*(b.y - c.y) + b.x*(c.y - a.y) + c.x*(a.y - b.y) < 0 ;
}

bool ccw(pt a , pt b , pt c) {
    return a.x*(b.y - c.y) + b.x*(c.y - a.y) + c.x*(a.y - b.y) > 0 ;
}

void convex_hull(vector<pt>& a , int n) {
    if (n < 3) {
        return ;
    }
    sort(a.begin() , a.end() , &cmp) ;
    pt p1 = a[0] , p2 = a.back() ;
    vector<pt> up , down ;
    up.push_back(p1) ;
    down.push_back(p1) ;
    for (int i = 1 ; i < n ; ++i) {
        if (i == n-1 || cw(p1 , a[i] , p2)) {
            while (up.size() >= 2 && !cw(up[up.size()-2] , up[up.size()-1] , a[i])) {
                up.pop_back() ;
            }
            up.push_back(a[i]) ;
        }
        if (i == n-1 || ccw(p1 , a[i] , p2)) {
            while(down.size() >= 2 && !ccw(down[down.size()-2] , down[down.size()-1] , a[i])) {
                down.pop_back() ;
            }
            down.push_back(a[i]) ;
        }
    }
    vector<pt> hull ;
    for (int i = 0 ; i < (int)up.size() ; ++i) {
        hull.push_back(up[i]) ;
    }
    for (int i = down.size() - 2 ; i > 0 ; --i) {
        hull.push_back(down[i]) ;
    }
    cout << "Convex Hull : " ;
    for (int i = 0 ; i < (int)hull.size() ; ++i) {
        cout << "(" << hull[i].x << ", " << hull[i].y << ")  " ;
    }
}

int main() {
    ios_base :: sync_with_stdio(false) ;
    cin.tie(0) ;
    cout.tie(0) ;
    int n ;
    cin >> n ;
    vector<pt> pts(n) ;
    for (int i = 0 ; i < n ; ++i) {
        cin >> pts[i].x >> pts[i].y ;
    }
    convex_hull(pts , n) ;
    return 0 ;
}
```

### Let's solve a question now :

**Question-Link:**[Codechef June Challenge 2020 : CONTAIN](https://www.codechef.com/JUNE20B/problems/CONTAIN/)

The question is totally based on constructing Convex Hull(s).
As the question says that the cake is going to have multiple layers that means multiple convex hulls will have to be made.
Obviously, we will start making Convex Hulls from outside and as soon as a Convex Hull is made, we will remove the points that that lie `on` this Convex Hull which we just now made.
We will continue to make Convex Hulls untill we are left with less than 3 points.

> Where do we store these Convex Hulls ?
We can maintain a vector of a structure called HULL which itself has a data member i.e. vector of points.

> How to form multiple Convex Hulls ?
We can do this by maintaining a set of points which are active (not included in any Convex Hull till now).

> How to answer a query ?
We can use binary search to know exactly how many Convex Hulls contain the given point.

#### Implementation :

```cpp
#include<bits/stdc++.h>
using namespace std ;
#define int long long
#define pb push_back
 
struct point {
    int x , y ;
    point(int xx , int yy) : x(xx) , y(yy) {}
    point operator + (const point &p) const { return point(x+p.x, y+p.y) ; }
    point operator - (const point &p) const { return point(x-p.x, y-p.y) ; }
    bool operator < (const point &p) const { return x < p.x || (x == p.x && y<p.y) ; }
    bool operator == (const point &p) const { return x == p.x && y == p.y ; }
    int cross(const point &p) const { return x*p.y - y*p.x ; }
    int dot(const point &p) const { return x*p.x + y*p.y ; }
    int cross(const point &a , const point &b) const { return (a - *this).cross(b - *this) ; }
    int dot(const point &a , const point &b) const { return (a - (*this)).dot(b - (*this)) ; }
    int sqrlen() const { return this->dot(*this) ; }
} ;
 
bool cw(point a , point b , point c) {
    return a.x*(b.y - c.y) + b.x*(c.y - a.y) + c.x*(a.y - b.y) < 0 ;
}
bool ccw(point a , point b , point c) {
    return a.x*(b.y - c.y) + b.x*(c.y - a.y) + c.x*(a.y - b.y) > 0 ;
}

struct HULL {
    vector<point> vertices ;
    HULL(vector<point> v) : vertices(v) {
        vertices.pb(vertices[0]) ;
        reverse(vertices.begin() , vertices.end()) ;
        vertices.pop_back() ;
        int pos = 0 , n = (int)vertices.size() ;
        for(int i = 1 ; i < n ; i++)
            if(vertices[i].y < vertices[pos].y || (vertices[i].y == vertices[pos].y && vertices[i].x < vertices[pos].x))
                pos = i ;
        rotate(vertices.begin() , vertices.begin()+pos , vertices.end()) ;
        v.clear() ;
        v.pb(vertices[0]) ;
        v.pb(vertices[1]) ;
        for(int i = 2 ; i <= n ; i++) {
            if((v[(int)v.size()-1] - v[(int)v.size()-2]).cross(vertices[i%n] - v[(int)v.size()-2]) == 0)
                v.pop_back() ;
            v.pb(vertices[i]) ;
        }
        v.pop_back() ;
        vertices = v ;
    }
    bool PIP(point p) {
        int n = (int)vertices.size() ;
        bool ans = true ;
        for(int i = 0 ; i < n ; i++)
            ans &= ccw(vertices[i] , vertices[(i+1)%n] , p) ;
        return ans ;
    }
} ;
 
vector<HULL> hulls;
 
void convex_hull(vector<point> &a) {
    set<point> alive_pts ;
    for(auto z : a)
        alive_pts.insert(z) ;
    while(!((int)alive_pts.size()<3)) {
        point p1 = *alive_pts.begin() , p2 = *alive_pts.rbegin() ;
        vector<point> up , down ;
        up.pb(p1) ;
        down.pb(p1) ;
        for(auto it = ++alive_pts.begin() ; it != alive_pts.end() ; it++) {
            if(it == prev(alive_pts.end()) || !ccw(p1, *it, p2)) {
                while((int)up.size() > 1 && ccw(up[(int)up.size()-2] , up[(int)up.size()-1] , *it)) {
                    up.pop_back() ;
                }
                up.pb(*it) ;
            }
            if(it == prev(alive_pts.end()) || !cw(p1 , *it , p2)) {
                while((int)down.size() > 1 && cw(down[(int)down.size()-2] , down[(int)down.size()-1] , *it)) {
                    down.pop_back() ;
                }
                down.pb(*it) ;
            }
        }
        for(int i = (int)down.size()-2 ; i > 0 ; i--) {
            up.pb(down[i]) ;
        }
        if((up[1] - up[0]).cross(up[(int)up.size()-1] - up[0]) == 0) {
            break ;
        }
        for(auto z : up) {
            alive_pts.erase(z) ;
        }
        hulls.pb(HULL(up)) ;
    }
}
 
void solve(int test_case) {
    int n , q ;
    cin >> n >> q ;
    vector<point> a ;
    while(n--) {
        int xx , yy ;
        cin >> xx >> yy ;
        a.pb(point(xx , yy)) ;
    }
    hulls.clear() ;
    convex_hull(a) ;
    while(q--) {
        int u , v ;
        cin >> u >> v ;
        point p(u , v) ;
        int lo = 0 , hi = (int)hulls.size() - 1 ;
        while(lo <= hi) {
            int mid = (lo + hi) >> 1 ;
            if(!hulls[mid].PIP(p)) hi = mid - 1 ;
            else lo = mid + 1 ;
        }
        cout << hi + 1 << "\n" ;
    }
}
 
int32_t main() 
{
	ios_base :: sync_with_stdio(false) ;
	cin.tie(0) ;
	cout.tie(0) ;
    int tc = 1 ;
    cin >> tc ;
    while(tc--) solve(tc) ;
	return 0 ;
} 
```

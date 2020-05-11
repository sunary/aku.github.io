+++
author = "Sunary"
title = "132 Pattern"
date = "2020-04-21T10:00:00+07:00"
description = "132 Pattern"
tags = ["algorithm", "dp"]
categories = ["coding"]
series = []
aliases = []
+++

### Đề bài:

Các bạn có thể xem tại đây [132-pattern](https://leetcode.com/problems/132-pattern/) hoặc có thể xem mình diễn giải lại như sau:

```
Dãy số N được gọi là match với 132 pattern nếu như theo thứ tự tồn tại 3 phần tử thuộc N sao cho:
N[i] < N[k] < N[j] với điều kiện i < j < k
Kiểm tra xem dãy N cho trước có match với 132 pattern không?
```

#### Ví dụ:

+ input:

```python
N = [1, 2, 3, 4]
```

+ output:

```python
False
```

+ input:

```python
N = [3, 1, 4, 2]
```

+ output:

```python
True
```

+ giải thích:

Đơn giản rồi:
Với `N = [1, 2, 3, 4]` thì không khỏa mãn, nhưng với `N = [3, 1, 4, 2]` thì `[1, 4, 2]` tương ứng với pattern 132.

### Let the hunt begin:

Yeah, ấn tượng ban đầu là bài này dễ vãi nồi ra, sao lại dc xếp vào bài medium trên leetcode dc nhỉ? Thử submit xem, ..Nhầm, thử code xem, tất nhiên ko phải là (n^3) =)).
Khi đọc đề bài là mình có ý nghĩ sẽ làm theo [DP](https://sunary.github.io/public/post/002-dynamic-programming/) (mình nghĩ đúng hơn là `linear programming`) tức mình sẽ duyệt qua từng phần tử, nếu phát hiện nó là 132 pattern thì trả về kết quả luôn mà không cần phải duyệt các phần tử còn lại.

#### Vậy duyệt như thế nào?

Ý tưởng ban đầu của mình là khi duyệt sẽ chọn 1 cặp số `(one, three)`, sau đó với mối số tiếp theo có thỏa mãn `one < x and x < three` hay không thôi.
Ví dụ: `N = [10, 5, 3, 15, 7]` thì `one = 3` và `three = 15`

Code sẽ là:

```python
one = N[0]
three, has_three = 0, False

for i in range(1, len(N)):
    if has_three:
        if one < N[i] and N[i] < three:
            return True
        elif N[i] > three:
            three = N[i]
    elif N[i] > one:
        three, has_three = N[i], True
    else:
        one = N[i]

return False
```

Hiểu sơ là chọn `one` nhỏ nhất trước khi gặp `three`, sau đó chọn `three` lớn nhất, còn lại thì đi kiểm tra nó có match với pattern 132 không thôi.
Submit thôi, và kết quả là SAI!!!!

Có test case luôn: `N = [8, 10, 4, 6, 5]`, lý do sai là có rất nhiều cặp số `(one, three)` tồn tại mà mình chỉ chọn 1. Cụ thể là `(one, three) = (8, 10)` mà bỏ qua trường hợp `(one, three) = (4, 6)`.

Vậy ý tưởng trên sẽ trở thành chọn các cặp số `(one, three)`, sau đó với mỗi số tiếp theo có thỏa mãn nó có nằm giữa các cặp số `(one, three)` hay không, hay là nó sẽ tạo ra thêm 1 cặp số `(one, three)` mới.

Vậy code lại:

```python
tps = []
one = N[0]
three, has_three = 0, False

for i in range(1, len(N)):
    for t in tps:
        if t[0] < N[i] and N[i] < t[1]:
            return True

    if has_three:
        if one < N[i] and N[i] < three:
            return True
        elif N[i] > three:
            three = N[i]
        else:
            tps += [(one, three)]
            one = N[i]
            has_three = False
    elif N[i] > one:
        three, has_three = N[i], True
    else:
        one = N[i]

return False
```

Tương tự như trên, khi duyệt qua mỗi phần tử thì tìm `one`, sau đó chọn `three` lớn nhất rồi lưu vào `tps`. Rồi tiếp tục quá trình đó, vừa tìm các cặp số `(one, three)` vừa kiểm tra xem có số `two` nào lọt giữa không.

#### Submit

Ok, submit thôi

```
Success
Details 
Runtime: 6592 ms, faster than 5.25% of Python online submissions for 132 Pattern.
Memory Usage: 14.2 MB, less than 50.00% of Python online submissions for 132 Pattern.
```

#### Tối ưu

Ban đầu còn tưởng cái submit này failed cơ, để viết cái tối ưu, mà nó passed rồi nên cũng nói luôn.
Theo bạn thì lời giải trên độ phức tạp bao nhiêu???

Kết quả là `O(n^2)`, khó có thể chấp nhận được với 1 bài không mấy khó khăn, và lý do ở đâu?
Tất nhiên, 1 lần duyệt qua các số, và 1 lần còn lại kiểm tra xem ứng với mỗi `(one, three)` thì có tồn tại số `one < x and x < three` hay không, chính việc kiểm tra này làm tăng độ phức tạp bài toán lên đáng kể.

Vậy để kiểm tra trên trở thành `O(log(n))` thì chỉ có nước tạo index cho nó.

Yeah, xem như đây là 1 bài toán con đi:

##### Đề bài

```
Cho T là dãy các tuple (t1, t2) t1 < t2 và số k cho trước.
Kiểm tra xem có tồn tại tuple (t1, t2) sao cho t1 < k < t2
```

Thực ra nó là bài [merge-intervals](https://leetcode.com/problems/merge-intervals)

Chúng ta sẽ dùng binary search cho bài này, nên cần có 2 method là `add()` và `find()`

```python

def add(t):
    if not T:
		return [t]

	left, right = 0, len(T) - 1
	mid = 0
	while True:
	    mid = (left + right)/2
	    if mid == 0 or mid == len(T) - 1 or (T[mid][0] <= t[0] and t[0] < T[mid + 1][0]):
	         break 
	    elif t[0] < T[mid][0]:
	        right = mid - 1
	    else:
	        left = mid + 1

	if mid == 0 and t[0] < T[0][0]:
		T = [t] + T
	else:
        while mid < len(T) - 1 and t[0] > T[mid+1][0]:
			mid += 1
		T = T[:mid+1] + [t] + T[mid+1:]
		mid += 1

	if mid > 0 and T[mid-1][0] == T[mid][0]:
		T = T[:mid-1] + [[T[mid][0], max(T[mid-1][1], T[mid][1])]] + T[mid+1:]
        mid -= 1
	elif mid > 0 and T[mid-1][1] >= T[mid][0]:
		T = T[:mid-1] + [[T[mid-1][0], max(T[mid-1][1], T[mid][1])]] + T[mid+1:]
        mid -= 1

	skip = 1
	while mid+skip < len(T) and T[mid][1] >= T[mid+skip][0]:
		skip += 1
	if skip > 1:
		T = T[:mid] + [[T[mid][0], max(T[mid][1], T[mid + skip - 1][1])]] + T[mid + skip:]
	return T

def find(T, x):
    left, right = 0, len(T) - 1
    mid = 0
    while True:
        mid = (left + right)/2
        if mid == 0 or mid == len(T) - 1 or (T[mid][0] <= x and x < T[mid + 1][0]):
             break 
        elif x < T[mid][0]:
            right = mid - 1
        else:
            left = mid + 1

    return T[mid][0] < x and x < T[mid][1]
```

@@ quả không đơn giản.
Mình sẽ cố gắng duy trì `T` luôn được sort theo thứ tự tăng dần, và merge lại khi có giao nhau.

Ví dụ với `T = [(1, 3), (8, 10)]`

sau khi thêm `t = (4, 6)` sẽ thành: `T = [(1, 3), (4, 6), (8, 10)]`

sau khi thêm `t = (2, 9)` sẽ thành: `T = [(1, 10)]`
...

cách làm sẽ là bạn sẽ insert `t` mới vào trong `T` theo đúng thứ tự, và cố gắng merge lại những phần tử overlap.

và hàm `find()` sẽ dựa trên order tuple đó để tìm thay vì duyệt qua toàn bộ.

Sau lhi dùng index này vào bài toán, thử submit xem:

```
TBD
```

---

#### References:

+ [132-pattern](https://leetcode.com/problems/132-pattern)
+ [merge-intervals](https://leetcode.com/problems/merge-intervals)
+ [dynamic-programming-and-linear-programming](https://www.quora.com/What-is-the-difference-between-dynamic-programming-and-linear-programming)
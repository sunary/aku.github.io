+++
author = "Sunary"
title = "Đếm các cặp số hoán vị khác nhau bằng tổng cho trước"
date = "2020-04-10T10:00:00+07:00"
description = "Đếm các cặp số hoán vị khác nhau bằng tổng cho trước"
tags = ["algorithm", "sum", "count"]
categories = ["coding"]
series = []
aliases = []
+++

### Đề bài:

Bài này mình được 1 công ty nào đó hỏi lúc phỏng vấn, dạo gần đây có 1 người bạn gửi cho bài [4sum](https://leetcode.com/problems/4sum/) nên nhớ lại và chọn làm bài đầu tiên cho blog luôn.

```
Cho trước dãy số N, và số k.
Đếm tất cả các cặp số hoán vị duy nhất (a, b) sao cho: a + b = k với a, b thuộc N.
```

#### Ví dụ:

+ input:

```python
N = [1, 2, 3, 3, 4]
k = 5
```

+ output:

```python
2
```

+ giải thích:

Vì chỉ có 2 cặp số thỏa mãn: **(1, 4) và (2, 3)**
*lưu ý: (1, 4) cũng là cặp số (4, 1) nên chỉ đếm 1 lần, tương tự với (3, 2) và (2, 3).*

*// Đề bài là mình nhớ lại, nên ko bắt bẻ nếu không chính xác hoặc khó hiểu.*

### Let the hunt begin:

Nói luôn là bài này mình có hỏi ứng viên khi phỏng vấn ở công ty mình, thì họ phạm 2 lỗi sau:
1. Không giải quyết được trường hợp các cặp số hoán vị duy nhất.
2. Tối ưu.

#### 1. Không giải quyết được trường hợp các cặp số hoán vị duy nhất.

Lấy luôn ví dụ trên, thì bạn đó giải ra đếm các kết quả của **(1, 4), (2, 3), (2, 3), (3, 2), (3, 2), (4, 1)**

**Có 2 vấn đề khiến hoán vị bị lặp lại:**
+ **(1, 4) và (4, 1)**: Bạn đếm 1 hoán vị 2 lần: bạn để ý `a + b = k`, bạn đếm (a, b) rồi (b, a), không sai. Nhưng làm sao chỉ đếm 1 lần thôi, tức chỉ đếm cho a thôi, không đếm cho b. Rất đơn giản: `a + b = k` thì 1 trong 2 số a và b phải nhỏ hơn k/2 *(có trường hợp a = b = k/2 mình sẽ nói sau)*, nên code counter của mình sẽ là:

```python
if a < k/2 and a + b == k:
    counter += 1
```

+ **(2, 3) và (2, 3)**: Với số 3 xuất hiện 2 lần thì bạn đếm 2 lần. Cách đơn giản là tạo 1 biến để track những cặp số đã được count trước đó:

```python
marked = {}
if not marked.get((a, b)) and a + b == k:
    counter += 1
    marked[(a, b)] = True
```

Nhưng có 1 cách đơn giản hơn là: Tại sao bạn không loại bỏ những số trùng của mảng N cho trước:

```python
distinctN = set(N)
```

Bạn nên tự tạo hàm đưa vào *distinctN* cho riêng mình

```python
count = 0
countHaft = 0
distinctN = set()
for x in N:
    if x == k/2:
        countHaft += 1
    else:
        distinctN.add(x)

if countHaft >= 2:
    count += 1
```

Lý do mình tự tính distanceN là vì loại bỏ những số `a = b = k/2` như đã nói ở trên bằng cách đếm riêng, và xóa nó ra khỏi *distinctN*.

#### 2. Tối ưu:

Dễ thấy bài toán có thể giải bằng 2 vòng for duyệt qua *distinctN*:

```python
for a in distinctN:
    for b in distinctN:
        if a < k/2 and a + b == k:
            count += 1
```

Nhưng không biết bạn vừa bỏ qua gì ko? *distinctN* là set đã được index unique, tức là bạn có thể tìm 1 phần tử bất kì với `O(1)`, và code trên từ `O(n^2)` thành `O(n)`:

```python
for a in distinctN:
    if a < k/2 and k - a in distinctN:
        count += 1
```

Ok, vậy chương trình hoàn chỉnh sẽ là:

```python
count = 0
countHaft = 0
distinctN = set()
for x in N:
    if x == k/2:
        countHaft += 1
    else:
        distinctN.add(x)

if countHaft >= 2:
    count += 1

for a in distinctN:
    if a < k/2 and k - a in distinctN:
        count += 1
```

Thấy vẫn không ưng ý lắm, muốn tối ưu thêm 1 chút nữa: Chuyện là bạn phải duyệt qua tất cả các giá trị trong *distinctN* nhưng chỉ làm việc với 1 nửa của chúng bằng cách kiểm tra `x < k/2`. Vậy thì ngay từ đầu mình kiểm tra giùm nó luôn, khỏi phải duyệt một lần nữa:


```python
count = 0
countHaft = 0
distinctLowerN = set()
distinctHigherN = set()
for x in N:
    if x == k/2:
        countHaft += 1
    elif x < k/2:
        distinctLowerN.add(x)
    elif x > k/2:
        distinctHigherN.add(x)

if countHaft >= 2:
    count += 1

for a in distinctLowerN:
    if k - a in distinctHigherN:
        count += 1
```

#### Giải quyết bài 4sum đã đề cập ở trên:

Bài trên tìm tổng 2 số với O(n) thì bài này làm tương tự với độ phức tạp là O(n^3) *(Không phải O(n^2) nghe, bình thường là duyệt qua 4 vòng for, tối ưu được 1 vòng bằng cách tương tự trên thôi)*.
Nhưng không phải dùng set mà là dùng map, đơn giản vì set không lưu được số lượng trùng của các phần tử, và không được loại bỏ các phần tử bằng k/2 hay k/4

```python
distinctN = {}

for x in N:
    if x in distinctN:
        distinctN[x] += 1
    else:
        distinctN[x] = 1
```

Và tiến hành duyệt 3 vòng for qua N, và check giá trị `k - a - b - c` có thuộc trong `distinctN` hay không, nhân đây thì có 1 chút tối ưu: nếu N có nhiều hơn 4 phần tử thì sao? nó sẽ bị dư khi tính toán nên tốt nhất mình nên tái tạo lại N:

```python
newN = []
sortedKey = sorted(distinctN.keys())

for x in sortedKey:
    newN += [x] * min(distinctN.get(x), 4)
```

Vậy chương trình hoàn chỉnh sẽ là:

```python
count = 0
distinctN = {}

for x in N:
    if x in distinctN:
        distinctN[x] += 1
    else:
        distinctN[x] = 1

newN = []
sortedKey = sorted(distinctN.keys())

for x in sortedKey:
    newN += [x] * min(distinctN.get(x), 4)

marked = {}
for i in range(len(newN)):
    for j in range(i + 1, len(newN)):
        for l in range(j + 1, len(newN)):
            remain = k - newN[i] - newN[j] - newN[l]
            if remain < newN[i]:
                key = (remain, newN[i], newN[j], newN[l])
            elif remain < newN[j]:
                key = (newN[i], remain, newN[j], newN[l])
            elif remain < newN[l]:
                key = (newN[i], newN[j], remain, newN[l])
            else:
                key = (newN[i], newN[j], newN[l], remain)

            if not marked.get(key) and remain in distinctN:
                num = distinctN.get(remain)
                if remain == newN[i]:
                    num -= 1
                if remain == newN[j]:
                    num -= 1
                if remain == newN[l]:
                    num -= 1
                if num > 0:
                    count += 1
                    marked[key] = True
```

Giải thích 1 chút:
+ `newN[i], newN[j], newN[l]` đã được sort, nên khi chèn remain vào để làm 1 unique quadruplets thì *phải đảm bảo chèn remain vào vị trí hợp lí sao cho các giá trị trong quadruplets là tăng dần*.
+ Sẽ ra sao nếu chỉ có 1 số `a` nằm trong N mà `new[i] == a` và `remain == a`, đó là lý do biến `num` ra đời.

#### Submit

Thử submit thì pass, nhưng kết quả không được tốt lắm, chắc sẽ có thêm mấy cái cần tối ưu.

```
Success
Details 
Runtime: 2064 ms, faster than 5.88% of Python online submissions for 4Sum.
Memory Usage: 13 MB, less than 13.64% of Python online submissions for 4Sum.
```
*// Sẽ cập nhật nếu thêm được cái gì*

---

#### References:

+ [4sum](https://leetcode.com/problems/4sum/)
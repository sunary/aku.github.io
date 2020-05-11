+++
author = "Sunary"
title = "Quy hoạch động P1"
date = "2020-04-11T11:55:36+07:00"
description = "Quy hoạch động P1"
tags = ["algorithm", "dp"]
categories = ["coding", "method", "mathematic"]
series = []
aliases = []
+++

Chắc ai từng làm các bài thuật toán cũng từng nghe đến `quy hoạch động (DP)`, thực ra nó không cao siêu khó hiểu như các bạn nghĩ.
Lý do mình viết bài này để để tham chiếu, vì các bài trong tương lai mình nghĩ sẽ dùng phương pháp này nhiều.

### Định nghĩa:

Mình tìm kiếm 1 vài chỗ thì thấy DP được định nghĩa khá đầy đủ như sau:

```
Dynamic Programming is a method for solving a complex problem by breaking it down into a collection of simpler subproblems, solving each of those subproblems just once, and storing their solutions using a memory-based data structure (array, map,etc).
```

Hiểu 1 cách đơn giản đó là chia bài toán thành các bài toán nhỏ đơn giản hơn, nhưng *đơn giản hơn như thế nào???*

Nếu các bạn đã từng nghe có 1 phương pháp giải toán là `quy nạp` thì DP cũng tương tự như vậy:

```
Để chứng minh một mệnh đề đúng với mọi n ∈ N bằng phương pháp **quy nạp toán học**, ta thực hiện các bước sau:

1. Kiểm tra mệnh đề đúng với n = 1.
2. Giả sử mệnh đề đúng với n = k ≥ 1 (giả thiết quy nạp).
3. Cần chứng minh mệnh đề đúng với n = k + 1.
```

Thì mình định nghĩa lại DP ở đây một chút: *giả thiết các bạn giải quyết được bài toán với n = k, thì sẽ giải quyết được bài toán với n = k + 1.*

Cũng chưa đơn giản lắm, cứ đi vào ví dụ xem như thế nào.

### Let the hunt begin:

```
Cho mảng N, tính tổng của N.
```

=)) yeah, làm từ bài dễ đến vừa.

Rất đơn giản thôi:

```python 
sum_N = 0

for i in range(len(N)):
    sum_N += N[i]
```

Vậy DP ở đâu ra? để ý rằng để giả sử N bạn *giải quyết được bài toán với n = k (nhắc lại)* thì với `n = k + 1`, `sum_N` sẽ được tính lại bằng: `sum_N = sum_N + N[k + 1]`.
Chính việc bạn giải quyết bài toán qua từng phần tử thì gọi là DP rồi.

Tương tự như vậy, với bài fibonacci thì sao?

```python
f0 = 1
f1 = 1

for _ in range(2, n):
    f0, f1 = f1, f0 + f1
```

Tương tự như giải thích phía trên, *cứ mỗi bước i trong vòng lặp bạn đã giải quyết được bài toán đến i, thì các bước sau i cũng sẽ được giải quyết tương tự.*

Ok, sang 1 ví dụ vừa vừa nào

```
Tính tổng lớn nhất max_sum của dãy con liên tiếp thuộc dãy N.
```

// Không hiểu sao bài này được khá nhiều cty hỏi lúc pv. Mình gặp 2 cty rồi.

#### Ví dụ:

+ input:

```python
N = [4, -1, -2, 1, 5, -3]
```

+ ouput:

```python
7
```

+ giải thích:

Tổng lớn nhất của dãy con liên tiếp thuộc về dãy: `[4, -1, -2, 1, 5]` và có tổng là: 7

#### 

Chúng ta đang lấy ví dụ cho DP, thì qua về lý thuyết 1 chút:

1. Giải quyết bài toán với vị trí n = 0
2. Giải quyết bài toán với vị trí n = i
3. Giải quyết bài toán với vị trí n = i + 1

**Giải quyết bài toán với vị trí n = 0**

Với n = 0 thì `max_sum = N[0]` quá dễ.

**Giải quyết bài toán với vị trí n = i**

Ok, xem như ta giải quyết dc.

**Giải quyết bài toán với vị trí n = i + 1**

Nếu `max_sum` là tổng lớn nhất đến vị trí i, thì với i + 1 ta làm thế nào?
Lấy lại input của ví dụ trên:

```
max_sum = N[0] = 4
i = 0, n = 0 + 1 = 1, tức N[1] = -1. Nhận thấy nếu thêm -1 vào max_sum thì max_sum bị giảm (ko phải max nữa, nên ko cộng). max_sum vẫn là 4.
i = 1, n = 1 + 1 = 2, tức N[2] = -2. Nhận thấy nếu thêm -1 và -2 vào max_sum thì max_sum bị giảm (ko phải max nữa, nên ko cộng). max_sum vẫn là 4.
i = 2, n = 2 + 1 = 3, tức N[3] = 1. Nhận thấy nếu thêm -1, -2 và 1 vào max_sum thì max_sum bị giảm (ko phải max nữa, nên ko cộng). max_sum vẫn là 4.
i = 3, n = 3 + 1 = 4, tức N[4] = 5. Nhận thấy nếu thêm -1, -2, 1 và 5 vào max_sum thì max_sum tăng thành 7 (giử lại).
i = 4, n = 4 + 1 = 5, tức N[5] = -3. Nhận thấy nếu thêm -3 vào max_sum thì max_sum bị giảm (ko phải max nữa, nên ko cộng). max_sum vẫn là 7.

i chạy hết rồi, nên max_sum = 7 là kết quả bài toán.
```

Và cuối cùng là code:

```python
max_sum, last_max_sum = N[0], N[0]
id_last = 0
for i in range(1, len(N)):
    if last_max_sum <= 0:
        last_max_sum = N[i]
        id_last = i
    else:
        last_max_sum += N[i]

    if last_max_sum >= max_sum:
        max_sum = last_max_sum

```

Từ lập luận trên sao ra được đoạn code ngắn, khó hiểu vậy?
Giải thích từ lập luận thì mình nói trên rồi, giờ thử giải thích từ code:

```
last_max_sum là kết quả max_sum nhưng có chứa phần tử cuối cùng, vì sao cần biến này là nếu không có nó thì tổng của bạn không liên tục được khiến bài toán không thể giải được.
Ví dụ:
N = [-2, 4, -1]
thì max_sum = sum([4]), còn last_max_sum = sum([4, -1])
vì nếu
N = [-2, 4, -1, 3]
mà bạn không giử lại -1, thì bạn sẽ không tính được max_sum = last_max_sum = sum([4, -1, 3])
```

Phần giới thiệu đến đây là hết, nếu các bạn chưa rõ thì xin để lại phản hồi qua email.

Và thay cho lời kết thì mình cũng đưa ra 1 bài tương tự, dùng DP và cũng từng là 1 câu hỏi phỏng vấn của mình:

```
Cho dãy số N là giá trị cổ phiếu của 1 công ty theo thứ tự thời gian.
Bạn hãy tính lợi nhuận cao nhất nếu mua cổ phiếu cty tại thời điểm i và bán ở thời điểm j (tức i < j)
```

#### Ví dụ:

+ input:

```python
N = [7, 5, 8, 20, 3, 17]
```

+ ouput:

```python
15
```

+ giải thích:

Bạn mua vào thời điểm giá cổ phiếu là 5, bán vào lúc 20. Lợi nhuận là `20 - 5 = 15`

---

#### Bổ sung:

Có lẽ cách tiếp cận DP của mình giống với `linear programming`, mình có để link ở dưới cho bạn nào quan tâm.

#### References:

+ [dp2](https://aku.vn/public/post/002-dynamic-programming-2/)
+ [mathematical-induction](https://en.wikipedia.org/wiki/Mathematical_induction)
+ [top-50-dynamic-programming-practice-problems](https://blog.usejournal.com/top-50-dynamic-programming-practice-problems-4208fed71aa3)
+ [geeksforgeeks](https://www.geeksforgeeks.org/maximum-subarray-sum-using-divide-and-conquer-algorithm)
+ [dynamic-programming-and-linear-programming](https://www.quora.com/What-is-the-difference-between-dynamic-programming-and-linear-programming)
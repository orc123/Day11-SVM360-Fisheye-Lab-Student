# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_019560.jpg
- L3 center SPURIOUS
- L4+R3 edge WRONG_CLASS
- L6+R5 center WRONG_CLASS
- L7+R6 mid WRONG_CLASS

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 3 | 2 | 1 | 2 |
| mid | 2 | 1 | 1 | 1 |
| edge | 1 | 0 | 1 | 1 |

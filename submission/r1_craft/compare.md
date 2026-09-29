# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_145860.jpg
- L2 mid IGNORE_SCOPE
## adasind_167700.jpg
- L1 mid IGNORE_SCOPE
- L6 mid IGNORE_SCOPE
- L8+R4 center ATTRIBUTE
- L10+R8 center ATTRIBUTE
- L11 mid SPURIOUS
- R2 mid MISSING
## adasind_199770.jpg
- L5 mid IGNORE_SCOPE
- L1+R5 edge BOX_GEOMETRY
- L3+R9 center BOX_GEOMETRY
- L6+R7 mid WRONG_CLASS
- R3 mid MISSING
- R4 mid MISSING
- R6 edge MISSING

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 9 | 8 | 1 | 1 |
| mid | 7 | 3 | 4 | 2 |
| edge | 4 | 2 | 2 | 1 |

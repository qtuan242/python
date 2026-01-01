# NumPy Time-Series & KPI Cheat Sheet

Tài liệu này tổng hợp **toàn bộ kiến thức NumPy** đã trao đổi: xử lý time-series, KPI anomaly, ngưỡng fix, so sánh mẫu, vectorized logic.

---

## 1. Tư duy cốt lõi

- **NumPy = xử lý mảng + toán học**
- **Pandas = hiển thị + index**
- **Không dùng for-loop nếu có thể vectorized**

---

## 2. Tạo & khởi tạo mảng

```python
np.array([1,2,3])
np.zeros(n)
np.ones(n)
np.full(n, 5)
np.arange(10)
np.linspace(0, 1, 5)
```

---

## 3. Lấy dữ liệu từ Pandas sang NumPy

```python
v = df["value"].to_numpy()  # mảng 1 chiều
n = len(v)
```

---

## 4. So sánh & mask boolean

```python
mask = v < 100
mask = (v < a) & (v > b)
mask = (v < a) | (v > b)
```

---

## 5. Gán theo điều kiện

```python
np.where(cond, A, B)
```

Ví dụ:
```python
note = np.where(v < 88, "LOW", "OK")
```

---

## 6. Shift / Lag (time-series)

```python
v[1:]    # t0
v[:-1]   # t1
```

So sánh liên tiếp:
```python
v[1:] < v[:-1]
```

---

## 7. So sánh N mẫu (vectorized)

Điều kiện:
- t0 < tN - nguong
- t1 < tN - nguong
- ...

```python
cond = np.ones(n - N, dtype=bool)
for k in range(N):
    cond &= (v[N-k : n-k] < v[: n-N] - nguong)
```

---

## 8. Ngưỡng cố định (nguongFix)

```python
checkNguongfix = v < nguongFix
```

Ghi ra DataFrame:
```python
df["checkNguongfix"] = checkNguongfix
```

---

## 9. Gộp logic ngưỡng động + ngưỡng fix

```python
final_cond = cond & checkNguongfix[:n-N]
```

---

## 10. Mapping mức độ (severity)

```python
severity = np.select(
    [check==1, check==2, check>=3],
    ["LOW", "MED", "HIGH"],
    default="OK"
)
```

---

## 11. Thống kê nhanh

```python
np.mean(v)
np.std(v)
np.min(v)
np.max(v)
np.percentile(v, 95)
```

---

## 12. Time-series nâng cao

```python
np.diff(v)        # delta
np.cumsum(v)      # tích lũy
np.convolve(v, np.ones(5)/5, mode="valid")  # SMA
```

---

## 13. Ghi note kết hợp

```python
df["note"] = (
    np.where(df["checkNguongfix"],
             f"thấp hơn ngưỡng cố định {nguongFix}",
             f"cao hơn ngưỡng cố định {nguongFix}")
    +
    np.where(df["check"] > 0,
             " | vi phạm " + df["check"].astype(str) + " mẫu",
             "")
)
```

---

## 14. Câu nhớ nhanh

> **Mask trước – gán sau – không loop**

> **NumPy để nghĩ – Pandas để nhìn**

---

📌 Phù hợp cho:
- KPI anomaly detection
- Alarm engine
- Trading signal (Renko / EMA / SMC)

---

🚀 Có thể dùng trực tiếp làm tài liệu nội bộ hoặc README.md


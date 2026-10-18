# HW1 · T6–T9 Solutions（讲解 + 解答）

> Key steps are shown for every part（每一问都写了关键步骤）. Bits are numbered from right to left, starting at 0（位从右往左编号，从 0 开始）.

---

## T6 · Bitwise operations on 16-bit hex（16 位十六进制的按位运算）

### Key ideas（知识点）

- **Hex ↔ binary（十六进制 ↔ 二进制）**: each hex digit = exactly 4 bits（每个十六进制数字正好对应 4 位）.

  | hex | bin | hex | bin | hex | bin | hex | bin |
  |---|---|---|---|---|---|---|---|
  | 0 | 0000 | 4 | 0100 | 8 | 1000 | C | 1100 |
  | 1 | 0001 | 5 | 0101 | 9 | 1001 | D | 1101 |
  | 2 | 0010 | 6 | 0110 | A | 1010 | E | 1110 |
  | 3 | 0011 | 7 | 0111 | B | 1011 | F | 1111 |

- **Bitwise（按位）**: do the operation on **each column separately**（每一列单独运算，互不进位）.
  - AND: 1 only if both are 1（都为 1 才得 1）
  - OR: 1 if at least one is 1（有 1 就得 1）
  - XOR: 1 if the two bits are **different**（不同得 1，相同得 0）
  - NOT: flip every bit（每位取反）

### (1) (x5A3C AND x0FF0) OR x8001

```
  x5A3C = 0101 1010 0011 1100
  x0FF0 = 0000 1111 1111 0000
  ---------------------------- AND
          0000 1010 0011 0000  = x0A30
  x8001 = 1000 0000 0000 0001
  ---------------------------- OR
          1000 1010 0011 0001  = x8A31
```

**Answer: x8A31**

> Tip（小技巧）: AND with x0FF0 keeps the middle 8 bits and clears the rest（AND x0FF0 = 保留中间 8 位，其余清零）.

### (2) NOT(x00F0 XOR x0F0F)

```
  x00F0 = 0000 0000 1111 0000
  x0F0F = 0000 1111 0000 1111
  ---------------------------- XOR
          0000 1111 1111 1111  = x0FFF
  ---------------------------- NOT
          1111 0000 0000 0000  = xF000
```

**Answer: xF000**

### (3) Rewrite NOT(A OR B) using only AND and NOT（德摩根定律）

**DeMorgan's Law（德摩根定律）**:

$$\text{NOT}(A \text{ OR } B) = (\text{NOT } A) \text{ AND } (\text{NOT } B)$$

In words: "neither A nor B" = "not A, and not B"（"A、B 都不是" = "不是 A 且不是 B"）.

**Answer: NOT(A OR B) = (NOT A) AND (NOT B)**

---

## T7 · Bit masks（位掩码）

Original vector（原向量）: `10111010`

| bit（位） | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---|---|---|---|---|---|---|---|
| value | 1 | 0 | 1 | 1 | 1 | 0 | 1 | 0 |

(1 = available 空闲, 0 = busy 忙)

### Key ideas（知识点）

| Goal（目的） | Operation | Mask rule（掩码怎么写） |
|---|---|---|
| Clear bits to 0（清零 → 设为忙） | **AND** | 0 at target bits, 1 elsewhere（目标位写 0，其余写 1） |
| Set bits to 1（置 1 → 设为空闲） | **OR** | 1 at target bits, 0 elsewhere（目标位写 1，其余写 0） |
| Toggle bits（翻转） | **XOR** | 1 at target bits, 0 elsewhere（目标位写 1，其余写 0） |
| Isolate a bit（单独取出一位） | **AND** | 1 only at that bit（只有那一位写 1） |

Why: x AND 1 = x, x AND 0 = 0; x OR 0 = x, x OR 1 = 1; x XOR 0 = x, x XOR 1 = NOT x.

### (1) Make devices 7 and 4 busy — one AND（设备 7、4 设为忙）

- Mask: 0 at bits 7 and 4 → `01101111`

```
    10111010
AND 01101111
------------
    00101010
```

**Mask `01101111`, AND, result `00101010`**

### (2) Make devices 0 and 2 available — one OR（设备 0、2 设为空闲）

- Mask: 1 at bits 2 and 0 → `00000101`

```
    10111010
 OR 00000101
------------
    10111111
```

**Mask `00000101`, OR, result `10111111`**

### (3) Toggle device 5 — one XOR（翻转设备 5）

- Mask: 1 at bit 5 → `00100000`

```
    10111010
XOR 00100000
------------
    10011010
```

**Mask `00100000`, XOR, result `10011010`** (bit 5 changes from 1 to 0)

### (4) Isolate device 3 and move it to the MSB（取出第 3 位，移到最高位）

**Step 1: isolate（取出）** with AND mask `00001000`:

```
    10111010
AND 00001000
------------
    00001000     (bit 3 was 1, every other bit is now 0)
```

**Step 2: move from bit 3 to bit 7 = shift left by 4（左移 4 位）.**
Adding a number to itself = ×2 = shift left by 1（自己加自己 = 乘 2 = 左移 1 位）, so add it to itself 4 times:

```
00001000 + 00001000 = 00010000   (bit 4)
00010000 + 00010000 = 00100000   (bit 5)
00100000 + 00100000 = 01000000   (bit 6)
01000000 + 01000000 = 10000000   (bit 7)
```

**Result `10000000`** (MSB = old bit 3 = 1, all other bits are 0)

---

## T8 · Truth table and DeMorgan（真值表与德摩根定律）

- $Q_1 = \text{NOT}((X \text{ AND } Y) \text{ OR } Z)$
- $Q_2 = (\text{NOT } X \text{ OR NOT } Y) \text{ AND NOT } Z$

### (1) Truth table（真值表）

Helper columns make it easy（加辅助列更清楚）:

| X | Y | Z | X AND Y | (X AND Y) OR Z | **Q1** | NOT X OR NOT Y | NOT Z | **Q2** |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 0 | **1** | 1 | 1 | **1** |
| 0 | 0 | 1 | 0 | 1 | **0** | 1 | 0 | **0** |
| 0 | 1 | 0 | 0 | 0 | **1** | 1 | 1 | **1** |
| 0 | 1 | 1 | 0 | 1 | **0** | 1 | 0 | **0** |
| 1 | 0 | 0 | 0 | 0 | **1** | 1 | 1 | **1** |
| 1 | 0 | 1 | 0 | 1 | **0** | 1 | 0 | **0** |
| 1 | 1 | 0 | 1 | 1 | **0** | 0 | 1 | **0** |
| 1 | 1 | 1 | 1 | 1 | **0** | 0 | 0 | **0** |

### (2) Are they equivalent?（是否等价）

**Yes.** Q1 and Q2 have the **same output in all 8 rows**, so they are equivalent（8 行输出完全相同，所以等价）.

### (3) Verify with DeMorgan's Laws（用德摩根定律证明）

Two forms of DeMorgan（两种形式）:
- NOT(P OR Q) = (NOT P) AND (NOT Q)
- NOT(P AND Q) = (NOT P) OR (NOT Q)

$$
\begin{aligned}
Q_1 &= \text{NOT}\big((X \text{ AND } Y) \text{ OR } Z\big) \\
&= \text{NOT}(X \text{ AND } Y) \;\text{AND}\; \text{NOT } Z &&\text{(DeMorgan, with } P = X\text{ AND }Y,\ Q = Z\text{)}\\
&= (\text{NOT } X \text{ OR NOT } Y) \;\text{AND}\; \text{NOT } Z &&\text{(DeMorgan again)}\\
&= Q_2
\end{aligned}
$$

---

## T9 · IEEE 754 single precision（单精度浮点数）

### Key ideas（知识点）

32 bits = **1** sign bit（符号位） + **8** exponent bits（阶码） + **23** fraction bits（尾数）

| Category（类别） | Exponent field（阶码） | Fraction（尾数） | Meaning（含义） |
|---|---|---|---|
| Normalized（规格化） | 00000001 to 11111110 | any | $(-1)^S \times 1.\text{fraction} \times 2^{E-127}$ |
| Subnormal（非规格化） | 00000000 | nonzero | $(-1)^S \times 0.\text{fraction} \times 2^{-126}$ |
| Special（特殊值） | 11111111 | all 0s or nonzero | all 0s → ±∞; nonzero → NaN |

（另外：阶码全 0 且尾数全 0 表示 ±0。）

- **Bias = 127（偏置）**: real exponent = exponent field − 127（真实指数 = 阶码 − 127）.
- **Hidden 1（隐含的 1）**: a normalized number is 1.fraction, and that 1 is **not stored**（规格化数前面的 1 不存储）.

### (1) Convert `0 10000100 01101000000000000000000` to decimal

| Part | Bits | Value |
|---|---|---|
| Sign（符号） | 0 | positive（正） |
| Exponent（阶码） | 10000100 = 132 | unbiased exponent = 132 − 127 = **5** |
| Significand（尾数） | 1.01101₂ | 1 + 1/4 + 1/8 + 1/32 = **1.40625** |

$$+1.01101_2 \times 2^5 = 101101_2 = 45$$

（check: 1.40625 × 32 = 45 ✓）

**Answer: 45**

### (2) IEEE 754 representation of −13.625

1. **Sign**: negative → S = **1**
2. **Binary**: 13 = 1101₂, 0.625 = 0.101₂ (0.5 + 0.125) → 13.625 = **1101.101₂**
3. **Normalize（规格化）**: 1101.101 = **1.101101 × 2³**
4. **Exponent field**: 3 + 127 = 130 = **10000010**
5. **Fraction**: bits after the point（小数点后面的位）`101101`, filled with 0s to 23 bits（补 0 到 23 位）

**Answer:**

```
1 10000010 10110100000000000000000
```

Grouped in 4s（4 位一组）: `1100 0001 0101 1010 0000 0000 0000 0000` = **xC15A0000**

### (3) Smallest positive normalized and subnormal numbers

| | Bit pattern | Value |
|---|---|---|
| Smallest positive **normalized**（最小正规格化数） | `0 00000001 00000000000000000000000` | $1.0 \times 2^{1-127} = 2^{-126}$ |
| Smallest positive **subnormal**（最小正非规格化数） | `0 00000000 00000000000000000000001` | $2^{-23} \times 2^{-126} = 2^{-149}$ |

Why: for normalized numbers, the smallest exponent field is 00000001 (all 0s is reserved) and the smallest significand is 1.0. For subnormal numbers, the value is 0.fraction × 2⁻¹²⁶, and the smallest nonzero fraction is 2⁻²³ (only the last bit is 1).

### (4) Exponent field all 1s（阶码全为 1）

- **(a) Fraction all 0s → infinity（无穷大）**: S = 0 gives +∞, S = 1 gives −∞. Example: the result of 1.0 / 0.
- **(b) Fraction nonzero → NaN (Not a Number, 非数)**: the result of an invalid operation, such as 0 / 0 or √(−1).

---

*T10 (ASCII) is not included here.*

# 第3章 密码学底座：困难问题、格与噪声预算

**一句话**：后面四章的所有路线都站在同一组底座上——困难问题给出安全性，格结构给 `FHE` 提供可计算的形态，而噪声预算决定了「能算多深」。

**为什么需要它**：不补这一层，`BFV`／`CKKS` 的参数、`bootstrapping` 的成本、`LWE` 的假设都会变成背名词。Gentry 博士论文的原句直指核心：The construction makes use of hard problems on ideal lattices. 全同态加密的安全性不是来自「加密得复杂」，而是来自某个被认为难解的数学问题。

**核心结构**：`lattice` 与 `LWE`（Learning With Errors）与 `RLWE`（ring learning with errors）构成现代 `lattice-based cryptography` 的基础；`commitment scheme` 提供「先承诺后揭示」，`cryptographic hash function` 与 `random oracle` 提供可验证的随机性。工程侧则是围绕噪声的一整套机制：`noise budget`、`modulus switching`、`relinearization`、`key switching`、`rotation key`、`evaluation key`、`RNS` 与 `NTT`。

**关键设计**：同态运算会让密文里的噪声增长，噪声超过阈值就解不出正确结果。`leveled FHE` 的做法是预先估计电路深度、把参数开到「刚好够用」；`FHE` 的做法是用 `bootstrapping` 周期性刷新。这两条路线的全部差别，几乎都能归结为「要不要为深度付自举的钱」。

**拆解**：
### 1. 为什么是格，不是别的困难问题
`RSA`、`ElGamal`、`Paillier` 也能做同态，但只支持单一运算（`PHE`）。格结构允许同时表达加法与乘法，并且能把多个明文槽打包进一个 `ciphertext` 做 `SIMD`／`batching`，这才让密文上的通用计算有了可实现的形态。

### 2. 噪声从哪里来、怎么被花掉
每次密文乘法都放大噪声并让密文维度上升，于是需要 `relinearization` 把维度压回去、`modulus switching` 按比例缩小模数与噪声、密钥切换类操作（`key switching`、`rotation key`、`evaluation key`）配合槽位旋转。`noise budget` 就是这套账本的名字，参数选小了算不完，选大了算得慢。

### 3. NTT 与 RNS 为什么总被一起提起
格密码里的多项式乘法占绝大部分算力，`NTT` 把它降到准线性，`RNS` 把大整数拆成多个小模数并行处理。Microsoft SEAL 与 OpenFHE 的官方 README 都把这类底层优化作为库的核心能力，而不是可选插件。

### 4. 底座决定了后面四章的取舍
`MPC` 用 `secret sharing` 与 `oblivious transfer` 绕开密文深度问题，`ZKP` 把命题编译成 `arithmetic circuit` 或 `Boolean circuit`，`DP` 干脆不碰密文。四条路线的分岔点，其实是「要不要在密文上做深度计算」这一件事。

**今天怎么做**：读一个 FHE 库的参数配置时，先找出它声明的 `security level`、多项式模数次数与 `noise budget` 估计，再问「这个参数能撑住我的电路深度吗」。参数不是调优项，是正确性边界。

<div class="chapend" markdown="1">

**容易误解的地方**：`RLWE` 不是「更强的 LWE」，而是带环结构、因而更高效的变体；`bootstrapping` 也不是「解密再加密」这么简单，它是在密文上同态地评估解密电路。另一个误读是把 `SIMD` 打包理解成压缩——它是把多个槽位并行算，不减少密文体积。

**记住这一句**：安全来自困难问题，能力来自格结构，而成本几乎全写在噪声预算上。

</div>

<!-- chapend -->

# 隐私计算精读

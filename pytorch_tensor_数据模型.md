# PyTorch Tensor 数据模型全面解析

## 一、核心概念阐述

### 1.1 Tensor 的本质

**Tensor（张量）** 是 PyTorch 的核心数据结构，本质上是**多维数组**的泛化：

| 维度 | 名称          | 示例                               |
| ---- | ------------- | ---------------------------------- |
| 0-D  | 标量 (Scalar) | `torch.tensor(5)`                  |
| 1-D  | 向量 (Vector) | `torch.tensor([1, 2, 3])`          |
| 2-D  | 矩阵 (Matrix) | `torch.tensor([[1, 2], [3, 4]])`   |
| 3-D+ | 张量 (Tensor) | `torch.tensor([[[1, 2], [3, 4]]])` |

### 1.2 Tensor 的五大核心属性

```python
import torch

x = torch.randn(3, 4, 5, dtype=torch.float32, device='cuda')

print(x.shape)    # torch.Size([3, 4, 5])  形状
print(x.dtype)    # torch.float32          数据类型
print(x.device)   # device(type='cuda')    设备位置
print(x.ndim)     # 3                      维度数
print(x.numel())  # 60                     元素总数
```

### 1.3 内存模型：存储与视图

```
┌─────────────────────────────────────────┐
│           Storage (连续内存块)           │
│  [1.0, 2.0, 3.0, 4.0, 5.0, 6.0, ...]    │
└─────────────────────────────────────────┘
              ↑
              │ offset + stride 映射
              ↓
┌─────────────────────────────────────────┐
│           Tensor (视图/元数据)           │
│  shape: [2, 3], stride: [3, 1]          │
└─────────────────────────────────────────┘
```

**关键理解**：
- **Storage**：实际存储数据的连续内存块
- **Tensor**：对 Storage 的**视图**，包含 shape、stride、offset 等元数据
- 多个 Tensor 可以共享同一 Storage（视图操作不复制数据）

---

## 二、操作指南

### 2.1 创建 Tensor

```python
# 从数据创建
torch.tensor([1, 2, 3])                    # 从列表
torch.as_tensor(np_array)                  # 从 numpy（零拷贝）
torch.from_numpy(np_array)                 # 从 numpy（共享内存）

# 初始化创建
torch.zeros(3, 4)                          # 全零
torch.ones(3, 4)                           # 全一
torch.full((3, 4), 5.0)                    # 填充指定值
torch.empty(3, 4)                          # 未初始化（最快）
torch.arange(0, 10, 2)                     # 等差数列
torch.linspace(0, 1, 5)                    # 等间距

# 随机创建
torch.rand(3, 4)                           # 均匀分布 [0, 1)
torch.randn(3, 4)                          # 标准正态分布
torch.randint(0, 10, (3, 4))               # 随机整数
```

### 2.2 形状操作

```python
x = torch.randn(2, 3, 4)

# 重塑
x.view(6, 4)                               # 改变形状（需连续）
x.reshape(6, 4)                            # 改变形状（自动处理非连续）
x.flatten()                                # 展平为一维
x.unsqueeze(0)                             # 增加维度
x.squeeze()                                # 移除大小为1的维度

# 转置
x.t()                                      # 2D 转置
x.transpose(0, 1)                          # 交换维度
x.permute(2, 0, 1)                         # 任意维度重排

# 拼接
torch.cat([x, y], dim=0)                   # 沿维度拼接
torch.stack([x, y], dim=0)                 # 在新维度堆叠
```

### 2.3 索引与切片

```python
x = torch.arange(12).reshape(3, 4)

# 基础索引
x[0]           # 第一行
x[:, 1]        # 第二列
x[0:2, 1:3]    # 切片

# 高级索引
x[[0, 2]]                    # 索引选择
x[x > 5]                     # 布尔掩码
x[torch.tensor([0, 2])]      # Tensor 索引

# 重要：索引返回的是视图还是副本？
# - 基础切片 → 视图（共享内存）
# - 高级索引 → 副本（独立内存）
```

### 2.4 数学运算

```python
# 逐元素运算
x + y, x - y, x * y, x / y
torch.add(x, y)
torch.mul(x, y)

# 矩阵运算
x @ y                        # 矩阵乘法
torch.matmul(x, y)
torch.mm(x, y)               # 2D 矩阵乘法
torch.bmm(x, y)              # 批处理矩阵乘法

# 归约运算
x.sum(), x.mean(), x.std()
x.sum(dim=0)                 # 沿指定维度
x.argmax(), x.argmin()

# 广播机制
x = torch.randn(3, 1)
y = torch.randn(1, 4)
z = x + y                    # 结果形状 (3, 4)
```

---

## 三、API 手册（分类速查）

### 3.1 创建类 API

| API                | 描述       | 示例                    |
| ------------------ | ---------- | ----------------------- |
| `torch.tensor()`   | 从数据创建 | `torch.tensor([1,2,3])` |
| `torch.zeros()`    | 全零张量   | `torch.zeros(3,4)`      |
| `torch.ones()`     | 全一张量   | `torch.ones(3,4)`       |
| `torch.empty()`    | 未初始化   | `torch.empty(3,4)`      |
| `torch.arange()`   | 等差数列   | `torch.arange(0,10,2)`  |
| `torch.linspace()` | 等间距     | `torch.linspace(0,1,5)` |
| `torch.rand()`     | 均匀随机   | `torch.rand(3,4)`       |
| `torch.randn()`    | 正态随机   | `torch.randn(3,4)`      |
| `torch.eye()`      | 单位矩阵   | `torch.eye(3)`          |

### 3.2 形状类 API

| API                  | 描述     | 示例                       |
| -------------------- | -------- | -------------------------- |
| `tensor.view()`      | 改变形状 | `x.view(6,4)`              |
| `tensor.reshape()`   | 改变形状 | `x.reshape(6,4)`           |
| `tensor.flatten()`   | 展平     | `x.flatten()`              |
| `tensor.unsqueeze()` | 增维     | `x.unsqueeze(0)`           |
| `tensor.squeeze()`   | 减维     | `x.squeeze()`              |
| `tensor.permute()`   | 维度重排 | `x.permute(2,0,1)`         |
| `tensor.transpose()` | 交换维度 | `x.transpose(0,1)`         |
| `torch.cat()`        | 拼接     | `torch.cat([x,y],dim=0)`   |
| `torch.stack()`      | 堆叠     | `torch.stack([x,y],dim=0)` |

### 3.3 数学类 API

| API               | 描述     | 示例                     |
| ----------------- | -------- | ------------------------ |
| `torch.add()`     | 加法     | `torch.add(x,y)`         |
| `torch.mul()`     | 乘法     | `torch.mul(x,y)`         |
| `torch.matmul()`  | 矩阵乘   | `torch.matmul(x,y)`      |
| `torch.sum()`     | 求和     | `torch.sum(x,dim=0)`     |
| `torch.mean()`    | 均值     | `torch.mean(x)`          |
| `torch.max()`     | 最大值   | `torch.max(x)`           |
| `torch.argmax()`  | 最大索引 | `torch.argmax(x)`        |
| `torch.softmax()` | Softmax  | `torch.softmax(x,dim=1)` |

### 3.4 设备与类型 API

| API               | 描述          | 示例                    |
| ----------------- | ------------- | ----------------------- |
| `tensor.to()`     | 转换设备/类型 | `x.to('cuda')`          |
| `tensor.cuda()`   | 移至 GPU      | `x.cuda()`              |
| `tensor.cpu()`    | 移至 CPU      | `x.cpu()`               |
| `tensor.type()`   | 转换类型      | `x.type(torch.float64)` |
| `tensor.detach()` | 分离计算图    | `x.detach()`            |
| `tensor.clone()`  | 深拷贝        | `x.clone()`             |

---

## 四、最佳实践

### 4.1 性能优化

```python
# ✅ 推荐：使用 inplace 操作节省内存
x.add_(y)          # inplace 加法
x.relu_()          # inplace 激活

# ✅ 推荐：避免不必要的 .item() 调用
loss = loss.item()  # 仅在需要 Python 标量时调用

# ✅ 推荐：批量操作优于循环
# 慢
for i in range(n):
    result[i] = x[i] * 2
# 快
result = x * 2

# ✅ 推荐：使用 torch.no_grad() 禁用梯度
with torch.no_grad():
    model.eval()
    output = model(input)
```

### 4.2 内存管理

```python
# ✅ 及时释放不需要的 Tensor
del large_tensor
torch.cuda.empty_cache()  # 清理 GPU 缓存

# ✅ 使用 .detach() 断开计算图
hidden = hidden.detach()  # 防止梯度回溯

# ✅ 避免在循环中累积计算图
for batch in dataloader:
    with torch.no_grad():  # 验证时
        ...
```

### 4.3 设备管理

```python
# ✅ 推荐：设备无关代码
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
model = model.to(device)
input = input.to(device)

# ✅ 推荐：Tensor 与模型在同一设备
assert model.weight.device == input.device
```

### 4.4 数据类型选择

| 场景         | 推荐 dtype                  | 理由           |
| ------------ | --------------------------- | -------------- |
| 默认训练     | `torch.float32`             | 精度与速度平衡 |
| 混合精度训练 | `torch.float16`             | 节省显存，加速 |
| 高精度计算   | `torch.float64`             | 数值稳定性     |
| 索引/掩码    | `torch.long` / `torch.bool` | 内存效率       |
| 量化模型     | `torch.int8`                | 极致压缩       |

---

## 五、避坑指南

### 5.1 常见陷阱

#### ❌ 陷阱 1：视图 vs 副本混淆

```python
# 问题：以为修改视图不影响原 Tensor
x = torch.arange(6).reshape(2, 3)
y = x[:, 0]        # 视图（共享内存）
y[0] = 100         # ⚠️ x 也会被修改！

# 解决：明确需要副本时使用 .clone()
y = x[:, 0].clone()
```

#### ❌ 陷阱 2：非连续 Tensor 的 view 操作

```python
# 问题：对非连续 Tensor 使用 view()
x = torch.randn(3, 4).t()   # 转置后非连续
y = x.view(12)              # ⚠️ 报错！

# 解决：使用 reshape() 或先 contiguous()
y = x.reshape(12)           # ✅ 自动处理
y = x.contiguous().view(12) # ✅ 先变连续
```

#### ❌ 陷阱 3：设备不匹配

```python
# 问题：模型和数据在不同设备
model = model.cuda()
input = input  # 仍在 CPU
output = model(input)  # ⚠️ 报错！

# 解决：确保设备一致
input = input.to(model.device)
```

#### ❌ 陷阱 4：梯度累积

```python
# 问题：忘记清零梯度
for batch in dataloader:
    loss = criterion(output, target)
    loss.backward()
    # ⚠️ 缺少 optimizer.zero_grad()

# 解决：每步清零
optimizer.zero_grad()
loss.backward()
optimizer.step()
```

#### ❌ 陷阱 5：计算图泄露

```python
# 问题：在循环中累积计算图
total_loss = 0
for batch in dataloader:
    output = model(batch)
    total_loss += loss_fn(output, target)  # ⚠️ 计算图累积！

# 解决：使用 .item() 提取标量
total_loss = 0
for batch in dataloader:
    output = model(batch)
    total_loss += loss_fn(output, target).item()  # ✅
```

### 5.2 调试技巧

```python
# 检查 Tensor 属性
print(f"shape: {x.shape}, dtype: {x.dtype}, device: {x.device}")
print(f"requires_grad: {x.requires_grad}, is_leaf: {x.is_leaf}")

# 检查是否连续
print(f"is_contiguous: {x.is_contiguous()}")

# 检查梯度
print(f"grad_fn: {x.grad_fn}")
print(f"grad: {x.grad}")

# 内存检查
print(f"numel: {x.numel()}, element_size: {x.element_size()}")
print(f"memory (MB): {x.numel() * x.element_size() / 1024 / 1024}")
```

### 5.3 性能诊断

```python
# 使用 torch.profiler 分析性能
with torch.profiler.profile(
    activities=[torch.profiler.ProfilerActivity.CUDA],
    record_shapes=True
) as prof:
    output = model(input)

print(prof.key_averages().table(sort_by="cuda_time_total"))
```

---

## 六、高级主题

### 6.1 自动微分机制

```python
x = torch.tensor([1.0, 2.0, 3.0], requires_grad=True)
y = x ** 2
z = y.sum()
z.backward()

print(x.grad)  # [2.0, 4.0, 6.0]  ∂z/∂x
```

### 6.2 自定义 Tensor 操作

```python
# 继承 torch.autograd.Function
class MyFunction(torch.autograd.Function):
    @staticmethod
    def forward(ctx, input):
        ctx.save_for_backward(input)
        return input * 2
  
    @staticmethod
    def backward(ctx, grad_output):
        input, = ctx.saved_tensors
        return grad_output * 2
```

### 6.3 分布式 Tensor

```python
# 多 GPU 训练
model = torch.nn.parallel.DistributedDataParallel(model)

# Tensor 并行
from torch.distributed import tensor
dt = tensor.from_local(local_tensor, device_mesh, placements)
```

---

## 七、速查总结

```
┌─────────────────────────────────────────────────────────────┐
│                    PyTorch Tensor 核心要点                   │
├─────────────────────────────────────────────────────────────┤
│ 1. Tensor = Storage(数据) + 元数据(shape/stride/device)     │
│ 2. 视图操作共享内存，高级索引创建副本                         │
│ 3. 非连续 Tensor 不能直接用 view()，用 reshape()            │
│ 4. 设备匹配：model.to(device) + input.to(device)           │
│ 5. 梯度管理：zero_grad() + backward() + step()             │
│ 6. 计算图隔离：.detach() 或 torch.no_grad()                │
│ 7. 内存优化：inplace 操作 + 及时 del + empty_cache()       │
└─────────────────────────────────────────────────────────────┘
```

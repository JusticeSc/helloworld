# 深入Python数据分析工具Pandas

---

## 一、理解底层：Pandas的架构哲学与NumPy基因

### 1.1 从Excel到Pandas：两种不同的设计哲学

| 维度         | Excel（传统电子表格） | Pandas（现代数据分析库） |
| ------------ | --------------------- | ------------------------ |
| **数据模型** | 二维单元格网格        | 带标签的多维数组         |
| **计算范式** | 单元格公式，逐格计算  | **向量化运算**，整列操作 |
| **内存布局** | 分散存储，高开销      | 连续内存块，低开销       |
| **扩展性**   | 单机、单线程          | 原生支持并行、集群扩展   |

**核心原理**：Pandas的DataFrame本质上是一个**元数据层 + NumPy数组容器**。
```python
# 深入查看底层数据结构
import pandas as pd
import numpy as np

# 创建示例DataFrame
df = pd.DataFrame({
    'A': np.arange(1_000_000, dtype='int32'),  # 4字节/元素
    'B': np.random.randn(1_000_000),          # 8字节/元素
    'C': pd.date_range('2023-01-01', periods=1_000_000, freq='T')
})

# 关键洞察：DataFrame是列式存储
print(f"内存占用: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")
print(f"列'A'的NumPy数组类型: {type(df['A'].values)}")
print(f"列'A'的NumPy数组dtype: {df['A'].values.dtype}")
```

**为什么向量化快100倍？**
```python
# 反例：Python级循环（极慢！）
slow_result = []
for val in df['A']:
    slow_result.append(val * 2)

# 正例：Pandas向量化（瞬间完成）
fast_result = df['A'] * 2

# 正例：NumPy底层操作（最快）
fastest_result = df['A'].values * 2

# 性能对比（概念性）：
# 循环: O(n) × Python解释器开销 ≈ 10-100倍慢
# 向量化: O(n) × C级优化 ≈ 原生速度
```

### 1.2 Series的深入剖析：不只是“带标签的数组”

一个常见的误解是Series = 列表 + 标签。实际上，Series的设计精妙得多：

```python
# 创建Series的多种方式及其内存含义
# 方式1：从列表创建（自动生成RangeIndex）
s1 = pd.Series([10, 20, 30, 40])
print(f"s1.index类型: {type(s1.index)}")  # RangeIndex：几乎零开销

# 方式2：从字典创建（显式标签）
s2 = pd.Series({'a': 10, 'b': 20, 'c': 30, 'd': 40})
print(f"s2.index类型: {type(s2.index)}")  # Index：存储哈希映射，支持快速查找

# 方式3：从NumPy数组创建（共享内存）
arr = np.array([10, 20, 30, 40], dtype='float32')
s3 = pd.Series(arr, index=['x', 'y', 'z', 'w'])
print(f"s3.values与arr共享内存: {s3.values is arr}")  # True：零拷贝！

# Series的键能力：自动对齐（Auto-alignment）
s_left = pd.Series([1, 2, 3], index=['a', 'b', 'c'])
s_right = pd.Series([10, 20, 30], index=['b', 'c', 'd'])

# 魔法时刻：索引自动对齐，而不是位置匹配
result = s_left + s_right
print("自动对齐结果:")
print(result)
# a     NaN  # 仅左有 → NaN
# b    12.0  # 左右都有 → 1+10
# c    23.0  # 左右都有 → 2+20
# d     NaN  # 仅右有 → NaN
```

---

## 二、DataFrame：理解“列的字典”设计范式

### 2.1 DataFrame的三种创建方式与内存布局

```python
# 方法1：字典式创建（最直观）
df_dict = pd.DataFrame({
    'employee_id': [101, 102, 103, 104],
    'name': ['Alice', 'Bob', 'Charlie', 'Diana'],
    'salary': [75000, 82000, 68000, 91000],
    'join_date': pd.to_datetime(['2020-03-15', '2019-11-20', '2021-01-10', '2018-07-30'])
})
print("DataFrame结构预览:")
print(df_dict)
print(f"列类型: {df_dict.dtypes.to_dict()}")

# 方法2：列表的列表（类似NumPy）
data = [
    [101, 'Alice', 75000, '2020-03-15'],
    [102, 'Bob', 82000, '2019-11-20'],
    [103, 'Charlie', 68000, '2021-01-10'],
    [104, 'Diana', 91000, '2018-07-30']
]
df_list = pd.DataFrame(data, columns=['employee_id', 'name', 'salary', 'join_date'])

# 方法3：从NumPy结构化数组（最高效）
dtype = [('employee_id', 'int32'), ('name', 'U20'), ('salary', 'float64'), ('join_date', 'datetime64[s]')]
structured_array = np.array([
    (101, 'Alice', 75000.0, np.datetime64('2020-03-15')),
    (102, 'Bob', 82000.0, np.datetime64('2019-11-20')),
    (103, 'Charlie', 68000.0, np.datetime64('2021-01-10')),
    (104, 'Diana', 91000.0, np.datetime64('2018-07-30'))
], dtype=dtype)
df_structured = pd.DataFrame.from_records(structured_array)
```

### 2.2 属性详解：`.shape`, `.columns`, `.dtypes`, `.info()`

```python
# 实战：快速数据质量诊断工作流
def data_health_check(df):
    """全面的数据健康检查"""
    print("=" * 60)
    print("DATA HEALTH CHECK REPORT")
    print("=" * 60)

    # 1. 基本维度
    print(f"📊 数据规模: {df.shape[0]} 行 × {df.shape[1]} 列")

    # 2. 内存使用
    mem_usage = df.memory_usage(deep=True)
    print(f"💾 内存占用: {mem_usage.sum() / 1024**2:.2f} MB")
    print("   各列详情:")
    for col, usage in mem_usage.items():
        print(f"     - {col}: {usage / 1024:.1f} KB")

    # 3. 类型分析（关键！）
    print("🔧 数据类型分布:")
    type_counts = df.dtypes.value_counts()
    for dtype, count in type_counts.items():
        print(f"     - {dtype}: {count} 列")

    # 4. 缺失值统计
    missing = df.isnull().sum()
    missing_pct = (missing / len(df) * 100).round(2)
    print("⚠️  缺失值分析:")
    for col in missing[missing > 0].index:
        print(f"     - {col}: {missing[col]} 个 ({missing_pct[col]}%)")

    # 5. 唯一值统计（识别低基数分类特征）
    print("🎯 唯一值分析:")
    for col in df.select_dtypes(include=['object']).columns:
        unique_count = df[col].nunique()
        if unique_count < 20:  # 低基数特征
            print(f"     - {col}: {unique_count} 个唯一值")
            print(f"       样本: {df[col].unique()[:5]}...")

    print("=" * 60)

# 执行诊断
data_health_check(df_dict)
```

---

## 三、索引大师课：`loc` vs `iloc` 的深度对决

### 3.1 核心区别：标签索引 vs 位置索引

| 特性         | `loc` (Label-based)            | `iloc` (Position-based)        |
| ------------ | ------------------------------ | ------------------------------ |
| **索引类型** | 标签（字符串、整数等）         | 整数位置（0-based）            |
| **包含末端** | 包含结束标签                   | 不包含结束位置                 |
| **索引器**   | 单一标签、列表、切片、布尔数组 | 整数、整数列表、切片、布尔数组 |
| **核心用途** | 业务逻辑查询（按姓名、日期等） | 数据操作（前N行、采样等）      |

### 3.2 实战对比：易错场景全解析

```python
# 构建具有挑战性的数据集
df_sales = pd.DataFrame({
    'date': pd.date_range('2024-01-01', periods=10, freq='D'),
    'product_id': [101, 102, 103, 101, 104, 102, 101, 103, 104, 105],
    'sales': [150, 220, 180, 90, 310, 420, 130, 280, 190, 240],
    'region': ['North', 'South', 'East', 'West', 'North', 'South', 'East', 'West', 'North', 'South']
})
df_sales.set_index('date', inplace=True)  # 设置日期为索引
print("数据集预览:")
print(df_sales)
print(f"\n索引类型: {type(df_sales.index)}")

# ========== 场景1：单元素访问 ==========
print("\n🔍 场景1：获取特定日期的销售数据")
# loc：使用日期标签
print(f"loc['2024-01-03']:\n{df_sales.loc['2024-01-03']}")
# iloc：使用位置（第2行，因为0-based）
print(f"iloc[2]:\n{df_sales.iloc[2]}")

# ========== 场景2：切片操作（关键差异！） ==========
print("\n🔍 场景2：获取1月1日到1月5日的数据")
# loc：包含结束标签
print("使用loc切片（包含末端）:")
print(df_sales.loc['2024-01-01':'2024-01-05'])  # 5行数据

# iloc：不包含结束位置
print("\n使用iloc切片（不包含末端）:")
print(df_sales.iloc[0:5])  # 0,1,2,3,4 → 5行数据
print(f"注意：iloc[0:5] 获取的是前5行，而loc['2024-01-01':'2024-01-05']是1-5日共5行")

# ========== 场景3：混合索引（行标签+列位置） ==========
print("\n🔍 场景3：获取特定日期的第1、3列")
# 错误尝试：会引发异常
try:
    df_sales.loc['2024-01-03', [0, 2]]
except Exception as e:
    print(f"错误：{type(e).__name__}: {e}")

# 正确做法：loc需要列标签，iloc需要列位置
print("\n正确做法 - 方法A（使用列名）:")
print(df_sales.loc['2024-01-03', ['product_id', 'region']])

print("\n正确做法 - 方法B（使用列位置）:")
print(df_sales.iloc[2, [0, 2]])  # 第2行，第0和2列

# ========== 场景4：布尔索引 ==========
print("\n🔍 场景4：查找销售额大于200的记录")
high_sales_mask = df_sales['sales'] > 200

# loc：使用布尔数组
print("使用loc + 布尔数组:")
print(df_sales.loc[high_sales_mask])

# iloc：同样可以，但需要将布尔数组与位置对应
print("\n使用iloc + 布尔数组:")
print(df_sales.iloc[high_sales_mask.values])

# ========== 场景5：修改数据（避免SettingWithCopyWarning） ==========
print("\n🔍 场景5：安全地修改数据")
# 危险：链式索引（可能引发警告或不可预测行为）
df_sales[df_sales['region'] == 'North']['sales'] = 999  # 不推荐！

# 正确：使用loc进行条件赋值
df_sales.loc[df_sales['region'] == 'North', 'sales'] = 888
print("使用loc安全修改后的North地区销售数据:")
print(df_sales[df_sales['region'] == 'North'])

# ========== 场景6：多层索引（高级） ==========
print("\n🔍 场景6：多层索引场景")
# 创建多层索引DataFrame
df_multi = df_sales.reset_index().set_index(['region', 'date'])
df_multi.sort_index(inplace=True)
print("多层索引DataFrame:")
print(df_multi)

# loc的多层索引查询
print("\n查询North地区所有数据:")
print(df_multi.loc['North'])

print("\n查询North地区1月1日到1月3日数据:")
print(df_multi.loc[('North', slice('2024-01-01', '2024-01-03')), :])
```

### 3.3 性能考量：何时用`loc`，何时用`iloc`？

```python
# 性能测试：大数据量下的索引性能
large_df = pd.DataFrame(np.random.randn(1_000_000, 5), 
                       columns=['A', 'B', 'C', 'D', 'E'],
                       index=pd.date_range('2020-01-01', periods=1_000_000, freq='T'))

# 场景A：已知位置的高频访问 → iloc更快
import time

# iloc：直接位置访问
start = time.time()
for _ in range(1000):
    _ = large_df.iloc[500000]
iloc_time = time.time() - start

# loc：需要哈希查找
start = time.time()
target_date = large_df.index[500000]
for _ in range(1000):
    _ = large_df.loc[target_date]
loc_time = time.time() - start

print(f"单行访问性能对比:")
print(f"  iloc: {iloc_time:.4f}秒 (直接内存偏移)")
print(f"  loc:  {loc_time:.4f}秒 (哈希查找 + 内存偏移)")
print(f"  性能差异: {loc_time/iloc_time:.1f}倍")

# 最佳实践总结：
# 1. 业务查询（按日期、ID等） → 使用loc
# 2. 数据操作（前N行、随机采样等） → 使用iloc
# 3. 循环中避免重复索引计算 → 先获取iloc位置
```

---

## 四、数据清洗实战：缺失值处理的策略与陷阱

### 4.1 系统化缺失值诊断

```python
# 创建带有复杂缺失模式的数据集
df_with_nulls = pd.DataFrame({
    'customer_id': [1001, 1002, 1003, 1004, 1005, 1006, 1007, 1008],
    'age': [25, 32, np.nan, 41, 29, np.nan, 35, 28],
    'income': [50000, 62000, 48000, np.nan, 53000, 71000, np.nan, 59000],
    'purchase_amount': [120.5, np.nan, 89.9, 210.3, 150.0, np.nan, np.nan, 95.7],
    'last_purchase': pd.to_datetime(['2023-12-01', '2024-01-15', np.nan, 
                                     '2024-02-20', '2024-01-30', np.nan, 
                                     '2023-11-10', '2024-02-28']),
    'segment': ['A', 'B', 'A', np.nan, 'C', 'B', 'A', 'C']
})

print("原始数据（显示缺失值）:")
print(df_with_nulls)
print("\n缺失值统计:")
print(df_with_nulls.isnull().sum())
print("\n缺失值百分比:")
print((df_with_nulls.isnull().sum() / len(df_with_nulls) * 100).round(2))

# 可视化缺失模式
import matplotlib.pyplot as plt
import seaborn as sns

plt.figure(figsize=(10, 6))
sns.heatmap(df_with_nulls.isnull(), cbar=False, cmap='viridis', 
            yticklabels=False, cbar_kws={'label': 'Missing'})
plt.title('缺失值分布热图', fontsize=14, pad=20)
plt.tight_layout()
plt.show()

# 识别缺失模式：MCAR、MAR、MNAR？
print("\n缺失模式分析:")
# 检查缺失是否随机
for col in df_with_nulls.columns:
    if df_with_nulls[col].isnull().any():
        # 计算其他列在缺失行和非缺失行的均值差异
        for other_col in df_with_nulls.columns:
            if other_col != col and df_with_nulls[other_col].dtype in ['int64', 'float64']:
                mean_when_null = df_with_nulls.loc[df_with_nulls[col].isnull(), other_col].mean()
                mean_when_not_null = df_with_nulls.loc[~df_with_nulls[col].isnull(), other_col].mean()
                diff_pct = abs(mean_when_null - mean_when_not_null) / mean_when_not_null * 100
                if diff_pct > 20:  # 差异超过20%
                    print(f"  {col}缺失时，{other_col}均值差异: {diff_pct:.1f}% (可能非随机缺失)")
```

### 4.2 缺失值处理策略矩阵

```python
def handle_missing_data(df, strategy='auto'):
    """
    智能缺失值处理
    策略选项: 'auto', 'delete', 'mean', 'median', 'mode', 'forward', 'interpolate'
    """
    df_clean = df.copy()

    for col in df_clean.columns:
        if df_clean[col].isnull().sum() == 0:
            continue
        
        null_pct = df_clean[col].isnull().sum() / len(df_clean)
        dtype = df_clean[col].dtype
    
        print(f"\n处理列: {col} (缺失率: {null_pct:.1%}, 类型: {dtype})")
    
        # 策略选择逻辑
        if strategy == 'auto':
            # 自动策略选择
            if null_pct > 0.3:  # 缺失过多
                current_strategy = 'delete'
            elif dtype in ['int64', 'float64']:
                # 数值型：检查偏度
                skewness = df_clean[col].skew()
                if abs(skewness) > 1:  # 偏态分布
                    current_strategy = 'median'
                else:  # 近似正态
                    current_strategy = 'mean'
            elif dtype == 'object':
                current_strategy = 'mode'
            elif pd.api.types.is_datetime64_any_dtype(df_clean[col]):
                current_strategy = 'forward'
            else:
                current_strategy = 'delete'
        else:
            current_strategy = strategy
    
        # 执行处理
        if current_strategy == 'delete':
            if null_pct > 0.5:
                print(f"  → 删除列（缺失过多）")
                df_clean.drop(columns=[col], inplace=True)
            else:
                print(f"  → 删除行（{df_clean[col].isnull().sum()}行）")
                df_clean = df_clean.dropna(subset=[col])
    
        elif current_strategy == 'mean':
            fill_value = df_clean[col].mean()
            print(f"  → 均值填充: {fill_value:.2f}")
            df_clean[col] = df_clean[col].fillna(fill_value)
    
        elif current_strategy == 'median':
            fill_value = df_clean[col].median()
            print(f"  → 中位数填充: {fill_value:.2f}")
            df_clean[col] = df_clean[col].fillna(fill_value)
    
        elif current_strategy == 'mode':
            fill_value = df_clean[col].mode()[0] if not df_clean[col].mode().empty else 'Unknown'
            print(f"  → 众数填充: {fill_value}")
            df_clean[col] = df_clean[col].fillna(fill_value)
    
        elif current_strategy == 'forward':
            print(f"  → 前向填充")
            df_clean[col] = df_clean[col].fillna(method='ffill')
    
        elif current_strategy == 'interpolate':
            print(f"  → 线性插值")
            df_clean[col] = df_clean[col].interpolate()

    print(f"\n处理完成。原始形状: {df.shape}, 清洗后形状: {df_clean.shape}")
    return df_clean

# 执行智能清洗
df_cleaned = handle_missing_data(df_with_nulls, strategy='auto')
print("\n清洗后数据:")
print(df_cleaned)
```

### 4.3 高级技巧：基于模型的缺失值填充

```python
# 使用KNN或随机森林进行智能填充（适用于复杂模式）
from sklearn.impute import KNNImputer

# 只对数值列进行KNN填充
numeric_cols = df_with_nulls.select_dtypes(include=[np.number]).columns
df_numeric = df_with_nulls[numeric_cols].copy()

# 标准化（KNN对尺度敏感）
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
df_scaled = scaler.fit_transform(df_numeric)

# KNN填充
imputer = KNNImputer(n_neighbors=3)
df_imputed = imputer.fit_transform(df_scaled)

# 反标准化
df_imputed_original = scaler.inverse_transform(df_imputed)
df_final = pd.DataFrame(df_imputed_original, columns=numeric_cols, index=df_with_nulls.index)

print("KNN填充结果（数值列）:")
print(df_final)
```

---

## 五、数据IO：高性能读写实战指南

### 5.1 读取大型文件的正确姿势

```python
# 场景：处理5GB的CSV文件
import os

# 技巧1：指定数据类型，减少内存占用
dtype_spec = {
    'user_id': 'int32',      # 原本可能是int64，节省50%内存
    'product_id': 'int32',
    'price': 'float32',      # 原本可能是float64
    'category': 'category',  # 分类数据使用category类型
    'timestamp': 'str'       # 先以字符串读入，再转换
}

# 技巧2：只读取需要的列
usecols = ['user_id', 'product_id', 'price', 'timestamp', 'is_purchased']

# 技巧3：分块读取（真正的流式处理）
chunksize = 100_000  # 每次读取10万行
chunks = []

# 假设文件路径
csv_path = 'large_sales_data.csv'

# 分块读取并处理
for i, chunk in enumerate(pd.read_csv(csv_path, 
                                       dtype=dtype_spec,
                                       usecols=usecols,
                                       chunksize=chunksize)):
    # 在内存中进行处理
    chunk['timestamp'] = pd.to_datetime(chunk['timestamp'])
    chunk['month'] = chunk['timestamp'].dt.month

    # 聚合或过滤
    monthly_summary = chunk.groupby('month')['price'].sum()

    chunks.append(monthly_summary)

    if i % 10 == 0:
        print(f"已处理 {(i+1)*chunksize:,} 行数据")

    # 可选：达到一定量后保存中间结果
    if i == 50:
        print("保存中间结果...")
        # pd.concat(chunks).to_parquet('intermediate_result.parquet')

# 合并结果
final_result = pd.concat(chunks, axis=1).sum(axis=1)
print(f"\n最终月度汇总:\n{final_result}")

# 技巧4：使用高效格式存储
print("\n不同格式的性能对比:")
test_data = pd.DataFrame(np.random.randn(1_000_000, 10), 
                         columns=[f'col_{i}' for i in range(10)])

# 测试CSV
csv_time = %timeit -o -r 3 test_data.to_csv('test.csv', index=False)
print(f"CSV写入: {csv_time.average:.2f}秒")

# 测试Parquet（现代列式存储）
parquet_time = %timeit -o -r 3 test_data.to_parquet('test.parquet', index=False)
print(f"Parquet写入: {parquet_time.average:.2f}秒 ({csv_time.average/parquet_time.average:.1f}倍更快)")

# 读取对比
csv_read_time = %timeit -o -r 3 pd.read_csv('test.csv')
print(f"CSV读取: {csv_read_time.average:.2f}秒")

parquet_read_time = %timeit -o -r 3 pd.read_parquet('test.parquet')
print(f"Parquet读取: {parquet_read_time.average:.2f}秒 ({csv_read_time.average/parquet_read_time.average:.1f}倍更快)")

# 文件大小对比
csv_size = os.path.getsize('test.csv') / 1024**2
parquet_size = os.path.getsize('test.parquet') / 1024**2
print(f"文件大小: CSV={csv_size:.1f}MB, Parquet={parquet_size:.1f}MB ({csv_size/parquet_size:.1f}倍压缩)")
```

---

## 六、统计分析进阶：描述性统计与聚合模式

### 6.1 全面的描述性统计框架

```python
# 创建复杂数据集
np.random.seed(42)
df_stats = pd.DataFrame({
    'department': np.random.choice(['Sales', 'Marketing', 'Engineering', 'HR'], 1000),
    'salary': np.random.lognormal(mean=10.5, sigma=0.3, size=1000).round(2),
    'bonus': np.random.exponential(scale=5000, size=1000).round(2),
    'years_experience': np.random.randint(1, 20, 1000),
    'performance_score': np.random.beta(a=2, b=5, size=1000) * 100,
    'is_manager': np.random.choice([0, 1], 1000, p=[0.8, 0.2])
})

print("数据集描述性统计:")
print(df_stats.describe())

# 扩展统计：偏度、峰度、分位数
def extended_describe(df):
    """增强版描述性统计"""
    stats_df = pd.DataFrame(index=df.select_dtypes(include=[np.number]).columns)

    # 基础统计
    stats_df['count'] = df.count()
    stats_df['mean'] = df.mean()
    stats_df['std'] = df.std()
    stats_df['min'] = df.min()
    stats_df['25%'] = df.quantile(0.25)
    stats_df['median'] = df.median()
    stats_df['75%'] = df.quantile(0.75)
    stats_df['max'] = df.max()
    stats_df['range'] = df.max() - df.min()

    # 高级统计
    stats_df['skewness'] = df.skew()  # 偏度
    stats_df['kurtosis'] = df.kurt()  # 峰度
    stats_df['cv'] = (df.std() / df.mean() * 100).round(2)  # 变异系数
    stats_df['iqr'] = df.quantile(0.75) - df.quantile(0.25)  # 四分位距
    stats_df['mad'] = df.mad()  # 平均绝对偏差

    # 缺失值
    stats_df['missing'] = df.isnull().sum()
    stats_df['missing_pct'] = (df.isnull().sum() / len(df) * 100).round(2)

    # 异常值检测（基于IQR）
    Q1 = df.quantile(0.25)
    Q3 = df.quantile(0.75)
    IQR = Q3 - Q1
    outliers = ((df < (Q1 - 1.5 * IQR)) | (df > (Q3 + 1.5 * IQR))).sum()
    stats_df['outliers'] = outliers
    stats_df['outliers_pct'] = (outliers / len(df) * 100).round(2)

    return stats_df.T  # 转置以更好显示

print("\n增强版描述性统计:")
extended_stats = extended_describe(df_stats.select_dtypes(include=[np.number]))
print(extended_stats)
```

### 6.2 高效聚合模式：GroupBy的进阶用法

```python
# GroupBy的多层聚合
grouped = df_stats.groupby('department')

# 方法1：一次性计算多个聚合
agg_results = grouped.agg({
    'salary': ['mean', 'median', 'std', 'min', 'max'],
    'bonus': ['sum', 'mean', lambda x: x.quantile(0.9)],  # 自定义90分位数
    'years_experience': 'mean',
    'performance_score': ['mean', 'count']
})

print("多层聚合结果:")
print(agg_results)

# 方法2：命名聚合（Pandas 0.25+）
agg_named = grouped.agg(
    avg_salary=('salary', 'mean'),
    median_salary=('salary', 'median'),
    total_bonus=('bonus', 'sum'),
    top10_bonus=('bonus', lambda x: x.quantile(0.9)),
    exp_years_mean=('years_experience', 'mean'),
    high_performers=('performance_score', lambda x: (x > 80).sum())
)

print("\n命名聚合结果:")
print(agg_named)

# 方法3：transform（保持原始形状）
# 计算每个部门相对于部门平均的z-score
df_stats['salary_zscore'] = grouped['salary'].transform(
    lambda x: (x - x.mean()) / x.std()
)

# 计算部门排名
df_stats['dept_salary_rank'] = grouped['salary'].rank(ascending=False, method='dense')

print("\nTransform操作后的数据（前10行）:")
print(df_stats[['department', 'salary', 'salary_zscore', 'dept_salary_rank']].head(10))

# 方法4：filter（基于组特征过滤）
# 只保留部门平均绩效大于60的部门成员
high_perf_depts = grouped.filter(lambda x: x['performance_score'].mean() > 60)
print(f"\n高绩效部门成员数: {len(high_perf_depts)} (原始: {len(df_stats)})")
```

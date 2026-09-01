# 7-25 念数字（R语言实现）

## 前言

本题（7-25 念数字）的主要考点是：准确理解题意、规范处理输入输出格式，并正确处理边界与精度。下面先给出题目描述与格式要求，再通过清晰的思路与可直接运行的R代码进行讲解。

## 题目描述

输入一个整数，输出每个数字对应的拼音。当整数为负数时，先输出fu字。十个数字对应的拼音如下：
```in
0: ling
1: yi
2: er
3: san
4: si
5: wu
6: liu
7: qi
8: ba
9: jiu
```

## 输入格式

输入在一行中给出一个整数，如：1234。

提示：整数包括负数、零和正数。

## 输出格式

在一行中输出这个整数对应的拼音，每个数字的拼音之间用空格分开，行末没有最后的空格。如
yi er san si。

## 输入样例

```in
-600
```

## 输出样例

```out
fu liu ling ling
```

## 解题思路

### 1. 核心问题分析

将输入整数的每一位数字转换为对应的拼音输出，负数先输出"fu"。关键是符号处理、逐位转换和空格分隔（行末无空格）。

### 2. 算法原理

将输入读为字符串而非整数，便于逐位处理和直接判断负号。使用字符串数组pinyin[10]存储0-9对应的拼音，数字字符num[i]-'0'即为数组下标，直接索引取得对应拼音字符串。使用start变量标记数字起始位置（负数跳过负号），通过判断i>start来决定是否前置空格，保证行末无多余空格。

### 3. 具体计算步骤

1. 以字符串形式读取输入整数
2. 初始化pinyin数组映射0-9到对应拼音
3. 若首字符为'-'，先输出"fu"，数字从索引1开始
4. 遍历每个数字字符：
   - 若非第一个数字（i>start），先输出空格
   - 输出对应拼音pinyin[num[i]-'0']
5. 输出换行符结束

## 完整代码

```r
# 念数字（file="stdin"，修复 fu 后缺少空格）
lines <- readLines(file("stdin"))
if (length(lines) == 0 || trimws(lines[1]) == "") quit(save="no")
num <- trimws(lines[1])
if (is.na(num) || num == "") quit(save="no")
pinyin <- c("ling","yi","er","san","si","wu","liu","qi","ba","jiu")
chars <- strsplit(num, "")[[1]]
if (length(chars) == 0) quit(save="no")
out <- c()
if (length(chars) >= 1 && chars[1] == "-") {
  out <- c(out, "fu")
  chars <- chars[-1]
  if (length(chars) == 0) { cat(paste(out, collapse=" "), "\n", sep=""); quit(save="no") }
}
for (ch in chars) {
  d <- suppressWarnings(as.integer(ch))
  if (is.na(d)) next
  out <- c(out, pinyin[d + 1])
}
cat(paste(out, collapse=" "), "\n", sep="")
```

## 代码流程说明

1. **输入读取**：使用`readLines()`从标准输入读取数字字符串num
2. **拼音映射表**：定义pinyin字符向量，0-9对应"ling"到"jiu"（R向量下标从1开始，数字d对应pinyin[d+1]）
3. **负号判断**：用`strsplit()`将字符串拆为字符向量，若chars[1]=='-'，使用`cat()`输出"fu"，设置start=2；否则start=1
4. **逐位输出循环**：从start开始遍历到字符串末尾（R向量下标从1开始）
   - 非首位字符（i>start）先输出空格分隔
   - 用`as.integer()`将数字字符转为数字，再索引对应拼音并输出
5. **结束处理**：输出换行符。R是顶层脚本语言，代码自上而下执行，无需main函数与头文件

## 代码流程图

```mermaid
flowchart TD
    A["开始"] --> B["输入数字字符串"]
    B --> C["定义拼音映射数组0到9对应拼音"]
    C --> D["start=1"]
    D --> E{"首字符是负号?"}
    E -->|是| F["输出fu，start=2"]
    E -->|否| G["i=start"]
    F --> G
    G --> H{"未到字符串末尾?"}
    H -->|否| P["输出换行"]
    H -->|是| I{"不是第一个数字?"}
    I -->|是| J["输出空格"]
    I -->|否| K["查表输出对应拼音"]
    J --> K
    K --> L["i加1"]
    L --> H
    P --> Q["结束"]
```

## 解题流程图

```mermaid
flowchart TD
    A["输入整数N"] --> B["以字符串形式存储"]
    B --> C{"字符串首字符为'-'?"}
    C -->|是| D["先输出fu，从第2个字符开始处理"]
    C -->|否| E["从第1个字符开始处理"]
    D --> F["取当前字符c"]
    E --> F
    F --> G{"处理完所有字符?"}
    G -->|是| H["结束"]
    G -->|否| I{"是否是第一个数字?"}
    I -->|是| J["直接输出c对应拼音"]
    I -->|否| K["先输出空格，再输出c对应拼音"]
    J --> L["处理下一个字符"]
    K --> L
    L --> F
```

## 代码解析

```r
# 念数字（file="stdin"）
lines <- readLines(file("stdin"))
if (length(lines) == 0 || trimws(lines[1]) == "") quit(save="no")
num <- trimws(lines[1])
if (is.na(num) || num == "") quit(save="no")
pinyin <- c("ling","yi","er","san","si","wu","liu","qi","ba","jiu")
chars <- strsplit(num, "")[[1]]
if (length(chars) == 0) quit(save="no")
out <- c()
if (length(chars) >= 1 && chars[1] == "-") { out <- c(out, "fu"); chars <- chars[-1]; if (length(chars)==0) { cat(paste(out, collapse=" "), "\n", sep=""); quit(save="no") } }
for (ch in chars) { d <- suppressWarnings(as.integer(ch)); if (is.na(d)) next; out <- c(out, pinyin[d + 1]) }
cat(paste(out, collapse=" "), "\n", sep="")
```

### 代码解析要点

输入保留为字符串以避免逐位拆分丢失信息；负号先转换为 `fu`，其余字符按0到9的拼音映射，再用空格连接避免行末多余空格。

## 复杂度分析

设输入规模为 $n$（对数值类题目为参与运算的数据量，对字符串/序列类题目为长度）。

- **时间复杂度**：$O(n)$ 或 $O(n \log n)$，主要来自一次线性遍历与常数次数学运算，无嵌套高复杂度循环。
- **空间复杂度**：$O(n)$，用于存储输入、中间结果与输出字符串；若仅使用若干标量变量则为 $O(1)$。

## 常见易错点

### 1. 输入/输出格式不符
错误：多余空格、遗漏换行、大小写或精度不符。后果：判题系统判为格式错误。正确：严格按题目要求的格式输出，数值用合适精度。

### 2. 边界条件遗漏
错误：未处理 0、最小值、单字符或空输入等边界。后果：特例 WA。正确：先列出所有边界样例，在编码前单独分支处理。

### 3. 整数溢出与类型
错误：使用过小的整数类型或忽略负号。后果：大数计算溢出。正确：按数据范围选择合适类型，必要时用更大整数类型或字符串处理。

## 更多测试

### 测试一：常规边界

**输入：**

```text
105
```

**输出：**

```text
yi ling wu
```

### 测试二：特殊用例

**输入：**

```text
-105
```

**输出：**

```text
fu yi ling wu
```

## 总结

本题的核心在于理清「念数字」的输入输出关系与边界处理：先按格式读取输入，再依据规则逐步计算或遍历，最后按规范输出。该思路可迁移到同类格式化输入输出与模拟计算的题目。

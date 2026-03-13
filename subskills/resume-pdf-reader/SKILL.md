---
name: resume-pdf-reader
version: 1.0.0
description: |
  简历 PDF 专用解析工具。基于 pdfplumber 和 pypdf 提取 PDF 简历内容，
  支持扫描版 OCR、复杂格式处理、表格提取。为 resume-analyser skill 提供
  PDF 读取能力。
allowed-tools:
  - Read
  - Write
  - Edit
  - Exec
---

# Resume PDF Reader - 简历 PDF 解析

专门用于解析 PDF 格式简历的工具。支持多种 PDF 类型和复杂格式。

---

## 🔍 PDF 类型识别

### 1. 文本型 PDF
- **特征：** 可直接选中文本，格式清晰
- **解析方式：** 直接使用 pdfplumber 提取

### 2. 扫描版 PDF
- **特征：** 图片格式，无法选中文本
- **解析方式：** 需要 OCR 处理（pytesseract + pdf2image）

### 3. 加密 PDF
- **特征：** 打开时需要密码
- **解析方式：** 提示用户提供密码，或建议转换为非加密版本

---

## 🛠️ 解析流程

### Step 1: 文件验证
```
1. 检查文件是否存在
2. 检查文件扩展名（.pdf）
3. 检查文件大小（< 10MB）
4. 尝试打开文件
```

### Step 2: PDF 类型检测
```python
import pypdf

reader = pypdf.PdfReader(pdf_path)
try:
    page = reader.pages[0]
    text = page.extract_text()
    if text and len(text.strip()) > 50:
        return "文本型 PDF"
    else:
        return "扫描版 PDF（需要 OCR）"
except Exception as e:
    if "encrypted" in str(e).lower():
        return "加密 PDF"
    else:
        return "未知类型"
```

### Step 3: 内容提取

#### 方案 A：文本型 PDF（推荐）
```python
import pdfplumber

def extract_text_from_pdf(pdf_path):
    """从 PDF 提取文本内容"""
    content = []
    
    with pdfplumber.open(pdf_path) as pdf:
        for i, page in enumerate(pdf.pages):
            # 提取文本
            text = page.extract_text()
            if text:
                content.append(f"## 第{i+1}页\n")
                content.append(text)
            
            # 提取表格
            tables = page.extract_tables()
            if tables:
                content.append("\n### 表格内容\n")
                for j, table in enumerate(tables):
                    content.append(f"表格 {j+1}:")
                    for row in table:
                        content.append(" | ".join(str(cell) for cell in row if cell))
                    content.append("")
    
    return "\n".join(content)
```

#### 方案 B：扫描版 PDF（OCR）
```python
import pytesseract
from pdf2image import convert_from_path

def extract_text_from_scanned_pdf(pdf_path):
    """从扫描版 PDF 提取文本（OCR）"""
    content = []
    
    # 将 PDF 转换为图片
    images = convert_from_path(pdf_path, dpi=300)
    
    # 对每页进行 OCR
    for i, image in enumerate(images):
        text = pytesseract.image_to_string(image, lang='chi_sim+eng')
        if text.strip():
            content.append(f"## 第{i+1}页\n")
            content.append(text)
    
    return "\n".join(content)
```

### Step 4: 格式清理
```python
def clean_extracted_text(text):
    """清理提取的文本"""
    import re
    
    # 移除多余空行
    text = re.sub(r'\n{3,}', '\n\n', text)
    
    # 移除页眉页脚（常见模式）
    text = re.sub(r'^\s*\d+\s*$', '', text, flags=re.MULTILINE)
    text = re.sub(r'^\s*第\d+页\s*$', '', text, flags=re.MULTILINE)
    
    # 修复断行
    text = re.sub(r'(\w)\n(\w)', r'\1\2', text)
    
    # 统一空格
    text = re.sub(r' {2,}', ' ', text)
    
    return text.strip()
```

---

## 📋 输出格式

### 成功解析
```markdown
# PDF 解析结果

## 文件信息
- 文件名：resume.pdf
- 页数：2
- 类型：文本型 PDF
- 大小：256KB

## 提取内容
[完整的简历文本内容]

## 解析质量
- 文本完整度：95%
- 格式保留：良好
- 表格提取：成功（2 个表格）
```

### 解析失败
```markdown
## 解析失败

**原因：** [具体原因]

**建议：**
1. [解决方案 1]
2. [解决方案 2]
```

---

## ⚠️ 常见问题处理

### 问题 1：PDF 加密
**错误信息：** `PdfReadError: File is encrypted`

**解决方案：**
```markdown
这份 PDF 文件已加密，无法直接解析。

请您：
1. 用 Adobe Reader 或其他 PDF 软件打开文件
2. 选择"打印" → "另存为 PDF"，创建非加密版本
3. 或联系文件发送者获取未加密版本
```

### 问题 2：扫描版 PDF 质量差
**错误信息：** OCR 识别率低，文字混乱

**解决方案：**
```markdown
这份 PDF 是扫描版，OCR 识别效果不理想。

建议您：
1. 提供原始 Word 或 Markdown 版本简历
2. 或重新扫描，确保清晰度（建议 300 DPI 以上）
3. 或直接将简历内容粘贴为文本
```

### 问题 3：复杂格式错乱
**错误信息：** 表格、多栏布局解析混乱

**解决方案：**
```markdown
PDF 包含复杂格式（表格/多栏），解析可能不完整。

已尽力提取文本内容，建议您：
1. 检查提取内容是否完整
2. 如有缺失，可补充说明
3. 或提供 Word 版本简历
```

### 问题 4：文件过大
**错误信息：** `File size exceeds limit`

**解决方案：**
```markdown
PDF 文件过大（超过 10MB）。

建议您：
1. 压缩 PDF 文件（可用 smallpdf.com 等工具）
2. 或转换为 Word/Markdown 格式
3. 或拆分文件后分别上传
```

---

## 🔧 依赖安装

```bash
# 核心依赖
pip install pdfplumber pypdf

# OCR 支持（可选，用于扫描版 PDF）
pip install pytesseract pdf2image

# 系统依赖（OCR）
# Windows: 安装 https://github.com/tesseract-ocr/tesseract
# macOS: brew install tesseract poppler
# Linux: sudo apt install tesseract-ocr poppler-utils
```

---

## 📞 使用方式

### 方式 1：直接调用
```python
from resume_pdf_reader import extract_resume_text

text = extract_resume_text("path/to/resume.pdf")
print(text)
```

### 方式 2：作为 resume-analyser 子技能
```
用户：帮我分析简历 [上传 resume.pdf]

resume-analyser 调用：
1. resume-pdf-reader 解析 PDF
2. 提取结构化内容
3. 生成分析报告
```

---

## ✅ 质量检查清单

解析完成后，检查以下项目：

- [ ] 姓名、联系方式完整提取
- [ ] 工作经历时间线清晰
- [ ] 公司名称、职位名称准确
- [ ] 项目经历内容完整
- [ ] 技能清单无遗漏
- [ ] 教育背景信息正确
- [ ] 表格内容（如有）提取完整
- [ ] 无明显乱码或格式错误

---

**版本：** v1.0  
**创建日期：** 2026-03-13  
**维护者：** 工部侍郎 + 鲸选 AI-MK97

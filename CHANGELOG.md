# Resume Analyser 更新日志

---

## v1.1.1 (2026-03-13)

### 🐛 问题修复

#### 公司推荐链接优化
- **问题：** 早期版本公司推荐缺少官方投递链接
- **修复：** 所有公司推荐必须包含官方招聘网站链接
- **格式：** 按梯队展示（第一梯队≥80%，第二梯队 70%-79%，第三梯队<70%）
- **链接文字：** 统一为"立即投递"

### 📄 模板更新

#### SKILL.md 模板 3 更新
- 更新公司推荐列表模板，增加梯队分类
- 添加链接规范要求
- 明确官方链接优先原则

#### company-database.json 更新
- 版本号：1.0.0 → 1.1.0
- 所有公司 applyUrl 字段更新为官方招聘页面
- 新增数据标注相关岗位（针对应届生求职场景）

---

## v1.1.0 (2026-03-13)

### ✨ 新增功能

#### 1. Humanizer 去 AI 味集成
- 集成 `D:\openclaw\skills\humanizer` 技能
- 所有自我介绍和求职信自动经过去 AI 味处理
- 去除 AI 高频词（此外、强调、展示、关键等）
- 避免平行结构（不仅...而且...）
- 减少破折号过度使用
- 添加个人语气和情感

#### 2. PDF 解析子技能
- 创建 `resume-pdf-reader` 子技能
- 支持文本型 PDF 直接提取
- 支持扫描版 PDF OCR 识别
- 支持加密 PDF 检测与提示
- 完善的错误处理和用户指引

#### 3. 公司数据库
- 创建 `company-database.json`
- 内置 20+ 目标公司信息
- 包含：字节跳动、阿里巴巴、腾讯、美团、拼多多等
- 字段：技术栈、薪资范围、投递链接、备注

### 📄 新增文件

```
resume-analyser/
├── configs/
│   └── company-database.json         # 公司数据库（20+ 公司）
├── examples/
│   └── sample-resume.md              # 示例简历（Markdown 格式）
├── subskills/
│   └── resume-pdf-reader/
│       └── SKILL.md                  # PDF 解析子技能
├── QUICKSTART.md                     # 快速开始指南
└── CHANGELOG.md                      # 更新日志（本文件）
```

### 🔧 优化改进

#### SKILL.md 更新
- 版本号：v1.0 → v1.1.0
- 添加 resume-pdf-reader 到 allowed-tools
- 更新 PDF 解析优先级说明
- 添加 humanizer 详细使用说明
- 添加更新日志章节

#### 输出质量提升
- 自我介绍经过 humanizer 处理，更自然真实
- PDF 解析支持更多格式，容错性更强
- 公司推荐基于预设数据库，更精准

### 📝 文档完善

- 创建 QUICKSTART.md（快速开始指南）
- 创建 CHANGELOG.md（更新日志）
- 添加示例简历（sample-resume.md）
- 完善 PDF 解析错误处理指引

---

## v1.0.0 (2026-03-12)

### 🎉 初始版本

#### 核心功能
- 简历智能分析（PDF/DOCX/Markdown）
- 技能图谱生成
- 自我介绍生成（30 秒/1 分钟/3 分钟）
- 公司岗位匹配推荐
- 定制化求职信生成

#### 支持工具
- pdf / docx / read（文件解析）
- web_search / web_fetch（公司搜索）
- feishu-bitable（数据存储）

#### 配置文件
- industry-keywords.json（行业关键词库）

#### 模板文件
- analysis-report-template.md
- self-intro-template.md
- cover-letter-template.md

---

## 📋 未来计划

### v1.2.0 (计划中)
- [ ] 面试题库生成（根据简历技能）
- [ ] 薪资谈判建议
- [ ] 简历优化建议
- [ ] LinkedIn 档案优化

### v1.3.0 (计划中)
- [ ] 飞书多维表格集成（数据存储）
- [ ] 飞书日历集成（面试安排）
- [ ] 飞书任务集成（求职进度跟踪）

### v2.0.0 (愿景)
- [ ] 支持图片格式简历（JPG/PNG）
- [ ] 支持飞书文档链接直接解析
- [ ] 支持批量简历分析（HR 场景）
- [ ] 支持面试模拟和反馈

---

**维护者：** 鲸选 AI-MK97  
**最后更新：** 2026-03-13

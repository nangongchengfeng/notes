# OpenClaw 精选技能推荐

## 📌 已安装就绪核心技能（13款）
### 开发效率类
1. **coding-agent**：编码任务代理，支持托管复杂开发需求、代码重构、PR评审，替代手动执行大量编码操作
2. **github**：GitHub 原生操作，支持 issues/PR/CI 管理、API 高级查询，无需手动调用 gh 命令
3. **skill-creator**：技能开发与审计工具，支持从零创建新技能、优化/审核现有技能、规范技能目录结构
4. **summarize**：多模态内容摘要，支持网页、PDF、图片、音频、YouTube 视频自动提取核心内容

### 搜索增强类
5. **duckduckgo-search**：DuckDuckGo 网页搜索，无广告无追踪，获取实时公开信息
6. **tavily**：AI 优化专属搜索引擎，返回更适合 Agent 消费的高相关性结构化结果
7. **find-skills**：技能检索工具，快速查找匹配需求的可安装技能，扩展能力边界

### 自进化类
8. **capability-evolver**：能力自进化引擎，自动分析运行时历史优化执行策略
9. **self-improvement**：自我优化工具，自动记录错误/经验教训，持续提升任务完成质量
10. **ontology**：结构化知识图谱，支持实体存储、关联关系管理、跨技能数据共享
11. **humanizer**：AI 文本人性化润色，自动移除 AI 生成痕迹，输出更自然的人类语言风格

### 系统运维类
12. **healthcheck**：主机安全加固与健康检查，支持安全审计、防火墙/SSH 配置优化、定期巡检
13. **node-connect**：节点连接诊断工具，快速排查移动端/桌面端配对失败、网络连通性问题

## 📥 技能安装方式
### 1. ClawHub 官方源（https://clawhub.ai/）
```bash
# 搜索技能
npx clawhub search <关键词>

# 安装指定技能
npx clawhub install <技能名>

# 更新已安装技能
npx clawhub sync
```

### 2. 腾讯 SkillHub 源（https://skillhub.tencent.com/）
```bash
# 配置腾讯源（首次使用）
openclaw config set skills.source https://skillhub.tencent.com/api/v1

# 安装技能
openclaw skills install <技能名>
```

## 💡 热门待安装技能推荐
如果需要扩展更多能力，推荐安装以下热门技能：
- **1password**：密码与敏感信息管理，自动注入密钥避免明文泄露
- **mcporter**：MCP 服务工具，直接对接各类 MCP 协议工具/服务
- **nano-pdf**：PDF 智能编辑，自然语言指令即可修改 PDF 内容
- **openai-whisper**：本地语音转文字，离线免费处理音频转写
- **weather**：天气查询，无需 API 密钥即可获取全球天气/预报

# CIP Risk Calculator

**免疫检查点抑制剂相关肺炎（CIP）风险计算器 · 2026 年 9 月模型**

基于六项治疗前因素的 Logistic 回归模型，为接受一线免疫治疗的非小细胞肺癌（NSCLC）患者计算 CIP 的模型预测概率。项目采用原生 HTML、CSS 和 JavaScript，提供英文交互界面，所有计算均在浏览器内完成，可直接发布到 GitHub Pages。

## 功能

- **六因素输入**：五项 Yes/No 临床因素，以及连续变量血清肌酐。
- **结果展示**：预测概率、0–100% 概率刻度、线性预测值（logit）、阳性二分类因素数量和本次计算使用的肌酐值。
- **个体贡献展示**：显示各变量对 logit 的加性贡献（β × 输入值）。
- **模型透明度**：可展开计算公式，并查看回归系数、比值比（OR）、95% 置信区间和 p 值。
- **输入校验与重置**：提示缺失或超范围的肌酐输入，支持恢复表单默认值并清空结果。
- **响应式布局**：适配桌面和移动屏幕，包含表单标签、键盘焦点样式和动态结果提示。

无需后端、数据库、API 密钥、npm 依赖或构建步骤。

## 项目结构

所有项目文件均直接放在仓库根目录，README 与 `index.html` 位于同一级：

```text
.
├── index.html                    # 页面结构及入口
├── styles.css                    # 样式、响应式布局
├── model.js                      # 模型参数、输入校验与概率计算
├── app.js                        # 表单交互及结果渲染
├── cip_model_parameters.json     # 模型参数副本
└── README.md                     # 项目说明
```

`index.html` 按顺序加载 `model.js` 和 `app.js`，样式与脚本均使用相对路径，适用于 GitHub Pages 的仓库子路径。

**网页实际使用 `model.js` 中的参数，不会自动读取 `cip_model_parameters.json`。** 因此，将 JSON 文件放在根目录不影响当前版本的计算功能，也不需要创建 `assets/` 文件夹。

## 本地运行

在现代浏览器中打开 `index.html` 即可。当前版本没有远程资源依赖或运行时 JSON 请求，下载完整项目后可离线使用。

如需通过本地服务器预览，安装 Python 3 后，在包含 `index.html` 的目录运行：

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

打开 [http://localhost:8000](http://localhost:8000)，结束预览时按 `Ctrl+C`。

## 使用方法

1. 逐项确认五个二分类因素；默认值均为 **No**。
2. 输入治疗前血清肌酐，单位为 **µmol/L**，当前接受范围为 **40–260（含端点）**。
3. 点击 **Calculate risk**，查看预测概率及各变量贡献。
4. 修改输入后，再次点击 **Calculate risk** 更新结果；结果不会随输入自动重算。
5. 点击 **Reset**，将五项选择恢复为 No、清空肌酐输入并隐藏结果。

请按界面要求使用 µmol/L；当前版本不提供其他肌酐单位的自动换算。

## GitHub Pages 部署

项目文件已上传到仓库根目录后，按以下步骤启用网页：

1. 将本 `README.md` 添加或更新到同一仓库根目录。
2. 打开仓库 **Settings → Pages**。
3. 在 **Build and deployment** 中，将 **Source** 设置为 **Deploy from a branch**。
4. 选择存放项目文件的分支（通常为 **main**），目录选择 **/ (root)**，点击 **Save**。
5. 等待部署完成，从 Pages 设置页打开站点。普通项目仓库的网址通常为：

   ```text
   https://<GitHub用户名>.github.io/<仓库名>/
   ```

后续向发布分支提交更新后，GitHub Pages 会重新部署。本项目不需要自定义构建命令或自行编写 GitHub Actions 工作流。

部署后可用“五项均为 No、肌酐为 80 µmol/L”的输入检查计算：页面应显示约 **6.7%**。这仅是软件核对示例。

### 常见问题

| 现象 | 检查方法 |
| --- | --- |
| 站点显示 404 | 确认部署完成，发布分支正确，且所选根目录中包含 `index.html`。 |
| 页面没有样式 | 确认 `styles.css` 与 `index.html` 同级，文件名大小写正确。 |
| 点击计算没有结果 | 确认 `model.js`、`app.js` 完整上传，且肌酐输入有效。 |
| 更新后仍显示旧页面 | 在仓库 Actions 中检查部署状态；完成后刷新页面或清除浏览器缓存。 |
| 修改 JSON 后结果没有变化 | 当前计算使用 `model.js`；需要修改该文件中的参数。 |

参考：[GitHub Pages 发布源配置](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site) · [创建 GitHub Pages 站点](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site)

## 模型说明

模型标识：`CIP_Prediction_Model_2026_09`。

| 输入变量 | 代码字段 | 编码 / 单位 | 回归系数 β（约） |
| --- | --- | --- | ---: |
| 胸部放疗史 | `Radiotherapy1` | No = 0，Yes = 1 | 1.755602 |
| 基线影像网状阴影 | `Reticular_opacities1` | No = 0，Yes = 1 | 1.127572 |
| 心脏病史 | `Heart_disease1` | No = 0，Yes = 1 | 1.168522 |
| 慢性阻塞性肺疾病史 | `COPD1` | No = 0，Yes = 1 | 0.864630 |
| 肺部手术史 | `Lung_surgery1` | No = 0，Yes = 1 | 0.665227 |
| 治疗前血清肌酐 | `Serum_creatinine` | µmol/L | 0.019967 |

计算方式：

```text
logit = intercept + Σ(βᵢ × xᵢ)
probability = 1 / (1 + exp(−logit))
intercept = −4.22528841827508
```

实际计算使用 `model.js` 中的完整精度，界面概率保留一位小数。贡献条展示的是 logit 上的加性项，并按当前最大贡献缩放；它们不是概率占比，也不是临床阈值。页面未实现低、中、高风险分层。

## 维护与修改

- **模型参数与校验**：修改 `model.js`，这是网页运行时使用的参数来源。
- **参数副本**：同步更新根目录下的 `cip_model_parameters.json`，避免与实际计算参数不一致。
- **肌酐范围或单位**：同时检查 `model.js`、`index.html`、`app.js` 与 JSON 中相应的校验、输入属性和显示文案。
- **页面内容与交互**：分别修改 `index.html` 和 `app.js`。
- **配色与布局**：修改 `styles.css`。

## 数据处理与适用范围

当前代码不发送或持久化保存用户输入，未包含分析追踪脚本；计算和结果展示均在浏览器中完成。通过 GitHub Pages 访问时，静态页面资源仍由托管服务提供。

本工具用于研究和风险估计，不能替代临床评估、诊断检查或当地治疗规范。40–260 µmol/L 是当前软件接受的输入范围，不代表正常参考区间。原始项目未附模型论文、研究队列说明、外部验证结果或预测时间窗，不能仅凭此实现推断模型的临床有效性；相关研究信息应由维护者补充。

## 许可证

原始项目未提供 `LICENSE` 文件，本 README 不额外授予使用许可。如计划以开源项目发布，请由权利人选择合适的许可证并添加 `LICENSE` 文件。

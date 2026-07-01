# xuezhi-platform-crawler

学职平台独立爬虫仓库，只面向 `https://xz.chsi.com.cn` 的专业与职业数据采集。

本仓库从 `gaokao-xuezhi-crawler` 拆分而来，保留通用采集框架和 `src/sources/xuezhi`，去掉阳光高考 / 高考网相关 source，避免学职平台采集和高考源站采集继续耦合。

## 采集范围

| Target | 内容 | 入口 |
| --- | --- | --- |
| `xuezhi_major` | 学职平台专业列表与专业详情 | `https://xz.chsi.com.cn/speciality/index.action` |
| `xuezhi_career` | 学职平台职业列表与职业详情 | `https://xz.chsi.com.cn/occupation/index.action` |

## 目录

| Path | 说明 |
| --- | --- |
| `src/sources/xuezhi/` | 学职平台 source、parser、spider |
| `src/pipelines/` | 分阶段采集流程 |
| `src/common/` | 浏览器、HTTP、路径、日志等通用工具 |
| `src/storage/` | JSON/JSONL、manifest 等输出写入 |
| `configs/` | source、entity、pipeline 配置 |
| `data/` | 原始页、中间索引、最终结果输出 |
| `.github/workflows/` | 学职平台采集 GitHub Actions |

## 本地运行

```bash
python -m pip install -r requirements.txt
python -m playwright install chromium

python -m src.pipelines.stage_01_collect_list_pages --target xuezhi_major
python -m src.pipelines.stage_01_collect_list_pages --target xuezhi_career
```

详情页采集需要先有列表索引：

```bash
python -m src.pipelines.stage_03_collect_detail_pages --target xuezhi_major --limit 10
python -m src.pipelines.stage_03_collect_detail_pages --target xuezhi_career --limit 10
```

## 输出约定

- 列表页原始 HTML：`data/raw/xuezhi/{major|career}/list_pages/<run_id>/`
- 列表索引：`data/stage/01_list_index/<run_id>/xuezhi_*.jsonl`
- 详情页原始 HTML：`data/raw/xuezhi/{major|career}/detail_pages/<run_id>/`
- 详情索引：`data/stage/03_detail_index/<run_id>/xuezhi_*.jsonl`
- 运行 manifest：`data/runs/<run_id>/`

## 边界

- 本仓库只维护学职平台数据采集。
- 阳光高考 / 高考网采集继续留在对应高考采集仓库。
- `xuezhi-gaokao-crawler` 保留为历史迁移仓库，不作为新学职平台主仓库。

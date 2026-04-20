# 貢獻指南 Contributing Guide

感謝你有興趣為台灣運動 MET 資料庫做出貢獻。

## 資料標準

### 每筆 MET 數值必須有：
- **官方來源**：Ainsworth BE et al. (2011) Compendium of Physical Activities（PMID: 21681120），或其他 PMID 可查詢的運動科學文獻
- **正確的強度分類**：light（< 3 MET）、moderate（3–6 MET）、vigorous（> 6 MET）
- **台灣在地脈絡**：`taiwan_context` 欄位說明該運動在台灣的普及情況或特殊注意事項

### 熱量計算公式
```
kcal = MET × 體重(kg) × 時間(小時)
```
提交前請驗算 `kcal_per_hour_60kg`、`kcal_per_hour_70kg`、`kcal_30min_60kg` 是否正確。

### 資料格式
所有檔案遵循 `data/schema.json` 定義。提交前請驗證 JSON 格式正確。

```bash
npx jsonlint data/aerobic.json
```

## 歡迎的貢獻類型

- 新增台灣常見運動項目（如競技排球、跆拳道、直排輪）
- 更正 MET 數值（需附新文獻 PMID）
- 新增台灣特有活動（如廟會陣頭、農務活動）
- 補充 `taiwan_context` 欄位的在地資訊
- 增加日常活動類別（辦公室情境、通勤方式）

## 不接受的貢獻

以下 PR 將直接關閉，不做審查：

- ❌ 沒有文獻來源的 MET 數值（不接受「健身App說是 XX」）
- ❌ 廠商提供的運動手錶或設備消耗數據（個人化設備數據，非標準 MET）
- ❌ 含有外部商業連結的內容（健身房廣告、器材推薦等）
- ❌ 推廣特定健身課程、教練服務或運動品牌的內容
- ❌ 未符合 schema.json 格式的提交

## Pull Request 流程

1. Fork 此 repo
2. 建立功能分支：`git checkout -b add/activity-name`
3. 修改資料並確認 JSON 格式正確
4. PR 說明中需列出：運動項目名稱、MET 來源（Ainsworth 2011 Compendium Code 或 PMID）
5. 等待維護者審查（通常 3–7 個工作天）

## 資料來源參考

- **主要來源**：Ainsworth BE, Haskell WL, Herrmann SD, et al. (2011). 2011 Compendium of Physical Activities. *Medicine & Science in Sports & Exercise*, 43(8), 1575–1581. PMID: 21681120
- **線上查詢工具**：The Compendium of Physical Activities — https://pacompendium.com/
- **PAL 乘數來源**：FAO/WHO/UNU (2001). Human Energy Requirements. Food and Nutrition Technical Report Series 1. Rome: FAO.
- 衛福部國民健康署運動指引：https://www.hpa.gov.tw/Pages/List.aspx?nodeid=115

## 行為準則

請遵守友善、尊重的溝通方式。本庫的目標是為台灣民眾提供準確的運動熱量消耗參考數據，所有討論應以資料正確性為核心。

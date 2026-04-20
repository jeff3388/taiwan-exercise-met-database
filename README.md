# 台灣運動代謝當量資料庫（Taiwan Exercise MET Database）

以機器可讀格式整理常見運動與日常活動的代謝當量（MET）值，並補充台灣特有運動情境說明。可用於計算運動消耗熱量、評估身體活動量（PAL）、以及建立 TDEE 計算工具。

**熱量消耗公式**：`消耗熱量 (kcal) = MET × 體重 (kg) × 時間 (小時)`

---

## 為什麼建立這個

目前最完整的 MET 資料集是 Ainsworth et al. (2011) 的《身體活動彙整表》（Compendium of Physical Activities），但：

- 原始彙整表為英文，無台灣在地化說明
- 缺乏台灣特有運動的 MET 資料（飛輪課時長、台灣百岳爬山強度、太極拳）
- 現有台灣健康網站呈現的運動熱量多為靜態表格，缺乏機器可讀格式供開發者使用
- TDEE 計算工具需要結構化的 MET + 活動量乘數（PAL）對照資料

---

## 資料檔案

| 檔案 | 說明 |
|------|------|
| [`data/aerobic.json`](data/aerobic.json) | 有氧運動（跑步、騎車、游泳、飛輪、跳繩） |
| [`data/strength.json`](data/strength.json) | 重量訓練與徒手訓練 |
| [`data/daily-activities.json`](data/daily-activities.json) | 日常活動（走路、爬樓梯、家務、辦公室）|
| [`data/taiwan-sports.json`](data/taiwan-sports.json) | 台灣常見球類與傳統運動 |
| [`data/pal-multipliers.json`](data/pal-multipliers.json) | TDEE 活動量乘數（PAL）對照表 |
| [`data/schema.json`](data/schema.json) | JSON Schema 欄位定義 |

---

## 資料預覽

### 60 公斤成人各運動消耗熱量（30 分鐘）

| 運動 | MET | 30 min / 60kg | 30 min / 70kg |
|------|-----|--------------|--------------|
| 辦公室久坐 | 1.3 | 39 kcal | 46 kcal |
| 慢走（4 km/hr）| 2.8 | 84 kcal | 98 kcal |
| 快走（6 km/hr）| 3.5 | 105 kcal | 123 kcal |
| 慢跑（8 km/hr）| 8.3 | 249 kcal | 291 kcal |
| 飛輪（中強度） | 8.5 | 255 kcal | 298 kcal |
| 游泳（自由式）| 5.8 | 174 kcal | 203 kcal |
| 重量訓練（高強度）| 6.0 | 180 kcal | 210 kcal |
| 爬山（有坡度）| 7.0 | 210 kcal | 245 kcal |
| 跳繩 | 12.3 | 369 kcal | 431 kcal |
| 太極拳 | 3.0 | 90 kcal | 105 kcal |
| 桌球 | 4.0 | 120 kcal | 140 kcal |

---

## 使用範例

### JavaScript — 計算單次運動消耗熱量

```javascript
import aerobic from './data/aerobic.json'

/**
 * 計算運動消耗熱量
 * @param {string} exerciseId - 運動 ID
 * @param {number} weightKg - 體重（公斤）
 * @param {number} durationMin - 運動時間（分鐘）
 * @returns {number} 消耗熱量（大卡）
 */
function calcCaloriesBurned(exerciseId, weightKg, durationMin) {
  const exercise = aerobic.find(e => e.id === exerciseId)
  if (!exercise) throw new Error(`Exercise ${exerciseId} not found`)
  return Math.round(exercise.met_value * weightKg * (durationMin / 60))
}

// 範例：65 公斤的人跑步 30 分鐘（8 km/hr）消耗多少大卡？
const burned = calcCaloriesBurned('running-8kmh', 65, 30)
// → 270 kcal
```

### Python — 計算一週活動量（PAL）

```python
import json

with open('data/aerobic.json') as f:
    aerobic = json.load(f)
with open('data/pal-multipliers.json') as f:
    pal = json.load(f)

def weekly_calories_burned(sessions, weight_kg):
    """
    sessions: list of {'exercise_id': str, 'duration_min': int, 'days_per_week': int}
    """
    total = 0
    for session in sessions:
        ex = next((e for e in aerobic if e['id'] == session['exercise_id']), None)
        if ex:
            weekly_min = session['duration_min'] * session['days_per_week']
            total += ex['met_value'] * weight_kg * (weekly_min / 60)
    return round(total)

# 範例：每週跑步 3 次（30min）+ 重訓 2 次（45min）
sessions = [
    {'exercise_id': 'running-8kmh', 'duration_min': 30, 'days_per_week': 3},
    {'exercise_id': 'weight-training-vigorous', 'duration_min': 45, 'days_per_week': 2},
]
print(weekly_calories_burned(sessions, 65))  # → 約 1,107 kcal/週
```

---

## 資料來源

| 來源 | 用途 |
|------|------|
| Ainsworth BE et al. (2011). 2011 Compendium of Physical Activities: a second update of codes and MET values. *Medicine & Science in Sports & Exercise*, 43(8):1575–1581. | 所有 MET 基礎值 |
| Jetté M et al. (1990). Metabolic equivalents (METs) as a useful measure of functional aerobic capacity. *Canadian Family Physician*. | MET 概念定義 |
| FAO/WHO/UNU (2001). Human Energy Requirements. Technical Report Series 1. | PAL 活動量乘數 |
| 衛生福利部國民健康署 — 身體活動建議 | 台灣運動建議量（每週 150 分鐘中強度）|

---

## 健康聲明

**本資料庫僅供教育性與研究性用途，不構成醫療建議。MET 值為群體平均值，個人實際消耗熱量因年齡、體能水準、健康狀況和動作效率而異，誤差可達 ±20%。有心臟病、高血壓或其他慢性疾病者，開始新的運動計畫前請先諮詢醫師。**

---

## 相關資源

- [tdee-calculator-library](https://github.com/twjojo/tdee-calculator-library) — TypeScript TDEE 計算工具庫（使用此 MET 資料）
- [TDEE 計算機活動量說明與各活動消耗熱量估算：metabolab.tw](https://metabolab.tw/calculators/tdee)

---

## 授權

[![CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

[Creative Commons 姓名標示 4.0 國際授權](https://creativecommons.org/licenses/by/4.0/deed.zh_TW)  
引用格式：`Taiwan Exercise MET Database, github.com/twjojo/taiwan-exercise-met-database`

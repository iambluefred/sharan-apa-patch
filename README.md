# Sharan APA 方向機標定研究

Volkswagen 夏朗 J500／APA-BS 的 HCA 週期性中斷時間研究與繁體中文說明。

**原始來源：[https://gitee.com/helijia233/sharan_apa_patch](https://gitee.com/helijia233/sharan_apa_patch)**

原作者帳號為 `helijia233`（顯示名稱 `helijia21`）。本整理版依據上游版本 `91d854c73f927a47b5e18d674ad9694447445f34`，保留原始 SGO 與修改檔，將目錄改為英文命名，並補充繁體中文文件。

## 閱讀入口

- [詳細定位與校驗分析](timebomb/README.md)
- [HTML 圖文首頁](README.html)
- [HTML 詳細分析](timebomb/README.html)
- [論壇背景與操作導讀](docs/autohome-notes.html)
- [汽車之家原文](https://club.autohome.com.cn/bbs/thread/f342d3e7bfbae7cf/116486921-1.html)

HTML 文件可下載整個專案後，以瀏覽器開啟；請保留 `docs/guide.css` 與目錄結構。

## 研究背景

上游描述的 HCA 行為為：持續接受車道保持扭矩疊加約 360 秒後，中斷約 3 秒，再恢復約 300 秒。作者嘗試在既有 SGO 中找到對應計時常數，藉由修改標定縮短中斷時間。

這些位置目前屬於候選參數。上游詳細分析保留「沒有 ECU 程式碼，尚無法完全證明 HCA 狀態機讀取該位置」的限制。檔案校驗通過，不代表修改後的實際效果已獲驗證。

本文中的「中斷」指作者描述的 HCA 輔助功能行為，不能據此推定機械轉向鎖住或全部轉向助力消失。

## 研究版本

| 原始檔 | 零件號 | 上游列出的軟體識別 |
|---|---|---|
| [5N1909144R_1401_BP.sgo](originals/5N1909144R_1401_BP.sgo) | 5N1909144R | SV01.14.02／APA_SW.C14_20-S1／2013 |
| [5N1909144S_1501_BP.sgo](originals/5N1909144S_1501_BP.sgo) | 5N1909144S | SV01.15.02 B01／APA_SW.C15_20-S1／2015 |

N_1101（2011）被上游列為交叉比對樣本，但未包含在倉庫內。上述版本為研究範圍，不能當作其他料號或所有夏朗車型的相容清單。

## 候選時間常數

下表為解碼後映像的 **Flash 位址**，不是 SGO 檔案偏移。數值為 16 位元小端（u16 LE），時間依作者採用的 10 ms tick 解釋。

| 作者推定用途 | 數值 | R_1401_BP | S_1501_BP |
|---|---:|---|---|
| 初次工作 360 秒 | 36000／0x8CA0 | `0x0360F8` | `0x036170` |
| 恢復後工作 300 秒 | 30000／0x7530 | `0x036102` | `0x03617A` |
| 中斷 3 秒 | 300／0x012C | `0x036212` | `0x03628A` |

作者以 36000 的唯一出現位置為錨點，再比較 `+0x00A` 與 `+0x11A` 的候選值，以及不同版本間的相對布局。SGO 包含標頭、區塊與編碼，修改時必須先釐清位址映射。

## 修改檔案

| 檔案 | 目標數值改動 | 作者預期 |
|---|---|---|
| [R_1401_BP_lockout3s_to_0.01s.sgo](timebomb/R_1401_BP_lockout3s_to_0.01s.sgo) | R：300 → 1 | 3 秒 → 0.01 秒 |
| [R_1401_BP_lockout3s_to_0s.sgo](timebomb/R_1401_BP_lockout3s_to_0s.sgo) | R：300 → 0 | 3 秒 → 0 秒 |
| [S_1501_BP_lockout3s_to_0.01s.sgo](timebomb/S_1501_BP_lockout3s_to_0.01s.sgo) | S：300 → 1 | 3 秒 → 0.01 秒 |
| [S_1501_BP_lockout3s_to_0s.sgo](timebomb/S_1501_BP_lockout3s_to_0s.sgo) | S：300 → 0 | 3 秒 → 0 秒 |

六份 SGO 已核對與上游版本的位元組完全一致，本整理版未重新生成補丁。上游宣稱補丁的檔案與區塊校驗通過；本整理版尚未獨立重算其校驗，也沒有實車驗證。

## 目錄結構

```text
sharan-apa-patch/
├── README.md
├── README.html
├── originals/
│   ├── 5N1909144R_1401_BP.sgo
│   └── 5N1909144S_1501_BP.sgo
├── timebomb/
│   ├── README.md
│   ├── README.html
│   ├── R_1401_BP_lockout3s_to_0.01s.sgo
│   ├── R_1401_BP_lockout3s_to_0s.sgo
│   ├── S_1501_BP_lockout3s_to_0.01s.sgo
│   └── S_1501_BP_lockout3s_to_0s.sgo
└── docs/
    ├── guide.css
    └── autohome-notes.html
```

上游「原版」目錄在本整理版改名為 `originals/`。上游提到的 `work/scripts/sgo_tool.py` 未包含在其公開倉庫中。

## 來源與授權狀態

- [原始 Gitee 倉庫](https://gitee.com/helijia233/sharan_apa_patch)
- [固定版本的上游詳細分析](https://gitee.com/helijia233/sharan_apa_patch/blob/91d854c73f927a47b5e18d674ad9694447445f34/timebomb/README.md)
- [汽車之家相關討論](https://club.autohome.com.cn/bbs/thread/f342d3e7bfbae7cf/116486921-1.html)

上游目前未附 LICENSE。本版保留來源與作者歸屬，沒有另行宣告授權；相關再散布授權仍待確認。

整理日期：2026-10-28。

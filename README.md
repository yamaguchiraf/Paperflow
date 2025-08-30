# Paperflow


論文情報を **Markdown**, **Papis**, **PowerPoint** に一発で登録するための統合ツールです。
DOI / arXiv / URL / PDF すべて対応、PPT テーマとレイアウトも指定可能です。

---

## ✨ 特徴

* **1コマンドで3連動**

  * Markdown 追記
  * Papis に BibTeX 経由で登録（PDF添付可）
  * PowerPoint にスライド追加
* **DOI / arXiv / PDF / URL** すべて対応
* **テーマ & レイアウト指定**で会社テンプレも対応
* **タイトル自動解決**：

  * `-n` 未指定でも BibTeX → Crossref → arXiv → PDF名で自動設定
* **ライブラリ別運用**：

  * `-l bio` → `~/papers_inbox/bio/quick_notes.md`
  * PPT出力も `~/papers_slides/bio/paperdeck.pptx`
* **PDF添付**：`--pdf ~/Downloads/paper.pdf` で自動添付
* **環境変数で既定テーマ・レイアウト指定可**

---

## 🛠 インストール

### 1. 必要パッケージ

```bash
sudo apt-get install -y ripgrep curl
/home/ryohei/miniconda3/bin/python -m pip install -U python-pptx
/home/ryohei/miniconda3/envs/papis_env/bin/python -m pip install -U papis python-pptx
```

---

### 2. ファイル設置

```bash
mkdir -p ~/bin ~/.local/bin ~/papers_inbox ~/papers_slides ~/.cache/paperflow ~/templates
```

* `~/bin/paperflow` → 本体（v0.9）
* `~/.local/bin/make_paper_slide.py` → スライド作成サブコマンド

```bash
chmod +x ~/bin/paperflow
chmod +x ~/.local/bin/make_paper_slide.py
```

---

### 3. 環境変数設定（\~/.bashrc）

```bash
# PATH
export PATH="$HOME/bin:$PATH"

# 使用Python/Papis環境
export PAPERFLOW_PPT_PYTHON="/home/ryohei/miniconda3/bin/python"
export PAPERFLOW_PAPIS_BIN="/home/ryohei/miniconda3/envs/papis_env/bin/papis"
export PAPERFLOW_PAPIS_PYTHON="/home/ryohei/miniconda3/envs/papis_env/bin/python"

# デフォルトPPTテーマとレイアウト
export PAPERFLOW_PPT_THEME="$HOME/templates/CompanyTheme.pptx"
export PAPERFLOW_PPT_LAYOUT=1  # タイトル＋本文レイアウト

# デフォルトラベル
export PAPERFLOW_LABEL_REL="Relevance（関連性）"
export PAPERFLOW_LABEL_APP="Application（活用）"
```

```bash
source ~/.bashrc
```

---

## 🚀 使い方

### 1. DOIから登録

```bash
paperflow 10.1038/s41586-020-2622-0 \
  -l bio \
  -r "中和抗体の代表例" \
  -u "抗体工学の参考資料"
```

* **Markdown** → `~/papers_inbox/bio/quick_notes.md`
* **PPT** → `~/papers_slides/bio/paperdeck.pptx`
* **Papis** → `papis -l bio add ...`

---

### 2. arXivから登録

```bash
paperflow https://arxiv.org/abs/2306.05421 \
  -l bio \
  -r "タンパク質言語モデルの新展開" \
  -u "AlphaFold代替法の候補"
```

---

### 3. PDFだけ手元にある場合

```bash
paperflow ~/Downloads/review-protein-interaction.pdf \
  --pdf ~/Downloads/review-protein-interaction.pdf \
  -l bio \
  -r "情報資源整理" \
  -u "データベース調査"
```

---

### 4. テーマを指定

```bash
paperflow 10.1016/j.jbiotec.2018.08.011 \
  -l bio \
  -n "CHO Metabolic Model" \
  --theme ~/templates/Theme.pptx \
  --layout 1
```

---

### 5. 複数PDF添付

```bash
paperflow 10.1126/science.aaa8766 \
  -l bio \
  --pdf ~/Downloads/main.pdf,~/Downloads/supplement.pdf \
  -r "Cas9改良研究" \
  -u "遺伝子編集安全性の比較"
```

---

## 📁 出力先構造

```
~/papers_inbox/
    bio/
        quick_notes.md      # DOI, Link, Relevance, Applicationなど

~/papers_slides/
    bio/
        paperdeck.pptx      # 1ライブラリ1デッキ

~/.cache/paperflow/
    *.bib                   # DOI/URLから取得したBibTeXキャッシュ
```

---

## ⚙️ オプションまとめ

| オプション              | 説明              | 例                                       |
| ------------------ | --------------- | --------------------------------------- |
| `-l` / `--library` | Papisライブラリ名     | `-l bio`                                |
| `--pdf`            | PDF添付（複数可）      | `--pdf main.pdf,supp.pdf`               |
| `--theme`          | PPTテーマpptx      | `--theme ~/templates/CompanyTheme.pptx` |
| `--layout`         | PPTレイアウト番号      | `--layout 1`                            |
| `-n` / `--name`    | 手動タイトル指定        | `-n "BERTの元論文"`                         |
| `-r` / `--reason`  | Relevance（関連性）  | `-r "重要指標の確認"`                          |
| `-u` / `--usage`   | Application（活用） | `-u "レビュー用"`                            |
| `-t` / `--tags`    | タグ追加            | `-t "CRISPR,Cas9"`                      |
| `--deck`           | PPT出力先デッキ指定     | `--deck ~/slides/custom.pptx`           |
| `--no-ppt`         | PPT生成しない        |                                         |
| `--no-papis`       | Papis登録しない      |                                         |

---

## 🧩 推奨ワークフロー

1. **DeepResearchレポートPDF**を登録

   ```bash
   paperflow ~/Reports/DeepResearch-LLM-Scaling.pdf \
     --pdf ~/Reports/DeepResearch-LLM-Scaling.pdf \
     -l notes \
     -n "DeepResearch: LLM Scaling" \
     -r "調査サマリー" \
     -u "意思決定用資料"
   ```

2. 会議中は Markdown で検索：

   ```bash
   rg "Cas9" ~/papers_inbox/bio/quick_notes.md
   ```

3. プレゼン資料は自動で更新された `paperdeck.pptx` をそのまま使う

---

## 🧠 Tips

* デフォルトテーマを設定しておけば `--theme` 省略可能
* レイアウト番号は `--layout` で柔軟指定
* `make_paper_slide.py` は **タイトルと本文枠を厳密判定**するので会社テンプレでも崩れにくい
* `.bib` キャッシュは `~/.cache/paperflow` に溜まるので共有OK

---

## 🔜 今後の拡張アイデア

* `--debug-layout` でレイアウトのプレースホルダー一覧を出力
* Crossref/ArXiv APIでさらに強力なタイトル自動解決
* PDFサムネイルを自動生成してスライドに貼る

---

## ライセンス

MIT


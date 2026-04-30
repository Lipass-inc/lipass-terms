# lipass-terms

ライパス株式会社のサービス利用・運用に関する規定文書をバージョン管理するリポジトリです。

## ディレクトリ構成

```
lipass-terms/
├── 情報セキュリティ規程/          # 社内規程（docxから変換）
└── 利用規約・ポリシー/             # 公開ページから取得
```

### 情報セキュリティ規程

| ファイル | 内容 |
|---|---|
| 00_セキュリティホワイトペーパー.md | セキュリティホワイトペーパー |
| 01_個人データ取扱規程.md | 個人データ取扱規程 |
| 02_個人データ点検・監査規程.md | 個人データ点検・監査規程 |
| 03_インシデント対応規程.md | インシデント対応規程 |
| 04_パスワードポリシー.md | パスワードポリシー |
| 05_インターネット・電子メール利用規程.md | インターネット・電子メール利用規程 |
| 06_個人データ管理台帳.md | 個人データ管理台帳 |
| 07_廃棄記録様式.md | 廃棄記録様式 |
| 08_セキュリティ教育計画.md | セキュリティ教育計画 |
| 09_就業規則（個人データ条項）.md | 就業規則（個人データ条項） |
| 10_外部委託管理規程.md | 外部委託管理規程 |
| 11_外部委託契約書（標準ひな形）.md | 外部委託契約書（標準ひな形） |
| 12_入退室管理規程.md | 入退室管理規程 |
| 13_紙媒体持ち出し・封入発送規程.md | 紙媒体持ち出し・封入発送規程 |
| 14_BCP（情報セキュリティインシデント編）.md | BCP（情報セキュリティインシデント編） |
| 15_アクセス制御規程.md | アクセス制御規程 |

### 利用規約・ポリシー

| ファイル | 取得元URL |
|---|---|
| プライバシーポリシー.md | https://lipass.co.jp/privacy.html |
| AI関連サービス利用規約.md | https://lipass.co.jp/ai-service-terms.html |
| サービス利用規約.md | https://lipass.co.jp/terms.html |
| 特定商取引法に基づく表示.md | https://lipass.co.jp/tokushoho.html |

## 更新方法

### 情報セキュリティ規程を更新する場合

1. 新しい `.docx` ファイルを用意する
2. `pandoc` で Markdown に変換する

```bash
pandoc 規程名.docx -t markdown --wrap=none -o 情報セキュリティ規程/規程名.md
```

3. 変更内容を確認して commit する

```bash
git diff
git add 情報セキュリティ規程/規程名.md
git commit -m "規程名: 変更内容の概要"
```

### 利用規約・ポリシーを更新する場合

ウェブサイトの内容が更新されたら、以下のスクリプトで再取得する。

```bash
python3 << 'EOF'
import requests, html2text, re
from datetime import date

pages = [
    ("https://lipass.co.jp/privacy.html",         "利用規約・ポリシー/プライバシーポリシー.md"),
    ("https://lipass.co.jp/ai-service-terms.html", "利用規約・ポリシー/AI関連サービス利用規約.md"),
    ("https://lipass.co.jp/terms.html",            "利用規約・ポリシー/サービス利用規約.md"),
    ("https://lipass.co.jp/tokushoho.html",        "利用規約・ポリシー/特定商取引法に基づく表示.md"),
]

h = html2text.HTML2Text()
h.ignore_images = True
h.body_width = 0

today = date.today().isoformat()
for url, path in pages:
    resp = requests.get(url, timeout=15)
    resp.encoding = 'utf-8'
    md = re.sub(r'\n{3,}', '\n\n', h.handle(resp.text).strip())
    with open(path, 'w', encoding='utf-8') as f:
        f.write(f"<!-- 取得元: {url} -->\n<!-- 取得日: {today} -->\n\n{md}")
    print(f"updated: {path}")
EOF
```

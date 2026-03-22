# PDF → CSV 自動変換（Power Automate Desktop）

## ■ 概要

PDFファイルから表データを抽出し、不要行・不要列を削除してCSV形式で出力するRPAフローです。
ExcelとDataTableを組み合わせてデータ整形を行っています。

---

## ■ フロー全体

![flow1](flow1(PDF抽出).png)
![flow2](flow2(不要行削除).png)
![flow3](flow3(不要列削除).png)
![flow4](flow4(CSV保存).png)

---

## ■ 処理の流れ

1. PDFからテーブルを抽出
2. Excelへデータを書き込み
3. ヘッダー行（上部不要行）を削除
4. ExcelデータをDataTableとして取得
5. 特定行（3行目）を基準に不要列を削除
6. 整形後データをCSVとして出力

---

## ■ 実装のポイント

### ● DataTableを用いた列削除ロジック

3行目を基準に、値が空の列を削除しています。

* 列インデックスを後ろからループ
* 対象行（4行目）を取得
* セルが空の場合、その列を削除

これにより、列削除時のインデックスずれを防止しています。

```text
Loop CurrentIndex = colCount-1 → 0

    rowIndex = 0

    For each CurrentRow in ExcelData

        If rowIndex = 2

            cellValue = CurrentRow[CurrentIndex]

            If cellValue = ""
                → 列削除

            Break

        End

        rowIndex++

    End

End
```

---

### ● なぜ3行目を基準にしたか

PDFから抽出したデータは、完全な空列ではなく
「見た目は空でも実際には値が入っている」ケースがあるため、

→ 特定行を基準に判定することで安定した列削除を実現しました。

---

## ■ 工夫した点

* DataTable操作でExcel依存を減らした
* 列削除を後ろから行うことで安全に処理
* 条件分岐を使った柔軟なデータ整形
* 実務を想定したCSV出力まで一貫処理

---

## ■ 使用技術

* Power Automate Desktop
* Excel
* DataTable操作

---

## ■ 今後の改善

* 複数PDFの一括処理対応
* 列名の自動設定
* UI操作を減らした完全自動化

---

## ■ 補足

本フローは、業務での帳票データ整形を想定して作成しています。
VBAでの実装経験をベースに、RPAでの再現を行いました。

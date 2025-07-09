# Windows11システム要件チェッカー 仕様書

## 📋 プロジェクト概要

### アプリケーション名
**Windows11システム要件チェッカー**

### 目的
ユーザーがWindows設定画面からコピーしたデバイス仕様を解析し、Windows11の互換性を自動判定するWebアプリケーション

### 開発背景
- Windows10からWindows11へのアップグレード判定を簡素化
- 非対応PCユーザーへのサポート終了スケジュール提示
- Firebase開発練習の前段階プロジェクト

## 🎯 機能要件

### 主要機能

#### 1. システム仕様解析
- **入力**: Windowsデバイス仕様のテキスト（コピー&ペースト）
- **解析項目**:
  - プロセッサ（CPU）
  - 実装RAM
  - システムタイプ（64bit/32bit）

#### 2. Windows11要件判定
- **必須要件**:
  - プロセッサ: Intel第8世代以降 または AMD Zen2以降
  - RAM: 4GB以上
  - システム: 64bit対応
  - TPM: 2.0対応（手動確認項目）
  - セキュアブート: 対応（手動確認項目）

#### 3. 結果表示
- **合格項目**: 緑色のチェックマーク
- **不合格項目**: 赤色のバツマーク
- **要確認項目**: 黄色の警告マーク
- **総合判定**: 対応/非対応

#### 4. サポート終了警告
- 非対応PCの場合、Windows10サポート終了スケジュール表示
- 推奨アクションの提示

### 補助機能

#### 1. サンプルデータ読み込み
- Intel i5-7500のサンプルスペック提供
- 動作テスト用

#### 2. 操作ガイド
- Windows設定画面でのコピー方法説明

## 🔧 技術仕様

### 開発環境
- **フロントエンド**: HTML5, CSS3, Vanilla JavaScript
- **ホスティング**: Vercel
- **対応ブラウザ**: モダンブラウザ全般

### ファイル構成
```
project/
└── index.html    # 単一HTMLファイル（CSS, JS内包）
```

### 外部依存
- なし（完全自己完結型）

## 🎨 UI/UX設計

### カラーパレット
- **プライマリ**: `#0078d4` (Microsoft Blue)
- **成功**: `#10b981` (Green)
- **エラー**: `#ef4444` (Red)
- **警告**: `#ffc107` (Yellow)
- **背景**: グラデーション `#667eea` → `#764ba2`

### レイアウト構成
1. **ヘッダー**: タイトル・説明
2. **入力セクション**: テキストエリア・サンプルボタン
3. **実行セクション**: チェックボタン
4. **結果セクション**: 判定結果・総合評価・警告

### レスポンシブ対応
- モバイル: 最小幅320px
- タブレット: 768px以上
- デスクトップ: 1024px以上

## ⚙️ CPU判定ロジック詳細

### Intel CPU判定

#### Core シリーズ
```javascript
// 正規表現: /Intel.*Core.*i[3579]-(\d{1,2})\d{2,3}[A-Z]*/i
// 例: Intel(R) Core(TM) i5-8400 CPU @ 2.80GHz

✅ 対応: 第8世代以降 (i3/i5/i7/i9-8xxx+)
❌ 非対応: 第7世代以前 (i3/i5/i7-7xxx-)
```

#### 特殊シリーズ
```javascript
✅ Xeon E-2100シリーズ以降
✅ Pentium Gold G5400以降  
✅ Celeron N4100以降
❌ Core M シリーズ
❌ Atom シリーズ
❌ 一般Pentium/Celeron
```

### AMD CPU判定

#### Ryzen シリーズ
```javascript
// 正規表現: /AMD.*Ryzen\s+[3579]\s+(\d{1,2})\d{2}[A-Z]*/i
// 例: AMD Ryzen 5 3600 6-Core Processor

✅ 対応: 3000シリーズ以降 (Zen2+)
❌ 非対応: 1000/2000シリーズ (Zen/Zen+)
```

#### 特殊シリーズ
```javascript
✅ Threadripper 3000シリーズ以降
✅ EPYC 7000シリーズ以降
✅ Athlon 3000G
❌ FX シリーズ
❌ A-Series APU
```

### その他CPU
```javascript
✅ Qualcomm Snapdragon 850以降
⚠️ Apple M1/M2 (ARM版Windows11のみ)
⚠️ 仮想マシン環境 (ホスト依存)
❌ VIA プロセッサ
```

## 🔍 解析アルゴリズム

### テキスト解析フロー
1. **入力検証**: 空文字・無効入力チェック
2. **正規表現マッチング**: 各項目の抽出
3. **数値変換**: RAM容量等の数値化
4. **要件照合**: 各項目の合格/不合格判定
5. **結果生成**: 表示用データ構造作成

### 解析対象項目
```javascript
const parseSpecs = (input) => {
  return {
    cpu: extractCPU(input),        // プロセッサ情報
    ram: extractRAM(input),        // RAM容量(GB)
    systemType: extractSystem(input) // 64bit/32bit
  };
};
```

### 判定結果構造
```javascript
const result = {
  results: [
    {
      name: "プロセッサ",
      requirement: "第8世代Intel以降 または AMD Zen2以降",
      detected: "Intel(R) Core(TM) i5-7500",
      passed: false,
      details: "第7世代Intel Core (❌非対応)"
    }
    // ... 他の項目
  ],
  isCompatible: boolean  // 総合判定
};
```

## 🚀 デプロイメント

### Vercel デプロイ手順
1. **ファイル準備**: `index.html` 保存
2. **Vercelアカウント**: [vercel.com](https://vercel.com) 登録
3. **プロジェクト作成**: 「New Project」
4. **ファイルアップロード**: ドラッグ&ドロップ
5. **デプロイ実行**: 「Deploy」ボタン

### 設定ファイル（オプション）
```json
// vercel.json
{
  "functions": {
    "index.html": {
      "memory": 128
    }
  }
}
```

## 🔮 今後の拡張案

### Phase 2: 機能追加
- **TPM 2.0検出**: JavaScript API活用
- **セキュアブート検出**: ブラウザ制限内での検出
- **詳細レポート**: PDF出力機能

### Phase 3: データベース連携
- **判定統計**: 対応/非対応の統計データ
- **CPU データベース**: より多くのCPU対応
- **ユーザーフィードバック**: 判定精度向上

### Phase 4: Firebase統合
- **リアルタイム統計**: 判定結果の集計表示
- **FAQ機能**: よくある質問の管理
- **ユーザー認証**: 判定履歴の保存

## 🐛 既知の制限事項

### 技術的制限
- **ブラウザ制限**: TPM/セキュアブート情報の取得不可
- **仮想マシン**: ホストCPU情報の取得不可
- **言語依存**: 日本語Windows設定画面のみ対応

### 判定精度
- **新しいCPU**: 最新CPUモデルの判定遅れ
- **特殊構成**: OEMカスタムCPUの判定不可
- **表記揺れ**: メーカー固有の表記への未対応

## 📝 サンプルコード

### コア機能の実装例
```javascript
// CPU判定の基本構造
function checkCPU(cpu) {
  if (!cpu) return { passed: false, details: '検出不可' };
  
  // Intel Core判定
  const intelMatch = cpu.match(/Intel.*Core.*i[3579]-(\d{1,2})\d{2,3}/i);
  if (intelMatch) {
    const gen = parseInt(intelMatch[1]);
    return {
      passed: gen >= 8,
      details: `第${gen}世代Intel Core (${gen >= 8 ? '✅対応' : '❌非対応'})`
    };
  }
  
  // 他のCPU判定...
  return { passed: false, details: '判定不可' };
}
```

## 📊 テストケース

### 対応CPU テストデータ
```
Intel(R) Core(TM) i5-8400 CPU @ 2.80GHz   → ✅ 対応
AMD Ryzen 5 3600 6-Core Processor        → ✅ 対応
Intel(R) Pentium(R) Gold G5400 CPU       → ✅ 対応
```

### 非対応CPU テストデータ
```
Intel(R) Core(TM) i5-7500 CPU @ 3.40GHz  → ❌ 非対応
AMD Ryzen 5 2600 6-Core Processor        → ❌ 非対応
Intel(R) Pentium(R) CPU G4560            → ❌ 非対応
```

---

## 🔗 関連リソース

- **Windows11 公式要件**: [Microsoft公式ページ](https://www.microsoft.com/windows/windows-11-specifications)
- **Vercel ドキュメント**: [vercel.com/docs](https://vercel.com/docs)
- **正規表現テスト**: [regex101.com](https://regex101.com)

---

**作成日**: 2025年7月9日  
**バージョン**: 1.0  
**作成者**: Claude Sonnet 4  
**プロジェクト種別**: 学習・デモ用途
# IBMi-Outlook統合プロジェクト ドキュメント

このディレクトリには、IBMiとOutlook 2506をプライベートネットワーク経由で接続するための包括的な設計・実装ドキュメントが含まれています。

## 📚 ドキュメント一覧

### 1. [IBMi_Outlook_Integration_Plan.md](./IBMi_Outlook_Integration_Plan.md)
**基本実装計画書（873行）**

IBMiの標準SMTP/POP3機能を使用した基本的な統合計画です。

**主な内容:**
- システム構成図とアーキテクチャ
- IBMi側のSMTP/POP3サーバー設定手順
- Outlook側の設定手順（第2アカウント追加）
- セキュリティ設定とアクセス制御
- テスト計画（単体・結合・運用テスト）
- 実装スケジュール（4日間）
- トラブルシューティングガイド
- 運用・保守計画

### 2. [IBMi_Outlook_Authentication_Solutions.md](./IBMi_Outlook_Authentication_Solutions.md)
**Microsoft認証方式変更への対応策（738行）**

Outlook 2506でのModern Authentication要件に対応するための4つの解決策を提案しています。

**主な内容:**
- Microsoft認証方式変更の影響分析
- 解決策1: Outlookの基本認証設定確認（推奨）
- 解決策2: OAuth 2.0認証プロキシの導入（DavMail）
- 解決策3: メールファイル直接配置方式
- 解決策4: Webメール経由の統合
- 段階的実装アプローチ
- セキュリティ強化策
- 実装決定フローチャート

### 3. [IBMi_ILE_C_OAuth_Proxy_Implementation.md](./IBMi_ILE_C_OAuth_Proxy_Implementation.md)
**ILE-C OAuth認証プロキシ実装計画（1,456行）**

IBMi上でILE-Cを使用してOAuth 2.0認証プロキシを実装するための完全な設計書です。

**主な内容:**
- OAuth 2.0プロトコル実装
- ILE-Cソースコード設計
  - メインサーバープログラム (OAUTHSRV.C)
  - OAuth 2.0実装 (OAUTH2.C)
  - POP3プロキシ (POP3PROXY.C)
  - SMTPプロキシ (SMTPPROXY.C)
  - トークンDB操作 (TOKENDB.C)
- DB2データベース設計（トークンストレージ）
- ビルド・デプロイ手順（CLスクリプト）
- セキュリティ考慮事項（暗号化、TLS/SSL）
- パフォーマンス最適化
- 実装スケジュール（19日間）

### 4. [IBMi_Connection_Verification_Feature.md](./IBMi_Connection_Verification_Feature.md)
**事前接続検証機能設計書（1,100行以上）**

運用開始前に各コンポーネントの接続状態を個別に検証できる機能の設計書です。

**主な内容:**
- VFYOAUTHコマンド仕様
- 5つの検証タイプ
  - POP3バックエンド接続検証
  - SMTPバックエンド接続検証
  - OAuth認証機能検証
  - データベース接続検証
  - 総合検証
- ILE-C検証モジュール実装
  - VFYPOP3.C - POP3接続検証
  - VFYSMTP.C - SMTP接続検証
  - VFYOAUTH.C - OAuth機能検証
  - VFYDB.C - データベース検証
- 詳細レポート出力機能
- 自動化ヘルスチェック
- 運用ガイドライン

## 🎯 プロジェクト概要

### 目的
IBMi (AS/400) からOutlook 2506へのメール送信を、プライベートネットワーク経由で実現します。

### 制約条件
1. ✅ 中間に物理サーバーを置かない
2. ✅ メールクライアントはOutlookのみ許可
3. ✅ OutlookはMicrosoftのメールサーバーと接続されて使用中
4. ✅ IBMiは閉じたネットワークから外部に出ることは許可されていない
5. ✅ IBMiとMicrosoftのメールサーバーに接続することは許可されていない

### 解決策
IBMi上でILE-CによるOAuth 2.0認証プロキシを実装し、Outlookからの接続を受け付けます。

```
Outlook 2506 (OAuth 2.0)
    ↓
IBMi OAuth Proxy (ILE-C)
    ↓
IBMi SMTP/POP3 Server (基本認証)
```

## 📋 実装スケジュール

| フェーズ | 期間 | 内容 |
|---------|------|------|
| 設計 | 2日 | 詳細設計とレビュー |
| 開発 | 10日 | ILE-Cコード実装 |
| テスト | 5日 | 単体・結合テスト |
| デプロイ | 2日 | 本番環境展開 |
| **合計** | **19日** | |

## 🔧 技術スタック

- **IBMi OS**: V7R1以降推奨
- **プログラミング言語**: ILE-C
- **データベース**: DB2 for i
- **プロトコル**: SMTP, POP3, OAuth 2.0
- **暗号化**: OpenSSL (AES-256, SHA-256)
- **クライアント**: Microsoft Outlook 2506

## 🚀 クイックスタート

### 1. 環境準備
```cl
/* ライブラリ作成 */
CRTLIB LIB(OAUTHLIB) TEXT('OAuth Proxy Library')

/* データベーステーブル作成 */
RUNSQLSTM SRCFILE(OAUTHLIB/QDDSSRC) SRCMBR(TOKENTBL)
RUNSQLSTM SRCFILE(OAUTHLIB/QDDSSRC) SRCMBR(USERCRED)
```

### 2. ビルド
```cl
/* ビルドスクリプト実行 */
CALL OAUTHLIB/CRTOAUTH
```

### 3. 起動
```cl
/* OAuth Proxyサーバー起動 */
CALL OAUTHLIB/STROAUTH
```

### 4. 検証
```cl
/* 接続検証 */
VFYOAUTH TYPE(*ALL) USER(TESTUSER) PASSWORD(TEST123) +
         DETAIL(*FULL)
```

## 📖 ドキュメント読み順

1. **初めての方**: `IBMi_Outlook_Integration_Plan.md` から読み始めてください
2. **認証問題に直面している方**: `IBMi_Outlook_Authentication_Solutions.md` を参照
3. **実装担当者**: `IBMi_ILE_C_OAuth_Proxy_Implementation.md` で詳細を確認
4. **運用・テスト担当者**: `IBMi_Connection_Verification_Feature.md` で検証方法を確認

## 🔒 セキュリティ

- トークンはAES-256で暗号化してDB2に保存
- プライベートネットワーク内のみで通信
- IPアドレス制限によるアクセス制御
- 定期的なトークンローテーション
- 詳細な監査ログ

## 📞 サポート

問題が発生した場合は、各ドキュメントの「トラブルシューティング」セクションを参照してください。

## 📝 変更履歴

| 日付 | バージョン | 変更内容 |
|------|-----------|---------|
| 2026-01-05 | 1.0 | 初版作成 - 全ドキュメント完成 |

## 📄 ライセンス

このドキュメントは内部使用のみを目的としています。

---

**プロジェクト管理情報**
- プロジェクト名: IBMi-Outlook統合
- 作成日: 2026-01-05
- 最終更新: 2026-01-05
- ステータス: 設計完了
sequenceDiagram
    participant U as 医師/薬剤師
    participant MP as マイナポータル
    participant SP as スマートフォン
    participant HPKI as HPKI認証局
    participant MS as 医療機関システム
    participant PS as 薬局システム

    %% マイナポータル連携フロー
    Note over U,PS: 【初期セットアップ：マイナポータル連携フロー】
    U->>MP: 1. マイナポータルにログイン
    Note over U: マイナンバーカードで<br/>本人認証
    U->>MP: 2. HPKIセカンド証明書<br/>発行申請
    MP->>HPKI: 3. 証明書発行要求
    HPKI-->>HPKI: 4. セカンド証明書生成
    HPKI->>MP: 5. 証明書情報連携
    opt スマートフォン認証設定
        U->>MP: 6. スマートフォン連携設定
        MP->>SP: 7. アプリ認証情報送信
        SP-->>SP: 8. 生体認証等の設定
        SP->>MP: 9. 設定完了通知
    end
    MP->>U: 10. セットアップ完了通知

    %% 処方箋発行フロー
    Note over U,PS: 【処方箋発行フロー】
    U->>MS: 1. 処方箋データ作成
    MS->>HPKI: 2. 署名要求
    alt ICカードリーダー利用
        Note over U: 3a. マイナンバーカードで<br/>本人認証
        U->>MS: PIN入力
    else スマートフォン利用
        Note over U: 3b. スマートフォンで<br/>本人認証
        U->>SP: 生体認証
        SP->>MS: 認証結果送信
    end
    MS->>HPKI: 4. 認証情報送信
    HPKI-->>HPKI: 5. HPKIセカンド証明書で<br/>電子署名
    HPKI->>MS: 6. 署名済み処方箋返送
    MS->>PS: 7. 電子処方箋送信

    %% 処方箋認証・調剤フロー
    Note over U,PS: 【処方箋認証・調剤フロー】
    PS->>PS: 1. 処方箋受信
    PS->>HPKI: 2. 医師の証明書情報照会
    HPKI->>PS: 3. 証明書情報送信
    PS-->>PS: 4. 電子署名検証
    alt ICカードリーダー利用
        Note over U: 5a. マイナンバーカードで<br/>本人認証
        U->>PS: PIN入力
    else スマートフォン利用
        Note over U: 5b. スマートフォンで<br/>本人認証
        U->>SP: 生体認証
        SP->>PS: 認証結果送信
    end
    PS->>HPKI: 6. 薬剤師の署名要求
    HPKI-->>HPKI: 7. HPKIセカンド証明書で<br/>電子署名
    HPKI->>PS: 8. 署名済み調剤情報返送
    PS->>MS: 9. 調剤済情報送信

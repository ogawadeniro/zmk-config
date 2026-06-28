# BOTS40 ZMK Config

Seeed Xiao BLE (nRF52840) ベースの分割キーボード BOTS40 の ZMK ファームウェア設定。

## ビルド

- ローカルビルド不要。`main` への push / PR で GitHub Actions が自動ビルド (`build.yml` → `zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`)
- ビルド設定は `build.yaml`: `bots40_R` / `bots40_L` / `settings_reset` の3種類

## リポジトリ構造

```
config/
├── boards/shields/bots40/       # シールド定義
│   ├── bots40.dtsi              # 共通: キーマトリクス、トランスフォーム、物理レイアウト参照
│   ├── bots40-layouts.dtsi      # 物理レイアウト (zmk,physical-layout)
│   ├── position_map.dtsi        # 位置マップ
│   ├── bots40_L.overlay         # 左側: col-gpios 定義
│   ├── bots40_R.overlay         # 右側: col-gpios + col-offset 定義
│   ├── bots40_L.conf            # 左側: RGBLED, EC11
│   ├── bots40_R.conf            # 右側: BLE, マウス, Studio
│   └── Kconfig.shield           # シールド定義
├── bots40.keymap                # 6レイヤーキーマップ (Default/SYMBOL/NUM/MOUSE/FUNCTION/MANAGEMENT)
├── keycode_japanese.h           # JIS キーコードマッピング
└── west.yml                     # West マニフェスト (zmk, zmk-rgbled-widget)
```

## キーマップ編集のポイント

- **キーマップ**: `config/bots40.keymap` を編集
- **hold-tap / layer-tap を多用**: `&lt 5 DEL`, `&mt LCTRL Z`, `&lt 4 S` など
- **JIS配列**: `keycode_japanese.h` で JIS → US キーコードのマッピングを定義
- **マクロ**: `s_kp` (Shift+キー) と `cg_kp` (Ctrl+GUI+キー) の2つ定義済み。`&s_kp COMMA` のように使う
- **マウスキー**: Layer 3 (MOUSE) と Layer 4 (FUNCTION) で `&mmv` / `&msc` / `&mkp` を使ったマウス操作が可能
- **ZMK Studio**: 右側 (`bots40_R`) で有効 (`studio-rpc-usb-uart` スニペット)
- **RGBLED**: `zmk-rgbled-widget` でバッテリー/Bluetooth 状態表示

## 注意事項

- トラックボール (PMW3610) は使用していない。関連コード (`bots40_R.overlay` の SPI/pinctrl/trackball 設定、`west.yml` のドライバ参照) は削除済み
- `zephyr/module.yml` で `board_root: config` を指定し、ボード定義をリポジトリ内で完結
- `memo.txt` は古いCIビルドログのキャプチャ。指示ファイルではない
- `west.yml` の `revision: main` は ZMK 最新開発版を追従。必要に応じてバージョン固定すること

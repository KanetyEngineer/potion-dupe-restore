# Potion Dupe Restore（1.21.11 用 Fabric MOD）

1.21.1 で動いていた「投げたポーションのネザーゲート複製」（流云の蝙蝠药水复制、乌_龙_茶の 10gt 复合药水复制机 など）を 1.21.11 でも動くようにするサーバー側 MOD です。

## 何が変わったのか（1.21.1 → 1.21.11）

| 項目 | 1.21.1 | 1.21.2 以降 | この MOD |
| --- | --- | --- | --- |
| ゲートを通った tick の当たり判定 | ゲートで別ディメンションへ移った「元の」実体も同じ tick に当たり判定をして割れる（＝割れた効果＋向こう側のコピー） | `isAlive()` の条件が付き、移った元実体は割れない | 元実体が `CHANGED_DIMENSION` で消えた場合だけ当たり判定を通す |
| 投擲物のゲート待ち時間 | 300 tick | 2 tick | 300 tick に戻す |
| 投擲物の tick の順番 | ゲート処理 → 当たり判定 → ブロック効果（移動前の位置）→ 移動 → 減速 → 重力 | 重力 → 減速 → 当たり判定 → 移動 → ブロック効果 → ゲート処理 → 当たり判定 | 1.21.1 の順番で動かす |
| ハチミツブロックの滑り | 落下速度 -0.08 未満で -0.05 に固定 | 生物用の換算式（重力0.08）を通すため、ポーションは約3.5倍速く滑る | 1.21.1 の式に戻す |
| 実体への当たり判定の余白 | 常に 0.3 | 出てから数 tick は 0 から徐々に 0.3 | 常に 0.3 |

対象は既定でスプラッシュ／残留ポーションだけです（ほかの投擲物はバニラのまま）。

## ダウンロード

[Releases](../../releases/latest) から `potion-dupe-*.jar` をダウンロードしてください。

## 導入

1. Minecraft 1.21.11 の Fabric サーバーに Fabric API（1.21.11 版）を入れる
2. `potion-dupe-1.0.0+mc1.21.11.jar` を `mods/` に入れる
3. 起動すると `config/potiondupe.json` ができる

クライアントには不要です。

## コマンド（OP レベル2）

| コマンド | 内容 |
| --- | --- |
| `/potiondupe` | 現在の設定を表示 |
| `/potiondupe on` / `off` | まとめてオン／オフ（オフで完全にバニラ） |
| `/potiondupe scope potions` / `all` | 対象をポーションだけ／エンダーパール以外の全投擲物に |
| `/potiondupe hitAfterPortal true\|false` | 複製そのもの |
| `/potiondupe legacyPortalCooldown true\|false` | ゲート待ち時間 300 tick |
| `/potiondupe legacyPhysics true\|false` | tick 順・ハチミツ・当たり余白を 1.21.1 に |
| `/potiondupe reload` | 設定ファイルを読み直す |

## ビルド

Java 21 と Gradle 9.2 以上で `gradle build`。jar は `build/libs/` にできます。GitHub Actions の build ワークフローを release_tag 付きで手動実行するか、`v*` のタグを push すると Release ができます。

## 動作確認

Minecraft 1.21.11 + Fabric Loader 0.19.5 + Fabric API 0.141.6 で確認済みです。MOD オフでは投げたポーションがネザーへ移るだけですが、オンではゲートに入った tick にちょうど床へ当たるものが「こちらで割れる＋向こうにコピー」になり、1.21.1 と同じタイミング条件で複製されます。

## 注意

- 1.21.2 以降のサーバーは、誰もログインしていないと 60 秒で tick が止まります（`server.properties` の `pause-when-empty-seconds`）。無人で装置を動かしたいときは `-1` にしてください。
- サーバー側だけで動きます。シングルプレイでも使えます（クライアントの `mods/` に入れる）。
- バニラの仕様を意図的に戻す MOD です。導入するサーバーのルールに従ってください。
- Mojang / Microsoft とは無関係の非公式 MOD です。

## ライセンス

MIT

---

## English

Server-side Fabric mod for Minecraft 1.21.11 that restores the 1.21.1 thrown-potion nether-portal duplication (the `isAlive()` hit check added in 1.21.2), plus the projectile behaviour duper machines rely on: 300-tick portal cooldown for projectiles, the 1.21.1 projectile tick order, the 1.21.1 honey-block slide, and a constant 0.3 entity-hit margin. By default only splash/lingering potions are affected. Requires Fabric API. Toggle with `/potiondupe on|off` (op level 2). Settings live in `config/potiondupe.json`.

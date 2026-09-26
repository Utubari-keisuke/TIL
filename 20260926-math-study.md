#  Poimandresが公開したWebゲーム・アニメーション向け数学エンジン「math」の設計思想

## 概要 (Summary)

React Three Fiber（R3F）、Zustand、React Springなどを手掛ける開発者集団Poimandres（@pmndrs）から、Webゲームやリッチアニメーション、3Dグラフィックス向けの新しいJavaScript／TypeScript数学ライブラリ「math」が公開された（`npm install math`）。

Three.jsやBabylon.jsといった特定の描画ライブラリ・フレームワークに縛られず、WebGL・WebGPU・WebAssembly環境や独自エンジンとの高い相互運用性を備えている。
最大の特徴は、毎フレームの計算でGC（ガベージコレクション）によるフレーム落ちを起こさない「アロケーションフリー（Allocation-free / ゼロ割り当て）」なデータ指向設計と、AIエージェントによるコード生成を前提としたエコシステム連携にある。

---

## 1. 「math」の主要な特徴と設計思想

| 特徴 | 詳細・アプローチ |
| :--- | :--- |
| **Allocation-free（ゼロアロケーション）** | 計算の戻り値として新規インスタンスを生成せず、呼び出し元が渡した配列（バッファ）に直接結果を書き込む設計。GCによるカクつき（Stutter）を徹底排除。 |
| **Monomorphic（単態性）** | 独自の複雑なクラスを定義せず、プレーンな配列リテラル（`[x, y, z]` 等）をそのまま型として扱うことで、V8などのJSエンジンによる最適化（インライン化）を最大化。 |
| **Tree-shakeable & Tiny** | ドメインごとにサブパスエントリー（`math/noise`, `math/shapes`, `math/ik` など）に分割されており、必要な関数だけをバンドル可能。 |
| **Render-agnostic（描画非依存）** | Three.js、Pixi.js、WebGPUネイティブコード、カスタムWasmパイプラインなど、あらゆるレンダラー間で境界なく共通の計算基盤として機能。 |
| **AI Coding Assistant Ready** | AIコーディング支援ツール（Claude Code、Cursor、Copilot等）向けに、最適化された使い方を学習させるSkill（`npx skills add pmndrs/math --skill math`）が公式提供。 |

---

## 2. コード例とAPIの使い方

### 基本構文（アロケーションフリーな計算）

```typescript
import { type Vec3, vec3 } from 'math';

// 独自クラスのインスタンスではなく、プレーンな配列リテラル
const a: Vec3 = [1, 2, 3];
const b = vec3.fromValues(4, 5, 6);

// 計算結果を格納するバッファを事前に用意（再利用可能）
const out = vec3.create(); // [0, 0, 0]
```



// 第一引数に出力先（out）を渡すことで、計算時にメモリ割り当てを発生させない
vec3.add(out, a, b);       // out = [5, 7, 9]
vec3.normalize(out, out);  // 自己代入（エイリアシング）も安全

### 豊富なサブモジュール構成

- **`math`**: ベクトル（vec2/3/4）、クォータニオン、オイラー角、行列（mat2/3/4）、各種イージング・補間（lerp, remap, slerp）
- **`math/shapes`**: 形状プリミティブ（box, sphere, plane, obb3）および空間衝突クエリ（raycast, frustum）
- **`math/geometry`**: 凸包計算（quickhull）、多角形分割・三角形分割（triangulatePolygon）
- **`math/noise`**: Perlin / Simplexノイズ（手続き型テクスチャ・地形生成向け）
- **`math/ik`**: インバースキネマティクス（FABRIKアルゴリズムによる関節制御）
- **`math/time`**: スプリング物理（react-spring由来の物理計算）

---

## 3. なぜ今「共通の軽量数学エンジン」が必要なのか？

### 1. WebGPU時代のデータ指向への回帰
WebGPUやコンピュートシェーダーを扱う際、重厚なクラスインスタンスよりも、TypedArrayやフラットな数値配列のままデータを直接扱える方がバッファ転送（`queue.writeBuffer`）において圧倒的にオーバーヘッドが少ないため。

### 2. gl-matrixの後継・モダンTypeScript化
長年Web 3D界隈で重宝されてきた「gl-matrix」の設計（高速だが古く、型定義や周辺ツールが散逸していた）を、現代的なTypeScriptの型システムとエコシステムで完全に再定義した形になっている。

### 3. AI協調プログラミングへの適応
フラットで推論しやすい関数型APIと、明確なシグネチャを持ったドキュメント構造（`API.md`）により、LLMが誤ったインスタンス生成コードを出力しにくい工夫がなされている。

---

## 4. 関連記事・リポジトリ

> [!NOTE]
> 🔗 [Poimandres、Webゲームやアニメーション向けのJS／TSライブラリ「math」を公開 (gihyo.jp)](https://gihyo.jp/article/2026/09/pmndrs-math)  
> 🔗 [GitHub - pmndrs/math: The playful web's math engine](https://github.com/Utubari-keisuke/TIL/tree/main)

---

## 5. 学びと所感 (Takeaway)

- 毎フレーム60〜120fpsで呼び出されるアニメーションループ内では、「オブジェクトを新しく `new` しない」「引数の再利用（In-place mutation）」がいかにメモリ効率・CPUキャッシュ効率に直結するかを再認識した。
- 「ライブラリ専用のAI Skillを提供する」というアプローチが非常に現代的で、人間の開発者だけでなくAIエージェントが生成するコードの最適化までスコープに入れたOSS設計の先駆けとして非常に刺激的。

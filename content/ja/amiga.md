---
title: "Amiga テクニカルリファレンス — BFE001"
description: "Amigaのカスタムチップセット、メモリマップ、CIAレジスタの詳細 — $BFE001の裏にあるハードウェア。"
---

<!-- section: $DFF000 "チップセットアーキテクチャ" -->

Amigaの強さはカスタムチップセットにあった――3つの専用チップが
グラフィックス、サウンド、DMAをCPUから独立して処理していた。Motorola 68000が
コードを実行する一方で、カスタムチップがマシンを動かしていた。

<div class="chip-diagram">
    <div class="chip-box chip-cpu">
        <div class="chip-name">Motorola 68000</div>
        <div class="chip-detail">16ビットデータバス</div>
        <div class="chip-detail">24ビットアドレスバス</div>
    </div>
    <div class="chip-bus no-stub"></div>
    <div class="chip-row chip-connected">
        <div class="chip-box">
            <div class="chip-name">AGNUS</div>
            <div class="chip-detail">DMAコントローラ</div>
            <div class="chip-detail">Copper</div>
            <div class="chip-detail">Blitter</div>
            <div class="chip-detail">ビームカウンタ</div>
        </div>
        <div class="chip-box">
            <div class="chip-name">DENISE</div>
            <div class="chip-detail">映像出力</div>
            <div class="chip-detail">ビットプレーン</div>
            <div class="chip-detail">スプライト</div>
            <div class="chip-detail">衝突検出</div>
        </div>
        <div class="chip-box">
            <div class="chip-name">PAULA</div>
            <div class="chip-detail">4chオーディオ</div>
            <div class="chip-detail">ディスクI/O</div>
            <div class="chip-detail">シリアルI/O</div>
            <div class="chip-detail">割り込み</div>
        </div>
    </div>
    <div class="chip-bus no-stub"></div>
    <div class="chip-row chip-connected">
        <div class="chip-box chip-highlight">
            <div class="chip-addr">$BFE001</div>
            <div class="chip-name">CIA-A</div>
            <div class="chip-detail">キーボード</div>
            <div class="chip-detail">ゲームポート</div>
            <div class="chip-detail">LED / フィルタ</div>
            <div class="chip-detail">タイマー A/B</div>
        </div>
        <div class="chip-box">
            <div class="chip-addr">$BFD000</div>
            <div class="chip-name">CIA-B</div>
            <div class="chip-detail">パラレルポート</div>
            <div class="chip-detail">ディスク制御</div>
            <div class="chip-detail">シリアル制御</div>
            <div class="chip-detail">タイマー C/D</div>
        </div>
    </div>
</div>

**Agnus**（後にFat Agnus、さらにAlice）はカスタムチップセットの頭脳だった。
DMAを制御し――他のすべてのチップがCPUに負担をかけずにデータを
読み取れるようメモリアクセスを管理していた。Copper（映像ビームに同期して
ハードウェアレジスタを書き換えるプログラマブルコプロセッサ）とBlitter
（高速メモリコピー、線描画、塗りつぶし操作のためのハードウェアブロック転送
エンジン）を内蔵していた。

**Denise**（AGAチップセットではLisa）はすべての映像出力を担当していた：
ビットプレーングラフィックス、ハードウェアスプライト、衝突検出、カラーパレット。
Amigaはプレーナーグラフィックスモデルを採用していた――ピクセルをパックされた値として
格納するのではなく、ピクセルの色の各ビットが別々のビットプレーンから来ていた。
これによりスクロールや特定のエフェクトが非常に効率的だった。

**Paula**は4つの独立した8ビットオーディオチャンネル（ハードウェアボリュームと
周期制御付き）、ディスクドライブアクセス、シリアルI/O、割り込み管理を担当していた。
MOD音楽が機能したのは、PaulaのDMAチャンネルがチップRAMから波形サンプルを
連続的に読み取る一方でCPUが他の処理を行えたからだ――音楽は文字通り
自分自身で演奏していた。

**CIA 8520**（×2）――Complex Interface Adapter。CIA-Aは`$BFE001`に配置され、
キーボード、ゲームポートの発射ボタン、電源LED、オーディオローパスフィルタを
担当していた。CIA-Bは`$BFD000`に配置され、パラレルポート、シリアル制御線、
ディスクドライブ信号を担当していた。各CIAには2つの16ビットタイマーと
24ビット時刻カウンタが内蔵されていた。

<!-- section: $000000 "メモリマップ" -->

68000の24ビットアドレスバスは16 MBのアドレス空間を提供していた。すべての
ハードウェアには固有の場所があった。メモリアドレスへの書き込みは、マップ上の
位置に応じてデータの格納、グラフィックスの描画、サウンドの再生、
電源LEDの切り替えのいずれかを行った。

<div class="addr-map">
    <div class="addr-row">
        <span class="addr-label">$000000</span>
        <div class="addr-body">
            <div class="addr-name">チップRAM</div>
            <div class="addr-size">最大 2 MB</div>
            <div class="addr-comment">CPUとカスタムチップの共有; グラフィックス、オーディオ、ディスクデータ</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$200000</span>
        <div class="addr-body">
            <div class="addr-name">ファストRAM / Zorro II</div>
            <div class="addr-size">最大 8 MB</div>
            <div class="addr-comment">CPU専用、DMA競合なし</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$A00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">予約領域</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$BFD000</span>
        <div class="addr-body">
            <div class="addr-name">CIA-B レジスタ</div>
            <div class="addr-comment">パラレル、ディスク、シリアル制御</div>
        </div>
    </div>
    <div class="addr-row addr-highlight">
        <span class="addr-label">$BFE001</span>
        <div class="addr-body">
            <div class="addr-name">CIA-A レジスタ ★</div>
            <div class="addr-comment">キーボード、LED、フィルタ、ゲームポート</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$C00000</span>
        <div class="addr-body">
            <div class="addr-name">スローRAM</div>
            <div class="addr-size">512 KB（A500 トラップドア）</div>
            <div class="addr-comment">バス共有だがDMAアクセスなし</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$D00000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">予約 / 拡張</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$DFF000</span>
        <div class="addr-body">
            <div class="addr-name">カスタムチップレジスタ</div>
            <div class="addr-size">約256レジスタ</div>
            <div class="addr-comment">Agnus, Denise, Paula — ビットプレーン、スプライト、色、DMA</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$E80000</span>
        <div class="addr-body">
            <div class="addr-name addr-size">Autoconfig / Zorro II ID</div>
        </div>
    </div>
    <div class="addr-row">
        <span class="addr-label">$F80000</span>
        <div class="addr-body">
            <div class="addr-name">Kickstart ROM</div>
            <div class="addr-size">256–512 KB</div>
            <div class="addr-comment">exec.library, intuition, graphics — Amiga OSカーネル全体</div>
        </div>
    </div>
</div>

電源投入時、CIA-Aのオーバーレイビットがアドレス`$000000`にROMをマッピングし、
CPUがリセットベクタを読めるようにする。Kickstartが最初に行うことの一つが
このビットをクリアすることで、チップRAMを`$000000`に入れ替え、ROMを`$F80000`に
戻す。`exec.library`のジャンプテーブルアーキテクチャにより、ライブラリベースからの
固定された負のオフセットでOS関数を呼び出すことができた――何度も夜を費やして
探究した仕組みだ。

<!-- section: $BFE001 "CIA-Aレジスタ" -->

このサイトの名前の由来となったレジスタ。CIA-Aは`$BFE001`から始まる奇数
アドレスに配置されている（2つのCIAは奇数/偶数アドレスデコーディングを使って
同じ領域を競合なく共有している）。

<div class="bit-table">
    <div class="bit-header">
        <span class="bit-num">Bit</span>
        <span class="bit-dir">方向</span>
        <span class="bit-name">名前</span>
        <span class="bit-func">$BFE001 — PRA — ポートレジスタA</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">7</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/FIR1</span>
        <span class="bit-func">ゲームポート1 発射ボタン（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">6</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/FIR0</span>
        <span class="bit-func">ゲームポート0 発射ボタン（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">5</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/RDY</span>
        <span class="bit-func">ディスクレディ（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">4</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/TK0</span>
        <span class="bit-func">ディスクトラックゼロ（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">3</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/WPRO</span>
        <span class="bit-func">ディスク書き込み禁止（アクティブロー）</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">2</span>
        <span class="bit-dir">入力</span>
        <span class="bit-name">/CHNG</span>
        <span class="bit-func">ディスクチェンジ（アクティブロー）</span>
    </div>
    <div class="bit-row bit-highlight">
        <span class="bit-num">1</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">LED</span>
        <span class="bit-func">電源LED / オーディオローパスフィルタ</span>
    </div>
    <div class="bit-row">
        <span class="bit-num">0</span>
        <span class="bit-dir">出力</span>
        <span class="bit-name">OVL</span>
        <span class="bit-func">オーバーレイ — 1: ROMを$000000に, 0: RAM</span>
    </div>
</div>

ビット1が主役。ローにするとパワーLEDが点灯し、オーディオローパスフィルタが
有効になる。Amiga 500ではハードな切り替えだった。Amiga 2000以降では
LEDがディミングし――フィルタは約3.3 kHz以上の周波数を滑らかに減衰させた。
ゲームではトンネルを通過するような効果をシミュレートするために切り替えていた：
こもった音はソフトウェア処理ではなく、ハードウェアフィルタによるものだった。

命令`MOVE.b #$02,$BFE001`はビット1をセットし他のすべてをクリアする
――LEDを消灯し、フィルタを無効にする。他のビットに影響を与えずにLEDだけを
切り替えるには、レジスタを読み取り、ビット1をXORし、書き戻す：
`BCHG #1,$BFE001`。

<div class="register-block">
    <div class="reg-title">CIA-A その他のレジスタ</div>
    <div class="reg-row">
        <span class="reg-field">$BFE101</span>
        <span class="reg-val">PRB — ポートB: パラレルポートデータ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE201</span>
        <span class="reg-val">DDRA — データ方向レジスタA（1 = 出力, 0 = 入力）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE301</span>
        <span class="reg-val">DDRB — データ方向レジスタB</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE401</span>
        <span class="reg-val">TALO — タイマーA ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE501</span>
        <span class="reg-val">TAHI — タイマーA ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE601</span>
        <span class="reg-val">TBLO — タイマーB ローバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE701</span>
        <span class="reg-val">TBHI — タイマーB ハイバイト</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE801</span>
        <span class="reg-val">イベントカウンタ（TOD） — ローバイト（1/10秒）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFE901</span>
        <span class="reg-val">イベントカウンタ（TOD） — ミドルバイト（秒）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEA01</span>
        <span class="reg-val">イベントカウンタ（TOD） — ハイバイト（分）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFED01</span>
        <span class="reg-val">ICR — 割り込み制御レジスタ</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEE01</span>
        <span class="reg-val">CRA — 制御レジスタA（タイマーAモード、シリアルポート）</span>
    </div>
    <div class="reg-row">
        <span class="reg-field">$BFEF01</span>
        <span class="reg-val">CRB — 制御レジスタB（タイマーBモード、TODアラーム）</span>
    </div>
</div>

キーボードはCIA-Aのシリアルポート機構を通じて読み取られた。キー押下ごとに
8ビットのスキャンコードが生成され、KDAT線を介してシリアルに送信され、
KCLKでクロックされた。CIAがビットをシフトインし、1バイト受信完了時に
割り込みを発生させた。Amigaのキーボードは独自のマイクロコントローラを搭載し、
ハンドシェイクプロトコルを実行していた――ホストは次のキーコードが送信される前に
各キーコードを確認応答する必要があった。

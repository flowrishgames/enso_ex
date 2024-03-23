# enso_ex リカバリー

## リカバリー起動キーの組み合わせ

* `select`: sd2vita リカバリーを実行
* `start`: eMMC リカバリーを実行
* `start+circle`: eMMC リカバリーを無効化し、ブートオプションの切替を有効化
    * `+triangle`: ブートローダーと enso_ex を読み込み専用にする
    * `+dpad 上`: ブロック 1 ではなくブロック 3 から emuMBR を使用する
    * `+dpad 右`: os0 の初期化をスキップ
* `音量下`: bootmgr とカスタムカーネルローダーをスキップ
* `音量上`: カスタムカーネルローダーのカスタムプラグインをスキップ
* `PSTV では何も押さない`: sd2vita リカバリーを実行

## sd2VITA リカバリー

GC-SD リカバリーモードは、起動時に SELECT ボタンを押すことで起動します。このモードでは、enso_ex は sd2vita から最初のセクターを読み取り、内容に応じてそれぞれのリカバリー手順を実行します。

### RAW モード

* 最初の 0x10 バイトに特定の「マジック」値が含まれている場合、それは enso_ex がロードして実行する生のコードブロブのための リカバリーブロック構造: [https://github.com/SKGleba/enso_ex/blob/master/core/ex_defs.h#L38](https://github.com/SKGleba/enso_ex/blob/master/core/ex_defs.h#L38) として扱われます。

### FAT16 モード

* 最初のセクターが FAT16 パーティションの PBR である場合、enso_ex はそれをベースカーネルの `os0:` としてマウントします。

### SCE MBR モード

* 最初のセクターが SCE MBR である場合、enso_ex は EMMC MBR に基づいて sd2vita オフセットから `os0:` をマウントします。
* ベースカーネル用にのみ sd2vita からマウントされます。

### エラーの認識

* エラーが発生しなかった場合、またはデバイスが PSTV である場合、enso_ex はブートを続行します。
* sd2vita が読み込めなかった場合、ユーザーは TRIANGLE ボタンを押してブートを続ける必要があります。
* 不明なデータが見つかった場合、ユーザーは CIRCLE ボタンを押してブートを続ける必要があります。
* os0 のマウントまたはカスタムコードが失敗した場合、ユーザーは CROSS ボタンを押してブートを続ける必要があります。

#### 例

* heartbeat: recovery/external/heartbeat: KBL パラメータを使用して sd2vita の最初のセクターを上書きします。
* gui-usb-mount: recovery/external/gui-usb-mount: EMMCとそのパーティションを USB マウントできるメニュー
* enso_flash: recovery/external/enso_flash: sd2vita から enso_ex を更新します。

### eMMC リカバリー

EMMC リカバリーモードは、起動時に START ボタンを押すことで起動します。このモードでは、enso_ex は EMMC セクター 4 からデータをロードし、生のコードブロブとして実行します。

* `ux0:eex/recovery/rconfig.e2xp` にブロブを配置した後、enso_ex インストーラーの「同期」オプションを使用して更新できます。
* セクター 0x30+ にあるより大きなコードブロブのブートストラップとして使用することを目的としています。このブロブは、`ux0:eex/recovery/rblob.e2xp` に配置した後、enso_ex インストーラーの「同期」オプションを使用して更新できます。

### 例

* default: recovery/internal/default: 単に `os0:` を初期化する大きなブートストラップをロードします。


# enso_ex recovery
## Recovery key combinations
 - `select`: run sd2vita recovery
 - `start`: run emmc recovery
 - `start+circle`: disable emmc recovery, enable boot options toggle
   - `+triangle`: make the bootloaders and enso_ex read-only
   - `+dpad up`: use emuMBR from block 3 instead of block 1
   - `+dpad right`: skip os0 intialization
 - `volume down`: skip bootmgr and the custom kernel loader
 - `volume up`: skip custom kernel loader's custom plugins
 - `nothing on PSTV`: run sd2vita recovery
<br>
<br>

## SD2VITA recovery
GC-SD recovery mode is activated by holding SELECT at boot. In this mode enso_ex will read the first sector from sd2vita and depending on the contents follow their respective recovery routes.<br>
### RAW mode
 - If the first 0x10 bytes contain a specific *magic* value, then they are treated as a [recovery block structure](https://github.com/SKGleba/enso_ex/blob/master/core/ex_defs.h#L38) for enso_ex to load and execute a raw code blob<br>
### FAT16 mode
 - If the first sector is a fat16 partition PBR then enso_ex will mount it as `os0:` for the base kernel.<br>
### SCE MBR mode
 - If the first sector is a SCE MBR then enso_ex will mount `os0:` from sd2vita offset based on EMMC MBR.
 - It is mounted from sd2vita only for the base kernel.<br>
### Acknowledging errors
 - If no error occured, or the device is a PSTV, enso_ex will continue boot
 - If sd2vita could not be read, the user will need to press TRIANGLE to continue boot
 - If unknown data was found, the user will need to press CIRCLE to continue boot
 - If os0 mount or custom code failed, the user will need to press CROSS to continue boot <br>
### Examples
 - [heartbeat](recovery/external/heartbeat) - overwrites the first sd2vita sector with KBL param
 - [gui-usb-mount](recovery/external/gui-usb-mount) - menu that lets you USB-mount EMMC and its partitions
 - [enso_flash](recovery/external/enso_flash) - updates enso_ex from sd2vita
<br>
<br>

## EMMC recovery
EMMC recovery mode is activated by holding START at boot. In this mode enso_ex will load data from EMMC sector 4 and execute it as a raw code blob.<br>
 - It can be updated via the enso_ex installer's "Synchronize" option after placing the blob in `ux0:eex/recovery/rconfig.e2xp`.
 - It is intended to be used as a bootstrap for a bigger code blob located at sector 0x30+ that can be updated via the enso_ex installer's "Synchronize" option after placing the blob in `ux0:eex/recovery/rblob.e2xp`.<br>
### Examples
 - [default](recovery/internal/default) - loads a larger bootstrap that simply initializes `os0:`
<br>
<br>

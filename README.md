# enso_ex
### PlayStation Vita/TV(ファームウェア 3.65)向けの恒久的jailbreak とカスタムファームウェア(CFW)ローダー <br>
Untethered jailbreak and CFW loader for PlayStation Vita/TV units on firmware 3.65 <br>

## Features(特徴)
### カスタムカーネルローダー(Custom kernel loader)

このカーネルローダーは、vanillaの機能を再現し、加えてテキストファイルからカスタムモジュールリストを読み込むことをサポートします。<br>
カスタムモジュールはベースカーネルの後にロードされますが、ベースカーネルの前に開始されます - これにより、モジュールは'plugin'として機能し、素の状態でベースカーネルにパッチを当てることができます。<br>
また、ローダーは、enso_exからユーザーのカスタムモジュールにフック/パッチする「API」を渡します、 詳細はdeveloper readmeに。<br>

Provided is a kernel loader that replicates vanilla functionality with added support for a custom module list read from a text file.<br>
Custom modules are loaded after the base kernel, but started before the base kernel - this allows the modules to function as 'plugins' and patch the base kernel in a pristine state.<br>
The provided loader also passes a hooking/patching 'API' from enso_ex to the user's custom modules, detailed in the developer readme.<br>

### ベースカーネルの未署名モジュールをサポート
Support for unsigned base kernel modules

enso_exのカスタムカーネルローダーと組み合わせることで、ユーザーはベースカーネルに独自の *.skprx プラグインを追加できます。<br>
また、ベースカーネルモジュールを復号化された/未署名のものに置き換えることも可能です。 <br>
デフォルトでは、2つのプラグインが提供されています。"homebrew enabler"と"bootlogo replacer"です。これらの機能は後述のreadmeで詳細に説明されています。

In conjunction with enso_ex's custom kernel loader, this allows the user to add their own *.skprx plugins to the base kernel.<br>
It is also possible to outright replace base kernel modules with decrypted/unsigned alternatives. <br>
By default, provided are two plugins - a homebrew enabler and a bootlogo replacer, their functionality is detailed later in this readme.<br>

### ブートローダー・レベルでのコード実行
Code execution on the bootloader level

カーネルローダの前に、enso_ex は os0 パーティションから生コードblob をロードして実行しようとします。
これは enso_exの拡張として使用されることを目的としています。ファームウェアバージョン、ConsoleID、QA フラグ、セキュリティコプロセッサの動作などのコア情報や機能を変更など。

Before the kernel loader, enso_ex attempts to load and run a raw code blob from the os0 partition.<br>
This is intended to be used as an enso_ex extension that alters core information or functionality such as Firmware version, ConsoleID, QA flags, security coprocessor behavior, etc.<br>

### SD2VITAベースのリカバリー
SD2VITA-based recovery


ブートローダー・レベルのリカバリー機構も含まれています。<br>
トリガーされると、enso_exはsd2vitaを初期化し、emmcの置き換え、os0の置き換え、またはリカバリーコードブロブのソースとして使用する。<br>
この機能は、ファイルシステムの破損、パーティションのワイプ、アップデートの失敗、enso_exのバグなど、あらゆるものに対するセーフガードを提供します。
また、ハイブリッド・ファームウェアや「dual nand」のような、より高度な改造やいじりへの入り口です。

Included is a bootloader-level recovery mechanism.
When triggered, enso_ex will initialize and use the sd2vita as an emmc replacement, os0 replacement, or source of a recovery code blob.<br>
This feature provides a safeguard against any kind of filesystem corruption, partition wipes, update failures, enso_ex bugs, and much more.<br>
It also opens doors to more advanced mods and tinkering, such as hybrid firmware or 'dual nand'.<br>

### カーネルモジュールのロード／スタートのエラーは無視される
Kernel module load/start errors are ignored

enso_exは、いくつかのモジュールがロードや起動に失敗しても、ベースカーネルを "強制的に "起動させます。
この機能は、追加の復旧レイヤーを提供し、テスト用ファームウェアを一般販売向けユニットで起動させるように、さまざまな種類の純正ファームウェアを起動する機能をアンロックします。

enso_ex "forces" base kernel boot, even if some modules fail to load or start.<br>
This feature provides an additional recovery layer and unlocks the ability to boot vanilla firmwares of different types, such as testkit firmware on a retail unit.<br>

### その他起動オプション
Miscellaneous boot toggles

復元用 Readme には、特定のキーコンビネーションを押すことで起動する便利なトグル (オプション) がいくつか記載されています。
 - emuMBR: 通常の MBR (マスターブートレコード) ではなく、別のブロックを使用するようにします。
 - bootarea write-lock: MBR、ブートローダー、enso_ex への書き込みをブロックします。 (書き込み禁止にします)
 - EMMC recovery: EMMC からコードブロックを読み込んで実行します。
 - Adi-os0: os0 の初期化を無効にします。深刻な問題が発生した場合に役立ちます。

A few useful toggles, triggered by holding certain key combinations, are detailed in the recovery readme.
 - emuMBR: use a different block as MBR
 - bootarea write-lock: block writes to the MBR, bootloaders, and enso_ex
 - EMMC recovery: load and run a code blob from EMMC
 - Adi-os0: disable os0 init, useful in case of a serious misshap.
<br>


## インストールと設定
Installation and configuration

enso_ex インストーラーを含む VPK ファイルが提供されています。このインストーラーには以下のオプションがあります:

Provided is a VPK file containing the enso_ex installer, which has the following options:

### enso_ex インストール/再インストール
Install/reinstall the hack

このオプションを実行すると、以下の処理が行われます。

 - デバイスの種類に合わせた `boot_config.txt` ファイルが `ur0:tai/` ディレクトリに作成されます。
 - `ux0:eex/` ディレクトリ内に enso_ex のインストールに必要なファイルが準備されます。
 - enso_ex のプラグインが同期されます。
 - enso_ex のコア部分がインストールされます。
 - enso_ex の復元機能が更新されます。

This option will:
 - create a type-specific `boot_config.txt` in `ur0:tai/`
 - prepare the enso_ex installation in `ux0:eex/`
 - synchronize enso_ex plugins
 - install enso_ex core
 - update the enso_ex recovery

### ハックをアンインストールする
Uninstall the hack

このオプションはenso_ex coreをアンインストールし、`ur0:tai/boot_config.txt`を削除します。

This option will uninstall enso_ex core and remove `ur0:tai/boot_config.txt`

### ブート設定の修正
Fix boot configuration

このオプションは、`ur0:tai/` にタイプ固有の `boot_config.txt` を作成します。

This option will create a type-specific `boot_config.txt` in `ur0:tai/`

### enso_ex プラグインの同期
Synchronize enso_ex plugins

このオプションを実行すると、以下の処理が行われます。
 - 使用されなくなった拡張機能が削除されます。
 - `os0:ex/` ディレクトリが削除されます。(enso_ex 関連と思われる)
 - `ux0:eex/boot/*` ディレクトリ内のすべてのファイルが `os0:` ディレクトリにコピーされます。
   - ただし、`ux0:eex/boot/` ディレクトリ内に`e2x_ckldr.skprx`や`bootmgr.e2xp`ファイルが存在しない場合、それらのファイルは`os0:`ディレクトリからも削除されます。
 - `ux0:eex/custom/*` ディレクトリ内のすべてのファイルが`os0:ex/`ディレクトリにコピーされます。(おそらく enso_ex のカスタマイズ設定と思われる)

This option will:
 - remove deprecated extensions
 - remove `os0:ex/`
 - copy `ux0:eex/boot/*` to `os0:`
   - if `e2x_ckldr.skprx` or `bootmgr.e2xp` are not present in `ux0:eex/boot/`, they will be removed from `os0:`
 - copy `ux0:eex/custom/*` to `os0:ex/`
 
### enso_ex recoveryの更新
Update the enso_ex recovery

このオプションを実行すると、以下の処理が行われます。
 - 存在する場合、`ux0:eex/recovery/rconfig.e2xp`ファイルが EMMC のブロック 4 に書き込まれます。
 - 存在する場合、`ux0:eex/recovery/rblob.e2xp`ファイルが EMMC のブロック 0x30 以降に書き込まれます。(正確なブロック番号は不明)
 - 存在する場合、`ux0:eex/recovery/rmbr.bin`ファイルが EMMC のブロック 3 に書き込まれます。

This option will:
 - if exists, write `ux0:eex/recovery/rconfig.e2xp` to EMMC block 4
 - if exists, write `ux0:eex/recovery/rblob.e2xp` to EMMC block 0x30+
 - if exists, write `ux0:eex/recovery/rmbr.bin` to EMMC block 3
<br>


## ベースカーネル プラグイン
Base kernel plugins

カスタムベースカーネルプラグインを追加するには、`ux0:ex/custom/` に置き、`ux0:ex/custom/boot_list.txt` に追加し、enso_ex インストーラから "Synchronize" してください。<br> 
デフォルトでは以下のプラグインがインストールされます。

To add a custom base kernel plugin put it in `ux0:eex/custom/`, add it to the `ux0:eex/custom/boot_list.txt` and "Synchronize" via the enso_ex installer.<br>
By default, enso_ex installer installs the following plugins:

### e2xhencfg.skprx

- **未署名カーネルモジュールのサポート**
- **起動設定ファイルのリダイレクト**: システムブート時のファイル読み込みを以下のように変更します。
  - 通常起動時: `os0:psp2config_%model%.skprx` ファイルから `ur0:tai/boot_config.txt` ファイルにリダイレクトします。
  - セーフモード時: リダイレクトを行わず、`os0:psp2config_%model%.skprx` ファイルを読み込みます。
  - □ボタンを押しながら起動時: `ux0:eex/boot_config.txt` ファイルを読み込みます (セーフモード時も有効)。
  - devkitのPSTVモード時: `ur0:tai/boot_config_kitv.txt` または `ux0:eex/boot_config_kitv.txt` ファイルを読み込みます。

 - Adds support for unsigned kernel modules
 - Redirects `os0:psp2config_%model%.skprx` to `ur0:tai/boot_config.txt`
   - if in safe mode, the default redirect is skipped
   - if SQUARE is held, `ux0:eex/boot_config.txt` is used (also works in safe mode)
   - on devkits in PSTV mode, `ur0:tai/boot_config_kitv.txt` or `ux0:eex/boot_config_kitv.txt` is used

### e2xculogo.skprx

- **カスタムブートロゴ**: デフォルトの PlayStation ブートロゴが `os0:ex/bootlogo.raw` ファイルに置き換えられるようになりました。
- **ロゴの形式**: RGBA32 フォーマット、960x544 ピクセル
- **ロゴの有無**: ファイルが見つからない場合、ブートロゴは表示されません。
- **セーフモード**: セーフモード時にはカスタムロゴは無効になります。

 - replaces the default PlayStation boot logo with `os0:ex/bootlogo.raw`
   - format is RGBA32 960x544
   - if no logo found, no logo will be displayed
   - disabled in safe mode
<br>


## Advanced usage
 - [Using the built-in recovery mechanism](README-recovery.md)
 - [For developers](README-dev.md)
 - Core flow: [light](enso_ex-core-diagram.png) / [dark](enso_ex-core-diagram-dark.png)
<br>


## FAQ
### How does the jailbreak work?
 - Check out [xyz's writeup](https://blog.xyz.is/2018/enso.html) and [yifanlu's writeup](https://yifan.lu/2017/07/31/henkaku-enso-bootloader-hack-for-vita/)

### How to change, remove or restore the bootlogo?
 - To change the bootlogo, put the new image in `ux0:eex/custom/bootlogo.raw` and "Synchronize" via the enso_ex installer
 - To remove the bootlogo, remove `ux0:eex/custom/bootlogo.raw` and "Synchronize" via the enso_ex installer
 - To restore stock bootlogo, remove `ux0:eex/custom/e2xculogo.skprx` and "Synchronize" via the enso_ex installer

### How to uninstall enso_ex?
 - enso_ex can be uninstalled via the provided installer.
 - As a safety measure, enso_ex is also disabled (but not removed) on system update.

### How to update enso_ex?
 - Using the "Install/reinstall" installer option will update enso_ex
<br>


## Credits
 - Team molecule for taihenkaku and enso.
 - xerpi for his work on vita-libbaremetal.
 - Henkaku wiki contributors.
 - Everyone that helped me with this project over the years.

# Patch Noto CJK for Android
Patch Noto CJK & Source Han for Android by GitHub Action.

# GitHub Actions Usage
1. [Fork this repo](https://docs.github.com/en/get-started/quickstart/fork-a-repo#forking-a-repository) **without** option 'copy the `main` branch only'.

2. [Create a branch](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-and-deleting-branches-within-your-repository) based on `script`.

3. [Set new branch as default](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/changing-the-default-branch).

4. Add [two variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository) for GitHub Actions:

- `FONT_DIR`: `noto/serif/2.002` (The directory you want)
- `FONT_LINKS`: `https://github.com/notofonts/noto-cjk/raw/Serif2.002/Serif/Variable/OTC/NotoSerifCJK-VF.otf.ttc https://github.com/notofonts/noto-cjk/raw/Serif2.002/Serif/OTC/NotoSerifCJK-Regular.ttc` (Split multiple links by space)

5. [Trigger GitHub Actions by manual](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow).

# What it does
- Add chws, vchw, halt and vhal by tool [East Asian Contextual Spacing](https://github.com/kojiishi/east_asian_spacing#opentype-font-features). (For more, see README in tool)

- Remove codepoints of Emoji to display Emoji by Noto Emoji and Latin characters to display Latin characters by Roboto. ([subset_noto_cjk.py](https://cs.android.com/android/platform/superproject/main/+/main:external/noto-fonts/scripts/subset_noto_cjk.py;drc=d8536c6637d9506ccd8ffb09810ceb7e864e20d8) from AOSP)

# Android Usage

## `/system/etc/fonts.xml`

### TrueType Collection (.ttc) Font

You will see many <font-name>.ttc-<index-number>.ttx with font. For example:

```bash
$ cat noto/serif/2.002/NotoSerifCJK-Regular.ttc-2.ttx
```
```xml
<!-- some content -->
  <name>
    <!-- some content -->
    <namerecord nameID="1" platformID="3" platEncID="1" langID="0x409">
      Noto Serif CJK SC
    </namerecord>
    <namerecord nameID="2" platformID="3" platEncID="1" langID="0x409">
      Regular
    </namerecord>
<!-- some content -->
  <OS_2>
    <!-- some content -->
    <usWeightClass value="400"/>
<!-- some content -->
```

```bash
$ cat noto/serif/2.002/NotoSerifCJK-Regular.ttc-3.ttx
```
```xml
<!-- some content -->
  <name>
    <namerecord nameID="1" platformID="3" platEncID="1" langID="0x404">
      Noto Serif CJK TC
    </namerecord>
    <namerecord nameID="2" platformID="3" platEncID="1" langID="0x404">
      Regular
    </namerecord>
<!-- some content -->
  <OS_2>
    <!-- some content -->
    <usWeightClass value="400"/>
<!-- some content -->
```

It means:

- `zh-Hans` is at index `2` of `NotoSerifCJK-Regular.ttc`. The `weight` of `Regular` is `400`.
- `zh-Hant` is at index `3` of `NotoSerifCJK-Regular.ttc`. The `weight` of `Regular` is `400`.

So in `fonts.xml`:

```xml
<family lang="zh-Hans">
    <!-- some content -->
    <font weight="400" style="normal" index="2" fallbackFor="serif"
          postScriptName="NotoSerifCJKjp-Regular">NotoSerifCJK-Regular.ttc
    </font>
    <!-- some content -->
</family>
<family lang="zh-Hant,zh-Bopo">
    <!-- some content -->
    <font weight="400" style="normal" index="3" fallbackFor="serif"
          postScriptName="NotoSerifCJKjp-Regular">NotoSerifCJK-Regular.ttc
    </font>
    <!-- some content -->
</family>
```

Notice:
- `lang="zh-Hans"`
- `index="3"`
- `weight="400"`
- `NotoSerifCJK-Regular.ttc`

### Variable Font
See `fvar` table:

```bash
$ cat noto/serif/2.002/NotoSerifCJK-VF.otf.ttc-2.ttx
```
```xml
<!-- some content -->
  <fvar>

    <!-- some content -->

    <!-- ExtraLight -->
    <!-- PostScript: NotoSerifCJKsc-ExtraLight -->
    <NamedInstance flags="0x0" postscriptNameID="267" subfamilyNameID="266">
      <coord axis="wght" value="200.0"/>
    </NamedInstance>

    <!-- Light -->
    <!-- PostScript: NotoSerifCJKsc-Light -->
    <NamedInstance flags="0x0" postscriptNameID="269" subfamilyNameID="268">
      <coord axis="wght" value="300.0"/>
    </NamedInstance>
<!-- some content -->
```

`fonts.xml`:

```xml
<family lang="zh-Hans">
    <!-- some content -->
    <font weight="200" style="normal" fallbackFor="serif" postScriptName="NotoSerifCJKjp-ExtraLight">
        NotoSerifCJK-VF.otf.ttc
        <axis tag="wght" stylevalue="200.0"/>
    </font>
    <font weight="300" style="normal" fallbackFor="serif" postScriptName="NotoSerifCJKjp-ExtraLight">
        NotoSerifCJK-VF.otf.ttc
        <axis tag="wght" stylevalue="300.0"/>
    </font>
    <!-- some content -->
</family>
```

### `fallbackFor`

Add only for non-Latin serif fonts.

### `postScriptName`

> The PostScript name tagged in fonts.xml is used for an identifier of updating system fonts. To identify the TTC font file, uses the first font as a reference.
> ...

[`postScriptName` of Simplified Chinese, Traditional Chinese and Korean is wrong [204644136] - Visible to Public - Issue Tracker](https://issuetracker.google.com/issues/204644136)

## Test
- For testing chws, use [Variable-Font-Test](https://github.com/WordlessEcho/Variable-Font-Test)
- For testing codepoints removal, input any symbol from [subset_fonts.py](https://github.com/WordlessEcho/patch-noto-cjk-for-android/blob/af2c6a07e479555dfcb7b95526f9e1a04583e6ce/scripts/subset_fonts.py#L35-L77)

# Notes
You will always get new SHA256 everytime you run this script, even if the sources doesn't change. I guess afdko should be blamed?

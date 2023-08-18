# Patch Noto CJK for Android
Patch Noto CJK & Source Han for Android by GitHub Action.

# Usage
1. Fork this repo then add [two variables](https://docs.github.com/en/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository)

- `FONT_DIR`: `noto/serif/2.002` (The directory you want)
- `FONT_LINKS`: `https://github.com/notofonts/noto-cjk/raw/Serif2.002/Serif/Variable/OTC/NotoSerifCJK-VF.otf.ttc https://github.com/notofonts/noto-cjk/raw/Serif2.002/Serif/OTC/NotoSerifCJK-Regular.ttc` (Split multiple links by space)

2. [Trigger GitHub Actions by manual](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow).

# How it works
- Remove codepoints of Emoji to display Emoji by Noto Emoji and Latin characters to display Latin characters by Roboto. ([subset_noto_cjk.py](https://cs.android.com/android/platform/superproject/main/+/main:external/noto-fonts/scripts/subset_noto_cjk.py;drc=d8536c6637d9506ccd8ffb09810ceb7e864e20d8) from AOSP)

- Add chws, vchw, halt and vhal by tool [East Asian Contextual Spacing](https://github.com/kojiishi/east_asian_spacing#opentype-font-features). (For more, see README in tool)

# Notes
You will always get new SHA256 everytime you run this script. Even if the sources doesn't change. I guess afdko should be blamed?

# Faudio - git version, multilib with userpatches support - xWMA support enabled

https://github.com/FNA-XNA/FAudio

This package makes the native libs required for Wine's Faudio support, not external dlls to inject.

### You can use your own faudio patches by putting them in the same folder as the PKGBUILD and giving them the .myfaudiopatch extension.

### You can also revert faudio patches by putting them in the same folder as the PKGBUILD and giving them the .myfaudiorevert extension.


## Building the package:

```
git clone https://github.com/Frogging-Family/faudio-git.git
cd faudio-git
makepkg -si
```

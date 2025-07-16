# qt-openwrt
QT5/6 for Openwrt 24.10
=========================

Installation instructions
-------------------------

1. Add feeds in feeds.conf


```
src-git libqt https://github.com/coolshou/qt-openwrt.git
```

2. Update & install feeds

```
./scripts/feeds update -a 
./scripts/feeds install -a
# uninstall package
./scripts/feeds uninstall <package name>
```

3. Now you can find QT libs in Libraries->Qt5
4. config & select package Libraries->Qt5 ...
```
make menuconfig
```
Deps: libiconv, libmariaclient

5. install depense?
```
```
6. make package
```
make package/qt5/clean
make package/qt5/compile V=s 
```
ipk will be in bin\packages\<arch>\
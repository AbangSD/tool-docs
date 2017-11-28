#Building Lantern



## Ubuntu

```sh
tar -zxvf go1.9.linux-amd64.tar.gz -C /usr/local
sudo vim /etc/profile
# go
export PATH=$PATH:/usr/local/go/bin:/Users/abang/go/bin
export GOROOT=/usr/local/go
export GOPATH=your-go-path
```

```sh
sudo apt install git npm libappindicator3-dev libgtk-3-dev libappindicator3-1 make libc6-dev-i386
```

```sh
npm config set registry https://registry.npm.taobao.org
npm config get registry
npm i gulp-cli -g
```

```sh
git clone git@github.com:getlantern/lantern.git
cd lantern
make lantern
./lantern
```

## Make Error

### ONE

```sh
# github.com/getlantern/flashlight/proxied
src/github.com/getlantern/flashlight/proxied/proxied.go:391:5: tr.MaxIdleTime undefined (type *http.Transport has no field or method MaxIdleTime)
src/github.com/getlantern/flashlight/proxied/proxied.go:392:5: tr.EnforceMaxIdleTime undefined (type *http.Transport has no field or method EnforceMaxIdleTime)
```

**./src/github.com/getlantern/flashlight/proxied/proxied.go**

**line390 to line393**

change 

```go

if persistent {
		tr.MaxIdleTime = 30 * time.Second
		tr.EnforceMaxIdleTime()
	}
```

to

```go
	if persistent {
		tr.IdleConnTimeout = 30 * time.Second
	}
```

### TWO

```sh
# github.com/getlantern/flashlight/client
src/github.com/getlantern/flashlight/client/reverseproxy.go:20:14: unknown field 'MaxIdleTime' in struct literal of type http.Transport
src/github.com/getlantern/flashlight/client/reverseproxy.go:22:11: transport.EnforceMaxIdleTime undefined (type *http.Transport has no field or method EnforceMaxIdleTime)
```

**./src/github.com/getlantern/flashlight/client/reverseproxy.go**

**line18 to line22**

change

```go
	transport := &http.Transport{
		TLSHandshakeTimeout: 40 * time.Second,
		MaxIdleTime:         30 * time.Second,
	}
	transport.EnforceMaxIdleTime()
```

to

```go
	transport := &http.Transport{
		TLSHandshakeTimeout: 40 * time.Second,
		IdleConnTimeout:         30 * time.Second,
	}
	// transport.EnforceMaxIdleTime()
```

### THREE

```
# github.com/getlantern/systray
systray_linux.c: In function ‘nativeLoop’:
systray_linux.c:37:2: warning: ‘return’ with no value, in function returning non-void
  return;
  ^~~~~~
systray_linux.c:27:5: note: declared here
 int nativeLoop(void) {
     ^~~~~~~~~~
```

**./src/github.com/getlantern/systray/systray_linux.c**

**line37**

change

```c
	return;
```

to

```c
	return 1;
```


## Fedora

Install gtk3-devel-3.22.16-1.fc26.x86_64.rpm and libappindicator-gtk3-devel-12.10.0-14.fc26.x86_64.rpm first, and do the same works when on Ubuntu.



## Mac

```sh
brew install go
brew install git
```

```sh
npm config set registry https://registry.npm.taobao.org
npm config get registry
npm i gulp-cli -g
```

```sh
git clone git@github.com:getlantern/lantern.git
cd lantern
make lantern
./lantern
```

## Make Error

### ONE

```sh
# github.com/getlantern/flashlight/proxied
src/github.com/getlantern/flashlight/proxied/proxied.go:391:5: tr.MaxIdleTime undefined (type *http.Transport has no field or method MaxIdleTime)
src/github.com/getlantern/flashlight/proxied/proxied.go:392:5: tr.EnforceMaxIdleTime undefined (type *http.Transport has no field or method EnforceMaxIdleTime)
```

**./src/github.com/getlantern/flashlight/proxied/proxied.go**

**line390 to line393**

change 

```go
if persistent {
		tr.MaxIdleTime = 30 * time.Second
		tr.EnforceMaxIdleTime()
	}
```

to

```go
	if persistent {
		tr.IdleConnTimeout = 30 * time.Second
	}
```

### TWO

```sh
# github.com/getlantern/flashlight/client
src/github.com/getlantern/flashlight/client/reverseproxy.go:20:14: unknown field 'MaxIdleTime' in struct literal of type http.Transport
src/github.com/getlantern/flashlight/client/reverseproxy.go:22:11: transport.EnforceMaxIdleTime undefined (type *http.Transport has no field or method EnforceMaxIdleTime)
```

**./src/github.com/getlantern/flashlight/client/reverseproxy.go**

**line18 to line22**

change

```go
	transport := &http.Transport{
		TLSHandshakeTimeout: 40 * time.Second,
		MaxIdleTime:         30 * time.Second,
	}
	transport.EnforceMaxIdleTime()
```

to

```go
	transport := &http.Transport{
		TLSHandshakeTimeout: 40 * time.Second,
		IdleConnTimeout:         30 * time.Second,
	}
	// transport.EnforceMaxIdleTime()
```



## Windows

**I do these on ubuntu, but you can also do these on Windows.**

**shell**

```sh
cd /home/abang/GolandProjects
git clone git@github.com:getlantern/lantern.git
docker pull daocloud.io/ilanyu/lantern-build:master-48a1417
docker run -it -v /home/abang/GolandProjects/lantern:/lantern daocloud.io/ilanyu/lantern-build:master-48a1417 bash
```

**docker**

```docker
cd ../lantern
npm config set registry https://registry.npm.taobao.org
make generate-windows-icon
VERSION=9.9.9
export VERSION
make package-windows
```

## Change Makefile

### ONE

**line 296 to line 312**

change 

```go
package-windows: $(BNS_CERT) require-version windows
    @if [[ -z "$$BNS_CERT_PASS" ]]; then echo "BNS_CERT_PASS environment value is required."; exit 1; fi && \
    INSTALLER_RESOURCES="installer-resources/windows" && \
    rm -f $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    rm -f $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    cp installer-resources/$(PACKAGED_YAML) $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    cp $(LANTERN_YAML_PATH) $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    osslsigncode sign -pkcs12 "$(BNS_CERT)" -pass "$$BNS_CERT_PASS" -n "Lantern" -t http://timestamp.verisign.com/scripts/timstamp.dll -in "lantern_windows_386.exe" -out "$$INSTALLER_RESOURCES/lantern.exe" && \
    cat $$INSTALLER_RESOURCES/lantern.exe | bzip2 > update_windows_386.bz2 && \
    ls -l lantern_windows_386.exe update_windows_386.bz2 && \
    makensis -V1 -DVERSION=$$VERSION installer-resources/windows/lantern.nsi && \
    osslsigncode sign -pkcs12 "$(BNS_CERT)" -pass "$$BNS_CERT_PASS" -n "Lantern" -t http://timestamp.verisign.com/scripts/timstamp.dll -in "$$INSTALLER_RESOURCES/lantern-installer-unsigned.exe" -out "lantern-installer.exe" && \
    cp installer-resources/$(MANOTO_YAML) $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    cp $(LANTERN_YAML_PATH) $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    makensis -V1 -DVERSION=$$VERSION installer-resources/windows/lantern.nsi && \
    osslsigncode sign -pkcs12 "$(BNS_CERT)" -pass "$$BNS_CERT_PASS" -n "Lantern" -t http://timestamp.verisign.com/scripts/timstamp.dll -in "$$INSTALLER_RESOURCES/lantern-installer-unsigned.exe" -out "lantern-installer-manoto.exe" && \
    echo "-> lantern-installer.exe and lantern-installer-manoto.exe"
```

to

```go
package-windows: require-version windows
    INSTALLER_RESOURCES="installer-resources/windows" && \
    rm -f $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    rm -f $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    cp installer-resources/$(PACKAGED_YAML) $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    cp $(LANTERN_YAML_PATH) $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    cp "lantern_windows_386.exe" "$$INSTALLER_RESOURCES/lantern.exe" && \
    cat $$INSTALLER_RESOURCES/lantern.exe | bzip2 > update_windows_386.bz2 && \
    ls -l lantern_windows_386.exe update_windows_386.bz2 && \
    makensis -V1 -DVERSION=$$VERSION installer-resources/windows/lantern.nsi && \
    cp "$$INSTALLER_RESOURCES/lantern-installer-unsigned.exe" "lantern-installer.exe" && \
    cp installer-resources/$(MANOTO_YAML) $$INSTALLER_RESOURCES/$(PACKAGED_YAML) && \
    cp $(LANTERN_YAML_PATH) $$INSTALLER_RESOURCES/$(LANTERN_YAML) && \
    makensis -V1 -DVERSION=$$VERSION installer-resources/windows/lantern.nsi && \
    cp "$$INSTALLER_RESOURCES/lantern-installer-unsigned.exe" "lantern-installer-manoto.exe" && \
    echo "-> lantern-installer.exe and lantern-installer-manoto.exe"
```

### TWO

**line 49 to line 51**

change

```go
else
BNS_CERT := "../secrets/bns.pfx"
DOCKER_VOLS = "-v $$PWD:/lantern $(SECRETS_VOL)"
```

to

```go
// else
// BNS_CERT := "../secrets/bns.pfx"
// DOCKER_VOLS = "-v $$PWD:/lantern $(SECRETS_VOL)"
```
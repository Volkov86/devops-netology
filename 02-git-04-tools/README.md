##               Домашнее задание к занятию «2.4. Инструменты Git» <br/> <br/> <br/>
				   
				   

**1.** Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.

```shell
git show aefea
aefead2207ef7e2aa5dc81a34aedf0cad4c32545    Update CHANGELOG.md
```
<br/>

**2.** Какому тегу соответствует коммит 85024d3?

```shell
git show 85024d3
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)
````
<br/>

**3.** Сколько родителей у коммита b8d720? Напишите их хеши.

```shell
git show -s --format=%p b8d720
56cd7859e 9ea88f22f
или 
git show -s --format=%p b8d720 | wc -w
или
git show --pretty=format:' %P' b8d720
```
<br/> 

**4.** Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.

`git log  v0.12.23..v0.12.24  --oneline` <br/>
```shell
33ff1c03b (tag: v0.12.24) v0.12.24
b14b74c49 [Website] vmc provider links
3f235065b Update CHANGELOG.md
6ae64e247 registry: Fix panic when server is unreachable
5c619ca1b website: Remove links to the getting started guides old location 
06275647e Update CHANGELOG.md
d5f9411f5 command: Fix bug when using terraform login on Windows
4b6d06cc5 Update CHANGELOG.md
dd01a3507 Update CHANGELOG.md
225466bc3 Cleanup after v0.12.23 release
```
или

`git log --pretty=%H v0.12.23...v0.12.24`
```shell
33ff1c03bb960b332be3af2e333462dde88b279e
b14b74c4939dcab573326f4e3ee2a62e23e12f89
3f235065b9347a758efadc92295b540ee0a5e26e
6ae64e247b332925b872447e9ce869657281c2bf
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353
06275647e2b53d97d4f0a19a0fec11f6d69820b5
d5f9411f5108260320064349b757f55c09bc4b80
4b6d06cc5dcb78af637bbb19c198faff37a066ed
dd01a35078f040ca984cdd349f18d0b67e486c35 
225466bc3e5f35baa5d07197bbc079345b77525e 
```
<br/>

**5.** Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).

```shell
git log -S'func providerSource' --oneline
5af1e6234 main: Honor explicit provider_installation CLI config when present
8c928e835 main: Consult local directories as potential mirrors of providers
```
<br/>

**6.** Найдите все коммиты в которых была изменена функция globalPluginDirs.

```shell
git log -S globalPluginDirs
или
git log -S globalPluginDirs --oneline
35a058fb3 main: configure credentials from the CLI config file
c0b176109 prevent log output during init
8364383c3 Push plugin discovery down into command package
```
**7.**
```shell
git log -S synchronizedWriters --pretty=%an
или
git log -S'func synchronizedWriters' --pretty=format:'%h - %an %ae'
bdfea50cc - James Bardin j.bardin@gmail.com
5ac311e2a - Martin Atkins mart@degeneration.co.uk
```

<br/><br/>
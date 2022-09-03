# Домашнее задание к занятию «2.4. Инструменты Git»

Для выполнения заданий в этом разделе давайте склонируем репозиторий с исходным кодом 
терраформа https://github.com/hashicorp/terraform 

В виде результата напишите текстом ответы на вопросы и каким образом эти ответы были получены. 

1. Найдите полный хеш и комментарий коммита, хеш которого начинается на `aefea`.  

---  
Решение:  

Полный хеш: `aefead2207ef7e2aa5dc81a34aedf0cad4c32545`  
Комментарий: `Update CHANGELOG.md`  
Для получения информации воспользуемся следующей командой:
```
terraform % git show aefea
commit aefead2207ef7e2aa5dc81a34aedf0cad4c32545
Author: Alisdair McDiarmid <alisdair@users.noreply.github.com>
Date:   Thu Jun 18 10:29:58 2020 -0400

    Update CHANGELOG.md

```

---

2. Какому тегу соответствует коммит `85024d3`?  

---  
Решение:  

Коммит `85024d3` соответствует тегу `v0.12.23`:
```
terraform % git show 85024d3
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)
Author: tf-release-bot <terraform@hashicorp.com>
Date:   Thu Mar 5 20:56:10 2020 +0000

    v0.12.23


```

---

3. Сколько родителей у коммита `b8d720`? Напишите их хеши.  

---  
Решение:  

У коммита `b8d720` два родителя:
* `56cd7859e05c36c06b56d013b55a252d0bb7e158`
* `9ea88f22fc6269854151c571162c5bcf958bee2b`  
  
Получить их можно такой командой:
```
terraform % git show --pretty=format:'%P' b8d720
56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b
```

---

4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами  v0.12.23 и v0.12.24.  

---  
Решение:  

* b14b74c493 [Website] vmc provider links 
* 3f235065b9 Update CHANGELOG.md 
* 6ae64e247b registry: Fix panic when server is unreachable 
* 5c619ca1ba website: Remove links to the getting started guide's old location 
* 06275647e2 Update CHANGELOG.md 
* d5f9411f51 command: Fix bug when using terraform login on Windows 
* 4b6d06cc5d Update CHANGELOG.md 
* dd01a35078 Update CHANGELOG.md 
* 225466bc3e Cleanup after v0.12.23 release

```
terraform % git log --oneline v0.12.23^..v0.12.24
33ff1c03bb (tag: v0.12.24) v0.12.24
b14b74c493 [Website] vmc provider links
3f235065b9 Update CHANGELOG.md
6ae64e247b registry: Fix panic when server is unreachable
5c619ca1ba website: Remove links to the getting started guide's old location
06275647e2 Update CHANGELOG.md
d5f9411f51 command: Fix bug when using terraform login on Windows
4b6d06cc5d Update CHANGELOG.md
dd01a35078 Update CHANGELOG.md
225466bc3e Cleanup after v0.12.23 release
85024d3100 (tag: v0.12.23) v0.12.23
```

---

5. Найдите коммит в котором была создана функция `func providerSource`, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).  

---  
Решение:  

Функция `func providerSource` была создана в коммите `8c928e8358`
```
terraform % git log -S "func providerSource" --oneline
5af1e6234a main: Honor explicit provider_installation CLI config when present
8c928e8358 main: Consult local directories as potential mirrors of providers
```
  
Можно убедиться, что эта функция была добавлена такой командой:
`git show 8c928e8358`

---

6. Найдите все коммиты в которых была изменена функция `globalPluginDirs`.  

---  
Решение:  

Для начала найдём файл, в котором определена функция `globalPluginDirs`:
```
terraform % git grep --heading globalPluginDirs
commands.go
                GlobalPluginDirs: globalPluginDirs(),
        helperPlugins := pluginDiscovery.FindPlugins("credentials", globalPluginDirs())
internal/command/cliconfig/config_unix.go
                // FIXME: homeDir gets called from globalPluginDirs during init, before
plugins.go
// globalPluginDirs returns directories that should be searched for
func globalPluginDirs() []string {
```
  
Зная, что функция определена в файле `plugins.go`, найдём все коммиты, когда тело функции было изменено:
```
git log --oneline -L :globalPluginDirs:plugins.go
78b1220558 Remove config.go and update things using its aliases
...
52dbf94834 keep .terraform.d/plugins for discovery
...
41ab0aef7a Add missing OS_ARCH dir to global plugin paths
...
66ebff90cd move some more plugin search path logic to command
...
8364383c35 Push plugin discovery down into command package
```
Ответ:
* 78b1220558 Remove config.go and update things using its aliases
* 52dbf94834 keep .terraform.d/plugins for discovery
* 41ab0aef7a Add missing OS_ARCH dir to global plugin paths
* 66ebff90cd move some more plugin search path logic to command
* 8364383c35 Push plugin discovery down into command package

---

7. Кто автор функции `synchronizedWriters`?  

---  
Решение:  

Автором функции является автор коммита, в котором эта функция была впервые создана - Martin Atkins.

```
terraform % git log -S synchronizedWriters --oneline
bdfea50cc8 remove unused
fd4f7eb0b9 remove prefixed io
5ac311e2a9 main: synchronize writes to VT100-faker on Windows

terraform % git show 5ac311e2a9
commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5 (HEAD)
Author: Martin Atkins <mart@degeneration.co.uk>
Date:   Wed May 3 16:25:41 2017 -0700
```

---

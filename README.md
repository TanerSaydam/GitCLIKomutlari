# Git Komutları ve Seçenekleri — 2026 Referansı

> **Dokümantasyon kontrol tarihi:** 21 Kasım 2026  
> **Resmî Git dokümantasyonunda görünen güncel sürüm:** Git 2.56.1  
> **Ana kaynak:** https://git-scm.com/docs  
> **Kapsam:** Git'in resmî komut aileleri, temel/global seçenekleri ve günlük kullanımda veya ileri seviye işlerde karşılaşılan komutların açıklamalı seçenekleri.

---

## İçindekiler

1. [Git komutlarının genel yapısı](#1-git-komutlarının-genel-yapısı)
2. [Komut satırı söz dizimi](#2-komut-satırı-söz-dizimi)
3. [Genel Git seçenekleri](#3-genel-git-seçenekleri)
4. [Kurulum ve yapılandırma](#4-kurulum-ve-yapılandırma)
5. [Repository oluşturma ve kopyalama](#5-repository-oluşturma-ve-kopyalama)
6. [Dosyaları hazırlama ve commit](#6-dosyaları-hazırlama-ve-commit)
7. [Değişiklikleri görüntüleme ve geçmişi inceleme](#7-değişiklikleri-görüntüleme-ve-geçmişi-inceleme)
8. [Branch ve birleştirme işlemleri](#8-branch-ve-birleştirme-i̇şlemleri)
9. [Geri alma ve düzeltme komutları](#9-geri-alma-ve-düzeltme-komutları)
10. [Remote ve ekip çalışması](#10-remote-ve-ekip-çalışması)
11. [Tag, stash ve worktree](#11-tag-stash-ve-worktree)
12. [Arama, hata ayıklama ve inceleme](#12-arama-hata-ayiklama-ve-inceleme)
13. [Git nesneleri ve plumbing komutları](#13-git-nesneleri-ve-plumbing-komutları)
14. [Repository bakım ve yönetim komutları](#14-repository-bakım-ve-yönetim-komutları)
15. [Resmî komut envanteri](#15-resmî-komut-envanteri)
16. [Sık kullanılan iş akışları](#16-sık-kullanılan-i̇ş-akışları)
17. [Dikkat edilmesi gereken seçenekler](#17-dikkat-edilmesi-gereken-seçenekler)
18. [Seçeneklerin eksiksiz listesini nasıl görürüm?](#18-seçeneklerin-eksiksiz-listesini-nasıl-görürüm)
19. [Resmî kaynaklar](#19-resmî-kaynaklar)

---

## 1. Git komutlarının genel yapısı

Git, dağıtık bir versiyon kontrol sistemidir. `git` komutu üzerinden çalışan alt komutlarla repository, dosya, commit, branch ve remote işlemlerini yönetir.

Git komutları genel olarak iki gruba ayrılır:

- **Porcelain:** Geliştiricilerin günlük kullanımına yönelik, daha üst seviyeli komutlardır. Örnek: `add`, `commit`, `status`, `switch`, `merge`.
- **Plumbing:** Git'in iç veri yapıları ve işlemleri üzerinde daha düşük seviyede çalışan komutlardır. Script, otomasyon ve Git'in iç işleyişini incelemek için kullanılır. Örnek: `cat-file`, `hash-object`, `ls-tree`, `update-ref`.

Örnek komut:

```powershell
git add Program.cs
```

Burada `git` ana program, `add` alt komut, `Program.cs` ise komuta verilen argümandır.

### Git'in temel kavramları

| Kavram | Açıklama |
|---|---|
| Working Tree | Çalıştığın proje dosyalarının bulunduğu klasör yapısıdır. |
| Repository | Commit geçmişinin ve Git metadata bilgilerinin saklandığı alandır. Genellikle `.git` klasörüdür. |
| Index / Staging Area | Bir sonraki commit'e dahil edilecek değişikliklerin hazırlandığı alandır. |
| Blob | Bir dosyanın içeriğini temsil eden Git nesnesidir. |
| Tree | Dosya ve klasör isimlerini ve bunların nesne referanslarını tutan Git nesnesidir. |
| Commit | Bir snapshot'ı, parent commit bilgisini ve metadata'yı temsil eden nesnedir. |
| Branch | Bir commit geçmişini gösteren hareketli referanstır. |
| HEAD | O anda üzerinde bulunulan commit'i veya branch'i gösteren referanstır. |
| Remote | Başka bir repository'yi temsil eden uzak repository tanımıdır. |
| Tag | Bir commit'i isimlendiren referanstır. Sürüm etiketlemek için kullanılır. |

---

## 2. Komut satırı söz dizimi

Genel kalıp:

```text
git [GENEL_SEÇENEKLER] <komut> [KOMUT_SEÇENEKLERİ] [ARGÜMANLAR]
```

Örnek:

```powershell
git -C "C:\Projects\Demo" log --oneline -5
```

- `-C`: Git komutunu belirtilen klasörden çalıştırır.
- `log`: Commit geçmişini listeler.
- `--oneline`: Her commit'i tek satırda gösterir.
- `-5`: Son beş commit'i gösterir.

### Gösterim kuralları

| Gösterim | Anlamı |
|---|---|
| `<argüman>` | Verilmesi gereken değer. |
| `[argüman]` | İsteğe bağlı değer. |
| `...` | Birden fazla değer verilebilir. |
| `-m <mesaj>` | Kısa seçenek ve ardından değer. |
| `--global` | Uzun seçenek. |
| `--` | Seçeneklerin bittiğini, devamındakilerin dosya/argüman olduğunu belirtir. |

Kısa seçenekler genellikle tek tire (`-`), uzun seçenekler çift tire (`--`) ile başlar. Her kısa seçeneğin uzun karşılığı olmak zorunda değildir.

---

## 3. Genel Git seçenekleri

Bu seçenekler alt komuttan önce yazılır ve Git'in genel çalışma biçimini etkiler.

| Seçenek | Açıklama | Örnek |
|---|---|---|
| `-v`, `--version` | Git sürümünü gösterir. | `git --version` |
| `-h`, `--help` | Genel yardım bilgisini gösterir. | `git --help` |
| `-C <path>` | Komutu belirtilen klasörde çalıştırır. | `git -C ../App status` |
| `-c <name>=<value>` | Tek komut için geçici Git ayarı tanımlar; config dosyasını değiştirmez. | `git -c core.autocrlf=false status` |
| `--config-env=<name>=<envvar>` | Bir config değerini ortam değişkeninden alır. | `git --config-env=http.extraHeader=GIT_HEADER fetch` |
| `--exec-path[=<path>]` | Git'in yardımcı programlarının bulunduğu yolu gösterir veya ayarlar. | `git --exec-path` |
| `--html-path` | HTML dokümantasyonunun kurulu olduğu yolu gösterir. | `git --html-path` |
| `--man-path` | Man sayfalarının yolunu gösterir. | `git --man-path` |
| `--info-path` | Info dokümantasyonunun yolunu gösterir. | `git --info-path` |
| `-p`, `--paginate` | Terminal çıktısını pager üzerinden gösterir. | `git --paginate log` |
| `-P`, `--no-pager` | Pager kullanımını kapatır. | `git --no-pager log` |
| `--git-dir=<path>` | Kullanılacak Git metadata klasörünü belirtir. | `git --git-dir=../repo.git status` |
| `--work-tree=<path>` | Working tree klasörünü belirtir. | `git --git-dir=../repo.git --work-tree=../src status` |
| `--bare` | Komutu bare repository bağlamında çalıştırır. | `git --bare ...` |
| `--namespace=<name>` | Git namespace kullanır. Sunucu/ileri seviye senaryolara yöneliktir. | `git --namespace=team ...` |
| `--no-replace-objects` | Replacement ref'lerin nesne yerine geçmesini engeller. | `git --no-replace-objects log` |
| `--no-lazy-fetch` | Partial clone'da eksik nesnelerin ihtiyaç halinde otomatik indirilmesini engeller. | `git --no-lazy-fetch cat-file -e <object>` |
| `--no-optional-locks` | Zorunlu olmayan, lock gerektiren işlemleri yapmaz. | `git --no-optional-locks status` |
| `--no-advice` | Git'in yönlendirici tavsiye mesajlarını kapatır. | `git --no-advice status` |
| `--literal-pathspecs` | Dosya yollarındaki glob/pathspec yorumlamasını kapatır. | `git --literal-pathspecs add "*.cs"` |
| `--glob-pathspecs` | Pathspec ifadelerinde glob yorumlamasını açar. | `git --glob-pathspecs add '*.cs'` |
| `--noglob-pathspecs` | Genel glob yorumlamasını kapatıp literal davranışa geçer. | `git --noglob-pathspecs add '*.cs'` |
| `--icase-pathspecs` | Pathspec eşleştirmesini büyük/küçük harfe duyarsız yapar. | `git --icase-pathspecs add readme.md` |
| `--list-cmds=<group>` | Komutları belirtilen gruba göre listeler. Dahili/deneysel kullanım içindir. | `git --list-cmds=main` |
| `--attr-source=<tree-ish>` | `.gitattributes` bilgisinin çalışma klasörü yerine belirtilen tree'den okunmasını sağlar. | `git --attr-source=HEAD status` |

> Genel seçenekler ile alt komut seçeneklerini karıştırma. Örneğin `git -C . status` içindeki `-C` Git'in genel seçeneğidir; `git status --short` içindeki `--short` ise `status` alt komutuna aittir.

---

## 4. Kurulum ve yapılandırma

### `git config`

Git ayarlarını okur, ekler, değiştirir ve siler. Kullanıcı adı, e-posta, varsayılan branch ve editör gibi ayarlar burada tutulur.

```powershell
git config --global user.name "Taner Saydam"
git config --global user.email "email@example.com"
git config --global init.defaultBranch main
git config --list
```

| Seçenek | Açıklama |
|---|---|
| `--global` | Kullanıcının genel ayar dosyasını düzenler. Genellikle `~/.gitconfig`. |
| `--system` | Sistem genelindeki ayarları düzenler. Yönetici izni gerekebilir. |
| `--local` | Yalnızca mevcut repository ayarlarını düzenler. Varsayılan yazma kapsamıdır. |
| `--worktree` | Etkin worktree'ye özel ayarları düzenler. |
| `--file <dosya>` / `-f <dosya>` | Belirtilen config dosyasını kullanır. |
| `--list` / `-l` | Ayarları listeler. |
| `--show-origin` | Ayarın hangi dosyadan geldiğini gösterir. |
| `--show-scope` | Ayarın hangi kapsamdan (system/global/local/worktree/command) geldiğini gösterir. |
| `--get <key>` | Bir ayarın değerini okur. |
| `--get-all <key>` | Aynı anahtarın tüm değerlerini okur. |
| `--get-regexp <regex>` | Anahtarları regex ile arar. |
| `--add <key> <value>` | Anahtara yeni bir değer ekler. |
| `--replace-all <key> <value>` | İlgili anahtarın tüm değerlerini verilen değerle değiştirir. |
| `--unset <key>` | Bir ayarı siler. |
| `--unset-all <key>` | Anahtarın tüm değerlerini siler. |
| `--edit` / `-e` | Seçilen config dosyasını editörde açar. |
| `--type=<type>` | Değerin bool, int, path, expiry-date gibi türde yorumlanmasını sağlar. |
| `--bool`, `--int`, `--path`, `--fixed-value` | Değer okuma/yazma sırasında tür veya eşleşme davranışını belirtir. |
| `--default <value>` | Anahtar bulunamazsa varsayılan değer döndürür. |
| `--name-only` | Listelerken yalnızca ayar isimlerini gösterir. |

Yararlı örnekler:

```powershell
git config --global --list --show-origin
git config --get user.name
git config --global core.editor "code --wait"
git config --global --unset alias.st
```

### `git help`

Git komutlarının ve rehberlerinin yardım sayfalarını açar.

```powershell
git help
git help add
git help -a
git help -g
```

| Seçenek | Açıklama |
|---|---|
| `-a`, `--all` | Kullanılabilir tüm komutları listeler. |
| `-g`, `--guides` | Git kavram rehberlerini listeler. |
| `-c`, `--config` | Yapılandırma değişkenlerini listeler. |
| `-i`, `--info` | Info biçimindeki yardım sayfasını açar. |
| `-m`, `--man` | Man sayfasını açar. |
| `-w`, `--web` | Yardım sayfasını web tarayıcısında açar. |
| `--verbose` | `--all` ile birlikte daha ayrıntılı komut açıklamaları verir. |
| `--no-external-commands` | `--all` çıktısında PATH içindeki harici `git-*` komutlarını hariç tutar. |
| `--no-aliases` | `--all` çıktısında alias'ları hariç tutar. |

### `git version`

Git sürüm bilgisini ve derleme/sürüm ayrıntılarını gösterir.

```powershell
git version
git version --build-options
```

| Seçenek | Açıklama |
|---|---|
| `--build-options` | Git'in derleme ortamına ilişkin bilgileri gösterir. |

### `git bugreport`

Git ile ilgili hata bildirimi hazırlamak için sistem ve repository bilgilerini toplar.

```powershell
git bugreport
```

Ayrıntılı seçenekler: [git-bugreport](https://git-scm.com/docs/git-bugreport).

---

## 5. Repository oluşturma ve kopyalama

### `git init`

Mevcut klasörde yeni bir Git repository oluşturur. `.git` metadata alanını üretir.

```powershell
git init
git init MyProject
git init --initial-branch=main
```

| Seçenek | Açıklama |
|---|---|
| `-q`, `--quiet` | Gereksiz çıktı mesajlarını azaltır. |
| `--bare` | Working tree içermeyen bare repository oluşturur. Genellikle sunucu repository'lerinde kullanılır. |
| `--template=<directory>` | Başlangıç şablon dosyalarının alınacağı klasörü belirtir. |
| `--separate-git-dir=<git-dir>` | Git metadata klasörünü proje klasöründen ayrı bir konuma yerleştirir. |
| `-b <branch>`, `--initial-branch=<branch>` | İlk branch adını belirler. |
| `--shared[=<permissions>]` | Repository'nin birden fazla kullanıcı tarafından paylaşılacağı izin modelini ayarlar. |

### `git clone`

Uzak veya yerel bir repository'nin kopyasını oluşturur. Repository geçmişi de indirilir.

```powershell
git clone https://github.com/user/project.git
git clone https://github.com/user/project.git MyFolder
```

| Seçenek | Açıklama |
|---|---|
| `-q`, `--quiet` | İlerleme ve bilgilendirme çıktısını azaltır. |
| `-v`, `--verbose` | Daha ayrıntılı çıktı verir. |
| `-b <branch>`, `--branch=<branch>` | Clone sonrasında belirtilen branch'i checkout eder. |
| `--single-branch` | Yalnızca seçilen branch'in geçmişini alır. |
| `--no-single-branch` | Tüm branch geçmişlerinin alınmasına izin verir. |
| `--depth <n>` | Belirtilen commit derinliğine kadar geçmişi indirir (shallow clone). |
| `--shallow-since=<date>` | Belirtilen tarihten sonraki geçmişi alır. |
| `--shallow-exclude=<revision>` | Belirtilen revizyonun erişilebilir olduğu geçmişi dışarıda bırakır. |
| `--unshallow` | Shallow repository'yi mümkün olduğunca tam geçmişe dönüştürür; clone'dan çok fetch bağlamında kullanılır. |
| `--recurse-submodules[=<pathspec>]` | Submodule'leri de başlatıp indirir. |
| `--shallow-submodules` | Submodule'leri shallow clone olarak indirir. |
| `--remote-submodules` | Submodule'lerde kayıtlı commit yerine remote branch uçlarını takip eder. |
| `-o <name>`, `--origin=<name>` | Remote adını `origin` yerine belirler. |
| `-c <key>=<value>` | Clone tamamlanırken repository ayarı ekler. |
| `--bare` | Bare repository oluşturur; working tree oluşturmaz. |
| `--mirror` | Kaynak repository'nin refs dahil aynasını oluşturur. Bare davranışını da içerir. |
| `-n`, `--no-checkout` | Clone sonrası working tree checkout işlemini yapmaz. |
| `--filter=<filter-spec>` | Partial clone ile yalnızca gerekli nesneleri indirir. Örnek: `--filter=blob:none`. |
| `--sparse` | Sparse checkout ile sınırlı dosya ağacı oluşturur. |
| `--reference=<repository>` | Yerel başka bir repository'deki nesneleri referans alır. |
| `--reference-if-able=<repository>` | Referans repository mevcutsa kullanır, yoksa devam eder. |
| `--dissociate` | Clone sonrasında referans repository'ye bağımlılığı kaldırır. |
| `--jobs <n>`, `-j <n>` | Submodule indirme işlerinin paralellik sayısını belirler. |
| `--bundle-uri=<uri>` | İlk clone sırasında bundle kaynağını kullanır. |

---

## 6. Dosyaları hazırlama ve commit

### `git status`

Working tree ve staging area durumunu gösterir. Hangi dosyaların değiştiğini, eklendiğini veya silindiğini anlamak için kullanılır.

```powershell
git status
git status --short
```

| Seçenek | Açıklama |
|---|---|
| `-s`, `--short` | Kısa durum çıktısı verir. |
| `-b`, `--branch` | Branch ve upstream bilgilerini gösterir. |
| `--porcelain`, `--porcelain=v1`, `--porcelain=v2` | Script'lerin güvenilir şekilde ayrıştırabileceği sabit formatta çıktı verir. |
| `-u[<mode>]`, `--untracked-files[=<mode>]` | Takip edilmeyen dosyaları gösterme biçimini belirler: `no`, `normal`, `all`. |
| `--ignored` | Ignore edilen dosyaları da gösterir. |
| `--ignore-submodules[=<when>]` | Submodule değişikliklerini hangi koşulda yok sayacağını belirler. |
| `--column[=<options>]` | Untracked dosyaları sütunlar halinde gösterebilir. |
| `--ahead-behind` | Upstream'e göre ahead/behind sayılarını gösterir. |
| `--renames` | Rename algılamasını etkinleştirir. |
|

### `git add`

Dosyaların mevcut içeriklerini staging area'ya ekler. Commit atmaz.

```powershell
git add Program.cs
git add .
git add -A
```

| Seçenek | Açıklama |
|---|---|
| `-A`, `--all` | Yeni, değiştirilmiş ve silinmiş dosyaları staging area'ya ekler. |
| `-u`, `--update` | Yalnızca önceden takip edilen dosyalardaki değişiklikleri ve silmeleri stage eder. Yeni dosyaları eklemez. |
| `-p`, `--patch` | Değişikliklerin seçilen parçalarını (hunk) stage eder. |
| `-i`, `--interactive` | Etkileşimli staging menüsünü açar. |
| `-f`, `--force` | Ignore edilen dosyanın da eklenmesini sağlar. |
| `-n`, `--dry-run` | Nelerin ekleneceğini gösterir, ekleme yapmaz. |
| `-v`, `--verbose` | Eklenen dosyaları ayrıntılı gösterir. |
| `-e`, `--edit` | Patch'i editörde düzenleyerek stage etmeyi sağlar. |
| `--intent-to-add`, `-N` | Dosyayı eklenmesi planlanmış olarak index'e kaydeder; içeriği henüz stage etmez. |
| `--refresh` | Index'teki dosya metadata bilgilerini yeniler. |
| `--ignore-errors` | Bazı dosyalardaki ekleme hatalarına rağmen diğerlerine devam eder. |
| `--ignore-missing` | Dry-run ile birlikte, bulunmayan dosyaları hata kabul etmeden kontrol etmeye yardımcı olur. |
| `--renormalize` | Dosyaları normalize/clean filtrelerinden yeniden geçirir. |
| `--chmod=(+|-)x` | Index'teki executable bit değerini değiştirir. |

### `git commit`

Staging area'daki değişiklikleri repository geçmişine yeni bir commit olarak kaydeder.

```powershell
git commit -m "Add product service"
git commit -am "Fix validation"
```

| Seçenek | Açıklama |
|---|---|
| `-m <message>`, `--message=<message>` | Commit mesajını belirtir. |
| `-a`, `--all` | Takip edilen dosyalardaki değişiklik ve silmeleri otomatik stage edip commit eder. Yeni dosyaları otomatik eklemez. |
| `--amend` | Son commit'i yeni içerik/mesaj ile değiştirir. Paylaşılmış geçmişte dikkatli kullanılmalıdır. |
| `--no-edit` | `--amend` gibi işlemlerde mevcut commit mesajını açmadan korur. |
| `--allow-empty` | Dosya değişikliği olmasa da commit oluşturur. |
| `--allow-empty-message` | Boş commit mesajına izin verir. |
| `--author=<author>` | Author bilgisini özel olarak belirler. |
| `--date=<date>` | Author tarihini belirler. |
| `--cleanup=<mode>` | Commit mesajındaki yorum/boşluk temizleme biçimini belirler. |
| `-C <commit>` | Belirtilen commit'in mesajını ve metadata bilgisini kullanır. |
| `-c <commit>` | Belirtilen commit'in mesajını editörde düzenlemek üzere açar. |
| `-F <file>`, `--file=<file>` | Commit mesajını dosyadan okur. |
| `-s`, `--signoff` | Mesaja `Signed-off-by` satırı ekler. |
| `-S[<keyid>]`, `--gpg-sign[=<keyid>]` | Commit'i GPG/SSH imza yapılandırmasına göre imzalar. |
| `--no-gpg-sign` | İmzalamayı kapatır. |
| `--no-verify` | `pre-commit` ve `commit-msg` hook kontrollerini atlar. Genellikle önerilmez. |
| `--dry-run` | Commit'in ne yapacağını gösterir, commit oluşturmaz. |
| `-v`, `--verbose` | Commit mesajı editöründe diff gösterir. |
| `--quiet` | Çıktıyı azaltır. |
| `--fixup=<commit>` | Hedef commit için autosquash uyumlu düzeltme commit'i oluşturur. |
| `--squash=<commit>` | Hedef commit'e squash edilmek üzere mesajı düzenlenmiş commit oluşturur. |
| `--pathspec-from-file=<file>` | Commit edilecek pathspec listesini dosyadan okur. |
| `--pathspec-file-nul` | Pathspec dosyasındaki girdilerin NUL ile ayrıldığını belirtir. |

> `git commit -am` yeni ve henüz Git tarafından takip edilmeyen dosyaları eklemez. Yeni dosya için önce `git add` gerekir.

### `git rm`

Dosyayı working tree'den ve index'ten kaldırır.

```powershell
git rm OldFile.cs
git rm --cached secrets.json
```

| Seçenek | Açıklama |
|---|---|
| `-f`, `--force` | Index ile working tree'nin durumları farklı olsa da silmeye zorlar. |
| `-n`, `--dry-run` | Silme işlemini yapmadan ne olacağını gösterir. |
| `-r` | Klasörleri özyinelemeli kaldırır. |
| `--cached` | Yalnızca index'ten kaldırır; dosyayı diskten silmez. |
| `--ignore-unmatch` | Eşleşen dosya bulunmadığında hata döndürmez. |
| `-q`, `--quiet` | Çıktıyı azaltır. |

### `git mv`

Dosya veya klasörü taşır ya da yeniden adlandırır. Sonuç index'e yansıtılır.

```powershell
git mv OldName.cs NewName.cs
```

| Seçenek | Açıklama |
|---|---|
| `-f`, `--force` | Hedef dosya mevcutsa üzerine yazmaya zorlar. |
| `-k` | Hatalı taşıma işlemlerini atlayıp geçerli olanlara devam eder. |
| `-n`, `--dry-run` | Taşıma yapmadan sonucu gösterir. |
| `-v`, `--verbose` | Taşıma işlemlerini listeler. |

---

## 7. Değişiklikleri görüntüleme ve geçmişi inceleme

### `git diff`

İki durum arasındaki içerik farklarını gösterir. En çok kullanılan karşılaştırma komutudur.

```powershell
git diff                 # Working tree ile staging area
 git diff --staged       # Staging area ile son commit
 git diff HEAD            # Working tree + index ile HEAD
 git diff main..feature   # İki commit/branch ucu arasındaki fark
```

| Seçenek | Açıklama |
|---|---|
| `--staged`, `--cached` | Stage edilmiş değişiklikleri HEAD ile karşılaştırır. |
| `--stat` | Dosya bazında değişiklik istatistiği gösterir. |
| `--numstat` | Ekleme/silme sayılarını makine dostu biçimde gösterir. |
| `--shortstat` | Yalnızca toplam istatistik satırını gösterir. |
| `--name-only` | Yalnızca değişen dosya adlarını gösterir. |
| `--name-status` | Dosya adlarıyla birlikte A/M/D/R gibi durumları gösterir. |
| `--summary` | Create/delete/rename/mode değişikliklerini özetler. |
| `--check` | Whitespace hatalarını kontrol eder. |
| `-U<n>`, `--unified=<n>` | Değişen satırların çevresinde gösterilecek context satırı sayısını belirler. |
| `--word-diff[=<mode>]` | Satır yerine kelime bazında farkları gösterir. |
| `--color[=<when>]` | Renkli çıktı ayarlar: `always`, `never`, `auto`. |
| `--no-color` | Renkli çıktıyı kapatır. |
| `--no-index` | Git dışında kalan iki dosya/klasörü karşılaştırır. |
| `--binary` | Binary patch çıktısı üretir. |
| `--full-index` | Tam object ID gösterir. |
| `--minimal` | Daha küçük/temiz diff üretmeye çalışır. |
| `-w`, `--ignore-all-space` | Tüm whitespace farklarını yok sayar. |
| `-b`, `--ignore-space-change` | Boşluk miktarındaki farkları yok sayar. |
| `--ignore-space-at-eol` | Satır sonundaki boşluk farklarını yok sayar. |
| `--ignore-blank-lines` | Tamamen boş satır değişikliklerini yok sayar. |
| `--diff-filter=<types>` | A/M/D/R/C/U gibi belirli değişiklik türlerini filtreler. |
| `-M[<n>]`, `--find-renames[=<n>]` | Rename algılamasını açar ve eşik değerini belirleyebilir. |
| `-C[<n>]`, `--find-copies[=<n>]` | Copy algılamasını açar. |
| `--no-renames` | Rename algılamasını kapatır. |
| `--relative[=<path>]` | Alt klasörden çalışırken göreli yollar gösterir. |
| `--exit-code` | Fark varsa 1, yoksa 0 çıkış kodu döndürür. |
| `--quiet` | Çıktıyı bastırır; script kontrolü için kullanışlıdır. |
|

### `git log`

Commit geçmişini listeler.

```powershell
git log
git log --oneline
git log --oneline --graph --decorate --all
```

| Seçenek | Açıklama |
|---|---|
| `--oneline` | Commit hash'inin kısaltılmış hali ve mesajı tek satırda gösterir. |
| `--graph` | Branch/merge yapısını ASCII grafikle gösterir. |
| `--decorate` | Branch ve tag isimlerini commit yanında gösterir. |
| `--all` | Tüm refs tarafından erişilebilen commit'leri listeler. |
| `-n <n>`, `--max-count=<n>` | En fazla kaç commit gösterileceğini belirler. |
| `-p`, `--patch` | Commit'lerin diff'lerini gösterir. |
| `--stat` | Commit başına dosya değişiklik istatistiği verir. |
| `--name-only` | Değişen dosya isimlerini gösterir. |
| `--name-status` | Dosya isimleri ve değişiklik durumlarını gösterir. |
| `--format=<format>`, `--pretty=<format>` | Commit çıktısının biçimini belirler. |
| `--author=<pattern>` | Author bilgisi eşleşen commit'leri filtreler. |
| `--committer=<pattern>` | Committer bilgisi eşleşen commit'leri filtreler. |
| `--grep=<pattern>` | Commit mesajında metin arar. |
| `--grep-reflog=<pattern>` | Reflog mesajlarında arama yapar. |
| `--since=<date>`, `--after=<date>` | Belirtilen tarihten sonraki commit'leri listeler. |
| `--until=<date>`, `--before=<date>` | Belirtilen tarihten önceki commit'leri listeler. |
| `--all-match` | Birden fazla grep koşulunun tümünün eşleşmesini ister. |
| `--invert-grep` | Mesaj aramasının sonucunu tersine çevirir. |
| `--reverse` | Seçilen commit aralığını ters sırada gösterir. |
| `--first-parent` | Merge commit'lerin yalnızca ilk parent hattını izler. |
| `--no-merges` | Merge commit'leri gizler. |
| `--merges` | Yalnızca merge commit'leri gösterir. |
| `--follow` | Tek dosyanın geçmişinde rename durumlarını takip eder. |
| `-S<string>` | Belirli metnin sayısının değiştiği commit'leri bulur (pickaxe). |
| `-G<regex>` | Diff içinde regex ile eşleşen değişiklikleri bulur. |
| `--topo-order` | Parent ilişkilerini bozmayan topolojik sıraya göre listeler. |
| `--abbrev-commit` | Hash'leri kısaltır. |
| `--no-abbrev` | Tam hash gösterir. |
| `--date=<format>` | Tarih gösterim biçimini belirler. |

Örnek özel format:

```powershell
git log --pretty=format:"%h | %an | %ad | %s" --date=short
```

Yararlı format placeholder'ları: `%H` tam commit hash'i, `%h` kısa hash, `%an` author adı, `%ae` author e-postası, `%ad` author tarihi, `%cn` committer adı, `%s` subject, `%b` body.

### `git show`

Commit, tag, tree ve blob gibi Git nesnelerini gösterir. Commit için çoğunlukla commit mesajı ve diff çıktısı verir.

```powershell
git show HEAD
git show --stat HEAD
git show <commit>:Program.cs
```

Önemli seçenekler:

| Seçenek | Açıklama |
|---|---|
| `--stat` | Değişiklik istatistiği gösterir. |
| `--name-only` | Değişen dosyaları listeler. |
| `--name-status` | Dosya isimleri ve durumlarını gösterir. |
| `--format=<format>` / `--pretty=<format>` | Commit açıklama formatını belirler. |
| `-s`, `--no-patch` | Diff üretmez; nesne/commit bilgisini gösterir. |
| `-p`, `--patch` | Patch/diff gösterir. |
| `--raw` | Düşük seviye diff bilgisini gösterir. |
| `--text` | Binary kabul edilen içeriği metin olarak işlemeye çalışır. |

### `git shortlog`

Commit geçmişini author bazında özetler.

```powershell
git shortlog -sn
```

| Seçenek | Açıklama |
|---|---|
| `-s`, `--summary` | Her author için commit sayılarını özetler. |
| `-n`, `--numbered` | Author'ları commit sayısına göre sıralar. |
| `-e`, `--email` | E-posta adreslerini gösterir. |
| `--group=<type>` | Author/committer gibi gruplamayı seçer. |

### `git describe`

Bir commit'i en yakın uygun tag üzerinden okunabilir biçimde adlandırır.

```powershell
git describe --tags --always
```

| Seçenek | Açıklama |
|---|---|
| `--tags` | Annotated tag'lere ek olarak lightweight tag'leri de kullanır. |
| `--all` | Branch ve diğer refs'i de dikkate alır. |
| `--always` | Uygun ref bulunamazsa kısaltılmış commit hash'i gösterir. |
| `--long` | Tag'e yakın olsa bile `tag-count-g<hash>` biçimini kullanır. |
| `--abbrev=<n>` | Hash kısaltmasının uzunluğunu belirler. |
| `--match=<pattern>` | Yalnızca belirli isim kalıbındaki tag'leri kullanır. |
| `--exclude=<pattern>` | Belirli tag kalıplarını dışarıda bırakır. |
| `--dirty[=<mark>]` | Working tree kirliyse sona işaret ekler. |

---

## 8. Branch ve birleştirme işlemleri

### `git branch`

Branch'leri listeler, oluşturur, yeniden adlandırır veya siler.

```powershell
git branch
git branch feature/login
git branch -d feature/login
```

| Seçenek | Açıklama |
|---|---|
| `-a`, `--all` | Local ve remote-tracking branch'leri listeler. |
| `-r`, `--remotes` | Yalnızca remote-tracking branch'leri listeler. |
| `-l`, `--list` | Branch listesini gösterir; pattern ile filtrelenebilir. |
| `-v`, `--verbose` | Branch başına son commit bilgisi verir. |
| `-vv` | Upstream bilgilerini de gösterir. |
| `-d`, `--delete` | Güvenli koşullarda merge edilmiş branch'i siler. |
| `-D` | Merge edilmemiş olsa da branch'i zorla siler. |
| `-m`, `--move` | Branch'i yeniden adlandırır. |
| `-M` | Var olan hedef isim üzerine zorla yeniden adlandırır. |
| `-c`, `--copy` | Branch'i kopyalar. |
| `-C` | Hedef varsa üzerine yazarak branch'i kopyalar. |
| `--show-current` | Geçerli branch adını gösterir. |
| `--merged[=<commit>]` | Belirtilen commit'e merge edilmiş branch'leri listeler. |
| `--no-merged[=<commit>]` | Belirtilen commit'e merge edilmemiş branch'leri listeler. |
| `--contains <commit>` | Commit'i içeren branch'leri listeler. |
| `--no-contains <commit>` | Commit'i içermeyen branch'leri listeler. |
| `--sort=<key>` | Çıktıyı belirtilen alana göre sıralar. |
| `--track[=(direct|inherit)]` | Yeni branch'in upstream tracking ilişkisini kurar. |
| `--set-upstream-to=<upstream>` | Mevcut branch'in upstream'ini ayarlar. |
| `--unset-upstream` | Upstream bağlantısını kaldırır. |

### `git switch`

Branch değiştirir. `checkout` komutunun branch değiştirmeye ayrılmış daha anlaşılır alternatifidir.

```powershell
git switch main
git switch -c feature/payment
```

| Seçenek | Açıklama |
|---|---|
| `-c <branch>`, `--create <branch>` | Yeni branch oluşturur ve ona geçer. |
| `-C <branch>`, `--force-create <branch>` | Branch varsa yeniden oluşturup ona geçer. |
| `-d`, `--detach` | Belirtilen commit'e detached HEAD durumunda geçer. |
| `--track[=(direct|inherit)]` | Upstream tracking ilişkisi kurar. |
| `--no-track` | Tracking ilişkisi oluşturmaz. |
| `-f`, `--force`, `--discard-changes` | Yerel değişiklikleri kaybetme riskini kabul ederek geçiş yapar. |
| `-m`, `--merge` | Yerel değişiklikleri mümkünse merge ederek geçiş yapar. |
| `--orphan <branch>` | Önceki commit geçmişinden bağımsız yeni branch oluşturur. |
| `--guess` / `--no-guess` | Branch adı bulunamazsa remote'dan tahmin etme davranışını belirler. |
|

### `git checkout`

Eski ve hâlâ kullanılan çok amaçlı komuttur. Branch değiştirebilir veya dosyaları başka bir durumdan geri yükleyebilir.

```powershell
git checkout main
git checkout -b feature/search
git checkout -- Program.cs
```

| Seçenek | Açıklama |
|---|---|
| `-b <branch>` | Yeni branch oluşturup ona geçer. |
| `-B <branch>` | Branch'i oluşturur veya sıfırlayıp ona geçer. |
| `--track` | Upstream tracking ilişkisi kurar. |
| `--no-track` | Tracking ilişkisi kurmaz. |
| `--detach` | Detached HEAD durumuna geçer. |
| `-f`, `--force` | Yerel değişiklikleri korumadan geçişe zorlar. Veri kaybına neden olabilir. |
| `-m`, `--merge` | Geçiş sırasında yerel değişiklikleri merge etmeyi dener. |
| `--ours` | Merge conflict içeren dosyada current branch tarafını seçer. |
| `--theirs` | Merge conflict içeren dosyada diğer branch tarafını seçer. |
| `-p`, `--patch` | Dosya değişikliklerinin seçilen bölümlerini geri alır. |
| `--orphan <branch>` | Geçmişi olmayan yeni branch oluşturur. |
| `--ignore-skip-worktree-bits` | Sparse checkout işaretlerini yok sayar. |

> Yeni kullanımda branch değiştirmek için `git switch`, dosya geri yüklemek için `git restore` tercih edilebilir.

### `git merge`

Başka bir branch'in geçmişini mevcut branch'e birleştirir.

```powershell
git switch main
git merge feature/login
```

| Seçenek | Açıklama |
|---|---|
| `--no-ff` | Fast-forward mümkün olsa bile merge commit oluşturur. |
| `--ff-only` | Yalnızca fast-forward mümkünse merge eder; aksi durumda durur. |
| `--ff` | Fast-forward'a izin verir. |
| `--no-commit` | Merge sonucunu hazırlar ancak commit oluşturmaz. |
| `--commit` | Merge commit'i oluşturmaya izin verir. |
| `--squash` | Değişiklikleri tek bir birleşik çalışma ağacı/index sonucu olarak hazırlar; merge commit oluşturmaz. |
| `--no-squash` | Squash davranışını kapatır. |
| `--abort` | Devam eden merge işlemini iptal eder ve öncesindeki duruma dönmeye çalışır. |
| `--continue` | Conflict'ler çözüldükten sonra merge sürecini devam ettirir. |
| `--quit` | Merge sürecinin metadata bilgisini bırakıp çıkar. |
| `--strategy=<strategy>`, `-s` | Kullanılacak merge algoritmasını belirler. |
| `--strategy-option=<option>`, `-X` | Seçilen stratejiye özel seçenek verir. Örnek: `-X ours`. |
| `--no-edit` | Varsayılan merge mesajını editör açmadan kullanır. |
| `--edit` | Merge mesajını editörde düzenletir. |
| `--no-verify` | `pre-merge-commit` ve `commit-msg` hook kontrollerini atlar. |
| `--allow-unrelated-histories` | Ortak geçmişi olmayan iki geçmişi birleştirmeye izin verir. |
| `--autostash` | İşlem öncesi yerel değişiklikleri geçici stash'e alıp sonrasında uygular. |
| `--no-autostash` | Otomatik stash davranışını kapatır. |
| `--stat` / `--no-stat` | Merge istatistiğini gösterir veya gizler. |
| `--log[=<n>]` | Merge commit mesajına birleştirilen commit'lerden özet ekler. |

### `git mergetool`

Merge conflict'lerini yapılandırılmış harici merge aracıyla çözmeye yardımcı olur.

| Seçenek | Açıklama |
|---|---|
| `--tool=<tool>` | Kullanılacak merge aracını belirler. |
| `--tool-help` | Kullanılabilir araçları listeler. |
| `--gui` / `--no-gui` | GUI aracı kullanımını seçer. |
| `--prompt` / `--no-prompt` | Her dosyadan önce onay isteyip istemeyeceğini belirler. |
| `--trust-exit-code` | Aracın çıkış kodunu çözümün başarılı olup olmadığını belirlemek için kullanır. |
| `--no-symlinks` | Yedek dosyalar için symlink yerine kopya davranışını seçer. |

---

## 9. Geri alma ve düzeltme komutları

Bu bölümdeki komutlar birbirine benzer görünse de farklı alanları değiştirir. Özellikle `reset`, `restore` ve `revert` birbirinin yerine kullanılmaz.

### `git restore`

Working tree veya staging area'daki dosya değişikliklerini geri yükler. Branch geçmişini hareket ettirmez.

```powershell
git restore Program.cs
git restore --staged Program.cs
git restore --source=HEAD~1 Program.cs
```

| Seçenek | Açıklama |
|---|---|
| `--source=<tree>` | Dosyanın hangi commit/tree'den alınacağını belirtir. |
| `--staged` | Index/staging area'yı geri yükler. |
| `--worktree` | Working tree dosyalarını geri yükler. Varsayılan davranış bağlama göre değişir. |
| `-s <tree>`, `--source=<tree>` | Kaynak tree/commit belirtir. |
| `-p`, `--patch` | Değişikliklerin seçilen parçalarını geri yükler. |
| `--ours` | Unmerged dosyada index'in stage 2 içeriğini kullanır. |
| `--theirs` | Unmerged dosyada index'in stage 3 içeriğini kullanır. |
| `--merge` | Conflict marker'ları ve merge aşamalarını kullanarak dosyayı geri yükler. |
| `--conflict=<style>` | Conflict marker biçimini belirler: `merge`, `diff3`, `zdiff3` gibi. |
| `--ignore-unmerged` | Unmerged yolları yok sayar. |
| `--overlay` / `--no-overlay` | Hedefte olmayan dosyaların korunması veya kaldırılması davranışını belirler. |
| `--recurse-submodules` / `--no-recurse-submodules` | Submodule çalışma ağacının da geri yüklenmesini kontrol eder. |

### `git reset`

HEAD'i, staging area'yı ve bazı kiplerde working tree'yi değiştirir. Commit geçmişini yeniden yazma riski nedeniyle dikkatli kullanılmalıdır.

```powershell
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

| Seçenek | Açıklama |
|---|---|
| `--soft` | Yalnızca HEAD/branch pointer'ını taşır. Staging ve working tree değişikliklerini bırakır. |
| `--mixed` | HEAD ve index'i taşır; working tree dosyalarını değiştirmez. Varsayılan moddur. |
| `--hard` | HEAD, index ve working tree'yi hedef commit'e eşitler. Commit edilmemiş değişiklikler kaybolabilir. |
| `--merge` | Index/working tree'yi hedefe göre günceller; bazı yerel değişiklikleri korumaya çalışır. |
| `--keep` | Hedef commit'e geçerken yerel değişikliklerle çakışma olacaksa durur. |
| `-p`, `--patch` | Değişikliklerin seçilen parçalarını tersine alarak index'e uygular. |
| `-N`, `--intent-to-add` | Path reset işlemlerinde silinen yolları intent-to-add olarak işaretler. |
| `--pathspec-from-file=<file>` | Pathspec listesini dosyadan okur. |
| `--pathspec-file-nul` | Pathspec girdilerinin NUL ayrımlı olduğunu belirtir. |

**Önemli:** `git reset <commit> -- <file>` biçimi yalnızca belirtilen dosyanın index durumunu düzenler; branch/HEAD'i taşımaz.

### `git revert`

Geçmişteki bir commit'in yaptığı değişikliği tersine çeviren **yeni bir commit** oluşturur. Paylaşılmış branch'lerde genellikle reset'ten daha güvenli yaklaşımdır.

```powershell
git revert <commit>
git revert HEAD
```

| Seçenek | Açıklama |
|---|---|
| `--no-commit`, `-n` | Ters değişikliği commit etmeden index ve working tree'ye uygular. |
| `--edit`, `-e` | Revert commit mesajını editörde açar. |
| `--no-edit` | Varsayılan revert mesajıyla commit oluşturur. |
| `--mainline <parent-number>`, `-m` | Merge commit'i geri alırken hangi parent hattının ana hat sayılacağını belirtir. |
| `--signoff` | Commit mesajına Signed-off-by ekler. |
| `--strategy=<strategy>` | Kullanılacak merge stratejisini belirler. |
| `-X<option>` | Merge stratejisi seçeneği verir. |
| `--continue` | Conflict çözüldükten sonra işlemi sürdürür. |
| `--abort` | Revert sürecini iptal eder. |
| `--skip` | Mevcut commit'i atlayıp devam eder. |
| `--quit` | Sequencer durumunu bırakıp çıkar. |

### `git cherry-pick`

Başka bir commit'in yaptığı değişikliği mevcut branch'e uygular ve genellikle yeni commit oluşturur.

```powershell
git cherry-pick <commit>
```

| Seçenek | Açıklama |
|---|---|
| `-e`, `--edit` | Commit mesajını düzenler. |
| `-n`, `--no-commit` | Değişikliği commit oluşturmadan uygular. |
| `-x` | Oluşturulan mesajın içine kaynak commit hash'ini ekler. |
| `-s`, `--signoff` | Signed-off-by satırı ekler. |
| `-m <parent-number>` | Merge commit'ten hangi parent'a göre değişiklik alınacağını belirtir. |
| `--ff` | Mümkünse fast-forward davranışını kullanır. |
| `--allow-empty` | Boş sonucu olan commit'e izin verir. |
| `--allow-empty-message` | Boş commit mesajına izin verir. |
| `--keep-redundant-commits` | Zaten uygulanmış değişikliği temsil eden boş commit'i korur. |
| `--strategy=<strategy>` | Merge stratejisini belirler. |
| `-X<option>` | Merge stratejisine özel seçenek verir. |
| `--continue` | Conflict çözüldükten sonra işlemi sürdürür. |
| `--skip` | Mevcut commit'i atlar. |
| `--abort` | Cherry-pick dizisini iptal eder. |
| `--quit` | Sequencer metadata bilgisini bırakıp çıkar. |

### `git rebase`

Commit'leri yeni bir başlangıç commit'inin üzerine yeniden uygular. Branch geçmişini yeniden yazar; paylaşılmış branch üzerinde dikkatli kullanılmalıdır.

```powershell
git rebase main
git rebase -i HEAD~3
```

| Seçenek | Açıklama |
|---|---|
| `-i`, `--interactive` | Commit'leri sıralama, birleştirme, mesaj değiştirme veya silme için etkileşimli liste açar. |
| `--onto <newbase> [upstream]` | Commit aralığını belirtilen yeni tabanın üzerine taşır. |
| `--continue` | Conflict çözüldükten sonra rebase'e devam eder. |
| `--abort` | Rebase'i başlatılmadan önceki duruma döndürür. |
| `--skip` | Sorun çıkaran commit'i atlar. |
| `--quit` | Rebase metadata bilgisini bırakıp çıkar. |
| `--autosquash` | `fixup!` ve `squash!` commit'lerini interaktif liste içinde otomatik konumlandırır. |
| `--autostash` | Başlangıçta yerel değişiklikleri stash'e alıp sonunda yeniden uygular. |
| `--no-autostash` | Otomatik stash davranışını kapatır. |
| `--rebase-merges[=(rebase-cousins|no-rebase-cousins)]` | Yeniden oluştururken merge commit yapısını korumaya çalışır. |
| `--exec <command>` | Her yeniden uygulanan commit adımından sonra komut çalıştırır. |
| `--root` | Branch'in tüm geçmişini root commit'ten itibaren yeniden işler. |
| `--keep-base` | Upstream ile ortak tabanı koruyacak davranışı seçer. |
| `--empty=(drop|keep|stop)` | Boş hale gelen commit'lerin davranışını belirler. |
| `--force-rebase`, `-f` | Commit'leri değişiklik olmasa bile yeniden oluşturur. |
| `--no-ff` | Commit'leri yeniden oluşturma davranışını zorlar. |
| `--fork-point` | Upstream değişmiş olabileceğinde fork-point hesaplamasını kullanır. |
| `--no-verify` | `pre-rebase` hook'unu çalıştırmaz. |
| `-s <strategy>`, `--strategy=<strategy>` | Kullanılacak merge stratejisini belirtir. |
| `-X<option>` | Stratejiye özel seçenek verir. |

### `git clean`

Git tarafından takip edilmeyen dosyaları working tree'den siler. Yanlış kullanım veri kaybına neden olabilir.

```powershell
git clean -n
git clean -fd
```

| Seçenek | Açıklama |
|---|---|
| `-n`, `--dry-run` | Silinecek dosyaları listeler, silmez. Önce bunu kullanmak önerilir. |
| `-f`, `--force` | Silme işlemini onaylar. |
| `-d` | Takip edilmeyen klasörleri de siler. |
| `-x` | `.gitignore` tarafından ignore edilen dosyalar dahil temizler. |
| `-X` | Yalnızca ignore edilen dosyaları siler. |
| `-i`, `--interactive` | Etkileşimli temizlik menüsü açar. |
| `-q`, `--quiet` | Silme çıktısını azaltır. |

---

## 10. Remote ve ekip çalışması

### `git remote`

Uzak repository tanımlarını listeler ve yönetir.

```powershell
git remote -v
git remote add origin https://github.com/user/project.git
git remote set-url origin https://github.com/user/new-project.git
```

| Alt komut/seçenek | Açıklama |
|---|---|
| `-v`, `--verbose` | Remote adlarını fetch/push URL'leriyle gösterir. |
| `add <name> <url>` | Yeni remote tanımlar. |
| `remove <name>` / `rm <name>` | Remote tanımını siler. |
| `rename <old> <new>` | Remote adını değiştirir. |
| `set-url <name> <url>` | Remote URL'sini değiştirir. |
| `get-url <name>` | Remote URL'sini gösterir. |
| `show <name>` | Remote ve tracking branch bilgilerini gösterir. |
| `prune <name>` | Remote'da artık bulunmayan remote-tracking ref'leri temizler. |
| `update [group]` | Bir veya daha fazla remote'u fetch eder. |
| `set-head <name> <branch>` | Remote'un varsayılan branch referansını ayarlar. |
|

### `git fetch`

Remote repository'deki yeni commit ve ref bilgilerini indirir. Varsayılan olarak mevcut branch'e merge etmez.

```powershell
git fetch
git fetch origin
git fetch --all --prune
```

| Seçenek | Açıklama |
|---|---|
| `--all` | Tanımlı remote'ların tamamından fetch yapar. |
| `-p`, `--prune` | Remote'da artık olmayan remote-tracking ref'leri temizler. |
| `--prune-tags` | Tag prune davranışını etkinleştirir. |
| `-t`, `--tags` | Tag'leri de indirir. |
| `--no-tags` | Otomatik tag indirmeyi kapatır. |
| `--force`, `-f` | Ref güncelleme kısıtlarını zorlayabilir. Dikkatli kullanılmalıdır. |
| `--dry-run` | Nelerin alınacağını gösterir, güncelleme yapmaz. |
| `--depth=<n>` | Geçmiş derinliğini sınırlar. |
| `--deepen=<n>` | Shallow geçmişi belirtilen miktarda genişletir. |
| `--shallow-since=<date>` | Belirtilen tarihten sonraki geçmişi alır. |
| `--unshallow` | Shallow repository'yi tam geçmişe çevirmeye çalışır. |
| `--filter=<filter-spec>` | Partial clone filtrelemesi uygular. |
| `--refetch` | Sunucu tarafından filtreleme/nesne değişimi gibi durumlarda yeniden fetch yapar. |
| `--jobs=<n>` | Submodule fetch işlemlerinin paralelliğini belirler. |
| `--recurse-submodules[=<mode>]` | Submodule'leri de fetch eder. |
| `--no-recurse-submodules` | Submodule fetch işlemini kapatır. |
| `--multiple` | Birden fazla repository kaynağını kullanır. |
| `--append` | FETCH_HEAD içeriğine ekleme yapar. |
| `--atomic` | Ref güncellemelerini atomik hale getirir. |

### `git pull`

Önce fetch yapar, sonra yapılandırmaya veya seçilen seçeneğe göre değişiklikleri mevcut branch'e entegre eder.

```powershell
git pull
git pull --rebase
git pull --ff-only
```

| Seçenek | Açıklama |
|---|---|
| `--rebase` | Fetch edilen değişiklikleri merge yerine rebase ile entegre eder. |
| `--no-rebase` | Merge yöntemiyle entegre eder. |
| `--ff-only` | Yalnızca fast-forward mümkünse ilerler. |
| `--ff` / `--no-ff` | Fast-forward davranışını kontrol eder. |
| `--autostash` | Yerel değişiklikleri işlem öncesinde stash'e alır. |
| `--no-autostash` | Otomatik stash'i kapatır. |
| `--no-commit` | Merge tabanlı pull'da merge commit'ini otomatik oluşturmaz. |
| `--no-edit` | Merge mesajı editörünü açmaz. |
| `--stat` / `--no-stat` | Merge istatistiğini gösterir veya gizler. |
| `--log[=<n>]` | Merge mesajına commit özetlerini ekler. |
| `--all` | Tüm remote'lardan fetch davranışını kullanır. |
| `--tags`, `--no-tags` | Tag indirme davranışını kontrol eder. |
| `--prune` | Fetch sırasında eskimiş remote-tracking ref'leri temizler. |

### `git push`

Local commit ve ref'leri remote repository'ye gönderir.

```powershell
git push
git push -u origin main
git push origin feature/login
```

| Seçenek | Açıklama |
|---|---|
| `-u`, `--set-upstream` | Gönderilen branch için upstream ilişkisi kurar. |
| `-f`, `--force` | Remote geçmişini zorla güncelleyebilir; başkalarının commit'lerini erişilemez kılabilir. |
| `--force-with-lease[=<ref>:<expect>]` | Beklenen remote ref değeri değişmemişse force push yapar; `--force` seçeneğinden daha koruyucudur. |
| `--all` | Tüm branch'leri gönderir. |
| `--mirror` | Tüm refs'i aynalama mantığıyla gönderir. Çok dikkatli kullanılmalıdır. |
| `--tags` | Tüm tag'leri gönderir. |
| `--follow-tags` | Uygun annotated tag'leri commit'lerle birlikte gönderir. |
| `--delete` | Remote branch/ref siler. |
| `--prune` | Remote'da local karşılığı olmayan ref'leri temizler. |
| `--dry-run` | Gerçek push yapmadan sonucu gösterir. |
| `--atomic` | Sunucu destekliyorsa ref güncellemelerini atomik yapar. |
| `--no-verify` | `pre-push` hook'unu atlar. |
| `--receive-pack=<git-receive-pack>` | Sunucu tarafındaki receive-pack programını belirtir. |
| `--repo=<repository>` | Hedef repository'yi belirtir. |
| `--push-option=<string>` | Sunucuya ek push seçeneği gönderir. |
| `--signed=(true|false|if-asked)` | Push sertifikası imzalama davranışını belirler. |
| `-o <string>`, `--push-option=<string>` | Push option gönderir. |
| `--porcelain` | Script'ler için makine dostu çıktı verir. |

### `git ls-remote`

Remote repository'deki branch ve tag referanslarını, repository'yi clone etmeden listeler.

```powershell
git ls-remote origin
```

| Seçenek | Açıklama |
|---|---|
| `--heads` | Yalnızca branch referanslarını gösterir. |
| `--tags` | Yalnızca tag referanslarını gösterir. |
| `--refs` | Pseudoref'leri ve peeled tag kayıtlarını hariç tutar. |
| `--symref` | HEAD gibi symbolic ref hedeflerini gösterir. |
| `--get-url` | URL rewrite kurallarından sonra kullanılacak URL'yi gösterir. |
| `--exit-code` | Eşleşen ref bulunamazsa sıfır dışı çıkış kodu döndürür. |

### `git submodule`

Bir repository içinde başka Git repository'lerini alt modül olarak yönetir.

```powershell
git submodule add https://github.com/user/library.git libs/library
git submodule update --init --recursive
```

| Alt komut/seçenek | Açıklama |
|---|---|
| `add <url> [path]` | Yeni submodule ekler. |
| `status` | Submodule commit durumlarını listeler. |
| `init` | `.gitmodules` içindeki submodule URL/ayarlarını local config'e kopyalar. |
| `update` | Submodule'leri kayıtlı commit'lere getirir. |
| `--init` | Gerekirse submodule'leri başlatır. |
| `--recursive` | İç içe submodule'leri de işler. |
| `--remote` | Kayıtlı commit yerine remote branch'in güncel ucunu kullanır. |
| `--merge` | Submodule branch'ine merge ederek günceller. |
| `--rebase` | Submodule değişikliklerini rebase ederek günceller. |
| `sync` | Submodule URL'lerini `.gitmodules` ile senkronize eder. |
| `deinit` | Submodule çalışma klasörünü unregister eder. |
| `foreach <command>` | Her submodule içinde komut çalıştırır. |
| `absorbgitdirs` | Submodule Git klasörlerini ana repository'nin metadata düzenine taşır. |
| `summary` | Submodule commit farklarını özetler. |

---

## 11. Tag, stash ve worktree

### `git tag`

Commit'lere isim verir. Sıklıkla sürüm numaralarını (ör. `v1.0.0`) işaretlemek için kullanılır.

```powershell
git tag
git tag v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"
```

| Seçenek | Açıklama |
|---|---|
| `-a`, `--annotate` | Mesaj, tagger ve tarih içeren annotated tag oluşturur. |
| `-s`, `--sign` | İmzalı tag oluşturur. |
| `-u <keyid>`, `--local-user=<keyid>` | İmza için kullanılacak anahtarı belirtir. |
| `-m <message>`, `--message=<message>` | Tag mesajını belirtir; annotated tag oluşturur. |
| `-F <file>`, `--file=<file>` | Tag mesajını dosyadan alır. |
| `-d`, `--delete` | Tag'i siler. |
| `-f`, `--force` | Var olan tag'i yeniden oluşturur/günceller. |
| `-l`, `--list` | Tag'leri listeler. |
| `-n[<num>]` | Tag mesajının ilk satırlarını gösterir. |
| `-v`, `--verify` | Tag imzasını doğrular. |
| `--contains <commit>` | Belirtilen commit'i içeren tag'leri listeler. |
| `--no-contains <commit>` | Commit'i içermeyen tag'leri listeler. |
| `--merged[=<commit>]` | Belirtilen commit'e merge edilmiş tag'leri listeler. |
| `--sort=<key>` | Tag'leri belirtilen alana göre sıralar. |
| `--points-at=<object>` | Belirtilen nesneyi işaret eden tag'leri listeler. |
| `--column[=<options>]` | Liste çıktısını sütunlar halinde gösterir. |

### `git stash`

Commit oluşturmadan geçici olarak çalışma alanındaki değişiklikleri saklar. Branch değiştirmeden önce temiz çalışma alanı gerektiğinde yararlıdır.

```powershell
git stash push -m "Work in progress"
git stash list
git stash pop
```

| Alt komut/seçenek | Açıklama |
|---|---|
| `push` | Değişiklikleri stash'e kaydeder. |
| `-m <message>`, `--message=<message>` | Stash açıklaması ekler. |
| `-u`, `--include-untracked` | Untracked dosyaları da dahil eder. |
| `-a`, `--all` | Ignore edilenler dahil tüm untracked dosyaları da dahil eder. |
| `-k`, `--keep-index` | Stage edilmiş değişiklikleri staging area'da bırakır. |
| `--staged` | Yalnızca stage edilmiş değişiklikleri stash'e alır. |
| `-p`, `--patch` | Değişikliklerin seçilen parçalarını stash'e alır. |
| `list` | Stash kayıtlarını listeler. |
| `show -p` | Bir stash içindeki farkları gösterir. |
| `apply` | Stash'i uygular, stash kaydını silmez. |
| `pop` | Stash'i uygular ve başarılı olursa kaydı siler. |
| `drop` | Belirtilen stash kaydını siler. |
| `clear` | Tüm stash kayıtlarını siler. Geri dönüşü zordur. |
| `branch <name>` | Stash'teki değişikliklerden yeni branch oluşturur. |

### `git worktree`

Aynı repository'nin birden fazla working tree'sini farklı klasörlerde kullanmayı sağlar. Aynı anda birden fazla branch üzerinde çalışmak için yararlıdır.

```powershell
git worktree add ../App-Hotfix hotfix/login
git worktree list
```

| Alt komut/seçenek | Açıklama |
|---|---|
| `add <path> [branch]` | Yeni working tree oluşturur. |
| `add -b <branch> <path>` | Yeni branch oluşturarak worktree ekler. |
| `add --detach <path> <commit>` | Detached HEAD worktree oluşturur. |
| `list --porcelain` | Worktree'leri makine dostu biçimde listeler. |
| `remove <worktree>` | Worktree'yi kaldırır. |
| `remove --force` | Bazı koruma kontrollerini aşarak kaldırır. |
| `move <worktree> <new-path>` | Worktree konumunu değiştirir. |
| `prune` | Artık geçerli olmayan worktree metadata kayıtlarını temizler. |
| `lock [<worktree>]` | Worktree'nin otomatik prune edilmesini engeller. |
| `unlock <worktree>` | Worktree kilidini kaldırır. |
| `repair` | Worktree bağlantılarını düzeltir. |

---

## 12. Arama, hata ayıklama ve inceleme

### `git grep`

Git tarafından takip edilen dosyalarda metin arar.

```powershell
git grep "TODO"
git grep -n -i "product"
```

| Seçenek | Açıklama |
|---|---|
| `-n`, `--line-number` | Satır numarası gösterir. |
| `-i`, `--ignore-case` | Büyük/küçük harfe duyarsız arar. |
| `-w`, `--word-regexp` | Tam kelime eşleşmesi arar. |
| `-E`, `--extended-regexp` | Extended regular expression kullanır. |
| `-F`, `--fixed-strings` | Pattern'i regex değil sabit metin olarak ele alır. |
| `-e <pattern>` | Aranacak pattern'i belirtir. |
| `-I` | Binary dosyalarda arama yapmaz. |
| `-a`, `--text` | Binary içeriği metin gibi işler. |
| `-l`, `--files-with-matches` | Eşleşen dosya isimlerini gösterir. |
| `-L`, `--files-without-match` | Eşleşme bulunmayan dosyaları gösterir. |
| `-c`, `--count` | Dosya başına eşleşme sayısını gösterir. |
| `-v`, `--invert-match` | Eşleşmeyen satırları gösterir. |
| `-p`, `--show-function` | Eşleşmenin bulunduğu fonksiyon başlığını gösterir. |
| `--and`, `--or`, `--not`, `(`, `)` | Birden fazla pattern'i mantıksal ifadelerle birleştirir. |
| `--cached` | Working tree yerine index'teki içeriği arar. |
| `--untracked` | Untracked dosyaları da aramaya dahil eder. |
| `--no-index` | Git tarafından takip edilmeyen dosyalarda da arama yapar. |
| `--recurse-submodules` | Submodule'lerde de arama yapar. |

### `git blame`

Bir dosyanın satırlarının hangi commit ve author tarafından en son değiştirildiğini gösterir.

```powershell
git blame Program.cs
git blame -L 20,50 Program.cs
```

| Seçenek | Açıklama |
|---|---|
| `-L <start>,<end>` | Yalnızca belirtilen satır aralığını inceler. |
| `-w` | Whitespace değişikliklerini yok sayar. |
| `-M` | Aynı dosya içinde taşınmış kod satırlarını algılamaya çalışır. |
| `-C` | Diğer dosyalardan taşınmış satırları da algılamaya çalışır. Birden fazla `-C` algılama kapsamını artırabilir. |
| `-e`, `--show-email` | Author e-posta adresini gösterir. |
| `-f`, `--show-name` | Commit ile birlikte dosya adını gösterir. |
| `-n`, `--show-number` | Satır numarasını gösterir. |
| `--date=<format>` | Tarih formatını seçer. |
| `--root` | Root commit'lerdeki satırları da gösterir. |
| `--reverse <rev>..<rev>` | Bir satırın belirtilen aralıkta ne zaman ortaya çıktığını inceler. |
| `--first-parent` | Merge commit'lerde first-parent geçmişini kullanır. |

### `git bisect`

Bir bug'ın hangi commit ile ortaya çıktığını binary search mantığıyla bulur.

```powershell
git bisect start
git bisect bad
git bisect good <known-good-commit>
# Git'in seçtiği commit'i test et
git bisect good   # veya git bisect bad
git bisect reset
```

| Alt komut/seçenek | Açıklama |
|---|---|
| `start [bad] [good...]` | Bisect sürecini başlatır. |
| `bad [rev]` | Belirtilen commit'i hatalı işaretler. |
| `good [rev]` | Belirtilen commit'i hatasız işaretler. |
| `skip [rev...]` | Test edilemeyen commit'leri atlar. |
| `reset [rev]` | Bisect'i bitirir ve başlangıç konumuna döner. |
| `log` | Bisect kayıtlarını gösterir. |
| `replay <file>` | Daha önce kaydedilmiş bisect oturumunu tekrarlar. |
| `run <command> [args...]` | Her adımda test komutu çalıştırır; exit code'a göre good/bad kararı verir. |
| `visualize` / `view` | Commit grafik görünümünü açar. |

### `git range-diff`

İki commit aralığını karşılaştırır. Özellikle rebase öncesi ve sonrası commit serisinin nasıl değiştiğini incelemek için yararlıdır.

```powershell
git range-diff main...feature-old main...feature-new
```

Başlıca seçenekler: `--creation-factor=<n>` commit eşleştirmesinin benzerlik eşiğini ayarlar; `--no-dual-color` renkli karşılaştırmayı kapatır; `--diff-merges=<format>` merge commit farklarının gösterimini belirler; `--left-only` ve `--right-only` yalnızca ilgili taraftaki commit'leri gösterir. Tam seçenek listesi resmî sayfada bulunur.

---

## 13. Git nesneleri ve plumbing komutları

Bu komutlar Git'in iç veri modelini incelemek ve otomasyon yazmak için önemlidir. Geliştiricilerin günlük iş akışında her zaman kullanması gerekmez.

### `git cat-file`

Git object veritabanındaki nesnenin türünü, boyutunu veya içeriğini gösterir.

```powershell
git cat-file -t <object-id>   # Nesne türü: blob/tree/commit/tag
git cat-file -s <object-id>   # Nesne boyutu
git cat-file -p <object-id>   # Nesne içeriğini okunabilir biçimde gösterir
git cat-file -e <object-id>   # Nesne mevcut mu kontrol eder
```

| Seçenek | Açıklama |
|---|---|
| `-t` | Nesne türünü gösterir. |
| `-s` | Nesne boyutunu gösterir. |
| `-p` | Nesnenin içeriğini uygun biçimde pretty-print eder. |
| `-e` | Nesnenin var olup olmadığını kontrol eder; çıktı üretmeden exit code döndürebilir. |
| `--batch` | Nesne isteklerini standart girdiden toplu okur ve sonuçları yazar. |
| `--batch-check` | Toplu isteklerde nesne metadata bilgisini döndürür. |
| `--batch-all-objects` | Repository'deki tüm nesneleri toplu işler. |
| `--batch-command` | Batch komut protokolünü kullanır. |
| `--buffer` | Batch çıktısını buffer'lar. |
| `--unordered` | Batch all objects çıktısında sıralama garantisi olmadan verimliliği artırabilir. |
| `--filters` | Dosya içeriğini checkout filtrelerinden geçirir. |
| `--textconv` | Tanımlı textconv filtresini kullanır. |
| `--use-mailmap` | Author/committer kimliklerini mailmap'e göre dönüştürür. |
| `--follow-symlinks` | Tree içinden symlink takip davranışını etkinleştirir. |

PowerShell notu: `^{tree}` gibi revizyon ifadelerini tırnak içine al:

```powershell
git cat-file -p "HEAD^{tree}"
```

### `git ls-tree`

Bir tree nesnesinin dosya ve klasör kayıtlarını listeler.

```powershell
git ls-tree HEAD
git ls-tree -r HEAD
```

| Seçenek | Açıklama |
|---|---|
| `-r` | Alt klasörleri özyinelemeli listeler. |
| `-d` | Yalnızca tree/klasör kayıtlarını listeler. |
| `-t` | Tree kayıtlarını da gösterir. |
| `-l`, `--long` | Nesne boyutunu gösterir. |
| `--full-tree` | Pathspec ifadelerini mevcut alt klasör yerine repository köküne göre yorumlar. |
| `--object-only` | Yalnızca object ID değerlerini listeler. |
| `--format=<format>` | Tree girdileri için özel çıktı formatı tanımlar. |
| `--name-only` | Yalnızca isimleri gösterir. |
| `--name-status` | İsim ve durum biçimini gösterir. |
| `--full-name` | Tam repository path'ini gösterir. |
| `-z` | Satır sonu yerine NUL ayıracı kullanır. |
| `--abbrev[=<n>]` | Object ID'lerini kısaltır. |

### `git ls-files`

Index ve working tree içindeki dosyaları listeler.

```powershell
git ls-files
git ls-files --stage
git ls-files --others --exclude-standard
```

| Seçenek | Açıklama |
|---|---|
| `-s`, `--stage` | Mode, object ID ve stage numarası gösterir. |
| `-u`, `--unmerged` | Merge conflict durumundaki dosyaları gösterir. |
| `-o`, `--others` | Index'te olmayan dosyaları gösterir. |
| `-c`, `--cached` | Index'teki dosyaları gösterir. |
| `-m`, `--modified` | Working tree'de değişmiş dosyaları gösterir. |
| `-d`, `--deleted` | Silinmiş dosyaları gösterir. |
| `-k`, `--killed` | Checkout işlemini engelleyebilecek dosyaları gösterir. |
| `-i`, `--ignored` | Ignore edilen dosyaları listeler; genellikle `--others` ile birlikte kullanılır. |
| `--exclude-standard` | Standart ignore kaynaklarını kullanır. |
| `--full-name` | Repository köküne göre tam isim verir. |
| `-z` | NUL ile ayrılmış çıktı verir. |
| `--eol` | Dosyaların EOL/line ending bilgilerini gösterir. |
| `--debug` | Index kayıtları için ek debug bilgisi verir. |

### `git hash-object`

Bir dosya veya standart girdinin Git object ID'sini hesaplar; `-w` ile nesneyi object database'e yazar.

```powershell
git hash-object Program.cs
git hash-object -w Program.cs
```

| Seçenek | Açıklama |
|---|---|
| `-w` | Hesaplanan nesneyi `.git/objects` veritabanına yazar. |
| `-t <type>` | Nesne türünü belirtir; varsayılan blob'dur. |
| `-p` | İçeriği standart girdiden okur. |
| `--stdin` | Dosya yerine standart girdiyi kullanır. |
| `--stdin-paths` | Standart girdiden dosya yolları okur. |
| `--path=<path>` | Filter/attribute uygulamak için dosya yolu bağlamı verir. |
| `--no-filters` | Clean filter'ları çalıştırmaz. |
| `--literally` | Nesne doğrulama kontrollerini esneterek ham nesne yazılmasına izin verir; ileri seviye kullanım içindir. |

### `git write-tree`

Index'in mevcut içeriğinden tree nesnesi oluşturur ve tree hash'ini döndürür.

```powershell
git write-tree
```

### `git read-tree`

Bir tree nesnesinin içeriğini index'e yükler. Merge ve checkout mekanizmalarının alt seviyesinde kullanılır.

```powershell
git read-tree HEAD
```

Önemli seçenekler: `-m` merge modunu kullanır; `--reset` index'i tree'ye göre sıfırlar; `-u` working tree güncellemesini de ister; `--prefix=<prefix>` tree içeriğini belirtilen path prefix altına yerleştirir; `--empty` boş tree kullanır.

### `git commit-tree`

Verilen tree ve parent commit referansları üzerinden doğrudan commit nesnesi üretir. Normalde `git commit` kullanılır; bu komut düşük seviyeli script/araçlar içindir.

```powershell
git commit-tree <tree-id> -p <parent-id> -m "Message"
```

Başlıca seçenekler: `-p <parent>` parent commit ekler; `-m <message>` mesaj ekler; `-F <file>` mesajı dosyadan alır; `-S[<keyid>]` imza kullanır; `--no-gpg-sign` imzalamayı kapatır.

### `git rev-parse`

Revision ve path ifadelerini çözümler; repository yollarını ve durumunu sorgular. Script yazarken çok kullanılır.

```powershell
git rev-parse HEAD
git rev-parse --show-toplevel
git rev-parse --is-inside-work-tree
```

| Seçenek | Açıklama |
|---|---|
| `--show-toplevel` | Working tree'nin kök klasörünü gösterir. |
| `--show-prefix` | Mevcut klasörün repository köküne göre göreli yolunu gösterir. |
| `--show-cdup` | Repository köküne çıkmak için gereken `../` yolunu gösterir. |
| `--git-dir` | Git metadata klasörünün yolunu gösterir. |
| `--git-common-dir` | Worktree'lerin paylaştığı ortak Git metadata yolunu gösterir. |
| `--is-inside-work-tree` | Working tree içinde olup olmadığını true/false olarak gösterir. |
| `--is-inside-git-dir` | Git metadata alanı içinde olup olmadığını gösterir. |
| `--is-bare-repository` | Bare repository olup olmadığını gösterir. |
| `--verify` | Tek bir revision parametresinin geçerli olduğunu doğrular. |
| `--short[=<length>]` | Object ID'yi kısaltır. |
| `--abbrev-ref` | Revision'ı branch/ref adı olarak kısaltılmış biçimde gösterir. |
| `--symbolic-full-name` | Revision'ın sembolik tam ref adını gösterir. |
| `--show-object-format` | Repository'nin object formatını gösterir. |
| `--default <arg>` | Bazı çözümleme işlemlerinde varsayılan argüman sağlar. |
| `--sq` / `--sq-quote` | Çıktıyı shell quoting kurallarına göre tek satırda üretir/quote eder. |

### `git show-ref`

Local repository'deki branch, tag ve diğer refs listesini gösterir.

```powershell
git show-ref
git show-ref --heads
git show-ref --tags
```

Önemli seçenekler: `--heads` yalnızca branch'leri, `--tags` tag'leri gösterir; `--verify` tam ref adını doğrular; `--quiet` çıktıyı bastırır; `--dereference` annotated tag hedefini de gösterir; `--exclude-existing` daha önce listelenmiş ref'leri filtrelemeye yardımcı olur.

### `git for-each-ref`

Ref listesini özel formatla üretir. Otomasyonlarda branch/tag metadata'sını raporlamak için kullanılır.

```powershell
git for-each-ref --format="%(refname:short) %(objectname:short)" refs/heads/
```

Önemli seçenekler: `--format=<format>` çıktı şablonunu belirler; `--sort=<key>` sıralar; `--count=<n>` sonuç sayısını sınırlar; `--contains`, `--no-contains`, `--merged`, `--no-merged` commit ilişkisine göre filtreler; `--points-at=<object>` nesneyi gösteren ref'leri bulur; `--color[=<when>]` renk davranışını ayarlar.

### `git merge-base`

İki veya daha fazla commit geçmişinin ortak ancestor'ını bulur.

```powershell
git merge-base main feature
```

Başlıca seçenekler: `--all` tüm en iyi ortak ancestor'ları verir; `--octopus` çoklu merge için ortak base arar; `--is-ancestor <A> <B>` A'nın B'nin ancestor'ı olup olmadığını kontrol eder; `--fork-point <ref> <commit>` fork-point mantığını kullanır.

### `git check-ignore`

Bir dosyanın neden ignore edildiğini incelemek için kullanılır.

```powershell
git check-ignore -v bin/Debug/file.dll
```

Başlıca seçenekler: `-v` eşleşen ignore kuralının kaynağını gösterir; `-n` eşleşme olmayan dosyalar için de kayıt üretir; `-q` çıktı vermeden exit code döndürür; `-z` NUL ayraçlı çıktı üretir; `--no-index` index'te bulunan dosyaları kontrol dışı bırakmaz.

---

## 14. Repository bakım ve yönetim komutları

### `git reflog`

HEAD ve ref'lerin zaman içindeki yerel hareket kayıtlarını gösterir. Yanlış reset/rebase sonrasında eski commit'e ulaşmak için çok değerlidir.

```powershell
git reflog
git reflog show main
```

Alt komutlar ve seçenekler: `show` kayıtları listeler; `expire` eski kayıtları temizler; `delete` kayıt siler; `exists` bir ref için reflog olup olmadığını kontrol eder; `--all` tüm reflog'ları listeler; `--date=<format>` tarih biçimini değiştirir; `--expire=<time>` ve `--expire-unreachable=<time>` kayıt saklama eşiklerini belirtir.

### `git gc`

Repository nesnelerini ve metadata'yı optimize eder; gereksiz/ulaşılamayan nesneleri temizleme ve packfile oluşturma işlemleri yapabilir.

```powershell
git gc
git gc --auto
```

Önemli seçenekler: `--auto` gerektiğinde bakım yapar; `--aggressive` daha yoğun optimizasyon uygular ve daha uzun sürebilir; `--prune=<date>` erişilemeyen nesnelerin ne zaman temizleneceğini belirler; `--no-prune` prune işlemini kapatır; `--quiet` çıktıyı azaltır; `--force` başka Git işlemleri sürüyor olsa da çalıştırmayı zorlayabilir.

### `git maintenance`

Repository bakım görevlerini yönetir. Büyük repository'lerde otomatik bakım yapılandırmak için kullanılır.

```powershell
git maintenance start
git maintenance run --task=gc
```

Başlıca alt komutlar: `run` bakım görevlerini çalıştırır; `start` zamanlanmış bakım başlatır; `stop` zamanlanmış bakımı durdurur; `register` repository'yi bakım kapsamına alır; `unregister` kapsamdan çıkarır; `unregister --force` daha güçlü kaldırma davranışı sağlar. `--task=<task>` belirli bir bakım görevini seçer; `--schedule=<frequency>` zamanlama belirler.

### `git fsck`

Object database'in bütünlüğünü kontrol eder; bozuk veya erişilemeyen nesneleri raporlar.

```powershell
git fsck --full
```

Önemli seçenekler: `--full` packfile ve alternatif object store dahil tam kontrol yapar; `--unreachable` erişilemeyen nesneleri raporlar; `--dangling` dangling nesneleri gösterir; `--no-reflogs` reflog referanslarını erişilebilirlik hesabında yok sayar; `--lost-found` bulunan erişilemeyen nesneleri `.git/lost-found` altına yazar; `--connectivity-only` içerik hash doğrulaması yerine bağlantı kontrolüne odaklanır; `--strict` daha katı kontroller uygular.

### `git count-objects`

Loose object sayısını ve disk kullanımını gösterir.

```powershell
git count-objects -v -H
```

- `-v`, `--verbose`: Packfile ve loose object ayrıntılarını verir.
- `-H`, `--human-readable`: Boyutları okunabilir birimlerle gösterir.

### `git archive`

Bir commit/tree içeriğini ZIP veya tar arşivine dönüştürür.

```powershell
git archive --format=zip --output=source.zip HEAD
```

Başlıca seçenekler: `--format=<fmt>` arşiv türünü seçer; `--output=<file>` dosyaya yazar; `--prefix=<prefix>` arşiv içindeki kök klasör adını belirler; `-v` dosyaları listeler; `--worktree-attributes` working tree `.gitattributes` bilgisini kullanır; `--remote=<repo>` uzak repository'den archive alır.

### `git bundle`

Repository nesne ve ref'lerini tek bir bundle dosyasında paketler. Ağ bağlantısı olmadan repository taşımaya yarar.

```powershell
git bundle create repo.bundle --all
git bundle list-heads repo.bundle
git bundle verify repo.bundle
```

Alt komutlar: `create` bundle oluşturur; `list-heads` içindeki ref'leri listeler; `verify` bundle'ın geçerliliğini kontrol eder; `unbundle` nesneleri mevcut repository'ye aktarır. `create` sırasında `--all`, `--branches`, `--tags`, `--stdin`, `--version=<n>` gibi seçenekler kullanılabilir.

---

## 15. Resmî komut envanteri

Aşağıdaki liste, Git'in resmî referansında yer alan ana komut ailelerini kapsar. Her komutun tam sözdizimi ve tüm seçenekleri için bağlantıyı aç. Bazı komutlar platform paketine, build seçeneğine veya Git kurulumuna göre bulunmayabilir. Ayrıca `PATH` üzerinde `git-<komut>` adıyla kurulu harici alt komutlar ve kullanıcı alias'ları da çalıştırılabilir.

### 15.1 Ana porcelain komutları

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git add` | İçeriği index'e ekler. | https://git-scm.com/docs/git-add |
| `git am` | Mailbox formatındaki patch serisini uygular. | https://git-scm.com/docs/git-am |
| `git archive` | Tree/commit içeriğinden arşiv oluşturur. | https://git-scm.com/docs/git-archive |
| `git backfill` | Partial clone'daki eksik nesneleri indirir. | https://git-scm.com/docs/git-backfill |
| `git bisect` | Hatanın ortaya çıktığı commit'i binary search ile bulur. | https://git-scm.com/docs/git-bisect |
| `git branch` | Branch listeler, oluşturur, siler ve yeniden adlandırır. | https://git-scm.com/docs/git-branch |
| `git bundle` | Repository nesnelerini bundle dosyasına aktarır. | https://git-scm.com/docs/git-bundle |
| `git checkout` | Branch değiştirir veya dosyaları geri yükler. | https://git-scm.com/docs/git-checkout |
| `git cherry-pick` | Mevcut commit değişikliklerini başka branch'e uygular. | https://git-scm.com/docs/git-cherry-pick |
| `git citool` | Commit için grafik arayüz açar. | https://git-scm.com/docs/git-citool |
| `git clean` | Untracked dosyaları temizler. | https://git-scm.com/docs/git-clean |
| `git clone` | Repository kopyalar. | https://git-scm.com/docs/git-clone |
| `git commit` | Stage edilmiş değişiklikleri commit eder. | https://git-scm.com/docs/git-commit |
| `git describe` | Commit için tag tabanlı okunabilir isim üretir. | https://git-scm.com/docs/git-describe |
| `git diff` | Değişiklikler arasındaki farkı gösterir. | https://git-scm.com/docs/git-diff |
| `git fetch` | Remote nesne ve ref'lerini indirir. | https://git-scm.com/docs/git-fetch |
| `git format-patch` | Commit'lerden e-posta ile gönderilebilir patch üretir. | https://git-scm.com/docs/git-format-patch |
| `git gc` | Repository'yi optimize eder ve bakım yapar. | https://git-scm.com/docs/git-gc |
| `git grep` | Dosya içeriklerinde metin arar. | https://git-scm.com/docs/git-grep |
| `git gui` | Git için grafik arayüzdür. | https://git-scm.com/docs/git-gui |
| `git history` | Deneysel geçmiş yeniden yazma komutudur. | https://git-scm.com/docs/git-history |
| `git init` | Yeni repository oluşturur. | https://git-scm.com/docs/git-init |
| `git log` | Commit geçmişini listeler. | https://git-scm.com/docs/git-log |
| `git maintenance` | Repository bakım görevlerini yönetir. | https://git-scm.com/docs/git-maintenance |
| `git merge` | Branch geçmişlerini birleştirir. | https://git-scm.com/docs/git-merge |
| `git mv` | Dosya/klasör taşır veya yeniden adlandırır. | https://git-scm.com/docs/git-mv |
| `git notes` | Git nesnelerine ek notlar ekler veya gösterir. | https://git-scm.com/docs/git-notes |
| `git pull` | Fetch yapar ve sonucu mevcut branch'e entegre eder. | https://git-scm.com/docs/git-pull |
| `git push` | Commit/ref'leri remote'a gönderir. | https://git-scm.com/docs/git-push |
| `git range-diff` | İki commit aralığını karşılaştırır. | https://git-scm.com/docs/git-range-diff |
| `git rebase` | Commit'leri başka tabanın üzerine yeniden uygular. | https://git-scm.com/docs/git-rebase |
| `git reset` | HEAD ve/veya index durumunu değiştirir. | https://git-scm.com/docs/git-reset |
| `git restore` | Working tree veya index dosyalarını geri yükler. | https://git-scm.com/docs/git-restore |
| `git revert` | Bir commit'in etkisini yeni commit ile tersine alır. | https://git-scm.com/docs/git-revert |
| `git rm` | Dosyaları working tree ve index'ten kaldırır. | https://git-scm.com/docs/git-rm |
| `git shortlog` | Commit log'unu author bazında özetler. | https://git-scm.com/docs/git-shortlog |
| `git show` | Commit/tag/tree/blob nesnelerini gösterir. | https://git-scm.com/docs/git-show |
| `git sparse-checkout` | Working tree'de yalnızca seçilen path'leri tutar. | https://git-scm.com/docs/git-sparse-checkout |
| `git stash` | Çalışma alanı değişikliklerini geçici saklar. | https://git-scm.com/docs/git-stash |
| `git status` | Working tree ve index durumunu gösterir. | https://git-scm.com/docs/git-status |
| `git submodule` | Alt repository'leri yönetir. | https://git-scm.com/docs/git-submodule |
| `git switch` | Branch değiştirir. | https://git-scm.com/docs/git-switch |
| `git tag` | Tag oluşturur, listeler, siler ve doğrular. | https://git-scm.com/docs/git-tag |
| `git worktree` | Bir repository için birden fazla working tree yönetir. | https://git-scm.com/docs/git-worktree |
| `gitk` | Repository geçmişini grafik olarak gösterir. | https://git-scm.com/docs/gitk |
| `scalar` | Büyük Git repository'lerini yönetmek için yardımcı araçtır. | https://git-scm.com/docs/scalar |

### 15.2 Yapılandırma, yönetim ve yardımcı porcelain komutları

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git config` | Ayarları okur ve düzenler. | https://git-scm.com/docs/git-config |
| `git fast-export` | Git geçmişini fast-import formatına aktarır. | https://git-scm.com/docs/git-fast-export |
| `git fast-import` | Hızlı repository import işlemlerinin backend'idir. | https://git-scm.com/docs/git-fast-import |
| `git filter-branch` | Branch geçmişini filtreleyerek yeniden yazar; yeni işlerde genellikle `git filter-repo` gibi harici araçlar tercih edilir. | https://git-scm.com/docs/git-filter-branch |
| `git mergetool` | Harici merge conflict çözüm aracını çalıştırır. | https://git-scm.com/docs/git-mergetool |
| `git pack-refs` | Branch ve tag ref'lerini paketler. | https://git-scm.com/docs/git-pack-refs |
| `git prune` | Erişilemeyen loose nesneleri temizler. | https://git-scm.com/docs/git-prune |
| `git reflog` | Ref hareket geçmişini yönetir. | https://git-scm.com/docs/git-reflog |
| `git refs` | Refs üzerinde düşük seviyeli işlemler yapar. | https://git-scm.com/docs/git-refs |
| `git remote` | Remote repository tanımlarını yönetir. | https://git-scm.com/docs/git-remote |
| `git repack` | Object'leri packfile içinde yeniden paketler. | https://git-scm.com/docs/git-repack |
| `git replace` | Bir nesne yerine başka nesne kullanılmasını sağlayan replace ref oluşturur. | https://git-scm.com/docs/git-replace |
| `git annotate` | Dosya satırlarını commit bilgileriyle işaretler; `blame` ile benzer amaçtadır. | https://git-scm.com/docs/git-annotate |
| `git blame` | Satırların son değiştirilme bilgisini gösterir. | https://git-scm.com/docs/git-blame |
| `git bugreport` | Hata raporu için tanılama bilgisi toplar. | https://git-scm.com/docs/git-bugreport |
| `git count-objects` | Object sayısı ve disk kullanımını gösterir. | https://git-scm.com/docs/git-count-objects |
| `git diagnose` | Tanılama bilgilerini ZIP arşivine toplar. | https://git-scm.com/docs/git-diagnose |
| `git difftool` | Harici diff aracıyla fark gösterir. | https://git-scm.com/docs/git-difftool |
| `git fsck` | Object database bütünlüğünü kontrol eder. | https://git-scm.com/docs/git-fsck |
| `git help` | Yardım sayfalarını açar. | https://git-scm.com/docs/git-help |
| `git instaweb` | Repository'yi gitweb üzerinden yerel web arayüzünde sunar. | https://git-scm.com/docs/git-instaweb |
| `git merge-tree` | Working tree/index'e dokunmadan merge sonucu hesaplar. | https://git-scm.com/docs/git-merge-tree |
| `git rerere` | Daha önce çözülmüş conflict'leri tekrar kullanır. | https://git-scm.com/docs/git-rerere |
| `git show-branch` | Branch'leri ve commit ilişkilerini gösterir. | https://git-scm.com/docs/git-show-branch |
| `git verify-commit` | Commit imzasını doğrular. | https://git-scm.com/docs/git-verify-commit |
| `git verify-tag` | Tag imzasını doğrular. | https://git-scm.com/docs/git-verify-tag |
| `git version` | Git sürüm bilgisini gösterir. | https://git-scm.com/docs/git-version |
| `git whatchanged` | Commit'lerin getirdiği diff'lerle log gösterir; eski kullanım, çoğu durumda `git log` tercih edilir. | https://git-scm.com/docs/git-whatchanged |
| `gitweb` | Repository'ler için web arayüzü sağlar. | https://git-scm.com/docs/gitweb |

### 15.3 Harici sistemler ve e-posta tabanlı iş akışları

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git am` | Mailbox patch serisini uygular. | https://git-scm.com/docs/git-am |
| `git apply` | Patch'i dosyalara/index'e uygular. | https://git-scm.com/docs/git-apply |
| `git imap-send` | Patch e-postalarını IMAP klasörüne yollar. | https://git-scm.com/docs/git-imap-send |
| `git format-patch` | Commit'leri e-posta patch'lerine dönüştürür. | https://git-scm.com/docs/git-format-patch |
| `git send-email` | Patch'leri e-posta olarak gönderir. | https://git-scm.com/docs/git-send-email |
| `git request-pull` | Upstream'e gönderilecek pull özeti oluşturur. | https://git-scm.com/docs/git-request-pull |
| `git svn` | Git ile Subversion arasında çift yönlü çalışma sağlar. | https://git-scm.com/docs/git-svn |
| `git p4` | Perforce ile import/submit entegrasyonu sağlar. | https://git-scm.com/docs/git-p4 |
| `git cvsimport` | CVS repository geçmişini Git'e aktarır. | https://git-scm.com/docs/git-cvsimport |
| `git cvsserver` | Git repository'leri için CVS server emülasyonu sağlar. | https://git-scm.com/docs/git-cvsserver |
| `git cvsexportcommit` | Bir commit'i CVS checkout alanına aktarır. | https://git-scm.com/docs/git-cvsexportcommit |
| `git archimport` | GNU Arch repository'sini Git'e aktarır. | https://git-scm.com/docs/git-archimport |
| `git quiltimport` | Quilt patch set'ini Git branch'ine uygular. | https://git-scm.com/docs/git-quiltimport |

### 15.4 Server ve transport komutları

Bunlar çoğunlukla server yönetimi veya Git'in transport altyapısı tarafından kullanılır.

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git daemon` | Git protokolü üzerinden basit repository sunucusu çalıştırır. | https://git-scm.com/docs/git-daemon |
| `git update-server-info` | Dumb HTTP server'lar için yardımcı metadata'yı günceller. | https://git-scm.com/docs/git-update-server-info |
| `git fetch-pack` | Başka repository'den eksik nesneleri alır. | https://git-scm.com/docs/git-fetch-pack |
| `git send-pack` | Git protokolüyle nesneleri gönderir. | https://git-scm.com/docs/git-send-pack |
| `git http-backend` | HTTP üzerinden Git server backend'idir. | https://git-scm.com/docs/git-http-backend |
| `git http-fetch` | HTTP ile remote repository'den indirme yapan yardımcıdır. | https://git-scm.com/docs/git-http-fetch |
| `git http-push` | HTTP/DAV üzerinden push yapan yardımcıdır. | https://git-scm.com/docs/git-http-push |
| `git receive-pack` | Sunucu tarafında push edilen nesneleri alır. | https://git-scm.com/docs/git-receive-pack |
| `git upload-pack` | Fetch/clone için nesne paketlerini sunar. | https://git-scm.com/docs/git-upload-pack |
| `git upload-archive` | Remote archive isteğini karşılar. | https://git-scm.com/docs/git-upload-archive |
| `git shell` | Git-only SSH erişimi için kısıtlı shell sağlar. | https://git-scm.com/docs/git-shell |

### 15.5 Plumbing — nesne/index manipülasyonu

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git apply` | Patch'i dosya/index'e uygular. | https://git-scm.com/docs/git-apply |
| `git checkout-index` | Index'teki dosyaları working tree'ye çıkarır. | https://git-scm.com/docs/git-checkout-index |
| `git commit-graph` | Commit-graph dosyalarını üretir/doğrular. | https://git-scm.com/docs/git-commit-graph |
| `git commit-tree` | Doğrudan commit nesnesi oluşturur. | https://git-scm.com/docs/git-commit-tree |
| `git hash-object` | Nesne hash'i hesaplar ve isteğe bağlı yazar. | https://git-scm.com/docs/git-hash-object |
| `git index-pack` | Packfile için index oluşturur. | https://git-scm.com/docs/git-index-pack |
| `git merge-file` | Üç yönlü dosya merge işlemi yapar. | https://git-scm.com/docs/git-merge-file |
| `git merge-index` | Merge gerektiren dosyalar için merge aracı çalıştırır. | https://git-scm.com/docs/git-merge-index |
| `git mktag` | Tag nesnesi oluşturur ve doğrular. | https://git-scm.com/docs/git-mktag |
| `git mktree` | ls-tree formatındaki girdiden tree nesnesi oluşturur. | https://git-scm.com/docs/git-mktree |
| `git multi-pack-index` | Multi-pack index oluşturur/doğrular. | https://git-scm.com/docs/git-multi-pack-index |
| `git pack-objects` | Nesneleri packfile'a dönüştürür. | https://git-scm.com/docs/git-pack-objects |
| `git prune-packed` | Packfile içinde zaten bulunan fazla loose object'leri temizler. | https://git-scm.com/docs/git-prune-packed |
| `git read-tree` | Tree bilgisini index'e okur. | https://git-scm.com/docs/git-read-tree |
| `git replay` | Deneysel olarak commit'leri yeni tabanda yeniden oynatır. | https://git-scm.com/docs/git-replay |
| `git symbolic-ref` | Symbolic ref okur/değiştirir/siler. | https://git-scm.com/docs/git-symbolic-ref |
| `git unpack-objects` | Pack arşivindeki nesneleri açar. | https://git-scm.com/docs/git-unpack-objects |
| `git update-index` | Index kayıtlarını düşük seviyede günceller. | https://git-scm.com/docs/git-update-index |
| `git update-ref` | Ref değerlerini güvenli biçimde günceller. | https://git-scm.com/docs/git-update-ref |
| `git write-tree` | Index'ten tree nesnesi oluşturur. | https://git-scm.com/docs/git-write-tree |

### 15.6 Plumbing — nesneleri sorgulama ve karşılaştırma

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git cat-file` | Nesne içeriği ve metadata'sını gösterir. | https://git-scm.com/docs/git-cat-file |
| `git cherry` | Upstream'e henüz uygulanmamış commit'leri bulur. | https://git-scm.com/docs/git-cherry |
| `git diff-files` | Working tree ile index'i karşılaştırır. | https://git-scm.com/docs/git-diff-files |
| `git diff-index` | Tree ile index/working tree'yi karşılaştırır. | https://git-scm.com/docs/git-diff-index |
| `git diff-pairs` | Verilen blob çiftlerini karşılaştırır. | https://git-scm.com/docs/git-diff-pairs |
| `git diff-tree` | İki tree nesnesinin farklarını karşılaştırır. | https://git-scm.com/docs/git-diff-tree |
| `git for-each-ref` | Ref'leri formatlı listeler. | https://git-scm.com/docs/git-for-each-ref |
| `git for-each-repo` | Repository listesi üzerinde komut çalıştırır. | https://git-scm.com/docs/git-for-each-repo |
| `git get-tar-commit-id` | Git archive tar dosyasından commit ID çıkarır. | https://git-scm.com/docs/git-get-tar-commit-id |
| `git last-modified` | Deneysel dosya son değiştirilme bilgisini gösterir. | https://git-scm.com/docs/git-last-modified |
| `git ls-files` | Index/working tree dosyalarını listeler. | https://git-scm.com/docs/git-ls-files |
| `git ls-remote` | Remote ref'lerini listeler. | https://git-scm.com/docs/git-ls-remote |
| `git ls-tree` | Tree içeriğini listeler. | https://git-scm.com/docs/git-ls-tree |
| `git merge-base` | Ortak ancestor bulur. | https://git-scm.com/docs/git-merge-base |
| `git name-rev` | Revision için sembolik isim bulur. | https://git-scm.com/docs/git-name-rev |
| `git pack-redundant` | Fazla packfile'ları bulur; eski/özel kullanım içindir. | https://git-scm.com/docs/git-pack-redundant |
| `git rev-list` | Commit nesnelerini listeler. | https://git-scm.com/docs/git-rev-list |
| `git rev-parse` | Revision ve repository parametrelerini çözümler. | https://git-scm.com/docs/git-rev-parse |
| `git show-index` | Pack index içeriğini gösterir. | https://git-scm.com/docs/git-show-index |
| `git show-ref` | Local ref'leri listeler. | https://git-scm.com/docs/git-show-ref |
| `git unpack-file` | Blob içeriğini geçici dosyaya çıkarır. | https://git-scm.com/docs/git-unpack-file |
| `git var` | Git'in bazı mantıksal değişkenlerini gösterir. | https://git-scm.com/docs/git-var |
| `git verify-pack` | Packfile/index bütünlüğünü doğrular. | https://git-scm.com/docs/git-verify-pack |

### 15.7 Dahili yardımcı komutlar

Bu komutlar çoğunlukla başka Git komutları veya script'ler tarafından çağrılır; günlük kullanıcı komutları değildir.

| Komut | Ne işe yarar | Resmî dokümantasyon |
|---|---|---|
| `git check-attr` | Dosya için gitattributes değerlerini gösterir. | https://git-scm.com/docs/git-check-attr |
| `git check-ignore` | Ignore kurallarını inceler. | https://git-scm.com/docs/git-check-ignore |
| `git check-mailmap` | Kişi isim/e-posta bilgilerini mailmap'e göre normalize eder. | https://git-scm.com/docs/git-check-mailmap |
| `git check-ref-format` | Ref adının geçerli olup olmadığını kontrol eder. | https://git-scm.com/docs/git-check-ref-format |
| `git column` | Veriyi sütunlar halinde biçimlendirir. | https://git-scm.com/docs/git-column |
| `git credential` | Credential helper protokolüyle kimlik bilgisi yönetir. | https://git-scm.com/docs/git-credential |
| `git credential-cache` | Kimlik bilgisini geçici olarak bellekte tutar. | https://git-scm.com/docs/git-credential-cache |
| `git credential-store` | Kimlik bilgisini diskte saklar; düz metin güvenlik riski vardır. | https://git-scm.com/docs/git-credential-store |
| `git fmt-merge-msg` | Merge commit mesajı oluşturur. | https://git-scm.com/docs/git-fmt-merge-msg |
| `git hook` | Git hook'larını çalıştırır/yönetir. | https://git-scm.com/docs/git-hook |
| `git interpret-trailers` | Commit mesajı trailer metadata'sını okur/düzenler. | https://git-scm.com/docs/git-interpret-trailers |
| `git mailinfo` | E-posta mesajından patch ve author bilgisini çıkarır. | https://git-scm.com/docs/git-mailinfo |
| `git mailsplit` | Mbox içeriğini ayrı e-posta dosyalarına böler. | https://git-scm.com/docs/git-mailsplit |
| `git merge-one-file` | Tek dosyalık standart merge helper'ıdır. | https://git-scm.com/docs/git-merge-one-file |
| `git patch-id` | Patch için benzersiz/karşılaştırılabilir kimlik üretir. | https://git-scm.com/docs/git-patch-id |
| `git sh-i18n` | Git shell script'leri için internationalization helper'ıdır. | https://git-scm.com/docs/git-sh-i18n |
| `git sh-setup` | Git shell script'leri için ortak kurulum kodudur. | https://git-scm.com/docs/git-sh-setup |
| `git stripspace` | Gereksiz whitespace'i temizler. | https://git-scm.com/docs/git-stripspace |

---

## 16. Sık kullanılan iş akışları

### Yeni bir repository başlatma

```powershell
mkdir MyProject
cd MyProject
git init
git config user.name "Taner Saydam"
git config user.email "email@example.com"
```

### İlk commit

```powershell
git status
git add .
git status --short
git commit -m "Initial commit"
git log --oneline
```

### GitHub'a ilk push

```powershell
git remote add origin https://github.com/user/project.git
git branch -M main
git push -u origin main
```

### Yeni feature branch

```powershell
git switch main
git pull --ff-only
git switch -c feature/new-feature
# Değişiklikleri yap
git add .
git commit -m "Add new feature"
git push -u origin feature/new-feature
```

### Son commit'i düzenleme

```powershell
git add ChangedFile.cs
git commit --amend
```

> Commit daha önce remote'a push edildiyse `--amend` geçmişi değiştirir. Ekip ile koordinasyon olmadan force push yapma.

### Son commit'i kaldırıp dosyaları stage alanında bırakma

```powershell
git reset --soft HEAD~1
```

### Son commit'i kaldırıp değişiklikleri working tree'de bırakma

```powershell
git reset HEAD~1
```

### Son commit'i tersine alan yeni commit oluşturma

```powershell
git revert HEAD
```

### Geçici olarak değişiklikleri saklama

```powershell
git stash push -m "Temporary work"
git switch main
git switch feature/other-task
git stash pop
```

### Yanlışlıkla branch/commit kaybedildiğinde geçmişi inceleme

```powershell
git reflog
git show <reflog-commit-id>
# Gerekirse kurtarma branch'i oluştur
git switch -c recovery/branch <commit-id>
```

---

## 17. Dikkat edilmesi gereken seçenekler

| Komut/seçenek | Risk | Açıklama |
|---|---|---|
| `git reset --hard` | Yüksek | Stage edilmemiş ve stage edilmiş değişiklikleri silebilir. |
| `git clean -fd` | Yüksek | Untracked dosya/klasörleri diskten siler. Önce `git clean -nd` çalıştır. |
| `git clean -fdx` | Çok yüksek | Ignore edilmiş dosyaları da siler; build çıktıları ve yerel ayarlar gidebilir. |
| `git push --force` | Yüksek | Remote branch geçmişini başkalarının commit'lerini kaybettirecek şekilde değiştirebilir. |
| `git branch -D` | Orta/Yüksek | Merge edilmemiş branch referansını zorla siler. |
| `git stash clear` | Yüksek | Bütün stash kayıtlarını siler. |
| `git gc --prune=now` | İleri seviye | Erişilemeyen nesneleri hemen temizleyebilir; kurtarma ihtimalini azaltabilir. |
| `git filter-branch` | Yüksek | Geçmişi yeniden yazar; resmî dokümantasyon performans ve güvenlik notları içerir. |
| `git commit --amend` | Paylaşılmış geçmişte riskli | Son commit'in kimliğini değiştirir. |

Özellikle reset/restore/revert ayrımı:

| Komut | Temel etkisi | Commit geçmişi |
|---|---|---|
| `git restore` | Dosya içeriğini geri yükler. | Değiştirmez. |
| `git reset` | HEAD ve/veya index'i değiştirir. | Branch'in ucunu taşıyabilir. |
| `git revert` | Ters değişiklik içeren yeni commit üretir. | Geçmişe yeni commit ekler; eski commit'i silmez. |

---

## 18. Seçeneklerin eksiksiz listesini nasıl görürüm?

Git komutlarının seçenekleri komut bazında tanımlanır. Tek bir ortak `--all-options` anahtarı yoktur. En güvenilir yöntem, kullandığın Git sürümünün kendi yardım çıktısını veya resmî komut sayfasını açmaktır.

### Terminalden

```powershell
# Git sürümü
git --version

# Tüm erişilebilir komutları listele
git help -a

# Bir komutun kısa yardımını göster
git add -h
git commit -h
git log -h

# Komutun ayrıntılı kılavuzunu aç
git help add
git help commit

# Rehberleri listele
git help -g
```

Windows PowerShell'de bazı revision ifadelerinde tırnak kullan:

```powershell
git cat-file -p "HEAD^{tree}"
git rev-parse "HEAD~3"
```

### Resmî web dokümantasyonu

- Bütün referans: https://git-scm.com/docs
- Tüm komutlar: https://git-scm.com/docs/git
- Git komut satırı söz dizimi: https://git-scm.com/docs/gitcli
- Git veri modeli: https://git-scm.com/docs/gitdatamodel
- Git günlük kullanım rehberi: https://git-scm.com/docs/giteveryday
- Git kullanıcı kılavuzu: https://git-scm.com/docs/user-manual

> **Eksiksizlik notu:** Git'in resmî referansı çok sayıda ana komut, plumbing komutu, server helper'ı ve dahili yardımcı program içerir. Komut seçenekleri de sürüme göre gelişebilir. Bu doküman resmî komut ailelerini envanter olarak kapsar ve geliştiricilerin en sık kullandığı komutların önemli seçeneklerini açıklar. Her komutun tüm ileri seviye/az kullanılan seçeneklerinin ayrı ayrı Türkçe açıklamalı tam dökümü, Git'in kendi man sayfalarının boyutuna yaklaşan çok daha büyük bir doküman olur. Bu nedenle bir komutun **mutlak tam seçenek listesi** için ilgili resmî bağlantıyı veya yerel `git <komut> -h` çıktısını esas al.

---

## 19. Resmî kaynaklar

1. Git Reference — https://git-scm.com/docs
2. Git ana komut referansı — https://git-scm.com/docs/git
3. Git komut satırı kuralları — https://git-scm.com/docs/gitcli
4. Git Everyday — https://git-scm.com/docs/giteveryday
5. Git User Manual — https://git-scm.com/docs/user-manual
6. Git Data Model — https://git-scm.com/docs/gitdatamodel
7. Git Glossary — https://git-scm.com/docs/gitglossary


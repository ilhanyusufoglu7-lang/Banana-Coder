Banana Coder

```text
████   ███  █   █  ███  █   █  ███      ███   ███  ████  ████  █████ ████
█   █ █   █ ██  █ █   █ ██  █ █   █    █     █   █ █   █ █   █ █     █   █
████  █████ █ █ █ █████ █ █ █ █████    █     █   █ █   █ █   █ ████  ████
█   █ █   █ █  ██ █   █ █  ██ █   █    █     █   █ █   █ █   █ █     █  █
████  █   █ █   █ █   █ █   █ █   █     ███   ███  ████  ████  █████ █   █
```

Terminal tabanli, cok saglayicili bir yapay zeka kodlama ajani. OpenRouter, Anthropic, OpenAI ve Google Gemini API'lerini ya da kendi indirdigin yerel GGUF modellerini tek bir etkilesimli REPL'den kullaniyor. Dosya sistemine tam erisim var: kod yazar, dosya okur, komut calistirir; her sey senin onayinla ya da tam otomatik modda.

Arayuz Turkce, ASCII banner'li, emoji'siz. Etkilesimli modda tam ekran Textual arayuzu acilir; istersen `/gui` ile ayni ajan ayri bir masaustu IDE penceresinde de calisir. Kodun icinde tek bir API anahtari bile gomulu degil.

## Icerik

- Ozellikler
- Gereksinimler
- Kurulum
- Calistirma
- Arayuz (Textual TUI)
- Masaustu IDE (PyQt5 Fluent)
- Hizli baslangic
- API anahtarlari
- Komutlar
- Araclar ve onay modlari
- Hazir model kisayollari
- Tum OpenRouter modelleri
- Yerel GGUF modelleri
- Sesli yanit (TTS)
- Prompt upgrade (boost)
- Canli islem paneli (preview)
- Web'den icerik getirme (web_fetch)
- Oturumlar, token ve maliyet
- Ayar dosyasi ve ortam degiskenleri
- Guvenlik ve gizlilik
- Gelistirme ve test
- Proje yapisi
- Lisans

## Ozellikler

- **Textual tam ekran TUI.** Piksel banner, kesikli cerceveli paneller, sagda canli islem akisi, slash komut onerileri ve alt durum cubugu. Renkli cikti Textual widget'lariyla cizilir; Rich'e bagimlilik yok. `--no-tui` ile eski duz akis moduna donulur.
- **Masaustu IDE.** `/gui` (veya `banana --gui`) dosya agaci, sekmeli kod editoru ve sohbet panelini PyQt5 ile cizilmis Fluent tasarimli tek pencerede acar; ayni model, oturum araclari ve onay sistemi calisir. Komut paleti, tema paletleri, ayar penceresi ve mikro animasyonlarla gelir.
- **Cok saglayicili.** OpenRouter, Anthropic, OpenAI, Gemini ve yerel GGUF tek arayuzde; model degistirmek tek komut.
- **Etkilesimli kurulum.** `.env` zorunlu degil: anahtari `/api openrouter` ile terminalde girersin, `~/.banana/config.json` dosyasina yazar.
- **Yerel ve cevrimdisi.** HuggingFace'ten tek komutla GGUF indir (`/model indir`), 8 GB RAM gibi dusuk makinelerde calisan kucuk quant'larla (Q2_K, Q4_K_M) API anahtarsiz calis.
- **Ucretsiz model tarayicisi.** `/models free` OpenRouter'in ucretsiz modellerini listeler ve secmeni saglar; `or-free` kisayolu ile secersin.
- **Tum OpenRouter katalogu.** `/model remote` 400'den fazla modeli arama ve sayfalama ile gezdirir; secim aninda model degisir. Arac desteklemeyen modeller otomatik sohbet moduna gecer.
- **Gercek arac seti.** `read_file`, `write_file`, `edit_file`, `list_dir`, `glob`, `grep`, `bash`, `web_fetch`.
- **Uc onay modu.** `ask` (varsayilan, her yazma/komut adiminda sorar), `yolo` (hepsi otomatik), `readonly` (yalnizca okuma).
- **Prompt upgrade (boost).** Yazdigin prompt once ikinci bir "upgrade" modeline verilir, yeniden yazilmis hali kodlama AI'ina gider.
- **Canli islem paneli (preview).** `/preview on` yazinca sag panelde arac cagrilari ve ag istekleri olay olay akar; duz modda `PV ...` satirlari ya da tmux bolmesi kullanilir. Her olay `~/.banana/preview.log` kuyrugunda.
- **Sesli yanit (TTS).** AI'in yaniti OpenRouter'in ucretsiz HeyGen TTS modeli ile seslendirilir.
- **Oturum kaliciligi.** Konusmalar dizin bazli kaydedilir; `-c` ile devam ettir, `--session AD` ile isimlendir.
- **Maliyet gorunurlugu.** `/usage` token ve maliyet ozeti verir.
- **Hiz limitine karsi saglamlik.** 429/5xx durumunda gorunur geri sayimli tekrar deneme.

## Gereksinimler

- Python 3.10 veya uzeri
- Bash/terminal (macOS veya Linux onerilir; Windows'ta WSL tavsiye edilir)
- En az bir saglayici icin API anahtari (yerel GGUF modelleri icin gerekmez)
- Opsiyonel: `tmux` (yalnizca `--no-tui` duz moddaki canli panel icin), `cmake` (yerel GGUF derlemesi), `afplay`/`ffplay`/`mpv`/`aplay`/`paplay` (ses cikisi), `ripgrep` (`grep` araci icin), PyQt5 (masaustu IDE)

## Kurulum

```bash
git clone https://github.com/KULLANICI/banana-coder.git
cd banana-coder
python3 -m venv .venv
source .venv/bin/activate
pip install -e .

# Yerel GGUF modelleri icin (opsiyonel ama onerilen):
pip install -e ".[local]"
# not: llama-cpp-python makinede derlenir; cmake kuruluysa sorun cikmaz

# Masaustu IDE icin (opsiyonel):
pip install -e ".[gui]"
```

`llama-cpp-python` derlenmezse sorun olmaz: yerel model disinda her sey calisir. Sanal ortamdan cikmak istersek `deactivate`.

## Calistirma

```bash
banana                            # tam ekran Textual arayuzu
banana --gui                      # masaustu IDE penceresi (editor + sohbet)
banana --no-tui                   # duz akis modu (tmux/terminal uyumlulugu)
banana -p "su dosyayi incele"     # tek atislik istek, cikip biter
banana -m or-free                 # ucretsiz OpenRouter modeliyle basla
banana -m "local/models/qwen.gguf"  # yerel GGUF ile basla
banana -c                         # son oturumu devam ettir
banana --yolo                     # onay sormadan calis
banana --read-only                # yalnizca okuma modu
.venv/bin/python main.py          # kurulum yapmadan repo icinden calistir
python -m banana.cli              # paket modulu olarak
```

Bayraklar:

| Bayrak | Aciklama |
|--------|----------|
| `-p`, `--prompt METIN` | REPL'e girmeden tek gorev calistir |
| `-m`, `--model AD` | Model veya kisayol (ornek: `or-claude`, `claude`, `gpt`, `gemini`, `local/...gguf`) |
| `-C`, `--dir DIZIN` | Calisma dizini (varsayilan: mevcut dizin) |
| `--yolo` / `--ask` / `--read-only` | Onay modunu bastan sec (birbirinin yerine gecer) |
| `-c`, `--continue` | Son oturumu yukle ve devam et |
| `--new` | Eski oturumu yok say, temiz basla |
| `--session AD` | Belirli isimle oturum kullan |
| `--max-tokens SAYI` | Yanit basina maksimum cikti tokeni |
| `--temperature SAYI` | Model sicakligi (0-2) |
| `--show-reasoning` | Modelin dusunme akisini goster |
| `--json` | Tek seferlik modda sonucu JSON yaz (yalnizca `-p` ile) |
| `--no-color` | Renkleri kapat |
| `--no-tui` | Tam ekran arayuzu kapat, duz REPL akisini kullan |
| `--gui` | Masaustu IDE penceresini ac (PyQt5 Fluent) |
| `--version` | Surumu yaz |

Alt komutlar:

```bash
banana test [openrouter anthropic openai gemini]   # anahtar/saglayici baglanti testi
banana models --free                               # ucretsiz OpenRouter modelleri
banana models --remote                             # OpenRouter canli model listesi
```

## Arayuz (Textual TUI)

Etkilesimli mod bir TTY'de tam ekran Textual arayuzu olarak acilir. Ekran dort katmandir:

- **Ust:** piksel ASCII banner, surum satiri ve kesikli cerceveli bilgi kutusu (model, saglayici, dizin, onay modu).
- **Orta:** solda konusma akisi; `/preview on` yazinca sagda canli islem paneli.
- **Alt:** slash komut onerileri (yazarken filtrelenir, aciklamalariyla) ve giris satiri. Onay sorulari da ayni giris satirinda `Onayla? [e]vet / [h]ayir / [t]umu` olarak belirir.
- **En alt:** surekli durum cubugu (model, onay modu, token, dizin).

Kisayollar:

| Tus | Is |
|-----|----|
| `Ctrl+C` | Cikis |
| `Ctrl+L` | Konusmayi temizle |
| `Ctrl+P` | Canli islem panelini ac/kapat |
| `F1` | Yardim (komut listesi) |

Tek satirlik girisler TUI icinde ayni giris satirini kullanir (API anahtari gizli girilir). TUI yalnizca gercek bir terminalde acilir; boru/CI veya `BANANA_NO_TUI=1` durumunda duz mod otomatik devreye girer.

## Masaustu IDE (PyQt5 Fluent)

`/gui` yazinca ajan ayri bir masaustu penceresinde acilir; ayni model, oturum, araclar ve onay modu gecerli kalir. Arayuz saf PyQt5 + QSS ile cizilen Fluent tasarimli bir IDE'dir: koyu yuzeyler, yumusak kartlar, aksan rengi odak halkalari ve yuvarlak koseler. Ekran uc paneldir:

- **Sol:** dosya agaci. Klasor adi cift tiklayinca tembel yuklenir, dosya cift tiklayinca sekmeli editor'de acilir. Gizli dosyalar ve `.venv`, `node_modules`, `__pycache__` gibi agir klasorler gizlenir. `Ctrl+B` ile daraltilir; durum hatirlanir.
- **Orta:** sekmeli kod editoru; satir numaralari, aktif satir vurgusu, Python sozdizimi renklendirmesi ve degisiklik isareti (`*`). Kaydetmeden sekme kapatilirsa onay istenir.
- **Sag:** sohbet paneli. Mesajlar kullanici/ajan balonlari halinde, arac cagrilari ve onay sorulari ayni akista gorunur. Ust kosedeki mod cipi yazdigin girdiyi aninda etiketler: `sohbet`, `komut` (`/`) veya `kabuk` (`!`).
- **Ust/alt cubuk:** model etiketi, onay modu (`ask`/`yolo`/`readonly`), arac anahtari, durum ve token/maliyet. Ust cubukta ayrica komut paleti ve ayarlar dugmeleri vardir.

Fluent ayrintilar:

- **Komut paleti (`Ctrl+K`).** Ekranin ortasinda acilan arama kutusu; tum komutlar, sekmeler ve temalar arasinda yazarak filtrele, `Enter` ile calistir.
- **Ayarlar (`Ctrl+,`).** Solda gezinme, sagda kartlar: tema rengi, hareket, kenar cubugu, onay modu, arac anahtari, ayar dosyasi yolu ve kisayol listesi.
- **Temalar.** `Banana`, `Nane`, `Gokyuzu`, `Gul`, `Menekse`. Secim aninda tum yuzey, ikon ve odak halkalari yeniden cizilir; `~/.banana/gui.ini` icine kaydedilir.
- **Bildirimler (toast).** Kaydetme, yenileme ve klasor degistirme gibi islemler altta kisa sureli, kendiliginden kapanan bir serit gosterir.
- **Hareket.** Basma dalgasi (ripple), kenar cubugu kaydirma ve akan yanit cercevesi 150-250 ms ile sinirlidir; `prefers-reduced-motion` benzeri `Ayarlar > Hareket` anahtari veya `BANANA_NO_MOTION=1` ile kapatilir.

Kisayollar:

| Tus | Is |
|-----|----|
| `Ctrl+S` (`Command+S`) | Aktif dosyayi kaydet |
| `Ctrl+N` (`Command+N`) | Yeni dosya (yol sorulur) |
| `Ctrl+O` (`Command+O`) | Klasor ac (calisma dizinini degistirir) |
| `Ctrl+W` (`Command+W`) | Aktif sekmeyi kapat |
| `Ctrl+K` (`Command+K`) | Komut paletini ac |
| `Ctrl+,` (`Command+,`) | Fluent ayarlari ac |
| `Ctrl+B` | Kenar cubugunu daralt/genislet |
| `Ctrl+R` | Dosya agacini yenile |
| `Enter` | Mesaji gonder |
| `Shift+Enter` | Sohbette yeni satir |

Sohbet kutusunda slash komutlari (`/model`, `/tools off`, `/help`, ...) ve `!komut` ile kabuk komutlari aynen calisir. `/gui [dizin]` vererek pencereyi baska bir klasorde acabilirsin.

`/gui` pencereyi ayri bir surecte baslatir (Textual REPL kapanmaz), ciktisini `~/.banana/gui.log` dosyasina yazar. Pencere icinden tekrar `/gui` calistirmak yeni pencere acmaz (`BANANA_GUI=1` korumasi). Masaustu arayuz icin PyQt5 gerekir: `pip install PyQt5` ya da `pip install -e ".[gui]"`. PyQt5 yoksa `--gui` aciklayici bir hata verir ve terminal arayuzu bozulmaz.

## Hizli baslangic

Ilk acilista Banana Coder seni yonlendirir: anahtar yoksa `/api` ile ekle, model secilmedikce konusma baslamaz.

```
banana
/api openrouter        # anahtar sorulur (gizli giris), ~/.banana/config.json'e yazilir
/model                 # numarali model secici
/model or-free         # ya da dogrudan kisayol ver
/menu                  # tum ana islemler numarali liste
/quit
```

Bir klasorde calisirken:

```
cd /projen
banana
@src/main.py icindeki yukleyici fonksiyonu incele, hata yonetimini duzelt
!git status            # kabuk komutunu dogrudan calistir
```

## API anahtarlari

Uc yoldan birini sec; oncelik sirasi ortam degiskeni > `~/.banana/config.json`.

1. **Terminalde (onerilen).** `/api openrouter` yaz, anahtari gizli gir. Dilersen `/api openrouter sk-...` ile tek satirda kaydet, `/api openrouter -` ile sil.
2. **Ortam degiskeni.**

| Saglayici | Ortam degiskeni |
|-----------|-----------------|
| OpenRouter | `OPENROUTER_API_KEY` |
| Anthropic | `ANTHROPIC_API_KEY` |
| OpenAI | `OPENAI_API_KEY` |
| Gemini | `GEMINI_API_KEY` veya `GOOGLE_API_KEY` |

3. **`.env` dosyasi.** Repodaki `.env.example` dosyasini `.env` olarak kopyala ve doldur; `.env` git tarafindan yok sayilir.

```bash
cp .env.example .env
```

Anahtar durumunu `/keys`, tam ayari `/config` ile gorursun. `/api` anahtarlari asla ekrana tam yazmaz.

## Komutlar

| Komut | Aciklama |
|-------|----------|
| `/model [ad]` | Model degistir; bos birakilirsan numarali secici acilir |
| `/model remote [ara]` | OpenRouter katalogunun tamami: arama, sayfalama ve aninda secim |
| `/model local` | Model klasorlerindeki GGUF dosyalarindan sec |
| `/model indir [no\|URL]` | HuggingFace'ten yerel GGUF modeli indir |
| `/models` | Hazir kisayollari listele |
| `/models remote [ara]` | OpenRouter katalogunu ac ve model sec |
| `/models free [ara]` | OpenRouter ucretsiz modellerinden sec |
| `/tools on\|off` | Arac cagrilarini ac/kapat (sohbet modeli icin) |
| `/api [saglayici] [anahtar]` | Anahtar goruntule / kaydet / sil |
| `/keys` | Anahtar durum ve kaynaklari |
| `/menu` | Etkilesimli ana menu |
| `/new`, `/clear` | Yeni oturum, ekran temiz |
| `/cd <dizin>`, `/pwd` | Calisma dizini |
| `/ask` | Islemler icin onay iste (varsayilan) |
| `/yolo` | Tum islemleri otomatik onayla |
| `/readonly` | Yalnizca okuma modu |
| `/reasoning on\|off` | Dusunme akisini goster/gizle |
| `/usage` | Token ve maliyet ozeti |
| `/voice on\|off` | AI yanitlarini sesli oku (OpenRouter TTS) |
| `/voice voice <ad>` | Ses sec: gareth, nadine, tabitha, bram, noelle, ... |
| `/voice test` | Ornek cumleyi seslendir |
| `/boost on\|off` | Yazdigin promptu once upgrade modeline yeniden yazdir |
| `/boost model [ad]` | Upgrade modelini sec veya degistir |
| `/preview on\|off` | Islemlerin aktigi canli panel (TUI'de sag panel, duz modda akis/tmux) |
| `/preview height 20-70` | Canli panel genisligi (yuzde) |
| `/preview log` | Olay kuyruk dosyasi ve `tail -f` tarifi |
| `/session` | Aktif oturum dosyasi bilgisi |
| `/sessions` | Bu dizindeki son oturumlar |
| `/config` | Etkin ayarlar |
| `/gui [dizin]` | Masaustu IDE penceresini ac (ayri surec, `~/.banana/gui.log`) |
| `/help` | Tum komutlar |
| `!komut` | Kabuk komutunu dogrudan calistir |
| `@dosya` | Mesaja dosya icerigi ekle |

## Araclar ve onay modlari

Yapay zeka asagidaki araclari cagrir. `ask` modunda yazma/komut adimlarinda sana sorulur; `readonly` modunda yalnizca okuma araclari acik kalir; `yolo` modunda hic sorulmaz (guvendigin dizinlerde kullan).

| Arac | Is | Onay ister mi |
|------|----|---------------|
| `read_file` | Dosya oku, satir numarali dondur | Hayir |
| `list_dir` | Dizinin icini listele | Hayir |
| `glob` | Dosya deseni ile ara (`**/*.py`) | Hayir |
| `grep` | Icerikte regex ara (ripgrep varsa onu kullanir) | Hayir |
| `write_file` | Dosya olustur / uzerine yaz | Evet |
| `edit_file` | Tam eslesen metni degistir | Evet |
| `bash` | Calisma dizininde kabuk komutu calistir | Evet |
| `web_fetch` | Genel bir http(s) adresini duz metin getir | Evet |

Arac ciktilari `/config`'teki `tool_output_limit` degerine gore kisaltilir; uzun konusmalar `max_context_chars` sinirina gore otomatik kirpilir.

## Hazir model kisayollari

`-m` veya `/model` icinde kisayol kullan:

```
claude, claude-opus, claude-haiku           Anthropic dogrudan
gpt, gpt-mini                               OpenAI dogrudan
gemini, gemini-flash                        Gemini dogrudan
or-claude, or-gpt, or-gemini                OpenRouter uzerinden
or-deepseek, or-qwen                        OpenRouter uzerinden
or-free, or-free-gemma, or-free-nemotron    OpenRouter ucretsiz
```

Kisayol listede yoksa ham model kimligi de yazabilirsin: `/model openrouter/meta-llama/llama-3.3-70b-instruct`. Kimlik `anthropic/`, `openai/` veya `google/` ile basliyorsa dogrudan saglayici sanilmasin diye `openrouter/` onekini kullan.

## Tum OpenRouter modelleri

OpenRouter katalogundaki her model secilebilir. Komut canli listeyi indirir; sayfalama ve arama ile gezersin:

```
/model remote                   # tum katalog, ilk sayfa
/model remote qwen              # "qwen" iceren modellerle basla
/models free                    # yalnizca ucretsiz modeller
```

Secici komutlari:

| Giris | Is |
|-------|----|
| `12` | Listelenen numarali modeli sec ve hemen aktive et |
| `a <metin>` | Model kimligi ve adinda ara (ornek: `a claude`) |
| `a` | Aramayi temizle, tam listeye don |
| `+` / `-` | Sonraki / onceki sayfa |
| `vendor/model` | Katalogda gezmeden dogrudan kimlik gir |
| bos Enter | Iptal |

Tablodaki `Arac` sutunu modelin arac cagrisini destekleyip desteklemedigini gosterir:

- `var`: dosya, kabuk ve web araclari calisir.
- `yok`: model secildiginde araclar otomatik kapanir ve model yalnizca sohbet eder; `/tools on` ile elle acabilirsin (model desteklemiyorsa istek hata verir).
- `?`: katalog bu bilgiyi vermiyor; varsayilan olarak araclar acik kalir.

Arac durumu model bazinda hatirlanir: secim `~/.banana/config.json` icine yazilir, ayni modelle yeniden acildiginda korunur. `/tools on|off` ile elle degistirebilirsin. Komut satirindan listelemek icin: `banana models --remote`, ucretsizler icin `banana models --free`.

## Yerel GGUF modelleri

Tamamen cevrimdisi, API anahtarsiz calisma. 8 GB RAM gibi dusuk makinelerde bile calisan kucuk GGUF'ler icin onerilen quant boyutu Q4_K_M (yaklasik 1-2.5 GB); Q2_K daha kucuk ama daha basit.

```
/model indir                              # numarali listeden sec, indirir ve aktarir
/model indir https://huggingface.co/.../model.gguf   # serbest URL ile indir
/model local                              # model klasorlerindeki GGUF'lardan sec
banana -m "local/models/qwen.gguf"        # bayrakla baslat
```

Model klasorleri (su sirayla taranir):

1. `models/` (repo koku - en kolayi)
2. proje koku (`main.py` ile ayni dizin)
3. `~/.banana/models`
4. `~/models`

Notlar:

- Yerel modeller su an **yalnizca sohbet** modunda; dosya ve kabuk araclari kapali (kucuk modeller arac cagrilarini guvenilir yapamiyor).
- `BANANA_LOCAL_MODEL` ortam degiskeni varsayilan yolu secer.
- `.gguf` dosyalari `.gitignore`'da; modeller repoya girmaz.

## Sesli yanit (TTS)

Yalnizca yapay zekanin mesajlari sesli okunur; senin yazdiklarin seslendirilmez. Ses acikken her yanittan sonra OpenRouter'in ucretsiz HeyGen TTS modeli (`heygen/heygen-voice-1-turbo:free`) cagrilir. HeyGen yalnizca ham `pcm` dondurur (44.1 kHz mono); Banana bunu WAV kapsayicina sarar ve sistemin oynaticisiyla (macOS `afplay`, yoksa `ffplay`/`mpv`/`aplay`/`paplay`) calar. Kendi OpenRouter anahtarin kullanilir; ayrica bir anahtar gerekmez.

```
/api openrouter        # ses icin OpenRouter anahtari gerekiyor
/voice on              # her AI yaniti sesli okunsun
/voice voice gareth    # ses degistir: gareth, nadine, tabitha, bram, noelle, ...
/voice test            # ornek cumleyi seslendir
/voice off             # kapat
```

- Kod bloklari ve markdown seslendirmeden once ayiklanir; uzun yanitlar kisaltilir.
- `:free` model oldugu icin ek ucret cikmaz.
- Kayit dosyasi: `/tmp/banana_tts_output.wav` (her yanitta uzerine yazilir).

## Prompt upgrade (boost)

Acikken yazdigin prompt ilk olarak ikinci bir "upgrade" modeline gider; o model promptunu daha acik, somut ve calisilabilir hale getirir. Gelistirilmis prompt ana kodlama AI'ina gonderilir, yani senin yazdigini degil upgrade edilmis halini isler. Iki AI ayni anda calismis olur: biri promptu iyistirir, digeri isi yapar.

```
/boost on                  # ac
/boost model               # upgrade modelini numarali sec
/boost model or-free       # ya da dogrudan kisayol vererek sec
/boost                     # durum goster
/boost off                 # kapat (varsayilan)
```

- Varsayilan upgrade modeli `or-free`; ucretsiz model secersen ek maliyet cikmaz.
- Upgrade basarisiz olursa (anahtar yok, hata, bos veya cok kisa yanit) promptun orijinal hali aynen gonderilir; konusma aksamaz.
- Upgrade edilen metin REPL'de "prompt upgrade edildi" panelinde gosterilir; `-p` tek seferlik modunda gosterilmez.
- Ana modelinden bagimsiz secilebilir: ana model `or-claude`, upgrade modeli `or-free` gibi.

## Canli islem paneli (preview)

`/preview on` yazinca sagdaki panelde yapay zekanin yaptigi her isi olay olay gorursun: arac cagrilari (`read_file`, `write_file`, `bash`), komutlarda gecen URL'ler, `web_fetch` istekleri (`GET https://...`), model indirmeleri, TTS cagrilari, prompt upgrade ve hatalar.

```
/preview on              # paneli ac
/preview off             # kapat
/preview                 # durum + son olaylar
/preview height 25       # panel genisligi (yuzde, 20-70)
/preview log             # kuyruk dosyasi ve tail -f tarifi
```

Nasil calisir:

- Olaylar `~/.banana/preview.log` kuyruk dosyasina yazilir (son 200 satir ekrana hazir sekilde tutulur, dosya 1 MB'i asinca kisalir).
- **Textual arayuzunde** panel uygulamanin sag tarafinda acilir; olaylar oraya canli eklenir.
- **`--no-tui` duz modda** tmux varsa ekran dikey bolunur ve panel kuyrugu `tail -f` ile gosterir (`brew install tmux`); tmux yoksa ayni satirlar `PV ...` olarak akista gorunur.
- `-p` tek seferlik modda ekrana yazilmaz, kuyruk dosyasi yine dolar.
- Panel ayari kaydedilir; bir sonraki acilista otomatik gelir.

## Web'den icerik getirme (web_fetch)

Yapay zekanin `web_fetch` araci bir adresi indirir, HTML'i temizleyip duz metin olarak konusmaya ekler; panelde `GET https://...` satiri olarak gorunur.

- Yalnizca `http`/`https` kabul eder; `localhost`, ozel aglar (10.x, 192.168.x, 127.x, 169.254.x) ve bulut metadata adresleri guvenlik geregi reddedilir.
- Her yonlendirme (redirect) adimi bu kontrolden tekrar gecer.
- Yanit 2 MB ile sinirli; icerik varsayilan 20.000 karaktere kisaltilir.
- `ask` modunda her istekten once izin ister (`/yolo` otomatik onaylar).
- Kendi yerel sunucunu test etmek istersen: `BANANA_ALLOW_PRIVATE_URLS=1 banana`.

## Oturumlar, token ve maliyet

- Her oturum `~/.banana/sessions/` altinda dizin bazli JSON olarak saklanir.
- `/sessions` yakin dizindeki son oturumlari listeler, `/session` aktif dosyayi gosterir.
- `-c` son oturumu devam ettirir, `--new` temiz baslatir, `--session AD` isimli oturum acar.
- `/usage` toplam giris/cikis tokenini ve tahmini maliyeti yazir.
- Oturum dosyalari kisisel veridir; repoya girmez (`~/.banana` dizinin tamamen disinda).

## Ayar dosyasi ve ortam degiskenleri

`~/.banana/config.json` secili model, kayitli API anahtarlari, yerel model yolu, ses, prompt upgrade ve canli panel ayarlarini tutar. `/api`, `/model`, `/voice`, `/boost` ve `/preview` komutlari buraya yazar. Secilen model ve model bazli arac destegi (`model_tools`) de kaydedilir; bir sonraki acilista ayni model ve araç modu gelir. Yazma islemi atomik yapilir (gecici dosya + tasma).

Masaustu IDE'nin kendi durumu ayri bir dosyada durur: `~/.banana/gui.ini` (QSettings). Tema rengi, hareket anahtari, kenar cubugu durumu ve pencere boyutu burada saklanir; `config.json`'a dokunmaz.

Klasoru degistirmek icin: `BANANA_HOME=/baska/yol banana`

| Ortam degiskeni | Is |
|-----------------|----|
| `OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `GEMINI_API_KEY`/`GOOGLE_API_KEY` | Anahtar saglar (config dosyasindan oncelikli) |
| `OPENROUTER_BASE_URL`, `ANTHROPIC_BASE_URL`, `OPENAI_BASE_URL`, `GEMINI_BASE_URL` | OpenAI uyumlu proxy/base adresi degistir |
| `BANANA_HOME` | Ayar/oturum klasoru (varsayilan `~/.banana`) |
| `BANANA_LOCAL_MODEL` | Varsayilan yerel GGUF yolu |
| `BANANA_ALLOW_PRIVATE_URLS` | `web_fetch` icin yerel/ozel ag yasagini kaldir |
| `BANANA_ONE_SHOT` | Tek seferlik modda panel satirlarini ekrana yazdirma |
| `BANANA_NO_TUI` | `1` ise tam ekran arayuzu kapat, duz mod kullan |
| `BANANA_NO_MOTION` | `1` ise masaustu IDE'de animasyonlari kapat |
| `NO_COLOR` | Renkleri kapat |

Varsayilanlar: model secilmedi, onay modu `ask`, `max_tokens` 8192, baglanti zamani 600 sn, arac cikti limiti 30.000 karakter, `boost` ve `preview` kapali.

## Guvenlik ve gizlilik

- Kodun icinde gomulu API anahtari yok. Girdigin anahtarlar `~/.banana/config.json` veya ortam degiskeninde durur; her ikisi de `.gitignore` ile repo disinda.
- `.env`, `.env.*`, `.banana/`, `*.gguf`, `models/*` ve `~/.banana` icindeki hicbir sey git'e girmez (`.env.example` istisna).
- Varsayilan onay modu `ask`: dosya yazma, komut calistirma ve web isteklerinde onay ister.
- `web_fetch` yalnizca genel http(s) adreslerine gider; ozel ag ve metadata adresleri engellenir. Saglayici haricinde baska bir yere veri gonderilmez.
- Anahtarin yanlislikla paylasildiysa derhal saglayici panelinden iptal edip yenisini olustur, sonra `/api <saglayici>` ile yenisini gir.
- Yerel GGUF modu tam cevrimdisi: hicbir veri disari cikmaz.

## Gelistirme ve test

```bash
pip install pytest
python -m pytest tests -q
```

Testler gercek API'ye gitmez: yerel bir SSE sahte sunucusu (OpenRouter uyumlu) uzerinden calisir; saglayici cagrilarinin hicbiri disariya cikmaz. Tarayici/istemci testleri icin `BANANA_ALLOW_PRIVATE_URLS=1` kullanilir. Masaustu IDE testleri PyQt5 gerektirir; PyQt5 yoksa ilgili testler atlanir.

## Proje yapisi

```
banana/
  cli.py          REPL, komutlar, model secme, bayraklar
  agent.py        agent dongusu ve arac cagrilarinin orkestrasyonu
  config.py       yapisandirma (env + ~/.banana/config.json)
  models.py       veri siniflari, model kisayollari, ayristirma
  net.py          httpx SSE akisi, geri sayimli tekrar denemeler
  hf.py           GGUF indirme (HuggingFace)
  tts.py          OpenRouter HeyGen seslendirme (PCM -> WAV)
  booster.py      prompt upgrade (boost) modeli cagrisi
  preview.py      canli islem paneli: olay kuyrugu + dinleyici/tmux bolmesi
  ui.py           ANSI tabanli duz arayuz (TUI olmayan mod)
  tui.py          Textual tam ekran arayuz (banner, paneller, giris, durum)
  gui.py          PyQt5 Fluent masaustu IDE (dosya agaci, editor, sohbet)
  providers/      openai-compat, anthropic, gemini, local (GGUF)
  tools/          dosya, kabuk ve web (web_fetch) araclari
main.py           giris noktasi (banana paketini cagirir)
tests/            pytest (yerel mock API ile)
models/           GGUF klasoru (dosyalar git disinda, README.txt haric)
```

---
layout: post
title: Pass store
---

`pass` -- удобная утилита для хранения и генерации паролей.


### Чем она отличается от других?
На хабре [была статья](https://habr.com/post/125248/), где сравнивали менеджеры паролей.
Если бы там был бы `pass`, то вот, что бы написали:

  * Бесплатно (открытый исходный код)
  * Синхронизация с помощью git
  * Шифруется с помощью gpg ([`man gpg`](https://www.gnupg.org/documentation/manpage.html))
  * Есть **куча** портов на andoid, **куча** gui клиентов для **кучи** ОС
  * Написаны расширения под браузеры (firefox, chrome)

В конце статьи будут ссылки на клиенты и расширения.


### Установка
Пакет `pass` есть в стандартных репозиториях Debian, Ubuntu и Fedora.

Для Debian и его подобных для установки используем `apt`.

```bash
apt install pass
```

`git` и `gpg` (`gnupg`) подкачаются в виде зависимостей.


### Настройка
Для работы понадобится создать ключ для шифрования.


#### Создание ключа
```bash
gpg2 --full-gen-key
```

*Debian на docker вывел "Permission denied". Решение:* 
`chown your_username $(tty)`

Вас спросят выбрать вид ключа.
Подробнее про разницу можно прочитать тут:
  * [Best encryption and signing algorithm for GnuPG: RSA/RSA or DSA/Elgamal?](https://superuser.com/a/541162)
  * [Using OpenPGP subkeys in Debian development](https://wiki.debian.org/Subkeys?action=show&redirect=subkeys)
  * [Web archive: Cryptography - Lecture 19 - Digital Signature Algorithms](https://web.archive.org/web/20140212143556/http://courses.cs.tamu.edu:80/pooch/665_spring2008/Australian-sec-2006/less19.html)

Debian в своих документах рекомендует использовать `RSA` ключ, поэтому выберем `(1) RSA and RSA`.

Чем больше количество битов вы выберете, тем сложнее будет взломать ваш ключ, но тогда ваше устройство будет дольше генерировать его.

Нильс Фергюсон и Брюс Шнайер (2003) писали в Practical Cryptography, что 2048-битного ключа хватит на то, чтобы защитить свою информацию на 20 лет:
> The absolute minimum size for n is 2048 bits or so if you want to protect your data for 20 years. [...] If you can afford it in your application, let n be 4096 bits long, or as close to this size as you can get it. 

NSA (Агентство национальной безопасности Соединённых Штатов) [рекомендует создавать ключи длиной 3072 бита или больше](https://cryptome.org/2016/01/CNSA-Suite-and-Quantum-Computing-FAQ.pdf):
> Q: Which specific algorithm parameters should NSS use?
>
> A: [...] RSA moduli should have a minimum size of 3072 bits (other than the noted PKI exception), and keys should be generated in accordance with all relevant NIST standards.

Поэтому лучше выбрать `4096` бита -- максимальное возможное значение gnupg.

Дальше введите время, после которого ваш ключ истечет, имя и почту.

Должно появиться окно для ввода пароля.
Это будет кодовая фраза, с помощью которой и зашифруется ключ, а после, и все пароли, хранящие в `pass`.

![Кодовая фраза][pass-phrase]

Готово. Можете проверить свои ключи командой `gpg2 --list-keys`.
*В примере был создан 1024-битный ключ*

![Список ключей][list-keys]


#### Создание хранилища паролей
```bash
pass init gpg-id
```
где `gpg-id` -- `id` вашего ключа.

`id` можно посмотреть по команде `gpg2 --list-keys` -- он находится в строках с публичными данными (pub).
В моем случае -- это `9434926A40EAC3CE94B0319F4F13D8F223D814F6`.

![Инициализация хранилища паролей][pass-init]


### Использование
#### Добавить
Чтобы добавить новый пароль, используйте `insert`:
```bash
pass insert pass-name
```
где `pass-name` -- это имя пароля.

Пароли можно группировать по папкам. Для этого пишите полный путь (даже не созданный) с именем.

Например:
```bash
pass insert Github/igoose1
```

#### Добавить
Можно получить дерево паролей -- для этого запустите `pass` без аргументов.

Чтобы получить сохраненный пароль, введите
```bash
pass pass-name
```
Потребуется ввести пароль от gpg-ключа.

![Получить пароль][pass-tree]


#### Сгенерировать
В `pass` есть генератор паролей, который можно использовать так:
```bash
pass generate pass-name pass-length
```
где `pass-length` -- длина пароля.

Пароли генерируются, используя дополнительные символы.
Чтобы сгенерировать строчку без них, надо передать аргумент `-n` (`--no-symbols`)

![Генератор паролей][pass-generate].


#### Скопировать
Аргумент `-c` (`--clip`) используется для того, чтобы сохранить пароль в буфер обмена.
**Внимание**: пароль сохраняется в буфере на 45 секунд.

Этот аргумент можно использовать для получения пароля и для генерации.


#### Удалить
В `pass` пароль удалить можно с помощью `rm`.
```bash
pass rm Random-site/user
```
или
```bash
pass rm -R Random-site/
```
для рекурсивного удаления папки.

### Синхронизация с git

**...**



[pass-phrase]: https://github.com/igoose1/igoose1.github.io/raw/master/images/pass-store/passphrase.png
[list-keys]: https://github.com/igoose1/igoose1.github.io/raw/master/images/pass-store/list-keys.png


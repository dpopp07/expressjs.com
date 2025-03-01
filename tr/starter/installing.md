---
layout: page
title: Express Kurulumu
menu: starter
lang: tr
description: Learn how to install Express.js in your Node.js environment, including
  setting up your project directory and managing dependencies with npm.
---

# Kurulum

[Node.js](https://nodejs.org/)'in kurulu olduğunu varsayarak, uygulamanızı
barındıracak bir dizin oluşturun ve o dizine geçiş yapın.

```bash
$ mkdir myapp
$ cd myapp
```

Uygulamanız için `package.json` dosyasını oluşturmak için `npm init` komutunu
çalıştırın. 
`package.json` dosyasının nasıl çalıştığı hakkında daha fazla bilgi edinmek için [Specifics of npm's package.json handling](https://docs.npmjs.com/files/package.json) adresini kullanın.

```bash
$ npm init
```

Bu komut size uygulamanızın adı ve versiyonu gibi bir kaç soru yöneltecektir.
Şimdilik, çoğu soru için ENTER tuşuna basıp varsayılan ayarları uygulayabilirsiniz, aşağıdaki hariç:


```bash
entry point: (index.js)
```

`app.js` ya da ana dosyanıza vermek istediğiniz ismi girin. Eğer ana dosyanızın `index.js` olmasını istiyorsanız, ENTER tuşu ile varsayılanı uygulayabilirsiniz.

Şimdi Express'i `myapp` dizinine kurun ve bağımlı uygulamalar listesine ekleyin. Örneğin:


```bash
$ npm install express
```

Express'i geçici olarak kurmak ve bağımlı uygulamalar listesine eklememek istiyorsanız:

```bash
$ npm install express --no-save
```

<div class="doc-box doc-info" markdown="1">
npm 5.0+ versiyonları için npm install komutu, kurulacak modülü varsayılan olarak `package.json` içindeki bağımlılıklar listesine ekler; daha eski npm versiyonları için `--save` ayrıca belirtilmelidir. Daha sonrasında, uygulama dizininde `npm install` komutunu çalıştırmak, bağımlılık listesindeki uygulamaları otomatik olarak yükler.
</div>

###  [Sonraki: Merhaba Dünya ](/{{ page.lang }}/starter/hello-world.html)

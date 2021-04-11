---
title: Localização do Azure Media Player
description: Suporte linguístico múltiplo para utilizadores de locais não ingleses.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449848"
---
# <a name="azure-media-player-localization"></a>Localização do Azure Media Player #

O suporte linguístico múltiplo permite que os utilizadores de locais não ingleses interajam de forma nativa com o leitor. O Azure Media Player instantaneamente com um dicionário global de idiomas, que irá localização das mensagens de erro com base no idioma da página. Um desenvolvedor também pode criar uma instância de jogador com uma linguagem definida forçada. A língua predefinida é inglês (pt).

> [!NOTE]
> Esta funcionalidade ainda se encontra a passar por alguns testes e, como tal, está sujeita a bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

A Azure Media Player suporta atualmente os seguintes idiomas com os respetivos códigos linguísticos:

| Linguagem            | Código | Linguagem                | Código   | Linguagem                | Código         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Português {predefinido}   | en   | Croata                | hr     | Romeno                | ro           |
| Árabe              | ar   | Húngaro               | hu     | Eslovaco                  | sk           |
| Búlgaro           | bg   | Indonésio              | ID     | Esloveno                 | sl           |
| Catalão             | ca   | Islandês               | é     | Sérvio - Cirílico      | sr-cyrl-cs   |
| Checo               | cs   | Italiano                 | que     | Sérvio - Latim         | sr-latn-rs   |
| Dinamarquês              | da   | Japonês                | ja     | Russo                 | ru           |
| Alemão              | de   | Cazaque                  | kk     | Sueco                 | sv           |
| Grego               | el   | Coreano                  | ko     | Tailandês                    | th           |
| Em espanhol             | es   | Lituano              | lt     | Tagalog                 | tl           |
| Estónio            | et   | Letão                 | lv     | Turco                 | tr           |
| Basco              | eu   | Malásia               | ms     | Ucraniano               | Reino Unido           |
| Rio Farsi               | fa   | Norueguês - Bokmã-l     | nb     | Urdu                    | o seu           |
| Finlandês             | fi   | Neerlandês                   | nl     | Vietnamita              | vi           |
| Francês              | fr   | Norueguês - Nynorsk     | nn     | Chinês - simplificado    | zh-hans      |
| Galego            | gl   | Polaco                  | pl     | Chinês - tradicional   | zh-hant      |
| Hebraico              | ele   | Português (Brasil)     | pt-br  |                         |              |
| Hindi               | Olá   | Português (Portugal)   | pt-pt  |                         |              |


> [!NOTE]
> Se não quiser que ocorra qualquer localização, deve forçar a língua a inglês

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)

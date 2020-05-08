---
title: Localização de jogador de mídia azure
description: Suporte de idiomas múltiplos para utilizadores de locais não ingleses.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779047"
---
# <a name="localization"></a>Localização #

O suporte linguístico múltiplo permite que os utilizadores de locais não ingleses interajam de forma nativa com o jogador. O Azure Media Player irá instantaneamente com um dicionário global de idiomas, que localizará as mensagens de erro com base na linguagem da página. Um desenvolvedor também pode criar uma instância de jogador com uma linguagem forçada. A língua padrão é inglês (pt).

> [!NOTE]
> Esta funcionalidade ainda está a passar por alguns testes e, como tal, está sujeita a bugs.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

O Azure Media Player suporta atualmente os seguintes idiomas com os respetivos códigos linguísticos:

| Idioma            | Código | Idioma                | Código   | Idioma                | Código         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Inglês {padrão}   | en   | Croata                | hr     | Romeno                | ro           |
| Árabe              | ar   | Húngaro               | hu     | Eslovaco                  | sk           |
| Búlgaro           | bg   | Indonésio              | ID     | Esloveno                 | sl           |
| Catalão             | ca   | Islandês               | é     | Sérvio - Cirílico      | sr-cyrl-cs   |
| Checo               | cs   | Italiano                 | lo     | Sérvio - Latim         | sr-latn-rs   |
| Dinamarquês              | da   | Japonês                | ja     | Russo                 | ru           |
| Alemão              | de   | Cazaque                  | kk     | Sueco                 | sv           |
| Grego               | el   | Coreano                  | ko     | Tailandês                    | th           |
| Espanhol             | es   | Lituano              | lt     | Tagalog                 | tl           |
| Estónio            | et   | Letão                 | lv     | Turco                 | tr           |
| Basco              | eu   | Malásia               | ms     | Ucraniano               | Reino Unido           |
| Farsi               | fa   | Norueguês - Bokmã¥l     | nb     | Urdu                    | o seu           |
| Finlandês             | fi   | Neerlandês                   | nl     | Vietnamita              | vi           |
| Francês              | fr   | Norueguês - Nynorsk     | nn     | Chinês - simplificado    | zh-hans      |
| Galego            | gl   | Polaco                  | pl     | Chinês - tradicional   | zh-hant      |
| Hebraico              | ele   | Português (Brasil)     | pt-br  |                         |              |
| Hindi               | Olá   | Português (Portugal)   | pt-pt  |                         |              |


> [!NOTE]
> Se não quiser que ocorra qualquer localização, deve forçar a língua ao inglês

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)

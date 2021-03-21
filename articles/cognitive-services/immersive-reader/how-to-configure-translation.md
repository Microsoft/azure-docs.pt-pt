---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como configurar as várias opções de tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608704"
---
# <a name="how-to-configure-translation"></a>Como configurar a tradução

Este artigo demonstra como configurar as várias opções de tradução no Leitor Imersivo.

## <a name="configure-translation-language"></a>Linguagem de tradução configurada

O `options` parâmetro contém todas as bandeiras que podem ser usadas para configurar a tradução. Desa parte do `language` parâmetro para o idioma para o quais pretende traduzir. Consulte o [Suporte linguístico](./language-support.md) para obter a lista completa de línguas apoiadas.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Traduzir automaticamente o documento na carga

Configurar `autoEnableDocumentTranslation` `true` para ativar automaticamente a tradução de todo o documento quando o Leitor Imersivo estiver carregado.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Ativar automaticamente a tradução de palavras

Configurar `autoEnableWordTranslation` `true` para ativar a tradução de uma única palavra.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
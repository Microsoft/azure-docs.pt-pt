---
title: Configurar a tradução
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como configurar as várias opções de tradução.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633149"
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
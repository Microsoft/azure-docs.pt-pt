---
title: Configurar a funcionalidade Ler em Voz Alta
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como configurar as várias opções para Ler Em Voz Alta.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 648227521e5e4e8feecd864d3e572a4758e551ca
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633268"
---
# <a name="how-to-configure-read-aloud"></a>Como configurar Ler Em Voz

Este artigo demonstra como configurar as várias opções para Ler Em Voz Alta no Leitor Imersivo.

## <a name="automatically-start-read-aloud"></a>Iniciar automaticamente ler em voz alta

O `options` parâmetro contém todas as bandeiras que podem ser usadas para configurar Ler Em Voz Alta. `autoplay`Desemjuste `true` para ativar automaticamente o início do Read Aloud após o lançamento do Leitor Imersivo.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> Devido às limitações do navegador, a reprodução automática não é suportada no Safari.

## <a name="configure-the-voice"></a>Configure a voz

Definir `voice` para um ou `male` `female` . Nem todas as línguas apoiam ambas as vozes. Para mais informações, consulte a página suporte para [idiomas.](./language-support.md)

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Vesundo a velocidade de reprodução

Definir `speed` para um número entre `0.5` (50%) e `2.5` (250%) inclusivamente. Os valores fora desta gama serão fixados a 0,5 ou 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
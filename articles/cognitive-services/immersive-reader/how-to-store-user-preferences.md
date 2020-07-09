---
title: Armazenar preferências de utilizador
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como armazenar as preferências do utilizador.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486924"
---
# <a name="how-to-store-user-preferences"></a>Como armazenar preferências dos utilizadores

Este artigo demonstra como armazenar as preferências do utilizador. Esta funcionalidade pretende ser um meio alternativo para armazenar as preferências do utilizador no caso de não ser desejável ou exequível a utilização de cookies.

## <a name="how-to-enable-storing-user-preferences"></a>Como permitir armazenar preferências de utilizador

O `options` parâmetro contém a `onPreferencesChanged` chamada. Esta função será chamada sempre que o utilizador alterar as suas preferências (por exemplo, alteram o tamanho do texto, a cor do tema, a fonte, e assim por diante). O `value` parâmetro contém uma cadeia, que representa as preferências atuais do utilizador. Esta corda pode ser armazenada utilizando qualquer mecanismo que prefira.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Como carregar as preferências dos utilizadores no Leitor Imersivo

Passe as preferências do utilizador ao Leitor Imersivo utilizando o `preferences` parâmetro. Um exemplo trivial para armazenar e carregar as preferências do utilizador é o seguinte:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
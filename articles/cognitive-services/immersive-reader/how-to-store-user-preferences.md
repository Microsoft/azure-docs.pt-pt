---
title: Armazenar as preferências do utilizador
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como armazenar as preferências do utilizador.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617697"
---
# <a name="how-to-store-user-preferences"></a>Como armazenar preferências dos utilizadores

Este artigo demonstra como armazenar as definições de UI do utilizador, formalmente conhecidas como preferências do **utilizador,** através das [opções SDK](./reference.md#options) do leitor imersivo alterado e [-onPreferences.](./reference.md#options)

Quando a opção [CookiePolicy](./reference.md#cookiepolicy-options) SDK é definida como *Enable,* a aplicação Imersiva Reader armazena as **preferências** do utilizador (tamanho de texto, cor temática, fonte, e assim por diante) em cookies, que são locais para um navegador e dispositivo específicos. Cada vez que o utilizador lançar o Leitor Imersivo no mesmo navegador e dispositivo, abrirá com as preferências do utilizador a partir da sua última sessão nesse dispositivo. No entanto, se o utilizador abrir o Leitor Imersivo num navegador ou dispositivo diferente, as definições serão inicialmente configuradas com as definições padrão do Leitor Imersivo, e o utilizador terá de definir novamente as suas preferências, e assim por diante para cada dispositivo que utilizar. As `-preferences` `-onPreferencesChanged` opções SDK do leitor e imersivo fornecem uma forma de as aplicações percorrerem as preferências de um utilizador em vários navegadores e dispositivos, de modo a que o utilizador tenha uma experiência consistente onde quer que utilize a aplicação.

Em primeiro lugar, ao fornecer a `-onPreferencesChanged` opção SDK de retorno ao lançar a aplicação Immersive Reader, o Leitor Imersivo enviará uma `-preferences` corda de volta para a aplicação do anfitrião sempre que o utilizador alterar as suas preferências durante a sessão De Leitor Imersivo. A aplicação do anfitrião é então responsável por armazenar as preferências do utilizador no seu próprio sistema. Em seguida, quando esse mesmo utilizador lançar novamente o Leitor Imersivo, a aplicação de anfitrião pode recuperar as preferências desse utilizador do armazenamento e fornecê-las como a `-preferences` opção SDK de cadeia ao lançar a aplicação Immersive Reader, de modo a que as preferências do utilizador sejam restauradas.

Esta funcionalidade pode ser utilizada como um meio alternativo para armazenar preferências dos **utilizadores** no caso de não ser desejável ou viável a utilização de cookies.

> [!CAUTION]
> **IMPORTANTE** Não tente alterar programáticamente os valores da `-preferences` cadeia enviada para e a partir da aplicação Immersive Reader, pois isso pode causar um comportamento inesperado, resultando numa experiência de utilizador degradada para os seus clientes. As aplicações de anfitrião nunca devem atribuir um valor personalizado ou manipular a `-preferences` cadeia. Ao utilizar a `-preferences` opção de cadeia, utilize apenas o valor exato que foi devolvido da `-onPreferencesChanged` opção de retorno.

## <a name="how-to-enable-storing-user-preferences"></a>Como permitir armazenar preferências de utilizador

o parâmetro de [lançamento](./reference.md#launchasync) Imersivo SDK `options` SDK contém o `-onPreferencesChanged` retorno. Esta função será chamada sempre que o utilizador alterar as suas preferências. O `value` parâmetro contém uma cadeia, que representa as preferências atuais do utilizador. Esta cadeia é então armazenada, para esse utilizador, pela aplicação de anfitrião.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Como carregar as preferências dos utilizadores no Leitor Imersivo

Passe as preferências do utilizador ao Leitor Imersivo utilizando a `-preferences` opção. Um exemplo trivial para armazenar e carregar as preferências do utilizador é o seguinte:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)
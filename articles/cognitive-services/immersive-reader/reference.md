---
title: Referência do SDK de leitor envolventes
titleSuffix: Azure Cognitive Services
description: Referência para o leitor de imersão SDK
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718401"
---
# <a name="immersive-reader-sdk-reference"></a>Referência do SDK de leitor envolventes

O SDK de leitor de imersão é uma biblioteca de JavaScript que permite-lhe integrar o leitor de imersão na sua aplicação web.

## <a name="functions"></a>Funções

O SDK expõe uma única função, `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Inicia o leitor envolventes dentro de um `iframe` na sua aplicação web.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| `token` | Cadeia de caracteres | O token de acesso, adquirido da chamada para o `issueToken` ponto final. |
| `resourceName` | Cadeia de caracteres | Reservado. Tem de ser definido como `null`. |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a serem apresentados no leitor envolvente e experimental. |
| `options` | [Opções](#options) | Opções de configuração de determinados comportamentos o leitor envolvente e experimental. Opcional. |

#### <a name="returns"></a>Devolve

Devolve um `Promise<HTMLDivElement>` que é resolvido quando o leitor de imersão é carregado. O `Promise` é resolvido para um `div` elemento cujo único filho é um `iframe` elemento que contém a página do leitor envolvente e experimental.

#### <a name="exceptions"></a>Exceções

Retornado `Promise` será rejeitada com um [ `Error` ](#error) objeto se o leitor de imersão Falha ao carregar. Para obter mais informações, consulte a [códigos de erro](#error-codes).

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a serem apresentados no leitor envolvente e experimental.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Tipos MIME suportados

| Tipo de MIME | Descrição |
| --------- | ----------- |
| text/plain | Texto sem formatação. |
| aplicação/mathml + xml | Linguagem de marcação matemática (MathML). [Saiba mais](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opções

Contém propriedades que configurar determinados comportamentos o leitor envolvente e experimental.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Erro

Contém informações sobre o erro.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Códigos de erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | Argumento fornecido é inválido, consulte `message` para obter detalhes. |
| Tempo limite | O leitor de imersão Falha ao carregar no tempo limite especificado. |
| TokenExpired| O token fornecido expirou. |

## <a name="launching-the-immersive-reader"></a>Iniciar o leitor envolvente

O SDK fornece o estilo do padrão para o botão para iniciar o leitor envolvente e experimental. Utilize o `immersive-reader-button` atributo de classe para ativar esta definição de estilo.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Utilize os seguintes atributos para configurar o aspeto e funcionalidade do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text`, ou `iconAndText`. Assume a predefinição `icon`. |
| `data-locale` | Define a Localidade, por exemplo, `en-US`, `fr-FR`. Predefinições para inglês. |
| `data-icon-px-size` | Define o tamanho do ícone em pixéis. Predefinição é 20px. |

## <a name="browser-support"></a>Suporte do browser

Utilize as versões mais recentes dos seguintes browsers para a melhor experiência com o leitor envolvente e experimental.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passos Seguintes

* Explorar o [leitor envolvente SDK no GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Quickstart: Criar uma aplicação web que inicia o leitor de imersão (C#)](./quickstart.md)
---
title: Referência sdk leitor imersivo
titleSuffix: Azure Cognitive Services
description: O SDK do Leitor Imersivo contém uma biblioteca JavaScript que lhe permite integrar o Leitor Imersivo na sua aplicação.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156408"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guia de referência do leitor imersivo SDK

O SDK do Leitor Imersivo contém uma biblioteca JavaScript que lhe permite integrar o Leitor Imersivo na sua aplicação.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>lançamentoAsync

Lança o Leitor Imersivo dentro de uma `iframe` aplicação web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| `token` | string | O símbolo de autenticação da AD Azure. |
| `subdomain` | string | O subdomínio personalizado do seu recurso Imersivo leitor em Azure. |
| `content` | [Conteúdo](#content) | Um objeto que contenha o conteúdo a mostrar no Leitor Imersivo. |
| `options` | [Opções](#options) | Opções para configurar certos comportamentos do Leitor Imersivo. Opcional. |

### <a name="returns"></a>Devolve

Devolve `Promise<LaunchResponse>`um , que resolve quando o Leitor Imersivo está carregado. A `Promise` decisão de [`LaunchResponse`](#launchresponse) um objeto.

### <a name="exceptions"></a>Exceções

O `Promise` devolvido será rejeitado [`Error`](#error) com um objeto se o Leitor Imersivo não carregar. Para mais informações, consulte os [códigos](#error-codes)de erro .

## <a name="close"></a>fechar

Fecha o Leitor Imersivo.

Um caso de utilização de exemplo para esta ```hideExitButton: true``` função é se o botão de saída estiver escondido, definindo [as opções](#options). Em seguida, um botão diferente (por exemplo, a ```close``` seta traseira de um cabeçalho móvel) pode chamar esta função quando é clicada.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Esta função modela e atualiza os elementos do botão Imersivo do Leitor do documento. Se ```options.elements``` for fornecida, esta função ```options.elements```irá renderizar botões dentro de . Caso contrário, os botões serão renderizados dentro dos ```immersive-reader-button```elementos do documento que tenham a classe .

Esta função é automaticamente chamada pelo SDK quando a janela carrega.

Consulte [Atributos Opcionais](#optional-attributes) para mais opções de renderização.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOp](#renderbuttonsoptions) | Opções para configurar certos comportamentos da função renderButtons. Opcional. |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a mostrar no Leitor Imersivo.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segmento

Um único pedaço de dados, que será transmitido para o conteúdo do Leitor Imersivo.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>Resposta de lançamento

Contém a resposta da `ImmersiveReader.launchAsync`chamada para .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolítica enum

Um enum usado para definir a política para o uso de cookies do Leitor Imersivo. Ver [opções](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Tipos mime suportados

| Tipo de MIME | Descrição |
| --------- | ----------- |
| texto/planície | Texto simples. |
| text/html | Conteúdo HTML. [Mais informações](#html-support)|
| aplicação/mathml+xml | Linguagem de marcação matemática (MathML). [Saiba mais](./how-to/display-math.md).
| aplicação/vnd.openxmlformats-officedocument.wordprocessingml.document | Documento de formato Microsoft Word .docx.

### <a name="html-support"></a>Suporte HTML

| HTML | Conteúdo Suportado |
| --------- | ----------- |
| Estilos de letra | Arrojado, itálico, sublinhado, código, strikethrough, superscript, subscript |
| Listas não ordenadas | Disco, Círculo, Quadrado |
| Listas ordenadas | Decimal, Upper-Alpha, Lower-Alpha, Upper-Roman, Lower-Roman |
| Hiperligações | Brevemente |

As etiquetas não suportadas serão renderizadas comparativamente. Imagens e mesas não são suportadas atualmente.

### <a name="options"></a>Opções

Contém propriedades que configuram certos comportamentos do Leitor Imersivo.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOp

Opções para renderizar os botões Imersivos leitor.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
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
| BadArgument | O argumento fornecido é `message` inválido, consulte para mais detalhes. |
| Tempo limite | O Leitor Imersivo não carregou dentro do prazo especificado. |
| TokenExpired | O token fornecido expirou. |
| Estrangulado | O limite da taxa de chamada foi ultrapassado. |

## <a name="launching-the-immersive-reader"></a>Lançamento do Leitor Imersivo

O SDK fornece um estilo predefinido para o botão para o lançamento do Leitor Imersivo. Utilize `immersive-reader-button` o atributo de classe para ativar este estilo. Consulte [este artigo](./how-to-customize-launch-button.md) para mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Utilize os seguintes atributos para configurar a aparência e a sensação do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode `icon`ser, `text` `iconAndText`ou . Incumprimentos `icon`para . |
| `data-locale` | Define o local. Por exemplo, `en-US` ou `fr-FR`. Incumprimentos em `en`inglês. |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. Incumprimentos a 20px. |

## <a name="browser-support"></a>Browser support (Suporte do browser)

Utilize as versões mais recentes dos seguintes navegadores para a melhor experiência com o Leitor Imersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK imersivo do leitor no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Quickstart: Criar uma aplicação web que lança o Leitor Imersivo (C#)](./quickstart.md)

---
title: Referência SDK do leitor imersivo
titleSuffix: Azure Cognitive Services
description: O SDK do leitor imersivo contém uma biblioteca JavaScript que lhe permite integrar o Leitor Imersivo na sua aplicação.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 6dfcd8d56232f893f881f310b33f3f849e2364a7
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85475956"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Referência SDK do leitor imersivo JavaScript (v1.1)

O SDK do leitor imersivo contém uma biblioteca JavaScript que lhe permite integrar o Leitor Imersivo na sua aplicação.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>lançarAsync

Lança o Leitor Imersivo dentro de uma `iframe` aplicação web. Note que o tamanho do seu conteúdo está limitado a um máximo de 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parâmetros

| Name | Tipo | Description |
| ---- | ---- |------------ |
| `token` | cadeia (de carateres) | O sinal de autenticação AD Azure. |
| `subdomain` | string | O subdomínio personalizado do seu recurso Immersive Reader em Azure. |
| `content` | [Conteúdo](#content) | Um objeto que contenha o conteúdo a ser mostrado no Leitor Imersivo. |
| `options` | [Opções](#options) | Opções para configurar certos comportamentos do Leitor Imersivo. Opcional. |

### <a name="returns"></a>Devoluções

Devolve um `Promise<LaunchResponse>` , que resolve quando o Leitor Imersivo está carregado. A `Promise` determinação é de um [`LaunchResponse`](#launchresponse) objeto.

### <a name="exceptions"></a>Exceções

O devolvido `Promise` será rejeitado com um objeto se o Leitor [`Error`](#error) Imersivo não carregar. Para mais informações, consulte os [códigos de erro.](#error-codes)

## <a name="close"></a>fechar

Fecha o Leitor Imersivo.

Um exemplo de caso de utilização para esta função é se o botão de saída estiver escondido definindo ```hideExitButton: true``` [as opções](#options). Em seguida, um botão diferente (por exemplo, a seta traseira de um cabeçalho móvel) pode chamar esta ```close``` função quando é clicada.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderAbtons

Esta função tem estilos e atualiza os elementos do botão Leitor Imersivo do documento. Se ```options.elements``` for fornecida, esta função tornará os botões dentro ```options.elements``` . Caso contrário, os botões serão renderizados dentro dos elementos do documento que têm a classe ```immersive-reader-button``` .

Esta função é automaticamente chamada pelo SDK quando a janela está carregada.

Consulte [Atributos Opcionais](#optional-attributes) para obter mais opções de renderização.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parâmetros

| Name | Tipo | Description |
| ---- | ---- |------------ |
| `options` | [Prestações DebutonsOptions](#renderbuttonsoptions) | Opções para configurar certos comportamentos da função renderbuttons. Opcional. |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no Leitor Imersivo.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segmento

Um único pedaço de dados, que será passado para o conteúdo do Leitor Imersivo.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Tipos de MIME suportados

| Tipo de MIME | Descrição |
| --------- | ----------- |
| texto/planície | Texto simples. |
| text/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicação/mathml+xml | Linguagem de marcação matemática (MathML). [Saiba mais](./how-to/display-math.md).
| ument de aplicação/vnd.openxmlformats-officedocument.wordprocessingml.doc | Documento de formato Microsoft Word .docx.

### <a name="options"></a>Opções

Contém propriedades que configuram certos comportamentos do Leitor Imersivo.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000).
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits.
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
    disableFirstRun?: boolean; // Disable the first run experience.
    readAloudOptions?: ReadAloudOptions; // Options to configure Read Aloud.
    translationOptions?: TranslationOptions; // Options to configure translation.
    displayOptions?: DisplayOptions; // Options to configure text size, font, etc.
    preferences?: string; // String returned from onPreferencesChanged representing the user's preferences in the Immersive Reader.
    onPreferencesChanged?: (value: string) => any; // Executes when the user's preferences have changed.
}
```

```typescript
enum CookiePolicy { Disable, Enable }
```

```typescript
type ReadAloudOptions = {
    voice?: string;      // Voice, either 'male' or 'female'. Note that not all languages support both genders.
    speed?: number;      // Playback speed, must be between 0.5 and 2.5, inclusive.
    autoplay?: boolean;  // Automatically start Read Aloud when the Immersive Reader loads.
};
```

> [!NOTE]
> Devido às limitações do navegador, a reprodução automática não é suportada no Safari.

```typescript
type TranslationOptions = {
    language: string;                         // Set the translation language, e.g. fr-FR, es-MX, zh-Hans-CN. Required to automatically enable word or document translation.
    autoEnableDocumentTranslation?: boolean;  // Automatically translate the entire document.
    autoEnableWordTranslation?: boolean;      // Automatically enable word translation.
};
```

```typescript
type DisplayOptions = {
    textSize?: number;          // Valid values are 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96.
    increaseSpacing?: boolean;  // Set whether increased spacing is enabled.
    fontFamily?: string;        // Valid values are 'Calibri', 'ComicSans', and 'Sitka'.
};
```

### <a name="launchresponse"></a>LaunchResponse

Contém a resposta da chamada para `ImmersiveReader.launchAsync` . Note que uma referência ao `iframe` que contém o Leitor Imersivo pode ser acedida através de `container.firstChild` .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
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
| BadArgument | O argumento fornecido é inválido, consulte `message` para mais detalhes. |
| Tempo Limite | O Leitor Imersivo não conseguiu carregar dentro do intervalo especificado. |
| TokenExpired | O token fornecido expirou. |
| Acelerador | O limite da taxa de chamada foi ultrapassado. |

### <a name="renderbuttonsoptions"></a>Prestações DebutonsOptions

Opções para a prestação dos botões Imersivos Reader.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

## <a name="launching-the-immersive-reader"></a>Lançamento do Leitor Imersivo

O SDK fornece um estilo predefinido para o botão de lançamento do Leitor Imersivo. Utilize o `immersive-reader-button` atributo de classe para ativar este estilo. Consulte [este artigo](./how-to-customize-launch-button.md) para mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Utilize os seguintes atributos para configurar o aspeto e a sensação do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode `icon` `text` ser, ou `iconAndText` . . Incumprimentos a `icon` . |
| `data-locale` | Define o local. Por exemplo, `en-US` ou `fr-FR`. Incumprimentos em inglês `en` . |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. Predefinições a 20px. |

## <a name="html-support"></a>Suporte HTML

| HTML | Conteúdo suportado |
| --------- | ----------- |
| Estilos de fonte | Arrojado, Itálico, Sublinhado, Código, Strikethrough, Superscript, Subscript |
| Listas não ordenadas | Disco, Círculo, Quadrado |
| Listas ordenadas | Decimal, Superior Alfa, Inferior-Alfa, Superior-Romano, Inferior-Romano |

As etiquetas não suportadas serão comparadas. Imagens e tabelas não são suportadas atualmente.

## <a name="browser-support"></a>Browser support (Suporte do browser)

Utilize as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o Leitor Imersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Quickstart: Criar uma aplicação web que lança o Leitor Imersivo (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)

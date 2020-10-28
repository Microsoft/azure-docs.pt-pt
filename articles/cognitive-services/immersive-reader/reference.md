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
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636532"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Referência SDK do leitor imersivo JavaScript (v1.1)

O SDK do leitor imersivo contém uma biblioteca JavaScript que lhe permite integrar o Leitor Imersivo na sua aplicação.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>lançarAsync

Lança o Leitor Imersivo dentro de uma `iframe` aplicação web. Note que o tamanho do seu conteúdo está limitado a um máximo de 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>lançar Parâmetros De aync

| Nome | Tipo | Description |
| ---- | ---- |------------ |
| `token` | cadeia (de carateres) | O sinal de autenticação AD Azure. Consulte [como criar um recurso de leitor imersivo](./how-to-create-immersive-reader.md) para mais detalhes. |
| `subdomain` | cadeia | O subdomínio personalizado do seu recurso Immersive Reader em Azure. Consulte [como criar um recurso de leitor imersivo](./how-to-create-immersive-reader.md) para mais detalhes. |
| `content` | [Conteúdo](#content) | Um objeto que contenha o conteúdo a ser mostrado no Leitor Imersivo. |
| `options` | [Opções](#options) | Opções para configurar certos comportamentos do Leitor Imersivo. Opcional. |

#### <a name="returns"></a>Devoluções

Devolve um `Promise<LaunchResponse>` , que resolve quando o Leitor Imersivo está carregado. A `Promise` determinação é de um [`LaunchResponse`](#launchresponse) objeto.

#### <a name="exceptions"></a>Exceções

O devolvido `Promise` será rejeitado com um objeto se o Leitor [`Error`](#error) Imersivo não carregar. Para mais informações, consulte os [códigos de erro.](#error-codes)

<br>

## <a name="close"></a>fechar

Fecha o Leitor Imersivo.

Um exemplo de caso de utilização para esta função é se o botão de saída estiver escondido definindo ```hideExitButton: true``` [as opções](#options). Em seguida, um botão diferente (por exemplo, a seta traseira de um cabeçalho móvel) pode chamar esta ```close``` função quando é clicada.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Botão de lançamento do leitor imersivo

O SDK fornece um estilo predefinido para o botão de lançamento do Leitor Imersivo. Utilize o `immersive-reader-button` atributo de classe para ativar este estilo. Consulte [como personalizar o botão Leitor Imersivo](./how-to-customize-launch-button.md) para obter mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Atributos opcionais

Utilize os seguintes atributos para configurar o aspeto e a sensação do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode `icon` `text` ser, ou `iconAndText` . . Incumprimentos a `icon` . |
| `data-locale` | Define o local. Por exemplo, `en-US` ou `fr-FR`. Incumprimentos em inglês `en` . |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. Predefinições a 20px. |

<br>

## <a name="renderbuttons"></a>renderAbtons

A ```renderButtons``` função não é necessária se estiver a utilizar a orientação [do botão How-To Customize o botão Leitor Imersivo.](./how-to-customize-launch-button.md)

Esta função tem estilos e atualiza os elementos do botão Leitor Imersivo do documento. Se ```options.elements``` for fornecido, os botões serão renderizados dentro de cada elemento fornecido em ```options.elements``` . A utilização do ```options.elements``` parâmetro é útil quando tem várias secções no documento para lançar o Leitor Imersivo, e quer uma forma simplificada de tornar vários botões com o mesmo estilo, ou pretender tornar os botões com um padrão de design simples e consistente. Para utilizar esta função com o parâmetro de [opções renderbuttons,](#renderbuttons-options) ligue ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` para a carga da página como demonstrado no corte de código abaixo. Caso contrário, os botões serão renderizados dentro dos elementos do documento que têm a classe ```immersive-reader-button``` indicada no [botão Como Personalizar o botão Leitor Imersivo](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Consulte os [Atributos Opcionais](#optional-attributes) acima para obter mais opções de renderização. Para utilizar estas opções, adicione qualquer um dos atributos de opção a cada um ```HTMLDivElement``` na sua página HTML.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderAmetões De Botões

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| `options` | [renderAs opções de Botões](#renderbuttons-options) | Opções para configurar certos comportamentos da função renderbuttons. Opcional. |

### <a name="renderbuttons-options"></a>renderIzar Opções de Botões

Opções para a prestação dos botões Imersivos Reader.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>renderButtons Parâmetros de opções

| Definição | Tipo | Descrição |
| ------- | ---- | ----------- |
| elementos | HTMLDivElement[] | Elementos para tornar os botões Imersivos do Leitor dentro |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Contém a resposta da chamada para `ImmersiveReader.launchAsync` . Note que uma referência ao `iframe` que contém o Leitor Imersivo pode ser acedida através de `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>Lançar Parâmetros de Resposta

| Definição | Tipo | Descrição |
| ------- | ---- | ----------- |
| contentor | HTMLDivElement | Elemento HTML que contém o iframe imivemero reader. |
| sessionId | String | Identificador globalmente único para esta sessão, usado para depurar. |
 
## <a name="error"></a>Erro

Contém informações sobre um erro.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Parâmetros de erro

| Definição | Tipo | Descrição |
| ------- | ---- | ----------- |
| code | String | Um de um conjunto de códigos de erro. Ver [códigos de erro](#error-codes). |
| message | String | Representação legível pelo erro. |

#### <a name="error-codes"></a>Códigos de erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, ver `message` parâmetro do [Erro](#error). |
| Tempo Limite | O Leitor Imersivo não conseguiu carregar dentro do intervalo especificado. |
| TokenExpired | O token fornecido expirou. |
| Acelerador | O limite da taxa de chamada foi ultrapassado. |

<br>

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no Leitor Imersivo.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Parâmetros de conteúdo

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| título | String | Texto do título mostrado no topo do Leitor Imersivo (opcional) |
| pedaços | [Pedaço[]](#chunk) | Matriz de pedaços |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Segmento

Um único pedaço de dados, que será passado para o conteúdo do Leitor Imersivo.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Parâmetros de pedaços

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| conteúdo | String | A cadeia que contém o conteúdo enviado ao Leitor Imersivo. |
| Lang | String | Linguagem do texto, o valor está no formato iETF BCP 47 imposições, por exemplo, en, es-ES. A linguagem será detetada automaticamente se não for especificada. Veja [Supported Languages](#supported-languages) (Idiomas Suportados). |
| mímicaType | cadeia | Texto simples, MathML, HTML & os formatos Microsoft Word DOCX são suportados. Consulte [os tipos de MIME suportados](#supported-mime-types) para obter mais detalhes. |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Tipos de MIME suportados

| Tipo de MIME | Descrição |
| --------- | ----------- |
| texto/planície | Texto simples. |
| text/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicação/mathml+xml | Linguagem de marcação matemática (MathML). [Saiba mais](./how-to/display-math.md).
| ument de aplicação/vnd.openxmlformats-officedocument.wordprocessingml.doc | Documento de formato Microsoft Word .docx.


<br>

## <a name="options"></a>Opções

Contém propriedades que configuram certos comportamentos do Leitor Imersivo.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Parâmetros de opções

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| uiLang | String | Idioma da UI, o valor está no formato iETF BCP 47 imposições, por exemplo, en, es-ES. Predefinições no idioma do navegador se não for especificado. |
| tempo limite | Número | Duração (em milissegundos) antes [do lançamentoAsync](#launchasync) falha com um erro de tempo limite (por defeito é 15000 ms). Este intervalo aplica-se apenas ao lançamento inicial da página Reader, onde o sucesso é observado quando a página Reader abre e o girador começa. Não deve ser necessário um ajuste do tempo limite. |
| uiZIndex | Número | Z-index do iframe que será criado (o padrão é 1000). |
| useWebview | Booleano| Utilize uma etiqueta webview em vez de um iframe, para compatibilidade com as Aplicações do Chrome (o padrão é falso). |
| onExit | Função | Executa quando o Leitor Imersivo sai. |
| permitir o ecrã Del | Booleano | A capacidade de alternar o ecrã completo (o padrão é verdadeiro). |
| HideExitButton | Booleano | Se deve ou não esconder a seta do botão de saída do Leitor Imersivo (o padrão é falso). Isto só deve ser verdade se existir um mecanismo alternativo para sair do Leitor Imersivo (por exemplo, a seta traseira de uma barra de ferramentas móvel). |
| cookiePolicy | [Política de Cookies](#cookiepolicy-options) | Definição para a utilização de cookies do leitor imersivo (padrão é *CookiePolicy.Disable).* É da responsabilidade da aplicação do anfitrião obter o consentimento necessário do utilizador de acordo com a Política de Conformidade com os Cookies da UE. Consulte [opções de política de cookies](#cookiepolicy-options). |
| desativarFirstRun | Booleano | Desative a primeira experiência de execução. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Opções para configurar Leia em voz alta. |
| traduções Opções | [Opções de Tradução](#translationoptions) | Opções para configurar a tradução. |
| displayOptions | [DisplayOptions](#displayoptions) | Opções para configurar o tamanho do texto, fonte, etc. |
| preferências | String | String retornado de onPreferencesChanged representando as preferências do utilizador no Leitor Imersivo. Consulte [parâmetros de definições](#settings-parameters) e [como armazenar as preferências do utilizador](./how-to-store-user-preferences.md) para obter mais informações. |
| onPreferencesOdado | Função | Executa quando as preferências do utilizador mudaram. Consulte [as preferências do utilizador para](./how-to-store-user-preferences.md) obter mais informações. |
| CustomDomain | String | Reservado para uso interno. Domínio personalizado onde o webapp imersivo do Leitor está hospedado (o padrão é nulo). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **IMPORTANTE** Não tente alterar programáticamente os valores da `-preferences` cadeia enviada para e a partir da aplicação Immersive Reader, pois isso pode causar um comportamento inesperado, resultando numa experiência de utilizador degradada para os seus clientes. As aplicações de anfitrião nunca devem atribuir um valor personalizado ou manipular a `-preferences` cadeia. Ao utilizar a `-preferences` opção de cadeia, utilize apenas o valor exato que foi devolvido da `-onPreferencesChanged` opção de retorno.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>Parâmetros de ReadAloudOptions

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| voz | String | Voz, "Feminino" ou "Macho". Note que nem todas as línguas apoiam ambos os sexos. |
| velocidade | Número | Velocidade de reprodução, deve ser entre 0,5 e 2,5, inclusive. |
| autoPlay | Booleano | Comece automaticamente a ler em voz alta quando o leitor imersivo estiver carregado. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> Devido às limitações do navegador, a reprodução automática não é suportada no Safari.

<br>

## <a name="translationoptions"></a>Opções de Tradução

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>Parâmetros de Opções de Tradução

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| language | String | Define a língua de tradução, o valor está no formato iETF BCP 47 imposição de tag, por exemplo, fr-FR, es-MX, zh-Hans-CN. É necessário ativar automaticamente a tradução de palavras ou documentos. |
| autoEnableDocumentTranslation | Booleano | Traduza automaticamente todo o documento. |
| autoEnableWordTranslation | Booleano | Ativar automaticamente a tradução de palavras. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>Parâmetros displayOptions

| Nome | Tipo | Descrição |
| ---- | ---- |------------ |
| textSize | Número | Define o tamanho do texto escolhido. |
| aumentar O espaçamento | Booleano | Define se o espaçamento do texto é alternado dentro ou fora. |
| fonteFamily | String | Define a fonte escolhida ("Calibri", "ComicSans" ou "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>Opções de Política de Cookies

```typescript
enum CookiePolicy { Disable, Enable }
```

**As definições listadas abaixo são apenas para fins informativos** . O Leitor Imersivo armazena as suas definições, ou preferências do utilizador, em cookies. Esta opção *cookiePolicy* **desativa** o uso de cookies por padrão para cumprir as leis de Conformidade com cookies da UE. Caso pretenda voltar a ativar os cookies e restaurar a funcionalidade padrão para as preferências do utilizador do Leitor Imersivo, terá de garantir que o seu website ou aplicação obtém o consentimento adequado do utilizador para ativar os cookies. Em seguida, para reecamar os cookies no Leitor Imersivo, tem de definir explicitamente a opção *cookiePolicy* para *CookiePolicy.Enable* ao lançar o Leitor Imersivo. A tabela abaixo descreve quais as configurações que o Leitor Imersivo armazena no seu cookie quando a opção *cookiePolicy* está ativada.

#### <a name="settings-parameters"></a>Parâmetros de definição

| Definição | Tipo | Descrição |
| ------- | ---- | ----------- |
| textSize | Número | Define o tamanho do texto escolhido. |
| fonteFamily | String | Define a fonte escolhida ("Calibri", "ComicSans" ou "Sitka"). |
| textSpacing | Número | Define se o espaçamento do texto é alternado dentro ou fora. |
| formattingEnabled | Booleano | Define se a formatação HTML é alternada ou desligada. |
| tema | String | Define o tema escolhido (por exemplo, "Luz", "Escuro"...). |
| syllabificationEnabled | Booleano | Define se a syllabificação alternada ou desligada. |
| substantivo | Booleano | que define se o destaque do substantivo é alternado dentro ou fora. |
| substantivo | String | Define a cor de destaque do substantivo escolhido. |
| verboHighlightingEnabled | Booleano | Define se o realce do verbo é alternado dentro ou fora. |
| verbHighlightingColor | String | Define a cor de destaque do verbo escolhido. |
| adjetivoHighlightingEnabled | Booleano | Define se o realce adjetivo é alternado dentro ou fora. |
| adjectivoHighlightingColor | String | Define a cor de realce adjetivo escolhida. |
| adverbHighlightingEnabled | Booleano | Define se o realce do advérte é alternado dentro ou fora. |
| adverbHighlightingColor | String | Define a cor de destaque do advérônio escolhido. |
| pictureDictionaryEnabled | Booleano | Define se o Dicionário de Imagens é alternado dentro ou fora. |
| posLabelsEnabled | Booleano | Define se o rótulo de texto sobrescrito de cada Parte de Discurso realçado é alternado dentro ou fora.  |

<br>

## <a name="supported-languages"></a>Idiomas Suportados

A funcionalidade de tradução do Leitor Imersivo suporta muitas línguas. Consulte [o Suporte linguístico](./language-support.md) para obter mais detalhes.

<br>

## <a name="html-support"></a>Suporte HTML

Quando a formatação estiver ativada, o seguinte conteúdo será prestado como HTML no Leitor Imersivo.

| HTML | Conteúdo suportado |
| --------- | ----------- |
| Estilos de fonte | Arrojado, Itálico, Sublinhado, Código, Strikethrough, Superscript, Subscript |
| Listas não ordenadas | Disco, Círculo, Quadrado |
| Listas ordenadas | Decimal, Superior Alfa, Alfa Inferior, Superior-Romano, Lower-Roman |

As etiquetas não suportadas serão comparadas. Imagens e tabelas não são suportadas atualmente.

<br>

## <a name="browser-support"></a>Browser support (Suporte do browser)

Utilize as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o Leitor Imersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Quickstart: Criar uma aplicação web que lança o Leitor Imersivo (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)

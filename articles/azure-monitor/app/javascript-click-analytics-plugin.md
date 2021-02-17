---
title: Clique no plugin de recolha automática de analíticos para insights de aplicação JavaScript SDK
description: Como instalar e utilizar o plugin de recolha automática do Click Analytics para o Sistema de Informações JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: 5ad3e1a5a4ff47fe3d5fee8b8bc79235838995b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593636"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Clique no plugin de recolha automática de analíticos para insights de aplicação JavaScript SDK

Este plugin rastreia automaticamente eventos de clique em páginas web e utiliza atributos de dados-* em elementos HTML para preencher telemetria de eventos.

## <a name="getting-started"></a>Introdução

Os utilizadores podem configurar o plugin de recolha automática click Analytics via npm.

### <a name="npm-setup"></a>configuração npm

Instalar pacote npm:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Como utilizar eficazmente o plugin

1. Os dados de telemetria gerados a partir dos eventos de clique são armazenados como `customEvents` na secção De Insights de Aplicação do portal Azure.
2. O `name` costumeEvent é povoado com base nas seguintes regras:
    1.  O `id` fornecido no será usado como o nome `data-*-id` personalizadoEvent. Por exemplo, se o elemento HTML clicado tiver o atributo "data-sample-id"="button1", então "botão1" será o nome personalizadoEvent.
    2. Se não existir tal atributo e se o `useDefaultContentNameOrId` for definido `true` na configuração, então o atributo HTML do elemento clicado `id` ou o nome de conteúdo do elemento será utilizado como o nome personalizadoEvent.
    3. Se `useDefaultContentNameOrId` for falso, então o nome customEvent será "not_specified".

    > [!TIP]
    > As nossas recomendações são para `useDefaultContentNameOrId` sermos verdadeiras para gerar dados significativos.  
3. `parentDataTag` faz duas coisas:
    1. Se esta etiqueta estiver presente, o plugin irá buscar os `data-*` atributos e valores de todos os elementos HTML dos elementos do elemento clicado.
    2. Para melhorar a eficiência, o plugin utiliza esta etiqueta como bandeira, quando encontrado irá impedir-se de continuar a processar o DOM (Modelo de Objeto de Documento) para cima.
    
    > [!CAUTION]
    > Uma vez `parentDataTag` utilizado, o SDK começará a procurar etiquetas parentais em toda a sua aplicação e não apenas o elemento HTML onde a usou.
4. `customDataPrefix` fornecida pelo utilizador deve sempre começar `data-` com, por `data-sample-` exemplo. Em HTML os `data-*` atributos globais formam uma classe de atributos chamados atributos de dados personalizados, que permitem a troca de informações de propriedade entre o HTML e a sua representação DOM por scripts. Os navegadores mais antigos (Internet Explorer, Safari) vão deixar cair atributos que não compreende, a menos que comecem por `data-` .

    O `*` in pode ser substituído por qualquer nome que a seguir à regra de `data-*` [produção dos nomes XML](https://www.w3.org/TR/REC-xml/#NT-Name) com as seguintes restrições:
    - O nome não deve começar com "xml", qualquer que seja o caso utilizado para estas letras.
    - O nome não deve conter qualquer ponto e vírgula (U+003A).
    - O nome não deve conter letras maiúsculas.

## <a name="configuration"></a>Configuração

| Nome                  | Tipo                               | Predefinição | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | Configuração de captura automática.                                                                                                         |
| callback              | [IValueCallback](#ivaluecallback)  | nulo    | Configuração de chamadas.                                                                                                                 |
| pageTags              | string                             | nulo    | Etiquetas de página.                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| nulo    | Etiquetas de dados personalizadas fornecidas para substituir tags predefinidos usadas para capturar dados de clique.                                                           |
| urlCollectHash        | boolean                            | false   | Permite o registo de valores após um caráter "#" do URL.                                                                          |
| urlCollectQuery       | boolean                            | false   | Permite o registo da cadeia de consulta do URL.                                                                                      |
| comportamentoValidator     | Função                           | nulo  | Função de retorno para usar para validação de `data-*-bhvr` valor. Para mais informações, aceda à [secção de comportamentoValidator](#behaviorvalidator).|
| defaultRightClickBhvr | número de cadeia (ou)                 | ''      | Valor de comportamento padrão quando ocorreu o evento Right Click. Este valor será ultrapassado se o elemento tiver o `data-*-bhvr` atributo. |
| dropInvalidEvents     | boolean                            | false   | Bandeira para deixar cair eventos que não têm dados de clique úteis.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Nome               | Tipo     | Predefinição | Descrição                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Função | nulo    | Função para anular o comportamento de captura de nome de página padrão.                           |
| pageActionPageTags | Função | nulo    | Uma função de retorno para aumentar as páginas predefinidosTags recolhidos durante o evento pageAction.  |
| conteúdoName        | Função | nulo    | Uma função de retorno para preencher conteúdo personalizadoName.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Nome                      | Tipo    | Predefinição   | Etiqueta padrão a utilizar em HTML |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | N/D         |Recolhe atributo HTML padrão para o nome de conteúdo quando um determinado elemento não está marcado com o Costume DefaultDataPrefix ou quando oDataPrefix personalizado não é fornecido pelo utilizador. |
| CustomDataPrefixo          | string  | `data-`   | `data-*`| Denominação de conteúdo de captura automática e valor dos elementos que estão marcados com prefixo fornecido. Por `data-*-id` exemplo, `data-<yourcustomattribute>` pode ser utilizado nas tags HTML.   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| O Plugin suporta um atributo de blob JSON em vez de `data-*` atributos individuais. |
| MeTadaPrefixo            | string  | nulo      | N/D  | Captura automática o nome e conteúdo do meta-elemento da cabeça HTML com prefixo fornecido durante a captura. Por exemplo, `custom-` pode ser usado na meta tag HTML. |
| capturarAllMetaDataContent | boolean | false     | N/D   | Capture automaticamente todos os nomes e conteúdos dos elementos de meta do html head. A predefinição é falso. Se estiver ativado, irá substituir o metadaDataPrefix. |
| ParentDataTag             | string  | nulo      |  N/D  | Para de atravessar o DOM para capturar o nome do conteúdo e o valor dos elementos quando encontrados com esta etiqueta. Por exemplo, `data-<yourparentDataTag>` pode ser utilizado nas tags HTML.|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| Os elementos HTML com este atributo serão ignorados pelo plugin para capturar dados de telemetria.|

### <a name="behaviorvalidator"></a>comportamentoValidator

O comportamento Funciona oValidator verifica automaticamente se os comportamentos marcados no código estão em conformidade com uma lista pré-definida. Isto garante que os comportamentos marcados são consistentes com a taxonomia estabelecida da sua empresa. Não é necessário ou esperado que a maioria dos clientes do Azure Monitor utilizem isto, mas está disponível para cenários avançados. Existem três comportamentos diferentes Funções de retorno doValidator expostas como parte desta extensão. No entanto, os utilizadores podem utilizar as suas próprias funções de retorno se as funções expostas não resolverem o seu requisito. A intenção é trazer a sua própria estrutura de dados de comportamentos, o plugin usa esta função validador enquanto extrai os comportamentos das etiquetas de dados.

| Nome                   | Descrição                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| ComportamentoValueValidator | Utilize esta função de retorno se a estrutura de dados dos seus comportamentos for uma matriz de cordas.|
| BehaviorMapValidator   | Utilize esta função de retorno se a estrutura de dados dos seus comportamentos for um dicionário.       |
| ComportamentoEnumValidator  | Utilize esta função de retorno se a estrutura de dados dos seus comportamentos for um Enum.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Utilização de amostra com comportamentoValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Aplicativo de amostra

[Aplicação web simples com Click Analytics Auto-coleção Plugin ativado](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Passos seguintes

- Consulte o [Pacote De Repositório GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) e [NPM](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) para o Plugin de Recolha Automática Click Analytics.
- Use [análise de eventos na experiência de utilização](usage-segmentation.md) para analisar cliques de topo e corte pelas dimensões disponíveis.
- Encontre os dados de clique no campo de conteúdo dentro do atributo CustomEvents na [Tabela De Registos.](../logs/log-analytics-tutorial.md#write-a-query) Consulte [a App sample para](https://go.microsoft.com/fwlink/?linkid=2152871) obter orientações adicionais.
- Construa um [Livro de Trabalho](../visualize/workbooks-overview.md) para criar visualizações personalizadas de dados de clique.

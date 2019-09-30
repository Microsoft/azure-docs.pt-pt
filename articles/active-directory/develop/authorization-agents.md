---
title: Agentes de autorização e como habilitá-los | Azure
description: Saiba mais sobre os diferentes agentes de autorização que a MSAL (biblioteca de autenticação da Microsoft) permite que seu aplicativo Android use e como habilitá-los.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7daf40d6c6e552d6b76e424359f57f031641039
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679818"
---
# <a name="authorization-agents-android"></a>Agentes de autorização (Android)

Este artigo descreve os diferentes agentes de autorização que a MSAL (biblioteca de autenticação da Microsoft) permite que seu aplicativo use e como habilitá-los.

A escolha de uma estratégia específica para agentes de autorização é opcional e representa a funcionalidade adicional que os aplicativos podem personalizar. A maioria dos aplicativos usará os padrões MSAL (consulte [entender o arquivo de configuração do MSAL do Android](msal-configuration.md) para ver os vários padrões).

O MSAL dá suporte à autorização usando um `WebView` ou o navegador do sistema.  A imagem abaixo mostra como ele se parece usando o `WebView` ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de logon do MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicações de logon único

Por padrão, os aplicativos integrados ao MSAL usam as guias personalizadas do navegador do sistema para autorizar. Ao contrário das exibições, as guias personalizadas compartilham um jar de cookie com o navegador do sistema padrão, permitindo menos entradas com a Web ou outros aplicativos nativos que se integraram com guias personalizadas.

Se o aplicativo usar uma estratégia `WebView` sem integrar Microsoft Authenticator ou Portal da Empresa suporte ao seu aplicativo, os usuários não terão uma experiência de logon único (SSO) em todo o dispositivo ou entre aplicativos nativos e Web.

Se o aplicativo usar MSAL com suporte a Microsoft Authenticator ou Portal da Empresa, os usuários poderão ter uma experiência de SSO entre aplicativos se o usuário tiver uma entrada ativa com um dos aplicativos.

## <a name="webview"></a>WebView

Para usar o WebView no aplicativo, coloque a seguinte linha no JSON de configuração do aplicativo que é passado para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao usar o `WebView` no aplicativo, o usuário entra diretamente no aplicativo. Os tokens são mantidos dentro da área restrita do aplicativo e não estão disponíveis fora do jar do cookie do aplicativo. Como resultado, o usuário não pode ter uma experiência de SSO entre aplicativos, a menos que os aplicativos se integrem com o autenticador ou Portal da Empresa.

No entanto, `WebView` fornece a capacidade de personalizar a aparência da interface do usuário de entrada. Consulte [Webviews do Android](https://developer.android.com/reference/android/webkit/WebView) para obter mais informações sobre como fazer essa personalização.

## <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais guias personalizadas

Por padrão, o MSAL usa o navegador e uma estratégia de [guias personalizadas](https://developer.chrome.com/multidevice/android/customtabs) . Você pode indicar explicitamente essa estratégia para evitar alterações em versões futuras para `DEFAULT` usando a seguinte configuração de JSON no arquivo de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Use essa abordagem para fornecer uma experiência de SSO por meio do navegador do dispositivo. O MSAL usa um jar de cookie compartilhado, que permite que outros aplicativos nativos ou aplicativos Web obtenham o SSO no dispositivo usando o cookie de sessão persistente definido por MSAL.

## <a name="browser-selection-heuristic"></a>Heurística de seleção do navegador

Como é impossível para MSAL especificar o pacote exato do navegador a ser usado em cada uma das grandes matrizes de telefones Android, o MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO entre dispositivos.

MSAL recupera a lista completa de navegadores instalados no dispositivo para selecionar qual navegador usar. A lista está na ordem retornada pelo Gerenciador de pacotes, que reflete indiretamente as preferências do usuário. Por exemplo, o navegador padrão, se definido, é a primeira entrada na lista. O _primeiro_ navegador na lista será escolhido, independentemente de ele dar suporte a guias personalizadas. Se o navegador oferecer suporte a guias personalizadas, o MSAL iniciará a guia personalizada. As guias personalizadas têm uma aparência mais próxima a um `WebView` no aplicativo e permitem a personalização básica da interface do usuário. Consulte [guias personalizadas no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver nenhum pacote de navegador no dispositivo, o MSAL usará o @no__t no aplicativo-0.

A ordem dos navegadores na lista de navegadores é determinada pelo sistema operacional. Ele é, na ordem, mais preferencial para o mínimo. Se a configuração padrão do dispositivo não for alterada, o mesmo navegador deverá ser iniciado para cada conexão para garantir uma experiência de SSO.

> [!NOTE]
> MSAL não prefere mais o Chrome se outro navegador estiver definido como padrão. Por exemplo, em um dispositivo que tem o Chrome e outro navegador pré-instalado, o MSAL usará o navegador que o usuário definiu como o padrão.

### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver se eles são redirecionados corretamente para o `"redirect_uri"` especificado no arquivo de configuração:

| | Navegador interno | Chrome | Opera  | Microsoft Edge | Navegador de UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passá | passá |Não aplicável |Não aplicável |Não aplicável |Não aplicável |
| Samsung S7 (API 25) | passá | passá | passá | passá | Recuperação |passá |
| Huawei (API 26) |Pass * * | passá | Recuperação | passá | passá |passá |
| Vivo (API 26) |passá|passá|passá|passá|passá|Recuperação|
| Pixel 2 (API 26) |passá | passá | passá | passá | Recuperação |passá |
| Oppo | passá | Não aplicável * * * |Não aplicável  |Não aplicável |Não aplicável | Não aplicável|
| OnePlus (API 25) |passá | passá | passá | passá | Recuperação |passá |
| Nexus (API 28) |passá | passá | passá | passá | Recuperação |passá |
|MI | passá | passá | passá | passá | Recuperação |passá |

\* O navegador interno da Samsung é a Samsung Internet.  
\* * O navegador interno do Huawei é o navegador Huawei.  
O navegador padrão não pode ser alterado dentro da configuração do dispositivo Oppo.

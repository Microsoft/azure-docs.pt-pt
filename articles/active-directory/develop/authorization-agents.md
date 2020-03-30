---
title: Agentes de autorização e como os habilitar Azure
description: Conheça os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que a sua aplicação Android utilize e como as habilitar.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085326"
---
# <a name="authorization-agents-android"></a>Agentes de autorização (Android)

Este artigo descreve os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que a sua aplicação utilize e como as habilitar.

Escolher uma estratégia específica para agentes de autorização é opcional e representa aplicações de funcionalidades adicionais que podem personalizar. A maioria das aplicações usará os predefinições do MSAL (ver Compreender o ficheiro de [configuração Android MSAL](msal-configuration.md) para ver os vários incumprimentos).

A MSAL suporta `WebView`a autorização utilizando um , ou o navegador do sistema.  A imagem abaixo mostra como `WebView`fica usando o , ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de login mSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicações únicas de inscrição

Por padrão, as aplicações integradas com o MSAL utilizam os Separadores Personalizados do navegador do sistema para autorizar. Ao contrário do WebViews, os Custom Tabs partilham um frasco de cookies com o navegador de sistema padrão, permitindo menos inscrições com web ou outras aplicações nativas que se integraram com Separadores Personalizados.

Se a aplicação utilizar uma `WebView` estratégia sem integrar o suporte do Microsoft Authenticator ou do Portal da Empresa na sua aplicação, os utilizadores não terão uma experiência single sign on (SSO) em todo o dispositivo ou entre aplicações nativas e aplicações web.

Se a aplicação utilizar o MSAL com suporte ao Microsoft Authenticator ou ao Portal da Empresa, os utilizadores podem ter uma experiência SSO em aplicações se o utilizador tiver um acesso ativo com uma das aplicações.

## <a name="webview"></a>WebView

Para utilizar o WebView na aplicação, coloque a seguinte linha na configuração da aplicação JSON que é passada para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao utilizar a `WebView`aplicação in-app, o utilizador assina diretamente na aplicação. Os tokens são mantidos dentro da caixa de areia da app e não estão disponíveis fora do pote de cookies da aplicação. Como resultado, o utilizador não pode ter uma experiência SSO em todas as aplicações, a menos que as aplicações se integrem com o Autenticador ou Portal da Empresa.

No `WebView` entanto, fornece a capacidade de personalizar o look e sentir por UI de inscrição. Consulte o [Android WebViews](https://developer.android.com/reference/android/webkit/WebView) para saber mais sobre como fazer esta personalização.

## <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais separadores personalizados

Por padrão, a MSAL utiliza o navegador e uma estratégia [de separadores personalizados.](https://developer.chrome.com/multidevice/android/customtabs) Pode indicar explicitamente esta estratégia para evitar `DEFAULT` alterações em futuras versões, utilizando a seguinte configuração JSON no ficheiro de configuração personalizado:

```json
"authorization_user_agent" : "BROWSER"
```

Utilize esta abordagem para proporcionar uma experiência SSO através do navegador do dispositivo. A MSAL utiliza um frasco de cookies partilhado, que permite que outras aplicações nativas ou aplicações web atinjam o SSO no dispositivo, utilizando o conjunto de cookies de sessão de persistência definido pela MSAL.

## <a name="browser-selection-heuristic"></a>Heurística de seleção de navegador

Como é impossível para o MSAL especificar o pacote exato do navegador para usar em cada uma das amplas gamas de telefones Android, o MSAL implementa um heurístico de seleção de navegador que tenta fornecer o melhor SSO cross-device.

A MSAL recupera a lista completa dos navegadores instalados no dispositivo para selecionar qual o navegador a utilizar. A lista está na ordem devolvida pelo gestor do pacote, o que reflete indiretamente as preferências do utilizador. Por exemplo, o navegador predefinido, se definido, é a primeira entrada na lista. O _primeiro_ navegador da lista será escolhido independentemente de suportar separadores personalizados. Se o navegador suportar separadores personalizados, o MSAL lançará o Separador Personalizado. `WebView` Consulte [Separadores Personalizados no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver pacotes de navegador no dispositivo, o `WebView`MSAL utiliza a aplicação .

A ordem dos navegadores na lista de navegadores é determinada pelo sistema operativo. É por ordem da maioria preferida para menos. Se a definição de predefinição do dispositivo não for alterada, o mesmo navegador deve ser lançado para cada sinal para garantir uma experiência SSO.

> [!NOTE]
> A MSAL já nem sempre prefere o Chrome se outro navegador estiver definido como padrão. Por exemplo, num dispositivo que tenha o Chrome e outro navegador pré-instalado, o MSAL utilizará o navegador que o utilizador definiu como padrão.

### <a name="tested-browsers"></a>Navegadores Testados

Os seguintes navegadores foram testados para ver `"redirect_uri"` se redirecionam corretamente para o especificado no ficheiro de configuração:

| | Navegador incorporado | Chrome | Ópera  | Microsoft Edge | UC Browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passar | passar |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | passe* | passar | passar | passar | falhar |passar |
| Huawei (API 26) |passe** | passar | falhar | passar | passar |passar |
| Vivo (API 26) |passar|passar|passar|passar|passar|falhar|
| Pixel 2 (API 26) |passar | passar | passar | passar | falhar |passar |
| Oppo | passar | não aplicável*** |não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passar | passar | passar | passar | falhar |passar |
| Nexus (API 28) |passar | passar | passar | passar | falhar |passar |
|MI | passar | passar | passar | passar | falhar |passar |

*O navegador incorporado da Samsung é a Samsung Internet.  
O navegador integrado da Huawei é o Huawei Browser.  
O navegador predefinido não pode ser alterado dentro da definição do dispositivo Oppo.

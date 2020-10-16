---
title: Agentes de autorização e como viabilizar- lhes o mesmo Rio Azure
description: Saiba mais sobre os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que a sua aplicação Android possa ser utilizada e como as capacitar.
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
ms.openlocfilehash: 83a33fa3891e01c484f298f22d67467bc54a7618
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551982"
---
# <a name="authorization-agents-android"></a>Agentes de autorização (Android)

Este artigo descreve os diferentes agentes de autorização que a Microsoft Authentication Library (MSAL) permite que a sua aplicação utilize e como as capacitar.

Escolher uma estratégia específica para os agentes de autorização é opcional e representa aplicações de funcionalidade adicionais que podem personalizar. A maioria das aplicações utilizará os padrão MSAL (ver [Consulte o ficheiro de configuração DO Android MSAL](msal-configuration.md) para ver as várias padrão).

A MSAL suporta a autorização utilizando um `WebView` , ou o navegador do sistema.  A imagem abaixo mostra como parece usando o `WebView` , ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de login da MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicações únicas de inscrição

Por predefinição, as aplicações integradas com o MSAL utilizam os Separadores Personalizados do navegador do sistema para autorizar. Ao contrário do WebViews, os Separadores Personalizados partilham um frasco de cookies com o navegador padrão do sistema, permitindo menos insusões com web ou outras aplicações nativas que se integraram com Separadores Personalizados.

Se a aplicação utilizar uma estratégia sem integrar o `WebView` suporte do Microsoft Authenticator ou do Portal da Empresa na sua aplicação, os utilizadores não terão uma experiência de Sign On (SSO) em todo o dispositivo ou entre aplicações nativas e aplicações web.

Se a aplicação utilizar o MSAL com o suporte do Microsoft Authenticator ou do Portal da Empresa, os utilizadores poderão ter uma experiência SSO em todas as aplicações se o utilizador tiver um s-in ativo com uma das aplicações.

## <a name="webview"></a>WebView

Para utilizar o WebView in-app, coloque a seguinte linha na configuração da aplicação JSON que é passada para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao utilizar a aplicação `WebView` in-app, o utilizador assina diretamente na aplicação. Os tokens são mantidos dentro da caixa de areia da app e não estão disponíveis fora do pote de cookies da aplicação. Como resultado, o utilizador não pode ter uma experiência SSO em todas as aplicações, a menos que as aplicações se integrem com o Autenticador ou Portal da Empresa.

No entanto, `WebView` fornece a capacidade de personalizar o visual e a sensação de UI de inscrição. Consulte [o Android WebViews](https://developer.android.com/reference/android/webkit/WebView) para saber mais sobre como fazer esta personalização.

## <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais separadores personalizados

Por padrão, o MSAL utiliza o navegador e uma estratégia [de separadores personalizados.](https://developer.chrome.com/multidevice/android/customtabs) Pode indicar explicitamente esta estratégia para evitar alterações em futuras versões `DEFAULT` utilizando a seguinte configuração JSON no ficheiro de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Utilize esta abordagem para proporcionar uma experiência SSO através do navegador do dispositivo. A MSAL utiliza um frasco de cookies partilhado, que permite que outras aplicações nativas ou aplicações web atinjam SSO no dispositivo utilizando o cookie de sessão de persistência definido pelo MSAL.

## <a name="browser-selection-heuristic"></a>Heurística de seleção de navegador

Como é impossível para a MSAL especificar o pacote de navegador exato para usar em cada um dos amplos conjuntos de telefones Android, o MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO de dispositivos cruzados.

A MSAL recupera a lista completa de navegadores instalados no dispositivo para selecionar qual o navegador a utilizar. A lista encontra-se na encomenda devolvida pelo gestor do pacote, o que reflete indiretamente as preferências do utilizador. Por exemplo, o navegador predefinido, se definido, é a primeira entrada na lista. O _primeiro_ navegador da lista será escolhido independentemente de suportar separadores personalizados. Se o navegador suportar Separadores Personalizados, o MSAL lançará o Separador `WebView` Personalizado. Consulte [separadores personalizados no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver pacotes de navegador no dispositivo, o MSAL utiliza a aplicação `WebView` in-app .

A ordem dos navegadores na lista de navegadores é determinada pelo sistema operativo. É por ordem da maioria preferida ao mínimo. Se a definição por defeito do dispositivo não for alterada, o mesmo navegador deve ser lançado para cada sinal para garantir uma experiência SSO.

> [!NOTE]
> O MSAL já não prefere sempre o Chrome se outro navegador estiver definido como padrão. Por exemplo, num dispositivo que tenha tanto o Chrome como outro navegador pré-instalado, o MSAL irá utilizar o navegador que o utilizador definiu como padrão.

### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver se redirecionam corretamente para o `"redirect_uri"` especificado no ficheiro de configuração:

| Dispositivo | Navegador Incorporado | Chrome | Ópera  | Microsoft Edge | Navegador UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passar | passar |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | passe* | passar | passar | passar | falhar |passar |
| Huawei (API 26) |passe** | passar | falhar | passar | passar |passar |
| Vivo (API 26) |passar|passar|passar|passar|passar|falhar|
| Pixel 2 (API 26) |passar | passar | passar | passar | falhar |passar |
| Rio Oppo | passar | não aplicável*** |não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passar | passar | passar | passar | falhar |passar |
| Nexus (API 28) |passar | passar | passar | passar | falhar |passar |
|MI | passar | passar | passar | passar | falhar |passar |

*O navegador incorporado da Samsung é a Samsung Internet.  
**O navegador incorporado da Huawei é o Navegador Huawei.  
O navegador predefinido não pode ser alterado dentro da definição do dispositivo Oppo.

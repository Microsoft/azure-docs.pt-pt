---
title: Modo de dispositivo partilhado para dispositivos iOS
titleSuffix: Microsoft identity platform | Azure
description: Saiba como ativar o modo de dispositivo partilhado para permitir que os Trabalhadores da Firstline partilhem um dispositivo iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a97e14bcb68629f5f175a4913146187949af08be
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561068"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modo de dispositivo partilhado para dispositivos iOS

>[!IMPORTANT]
> Esta característica [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Os trabalhadores da firstline, como os associados de retalho, os tripulantes de voo e os trabalhadores de serviços de campo, usam frequentemente um dispositivo móvel partilhado para realizar o seu trabalho. Estes dispositivos partilhados podem apresentar riscos de segurança se os seus utilizadores partilharem as suas palavras-passe ou PINs, intencionalmente ou não, para acederem a dados de clientes e negócios no dispositivo partilhado.

O modo de dispositivo partilhado permite configurar um dispositivo iOS 13 ou superior para ser partilhado de forma mais fácil e segura pelos colaboradores. Os colaboradores podem iniciar seduca e aceder rapidamente à informação do cliente. Quando terminarem o turno ou a tarefa, podem assinar fora do dispositivo e está imediatamente pronto para ser usado pelo próximo funcionário.

O modo de dispositivo partilhado também fornece a gestão do dispositivo com suporte de identidade da Microsoft.

Esta funcionalidade utiliza a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para gerir os utilizadores do dispositivo e para distribuir o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Crie uma aplicação de modo dispositivo compartilhado

Para criar uma aplicação de modo de dispositivo compartilhado, os desenvolvedores e os administradores de dispositivos em nuvem trabalham em conjunto:

1. **Os desenvolvedores** de aplicações escrevem uma aplicação de uma única conta (as aplicações de múltiplas contas não são suportadas no modo de dispositivo partilhado) e escrevem código para lidar com coisas como a assinatura do dispositivo partilhado.

1. **Os administradores de dispositivos** preparam o dispositivo para ser partilhado utilizando um fornecedor de gestão de dispositivos móveis (MDM) como o Microsoft Intune para gerir os dispositivos na sua organização. O MDM empurra a aplicação microsoft Authenticator para os dispositivos e liga o "Modo Partilhado" para cada dispositivo através de uma atualização de perfil para o dispositivo. Esta definição de Modo Partilhado é o que altera o comportamento das aplicações suportadas no dispositivo. Esta configuração do fornecedor MDM define o modo de dispositivo partilhado para o dispositivo e permite o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) que é necessário para o modo de dispositivo partilhado.

1. [ **Requerido apenas durante a Pré-visualização Pública** ] Um utilizador com [função de Administrador de Dispositivos Cloud](../roles/permissions-reference.md#cloud-device-administrator) deve então lançar a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e juntar o seu dispositivo à organização.

    Para configurar a adesão aos seus papéis organizacionais no portal Azure: **Azure Ative Directory**  >  **Roles and Administrators** Cloud  >  **Device Administrators**

As seguintes secções ajudam-no a atualizar a sua aplicação para suportar o modo de dispositivo partilhado.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Use Intune para permitir o modo de dispositivo partilhado & extensão SSO

> [!NOTE]
> O passo seguinte só é necessário durante a visualização pública.

O seu dispositivo necessita de ser configurado para suportar o modo de dispositivo partilhado. Deve ter o iOS 13+ instalado e estar matriculado no MDM. A configuração do MDM também precisa de permitir [o plug-in SSO da Microsoft Enterprise para dispositivos Apple](apple-sso-plugin.md). Para saber mais sobre as extensões SSO, consulte o vídeo da [Apple.](https://developer.apple.com/videos/play/tech-talks/301/)

1. No Portal de Configuração Intune, informe o dispositivo para ativar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) com a seguinte configuração:

    - **Tipo** : Redirecionamento
    - **ID de extensão** : com.microsoft.azureauthenticator.ssoextension
    - **Team ID** : (este campo não é necessário para iOS)
    - **URLs:**   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Dados adicionais para configurar:**
      - Chave: compartilhadoDeviceMode
      - Tipo: Boolean
      - Valor: verdadeiro

    Para obter mais informações sobre a configuração com o Intune, consulte a documentação de [configuração Intune](/intune/configuration/ios-device-features-settings).

1. Em seguida, configuure o seu MDM para empurrar a aplicação Microsoft Authenticator para o seu dispositivo através de um perfil MDM.

    Desa ajuste as seguintes opções de configuração para ligar o modo dispositivo partilhado:

    - Configuração 1:
      - Chave: compartilhadoDeviceMode
      - Tipo: Boolean
      - Valor: verdadeiro

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modifique a sua aplicação iOS para suportar o modo de dispositivo partilhado

Os seus utilizadores dependem de si para garantir que os seus dados não são vazados para outro utilizador. As seguintes secções fornecem sinais úteis para indicar à sua aplicação que ocorreu uma alteração e deve ser manuseada.

É responsável por verificar o estado do utilizador no dispositivo sempre que a sua aplicação é utilizada e, em seguida, limpar os dados do utilizador anterior. Isto inclui se for recarregado a partir do fundo em multi-tarefas.

Numa alteração de utilizador, deve certificar-se de que ambos os dados do utilizador anterior são limpos e que quaisquer dados em cache apresentados na sua aplicação são removidos. Recomendamos vivamente que você e a sua empresa realizem um processo de revisão de segurança depois de atualizar a sua app para suportar o modo de dispositivo partilhado.

### <a name="detect-shared-device-mode"></a>Detetar o modo de dispositivo partilhado

A deteção do modo dispositivo partilhado é importante para a sua aplicação. Muitas aplicações exigirão uma alteração na sua experiência de utilizador (UX) quando a aplicação é usada num dispositivo partilhado. Por exemplo, a sua aplicação pode ter uma funcionalidade de "Inscrição", o que não é apropriado para um Trabalhador da Firstline porque provavelmente já têm uma conta. Também pode querer adicionar segurança extra ao tratamento de dados da sua aplicação se estiver em modo de dispositivo partilhado.

Utilize a `getDeviceInformationWithParameters:completionBlock:` API no `MSALPublicClientApplication` para determinar se uma aplicação está a ser em execução num dispositivo em modo de dispositivo partilhado.

Os seguintes fragmentos de código mostram exemplos de utilização da `getDeviceInformationWithParameters:completionBlock:` API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obtenha o utilizador de assinatura e determine se um utilizador mudou no dispositivo

Outra parte importante do modo de suporte ao dispositivo partilhado é determinar o estado do utilizador no dispositivo e limpar os dados da aplicação se um utilizador tiver mudado ou se não houver qualquer utilizador no dispositivo. É responsável por garantir que os dados não são vazados para outro utilizador.

Pode utilizar `getCurrentAccountWithParameters:completionBlock:` a API para consultar a conta de insusição atual no dispositivo.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globalmente assine um utilizador

Quando um dispositivo é configurado como um dispositivo partilhado, a sua aplicação pode ligar para a `acquireTokenWithParameters:completionBlock:` API para assinar na conta. A conta estará disponível globalmente para todas as aplicações elegíveis no dispositivo depois da primeira aplicação assinar na conta.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globalmente, assine um utilizador

O código seguinte remove a conta de entrada assinada e limpa fichas em cache não só da aplicação, mas também do dispositivo que se encontrar no modo de dispositivo partilhado. No entanto, não esclarece os *dados* da sua aplicação. Tem de limpar os dados da sua aplicação, bem como limpar quaisquer dados em cache que a sua aplicação possa estar a apresentar ao utilizador.

#### <a name="clear-browser-state"></a>Limpar o estado do navegador

> [!NOTE]
> O passo seguinte só é necessário durante a visualização pública.

Nesta versão de pré-visualização pública, o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) limpa o estado apenas para aplicações. Não é claro no navegador Safari. Recomendamos que limpe manualmente a sessão de navegador para garantir que não há vestígios de estado de utilizador deixados para trás. Você pode usar a propriedade opcional `signoutFromBrowser` mostrada abaixo para limpar quaisquer cookies. Isto fará com que o navegador seja lançado brevemente no dispositivo.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Passos seguintes

Para ver o modo de dispositivo partilhado em ação, a seguinte amostra de código no GitHub inclui um exemplo de execução de uma aplicação Firstline Worker num dispositivo iOS no modo dispositivo partilhado:

[MSAL iOS Swift Microsoft Graph API Sample](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)

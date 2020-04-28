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
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550256"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modo de dispositivo partilhado para dispositivos iOS

> [!NOTE]
> Esta funcionalidade está em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Os trabalhadores da Firstline, como os associados de retalho, os tripulantes de voo e os trabalhadores do serviço de campo, usam frequentemente um dispositivo móvel partilhado para realizar o seu trabalho. Estes dispositivos partilhados podem apresentar riscos de segurança se os seus utilizadores partilharem as suas palavras-passe ou PINs, intencionalmente ou não, para aceder em dados de clientes e negócios no dispositivo partilhado.

O modo de dispositivo partilhado permite configurar um iOS 13 ou um dispositivo superior para ser mais fácil e seguramente partilhado pelos colaboradores. Os colaboradores podem iniciar sessão e aceder rapidamente à informação do cliente. Quando terminarem o turno ou a tarefa, podem assinar fora do dispositivo e está imediatamente pronto para ser usado pelo próximo funcionário.

O modo de dispositivo partilhado também fornece uma gestão do dispositivo apoiado pela identidade da Microsoft.

Esta funcionalidade utiliza a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para gerir os utilizadores no dispositivo e distribuir o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Criar uma aplicação de modo de dispositivo partilhado

Para criar uma aplicação de modo de dispositivo partilhado, os desenvolvedores e os administradores de dispositivos de nuvem trabalham em conjunto:

1. **Os desenvolvedores** de aplicações escrevem uma aplicação de uma única conta (as aplicações de várias contas não são suportadas no modo de dispositivo partilhado) e escrevem código para lidar com coisas como o sign-out de dispositivos partilhados.

1. Os administradores de **dispositivos** preparam o dispositivo para ser partilhado utilizando um fornecedor de gestão de dispositivos móveis (MDM) como o Microsoft Intune para gerir os dispositivos na sua organização. O MDM empurra a aplicação DoAutenticador microsoft para os dispositivos e liga o "Modo Partilhado" para cada dispositivo através de uma atualização de perfil para o dispositivo. Esta definição de Modo Partilhado é o que altera o comportamento das aplicações suportadas no dispositivo. Esta configuração do fornecedor MDM define o modo de dispositivo partilhado para o dispositivo e permite o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) que é necessário para o modo de dispositivo partilhado.

1. [ Obrigatório apenas durante a**pré-visualização pública]** Um utilizador com a função de Administrador de [Dispositivos Cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) deve então lançar a [aplicação Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e juntar-se ao seu dispositivo para a organização.

    Para configurar a adesão das suas funções organizacionais no portal Azure: **Azure Ative Directory** > **Roles and Administrators** > Cloud Device**Administrator**

As seguintes secções ajudam-no a atualizar a sua aplicação para suportar o modo de dispositivo partilhado.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Utilizar Insinapara ativar o modo de dispositivo partilhado & extensão SSO

> [!NOTE]
> O passo seguinte só é necessário durante a pré-visualização pública.

O seu dispositivo tem de ser configurado para suportar o modo de dispositivo partilhado. Deve ter o iOS 13+ instalado e estar inscrito em MDM. A configuração do MDM também precisa de ativar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md). Para saber mais sobre extensões SSO, consulte o vídeo da [Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. No Portal de Configuração Intune, informe o dispositivo para ativar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) com a seguinte configuração:

    - **Tipo**: Redirecionar
    - **ID de extensão**: com.microsoft.azureauthenticator.ssoextension
    - **ID da equipa**: SGGM6D27TK
    - **URLs:**https://login.microsoftonline.com
    - Dados adicionais para configurar:
      - Chave: modo dispositivo partilhado
      - Tipo: Boolean
      - Valor: Verdadeiro

    Para obter mais informações sobre configuração com Intune, consulte a documentação de [configuração Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

1. Em seguida, configure o seu MDM para empurrar a aplicação Microsoft Authenticator para o seu dispositivo através de um perfil de MDM.

    Detete as seguintes opções de configuração para ligar o modo Dispositivo Partilhado:

    - Configuração 1:
      - Chave: modo dispositivo partilhado
      - Tipo: Boolean
      - Valor: Verdadeiro

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modifique a sua aplicação iOS para suportar o modo de dispositivo partilhado

Os seus utilizadores dependem de si para garantir que os seus dados não sejam vazados para outro utilizador. As seguintes secções fornecem sinais úteis para indicar à sua aplicação que ocorreu uma alteração e deve ser manuseada.

É responsável por verificar o estado do utilizador no dispositivo sempre que a sua aplicação é utilizada e, em seguida, limpar os dados do utilizador anterior. Isto inclui se for recarregado a partir do fundo em multi-tarefas.

Numa alteração de utilizador, deve certificar-se de que os dados do utilizador anterior são limpos e que os dados em cache apresentados na sua aplicação são removidos. Recomendamos vivamente que você e a sua empresa realizem um processo de revisão de segurança após atualizar a sua aplicação para suportar o modo de dispositivo partilhado.

### <a name="detect-shared-device-mode"></a>Detetar o modo de dispositivo partilhado

A deteção do modo de dispositivo partilhado é importante para a sua aplicação. Muitas aplicações exigirão uma alteração na sua experiência de utilizador (UX) quando a aplicação for utilizada num dispositivo partilhado. Por exemplo, a sua aplicação pode ter uma funcionalidade de "Sign-Up", o que não é apropriado para um Trabalhador de Linha Em Primeirolinha, porque provavelmente já têm uma conta. Também pode querer adicionar segurança extra ao tratamento de dados da sua aplicação se estiver no modo de dispositivo partilhado.

Utilize `getDeviceInformationWithParameters:completionBlock:` a API `MSALPublicClientApplication` no dispositivo para determinar se uma aplicação está a funcionar num dispositivo no modo de dispositivo partilhado.

Os seguintes fragmentos de código `getDeviceInformationWithParameters:completionBlock:` mostram exemplos de utilização da API.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obtenha o utilizador inscrito e determine se um utilizador mudou no dispositivo

Outra parte importante do modo de suporte do dispositivo partilhado é determinar o estado do utilizador no dispositivo e limpar os dados da aplicação se um utilizador tiver mudado ou se não houver nenhum utilizador no dispositivo. É responsável por garantir que os dados não são divulgados a outro utilizador.

Pode utilizar `getCurrentAccountWithParameters:completionBlock:` a API para consultar a conta atualmente assinada no dispositivo.

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

### <a name="globally-sign-in-a-user"></a>Assine globalmente um utilizador

Quando um dispositivo é configurado como um dispositivo `acquireTokenWithParameters:completionBlock:` partilhado, a sua aplicação pode ligar para a API para assinar na conta. A conta estará disponível globalmente para todas as aplicações elegíveis no dispositivo após os primeiros sinais de aplicação na conta.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globalmente assinar um utilizador

O código seguinte remove a conta de acesso assinado e limpa os tokens em cache não só da aplicação, mas também do dispositivo que está no modo de dispositivo partilhado. No entanto, não limpa os *dados* da sua aplicação. Tem de limpar os dados da sua aplicação, bem como limpar quaisquer dados em cache que a sua aplicação possa estar a apresentar ao utilizador.

#### <a name="clear-browser-state"></a>Estado de navegador claro

> [!NOTE]
> O passo seguinte só é necessário durante a pré-visualização pública.

Nesta versão de pré-visualização pública, o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) apura-se apenas para aplicações. Não esclarece o estado no navegador Safari. Recomendamos que limpe manualmente a sessão do navegador para garantir que não são deixados vestígios de estado de utilizador. Você pode usar `signoutFromBrowser` a propriedade opcional mostrada abaixo para limpar quaisquer cookies. Isto fará com que o navegador seja brevemente lançado no dispositivo.

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

Para ver o modo de dispositivo partilhado em ação, a seguinte amostra de código no GitHub inclui um exemplo de executar uma aplicação Firstline Worker num dispositivo iOS no modo de dispositivo partilhado:

[MSAL iOS Swift Microsoft Graph API Sample](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)

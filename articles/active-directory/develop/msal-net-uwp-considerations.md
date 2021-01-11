---
title: Considerações UWP (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para utilizar a Universal Windows Platform (UWP) com a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6451368baf9c047f0318eb74d53ffac075d4a184
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063455"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerações para a utilização da Plataforma Universal do Windows com MSAL.NET
Os desenvolvedores de aplicações que utilizam a Universal Windows Platform (UWP) com MSAL.NET devem considerar os conceitos que este artigo apresenta.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma Windows Runtime (WinRT), `PublicClientApplication` tem a propriedade `UseCorporateNetwork` Boolean. Esta propriedade permite que aplicações do Windows 8.1 e aplicações UWP beneficiem da autenticação Integrada do Windows (IWA) se o utilizador estiver inscrito numa conta que tenha um inquilino federado do Azure Ative Directory (Azure AD). Os utilizadores que se inscrevam no sistema operativo também podem utilizar um único sinal de sso(SSO). Ao definir a `UseCorporateNetwork` propriedade, MSAL.NET utiliza um corretor de autenticação web (WAB).

> [!IMPORTANT]
> Definir o `UseCorporateNetwork` imóvel para verdadeiro supõe que o desenvolvedor de aplicações permitiu a IWA na aplicação. Para ativar o IWA:
> - No separador 's' da sua aplicação `Package.appxmanifest` UWP, no separador **Capabilites,** ative as seguintes capacidades:
>   - **Autenticação da Empresa**
>   - **Redes Privadas (Servidor & cliente)**
>   - **Certificado de Utilizador Compartilhado**

A IWA não é ativada por padrão porque a Microsoft Store requer um alto nível de verificação antes de aceitar aplicações que solicitam as capacidades de autenticação da empresa ou certificados de utilizador partilhados. Nem todos os desenvolvedores querem fazer este nível de verificação.

Na plataforma UWP, a implementação wab subjacente não funciona corretamente em cenários empresariais onde o acesso condicional é ativado. Os utilizadores vêem os sintomas deste problema quando tentam iniciar sção usando o Windows Hello. Quando o utilizador é solicitado a escolher um certificado:

- O certificado para o PIN não é encontrado.
- Depois de o utilizador escolher um certificado, não são solicitados para o PIN.

Pode tentar evitar este problema utilizando um método alternativo, como a palavra-passe do utilizador e a autenticação do telefone, mas a experiência não é boa.

## <a name="troubleshooting"></a>Resolução de problemas

Alguns clientes relataram o seguinte erro de inscrição em ambientes específicos da empresa em que sabem que têm uma ligação à Internet e que a ligação funciona com uma rede pública.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Pode evitar este problema certificando-se de que o WAB (o componente Windows subjacente) permite uma rede privada. Pode fazê-lo definindo uma chave de registo:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para obter mais informações, consulte [o corretor de autenticação Web - Violinista](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Próximos passos
As seguintes amostras fornecem mais informações.

Sample | Plataforma | Descrição 
|------ | -------- | -----------|
|[active-directy-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Uma aplicação de cliente UWP que usa MSAL.NET. Acede ao Microsoft Graph para um utilizador que autentica utilizando um ponto final Azure AD 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directy-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma aplicação Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AD AD através da plataforma de identidade microsoft. Também mostra como aceder ao Microsoft Graph e mostra o token resultante. <br>![Diagrama que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e AZure AD através da plataforma de identidade da Microsoft.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

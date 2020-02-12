---
title: Considerações da UWP (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar a Universal Windows Platform (UWP) com a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132532"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Considerações para usar a Plataforma Universal Windows com MSAL.NET
Os desenvolvedores de aplicações que utilizam a Universal Windows Platform (UWP) com MSAL.NET devem considerar os conceitos que este artigo apresenta.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma Windows Runtime (WinRT), `PublicClientApplication` tem a propriedade Boolean `UseCorporateNetwork`. Esta propriedade permite que as aplicações do Windows 8.1 e as aplicações UWP beneficiem da autenticação Integrada do Windows (IWA) se o utilizador for contratado numa conta que tenha um inquilino do Azure Ative Directory (Azure AD) federado. Os utilizadores que estejam inscritos no sistema operativo também podem utilizar um único sinal (SSO). Ao definir a propriedade `UseCorporateNetwork`, MSAL.NET usa um corretor de autenticação web (WAB).

> [!IMPORTANT]
> A definição da propriedade `UseCorporateNetwork` como verdadeira pressupõe que o desenvolvedor de aplicações permitiu a IWA na aplicação. Para permitir o IWA:
> - Na `Package.appxmanifest`da sua aplicação UWP, no separador **Capabilities,** ative as seguintes capacidades:
>   - **Autenticação empresarial**
>   - **Redes Privadas (Cliente e Servidor)**
>   - **Certificado de utilizador compartilhado**

A IWA não está ativada por padrão porque a Microsoft Store requer um alto nível de verificação antes de aceitar aplicações que solicitem as capacidades de autenticação da empresa ou certificados de utilizador partilhados. Nem todos os desenvolvedores querem fazer este nível de verificação.

Na plataforma UWP, a implementação subjacente ao WAB não funciona corretamente em cenários empresariais onde o acesso condicional é ativado. Os utilizadores vêem os sintomas deste problema quando tentam iniciar sessão utilizando o Windows Hello. Quando o utilizador é convidado a escolher um certificado:

- O certificado para o PIN não foi encontrado.
- Depois de o utilizador escolher um certificado, não são solicitados para o PIN.

Pode tentar evitar este problema utilizando um método alternativo, como username-password e autenticação telefónica, mas a experiência não é boa.

## <a name="troubleshooting"></a>Resolução de problemas

Alguns clientes relataram o seguinte erro de inscrição em ambientes específicos da empresa em que sabem que têm uma ligação à Internet e que a ligação funciona com uma rede pública.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Pode evitar este problema certificando-se de que o WAB (o componente windows subjacente) permite uma rede privada. Pode fazê-lo definindo uma chave de registo:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para mais informações, consulte o corretor de [autenticação web - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Passos seguintes
As seguintes amostras fornecem mais informações.

Sample | Plataforma | Descrição 
|------ | -------- | -----------|
|[active-directy-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Uma aplicação de cliente UWP que usa MSAL.NET. Acede ao Microsoft Graph para um utilizador que autentica utilizando um ponto final Azure AD 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directy-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma simples aplicação Xamarin Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e Azure AD através do ponto final do Azure AD 2.0. Também mostra como aceder ao Microsoft Graph e mostra o símbolo resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

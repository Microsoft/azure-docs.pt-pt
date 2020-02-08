---
title: Considerações da UWP (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar a Plataforma Universal do Windows com a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 4803b2bda63ef0e14137aaafe95a422089e7f671
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083658"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerações específicas da Plataforma Universal Windows com MSAL.NET
No UWP, há várias considerações que deve ter em conta ao utilizar MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma WinRT, `PublicClientApplication` tem a seguinte propriedade booleana ``UseCorporateNetwork``. Esta propriedade permite que as aplicações Win8.1 e UWP beneficiem da Autenticação Integrada do Windows (e, portanto, SSO com o utilizador contratado com o sistema operativo) se o utilizador for contratado com uma conta num inquilino ad'azure federado. Ao definir esta propriedade, MSAL.NET alavanca waB (Web Authentication Broker).

> [!IMPORTANT]
> A definição desta propriedade de forma verdadeira pressupõe que o desenvolvedor da aplicação permitiu a Autenticação Integrada do Windows (IWA) na aplicação. Para isto:
> - Na ``Package.appxmanifest`` para a sua aplicação UWP, no separador **Capabilities,** ative as seguintes capacidades:
>   - Autenticação empresarial
>   - Redes Privadas (Cliente e Servidor)
>   - Certificado de utilizador compartilhado

A IWA não está ativada por defeito porque as aplicações que solicitam as capacidades de Autenticação da Empresa ou certificados de utilizador partilhadores requerem um nível mais elevado de verificação para serem aceites na Windows Store, e nem todos os desenvolvedores podem desejar realizar o maior nível de verificação.

A implementação subjacente na plataforma UWP (WAB) não funciona corretamente em cenários empresariais onde o Acesso Condicional foi ativado. O sintoma é que o utilizador tenta iniciar sessão com o Windows Hello, e é proposto escolher um certificado, mas:

- o certificado para o pino não é encontrado,
- ou o utilizador escolhe-o, mas nunca é solicitado para o Pin.

Uma sucinta é utilizar um método alternativo (username/password + autenticação do telefone), mas a experiência não é boa.

## <a name="troubleshooting"></a>Resolução de problemas

Alguns clientes relataram que em alguns ambientes específicos da empresa, houve o seguinte erro de inscrição:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

enquanto sabem que têm uma ligação à Internet, e que funciona com uma rede pública.

Uma supcé é garantir que o WAB (o componente windows subjacente) permite a rede privada. Pode fazê-lo definindo uma chave de registo:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Para mais detalhes, consulte o corretor de [autenticação web - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Passos seguintes
Mais detalhes são fornecidos nas seguintes amostras:

Sample | Plataforma | Descrição 
|------ | -------- | -----------|
|[active-directy-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Uma aplicação cliente universal da Plataforma Windows utilizando msal.net, acedendo ao Microsoft Graph para um utilizador autenticando com o ponto final da AD Azure. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma simples aplicação Xamarin Forms que mostra como usar o MSAL para autenticar a MSA e a AD Azure através do ponto final da AAD v2.0, e aceder ao Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

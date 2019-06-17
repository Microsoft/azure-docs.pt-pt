---
title: Considerações de plataformas de Windows universais (biblioteca de autenticação da Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao utilizar a plataforma Universal do Windows com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544128"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerações de específicas da plataforma Windows universais com MSAL.NET
No Xamarin iOS, existem várias considerações que deve levar em conta quando com MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>A propriedade UseCorporateNetwork
Na plataforma do WinRT, `PublicClientApplication` tem a seguinte propriedade booleana ``UseCorporateNetwork``. Esta propriedade permite aos aplicativos Win8.1 e UWP para se beneficiar da autenticação integrada do Windows (e, portanto, SSO com o utilizador com sessão iniciada com o sistema operacional) caso o utilizador com sessão iniciada com uma conta num Azure federado inquilino do AD. Esta capacidade tira partido WAB (Mediador de autenticação da Web). 

> [!IMPORTANT]
> Definir essa propriedade como true assume que o desenvolvedor do aplicativo ativou a autenticação integrada do Windows (IWA) no aplicativo. Para isso:
> - Na ``Package.appxmanifest`` para a sua aplicação de UWP no **capacidades** separador, ative as seguintes capacidades:
>   - Autenticação empresarial
>   - Redes privadas (cliente e servidor)
>   - Certificado de utilizador partilhados

IWA não está ativada por predefinição, uma vez que as aplicações que solicitam as capacidades de autenticação empresarial ou certificados de utilizador partilhado requerem um nível mais elevado de verificação para ser aceites para a Windows Store, e nem todos os desenvolvedores talvez queiram realizar a maior nível de verificação. 

A implementação subjacente na plataforma UWP (WAB) não funciona corretamente em cenários empresariais em que o acesso condicional foi ativado. O sintoma é que o usuário tenta iniciar sessão com o hello do Windows e é proposto para escolher um certificado, mas o certificado para o pin não for encontrado, ou o utilizador escolhe-lo, mas nunca recebe o pedido para o Pin. Uma solução alternativa é usar um método alternativo (nome de utilizador/palavra-passe + phone autenticação), mas a experiência não é bom. 

## <a name="next-steps"></a>Passos Seguintes
Obter mais detalhes são fornecidos nos exemplos a seguir:

Exemplo | Plataforma | Descrição 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Aplicação de cliente de plataforma Universal do Windows com msal.net, acessar o Microsoft Graph para um utilizador a autenticar com o ponto final de v2.0 do Azure AD. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Uma aplicação Xamarin Forms simples que mostra como utilizar a MSAL para autenticar MSA e o Azure AD através do ponto de final de v2.0 do AAD e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
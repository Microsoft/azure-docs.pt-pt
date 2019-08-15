---
title: Tópicos de ajuda do portal de registro de aplicativo | Microsoft Docs
description: Uma descrição de vários recursos no portal de registro de aplicativo da Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49675d8b18020c73a27a41fedff47697e29d829e
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988498"
---
# <a name="app-registration-reference"></a>Referência de registo de aplicação
Este documento fornece contexto e descrições de vários recursos encontrados na experiência de [registros de aplicativo](https://aka.ms/appregistrations) no portal do Azure.

## <a name="my-applications-or-converged-applications"></a>Meus aplicativos ou aplicativos convergidos
Esta lista contém todos os seus aplicativos registrados para uso com o ponto de extremidade da plataforma Microsoft Identity (v 2.0). Esses aplicativos têm a capacidade de conectar usuários com contas da Microsoft pessoais e contas corporativas/de estudante da Azure Active Directory. Para saber mais sobre o ponto de extremidade da plataforma de identidade, consulte a [visão geral v 2.0](active-directory-appmodel-v2-overview.md). Esses aplicativos também podem ser usados para integrar com o ponto de extremidade de `https://login.live.com`autenticação conta Microsoft,.

## <a name="azure-ad-only-applications"></a>Aplicações apenas do Azure AD
Esta lista contém todos os seus aplicativos registrados para uso com o ponto de extremidade v 1.0 do Azure AD. Esses aplicativos só têm a capacidade de conectar usuários com contas corporativas/de estudante da Azure Active Directory. Essa lista inclui aplicativos que foram registrados usando a experiência de **registros de aplicativo** no [portal do Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplicativos do Live SDK
Esta lista contém todos os seus aplicativos registrados para uso exclusivo com conta Microsoft. Eles não estão habilitados para uso com Azure Active Directory. É aí que você encontra todos os aplicativos que foram registrados anteriormente com o portal do desenvolvedor `https://account.live.com/developers/applications`MSA em. Todas as funções que você executou `https://account.live.com/developers/applications` anteriormente em agora podem ser executadas em [registros de aplicativo](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Segredos da Aplicação
Os segredos do aplicativo são credenciais que permitem que seu aplicativo execute a autenticação confiável do [cliente](https://tools.ietf.org/html/rfc6749#section-2.3) com o Azure AD. No OAuth & OpenID Connect, um segredo do aplicativo é comumente conhecido como um `client_secret`. No protocolo v 2.0, qualquer aplicativo que recebe um token de segurança em um local endereçável da Web (usando `https` um esquema) deve usar um segredo do aplicativo para se identificar ao Azure ad após o resgate desse token de segurança. Além disso, qualquer cliente nativo que receba tokens em um dispositivo será proibido de usar um segredo do aplicativo para executar a autenticação do cliente. Isso desencoraja o armazenamento de segredos em ambientes inseguros.

Cada aplicativo pode conter dois segredos de aplicativo válidos em um determinado momento. Ao manter dois segredos, você tem a capacidade de executar a substituição de chave periódica em todo o ambiente do seu aplicativo. Depois de migrar todo o seu aplicativo para um novo segredo, você poderá excluir o segredo antigo e provisionar um novo.

Neste momento, somente dois tipos de segredos de aplicativo são permitidos no portal de registro de aplicativo. Escolher **gerar nova senha** gera e armazena um segredo compartilhado no respectivo armazenamento de dados, que pode ser usado em seu aplicativo. Escolher **gerar novo par de chaves** cria um novo par de chaves pública/privada que pode ser baixado e usado para autenticação de cliente no Azure AD. Escolher **carregar chave pública** permite que você use seu próprio par de chaves pública/privada.
Você precisa carregar um certificado que contém uma chave pública.

## <a name="profile"></a>Perfil
A seção de perfil do portal de registro de aplicativo pode ser usada para personalizar a página de entrada para seu aplicativo. Neste momento, você pode alterar o logotipo do aplicativo da página de entrada, a URL dos termos de serviço e a URL da política de privacidade. O logótipo tem de ser um ficheiro de imagem GIF, PNG ou JPEG transparente com 48 x 48 ou 50 x 50 pixéis. Limite de tamanho: 15 KB. Tente alterar os valores e exibir a página de entrada resultante!

## <a name="live-sdk-support"></a>Suporte ao Live SDK
Quando você habilita o "suporte ao Live SDK", qualquer segredo de aplicativo que você criar será provisionado nos armazenamentos de dados de conta do Azure AD e da Microsoft. Isso permite que seu aplicativo integre-se diretamente com o serviço de conta da Microsoft (login.live.com). Se você quiser criar um aplicativo usando a conta da Microsoft diretamente (em vez de usar o ponto de extremidade v 2.0), certifique-se de que o suporte ao Live SDK esteja habilitado.

A desabilitação do suporte ao Live SDK garante que o segredo do aplicativo seja gravado apenas no armazenamento de dados do Azure AD. O armazenamento de dados do Azure AD incorpora regulamentos de nível empresarial que permitem que ele atenda a determinados padrões, como a conformidade com a FISMA. Se você habilitar o suporte ao Live SDK, seu aplicativo poderá não obter conformidade com alguns desses padrões.

Se você só planeja usar o ponto de extremidade v 2.0, você pode desabilitar com segurança o suporte ao Live SDK.


---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503174"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Iniciar sessão dos utilizadores e chamar a API do Microsoft Graph a partir de uma aplicação de página única (SPA) de JavaScript

Este guia demonstra como uma aplicação de página única (SPA) de JavaScript pode iniciar sessão no trabalho pessoal, e contas de instituição de ensino, obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que precisam de tokens de acesso do ponto de extremidade do Microsoft identity platform.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações

O aplicativo de exemplo criado por este guia permite que um SPA do JavaScript consultar o Graph API da Microsoft ou uma API Web que aceita tokens de ponto final de plataforma de identidade Microsoft. Para este cenário, depois de um utilizador inicia sessão, um token de acesso é solicitado e adicionado a pedidos de HTTP por meio do cabeçalho de autorização. Aquisição do token e a renovação são processadas pelo Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia utiliza a biblioteca do seguinte:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticação da Microsoft para pré-visualização do JavaScript|

> [!NOTE]
> *msal* destinos a *ponto final de plataforma do Microsoft identity* -que permite que as contas pessoais, escolares e profissionais iniciar sessão e adquirir tokens. O *ponto final de plataforma de identidade do Microsoft* tem [algumas limitações](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Para compreender as diferenças entre pontos finais v1.0 e v2.0, leia os [guia de comparação de ponto final](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

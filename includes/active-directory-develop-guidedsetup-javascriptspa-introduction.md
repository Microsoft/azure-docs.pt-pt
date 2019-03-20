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
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a6fcbc0e8adac75f17d7379ff512ba650d0bb118
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203348"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Chamar a API do Microsoft Graph a partir de uma aplicação de página única (SPA) de JavaScript

Este guia demonstra como uma aplicação de página única (SPA) de JavaScript pode iniciar sessão no trabalho pessoal, e as contas de instituição de ensino, obter um token de acesso e chamar a API do Microsoft Graph ou outras APIs que precisam de tokens de acesso do ponto final v2.0 do Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de exemplo gerada por este guia

![Mostra como a aplicação de exemplo gerado por este funciona de tutoriais](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro-updated.png)

<!--start-collapse-->
### <a name="more-information"></a>Mais Informações

O aplicativo de exemplo criado por este guia permite que um SPA do JavaScript consultar o Graph API da Microsoft ou uma API Web que aceita tokens de ponto final de v2.0 do Azure Active Directory. Para este cenário, depois de um utilizador inicia sessão, um token de acesso é solicitado e adicionado a pedidos de HTTP por meio do cabeçalho de autorização. Aquisição do token e a renovação são processadas pelo Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia utiliza a biblioteca do seguinte:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticação da Microsoft para pré-visualização do JavaScript|

> [!NOTE]
> *msal* destinos a *ponto final de v2.0 do Azure Active Directory* -que permite que as contas pessoais, escolares e profissionais iniciar sessão e adquirir tokens. O *ponto final de v2.0 do Azure Active Directory* tem [algumas limitações](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Para compreender as diferenças entre pontos finais v1.0 e v2.0, leia os [guia de comparação de ponto final](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

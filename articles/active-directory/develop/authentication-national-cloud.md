---
title: Autenticação com o Azure AD em clouds nacionais
description: Saiba mais sobre pontos finais de autenticação e de registo de aplicação para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/20/2018
ms.author: negoe
ms.reviewer: negoe,andret,saeeda,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4feaf97de7b833514113af6c91b3745be0503eff
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113689"
---
# <a name="national-clouds"></a>Clouds Nacionais

Clouds nacionais (também conhecido como clouds soberanas) são instâncias isoladas fisicamente do Azure. Estas regiões do Azure foram concebidos para se certificar de que os requisitos de residência, de soberania de dados e de conformidade de dados são honrados dentro dos limites geográficos.

Incluindo nuvem global, o Azure Active Directory é implementado nas clouds nacionais do seguintes:  

- Azure US Government
- Azure Alemanha
- Azure China 21Vianet

Clouds nacionais são ambiente exclusivo e diferente que global do Azure. Portanto, é importante estar ciente de algumas diferenças fundamentais ao desenvolver a sua aplicação para estes ambientes, tais como registar aplicações, a aquisição de tokens e a configuração de pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos finais de registo de aplicação

Há um portal do Azure separado para cada um das nuvens nacionais. Para integrar aplicações com a plataforma de identidade Microsoft numa cloud nacional, tem de registar a aplicação separadamente em cada do portal do Azure específico do ambiente.

A tabela seguinte lista os URLs de bases para os pontos de extremidade do Azure Active Directory (Azure AD) utilizados para registar uma aplicação para cada nuvem nacional.

| Em nuvem nacionais | Ponto de final de portal AD do Azure
| --- | --- |
| Azure AD para o Governo dos E.U.A. |`https://portal.azure.us`
|O Azure AD Alemanha |`https://portal.microsoftazure.de`
|O Azure AD China, explorado pela 21Vianet |`https://portal.azure.cn`
|O Azure AD (serviço global)|`https://portal.azure.com` 

## <a name="azure-ad-authentication-endpoints"></a>Pontos finais de autenticação do Azure AD

Todas as nuvens nacionais autenticar os utilizadores separadamente em cada ambiente e tem pontos finais de autenticação separado.

A tabela seguinte lista os URLs de bases para os pontos de extremidade do Azure Active Directory (Azure AD) utilizados para adquirir tokens para cada nuvem nacional.

| Em nuvem nacionais | Ponto final de autenticação do Azure AD
| --- | --- |
| Azure AD para o Governo dos E.U.A. |`https://login.microsoftonline.us`
|O Azure AD Alemanha| `https://login.microsoftonline.de`
|O Azure AD China, explorado pela 21Vianet | `https://login.chinacloudapi.cn`
|O Azure AD (serviço global)|`https://login.microsoftonline.com`

- Pedidos para os pontos finais de autorização ou token do Azure AD podem ser formados utilizando o URL de base específico da região adequado. Por exemplo, para o Azure Alemanha:

  - Ponto de extremidade comum de autorização é `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Ponto final do token comum é `https://login.microsoftonline.de/common/oauth2/token`.

- Para aplicações de inquilino único, substitua comuns nos URLs anteriores com o ID do inquilino ou o nome, por exemplo, `https://login.microsoftonline.de/contoso.com`.

>[!NOTE]
> O [autorização do Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e pontos finais de token apenas estão disponíveis para o serviço global. Ele ainda não é suportado para implementações em nuvem nacionais.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Para saber como chamar as APIs do Microsoft Graph no ambiente em nuvem nacionais aceda à [Microsoft Graph na cloud nacional](https://developer.microsoft.com/graph/docs/concepts/deployments).



> [!IMPORTANT]
> Determinados serviços e funcionalidades que estão em regiões específicas do serviço global poderão não estar disponíveis em todas as nuvens nacionais. Para saber que serviços estão disponíveis aceda a [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [do Azure Government](https://docs.microsoft.com/azure/azure-government/).
- Saiba mais sobre [Azure China 21Vianet](https://docs.microsoft.com/azure/china/).
- Saiba mais sobre [do Azure Alemanha](https://docs.microsoft.com/azure/germany/).
- Saiba mais sobre o [Noções básicas de autenticação do Azure AD](authentication-scenarios.md).

---
title: Autenticação com o Azure Active Directory em clouds nacionais
description: Saiba mais sobre pontos finais de autenticação e de registo de aplicação para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd37366697a9c1f5019d2864e6d81a4dcd02e3a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235481"
---
# <a name="national-clouds"></a>Clouds nacionais

Clouds nacionais são instâncias isoladas fisicamente do Azure. Estas regiões do Azure foram concebidos para se certificar de que os requisitos de residência, de soberania de dados e de conformidade de dados são honrados dentro dos limites geográficos.

Incluindo na nuvem global, o Azure Active Directory (Azure AD) é implementado nas clouds nacionais do seguintes:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

Clouds nacionais são exclusivos e um ambiente separado do Azure global. É importante estar ciente das principais diferenças ao desenvolver a sua aplicação para estes ambientes. Diferenças incluem registar aplicações, a aquisição de tokens e a configuração de pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos finais de registo de aplicação

Há um portal do Azure separado para cada um das nuvens nacionais. Para integrar aplicações com a plataforma de identidade Microsoft numa cloud nacional, tem de registar a aplicação separadamente em cada portal do Azure que é específico para o ambiente.

A tabela seguinte lista os URLs de bases para os pontos de extremidade do Azure AD utilizados para registar uma aplicação para cada nuvem nacional.

| Em nuvem nacionais | Ponto de final de portal AD do Azure |
|----------------|--------------------------|
| Azure AD para o Governo dos E.U.A. | `https://portal.azure.us` |
| O Azure AD Alemanha | `https://portal.microsoftazure.de` |
| O Azure AD China, explorado pela 21Vianet | `https://portal.azure.cn` |
| O Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos finais de autenticação do Azure AD

Todas as nuvens nacionais autenticar os utilizadores separadamente em cada ambiente e tem pontos finais de autenticação separado.

A tabela seguinte lista os URLs de bases para os pontos de extremidade do Azure AD utilizados para adquirir tokens para cada nuvem nacional.

| Em nuvem nacionais | Ponto final de autenticação do Azure AD |
|----------------|-------------------------|
| Azure AD para o Governo dos E.U.A. | `https://login.microsoftonline.us` |
| O Azure AD Alemanha| `https://login.microsoftonline.de` |
| O Azure AD China, explorado pela 21Vianet | `https://login.chinacloudapi.cn` |
| O Azure AD (serviço global)| `https://login.microsoftonline.com` |

É possível formar pedidos para a autorização do Azure AD ou pontos finais do token utilizando o URL de base específico da região adequado. Por exemplo, para o Azure Alemanha:

  - Ponto de extremidade comum de autorização é `https://login.microsoftonline.de/common/oauth2/authorize`.
  - Ponto final do token comum é `https://login.microsoftonline.de/common/oauth2/token`.

Para aplicações de inquilino único, substitua "common" nos URLs anteriores com o ID do inquilino ou nome. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> O [autorização do Azure AD v2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) e pontos finais de token estão disponíveis apenas para o serviço global. Eles não estiverem suportados para implementações em nuvem nacionais.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Para saber como chamar as APIs do Microsoft Graph num ambiente em nuvem nacionais, aceda à [Microsoft Graph em implementações em nuvem nacionais](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Determinados serviços e funcionalidades que estão em regiões específicas do serviço global poderão não estar disponíveis em todas as nuvens nacionais. Para saber que serviços estão disponíveis, aceda a [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Para saber como criar uma aplicação com a plataforma de identidade da Microsoft, siga os [tutorial Microsoft Authentication Library (MSAL)](msal-national-cloud.md). Especificamente, esta aplicação irá iniciar sessão de um utilizador e obter um token de acesso para chamar o Microsoft Graph API.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [O Azure na China 21Vianet](https://docs.microsoft.com/azure/china/)
- [O Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- [Noções básicas de autenticação do Azure AD](authentication-scenarios.md)

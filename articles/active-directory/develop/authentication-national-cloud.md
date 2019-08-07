---
title: Autenticação usando Azure Active Directory em nuvens nacionais
description: Saiba mais sobre os pontos de extremidade de autenticação e registro de aplicativo para nuvens nacionais.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: negoe,CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abaf8c910710000930f4e0ab6200d0ebd75b8d46
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835205"
---
# <a name="national-clouds"></a>Nuvens nacionais

As nuvens nacionais são instâncias fisicamente isoladas do Azure. Essas regiões do Azure são projetadas para garantir que os requisitos de residência de dados, soberania e conformidade sejam respeitados dentro dos limites geográficos.

Incluindo a nuvem global, o Azure Active Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

As nuvens nacionais são exclusivas e um ambiente separado do Azure global. É importante estar ciente das principais diferenças ao desenvolver seu aplicativo para esses ambientes. As diferenças incluem o registro de aplicativos, a aquisição de tokens e a configuração de pontos de extremidade.

## <a name="app-registration-endpoints"></a>Pontos de extremidade de registro do aplicativo

Há um portal do Azure separado para cada uma das nuvens nacionais. Para integrar aplicativos com a plataforma de identidade da Microsoft em uma nuvem nacional, é necessário registrar seu aplicativo separadamente em cada portal do Azure que seja específico para o ambiente.

A tabela a seguir lista as URLs de base para os pontos de extremidade do Azure AD usados para registrar um aplicativo para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade do portal do Azure AD |
|----------------|--------------------------|
| Azure AD para o governo dos EUA | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operado por 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos de extremidade de autenticação do Azure AD

Todas as nuvens nacionais autenticam usuários separadamente em cada ambiente e têm pontos de extremidade de autenticação separados.

A tabela a seguir lista as URLs de base para os pontos de extremidade do Azure AD usados para adquirir tokens para cada nuvem nacional.

| Nuvem nacional | Ponto de extremidade de autenticação do Azure AD |
|----------------|-------------------------|
| Azure AD para o governo dos EUA | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operado por 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

Você pode formar solicitações para a autorização do Azure AD ou pontos de extremidade de token usando a URL base específica da região apropriada. Por exemplo, para o Azure Alemanha:

  - O ponto de extremidade `https://login.microsoftonline.de/common/oauth2/authorize`comum de autorização é.
  - O ponto de extremidade `https://login.microsoftonline.de/common/oauth2/token`comum do token é.

Para aplicativos de locatário único, substitua "Common" nas URLs anteriores pela sua ID de locatário ou nome. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

> [!NOTE]
> Os pontos de extremidade de autorização e token do [Azure ad v 2.0]( https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview) estão disponíveis somente para o serviço global. Eles não têm suporte para implantações de nuvem nacionais.

## <a name="microsoft-graph-api"></a>API de Microsoft Graph

Para saber como chamar as APIs de Microsoft Graph em um ambiente de nuvem nacional, acesse [Microsoft Graph em implantações de nuvem nacionais](https://developer.microsoft.com/graph/docs/concepts/deployments).

> [!IMPORTANT]
> Determinados serviços e recursos que estão em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens nacionais. Para descobrir quais serviços estão disponíveis, acesse [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Para saber como criar um aplicativo usando a plataforma de identidade da Microsoft, siga o [tutorial da MSAL (biblioteca de autenticação da Microsoft)](msal-national-cloud.md). Especificamente, esse aplicativo conectará um usuário e obterá um token de acesso para chamar a API de Microsoft Graph.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet do Azure na China](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- [Noções básicas de autenticação do Azure AD](authentication-scenarios.md)

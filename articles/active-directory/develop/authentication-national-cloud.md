---
title: Autenticação da AD Azure & nuvens nacionais Azure
titleSuffix: Microsoft identity platform
description: Conheça o registo de aplicativos e os pontos finais de autenticação para as nuvens nacionais.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 9a8d9c5c0a24e59c8f6345666f8838aa4c41d0f9
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845190"
---
# <a name="national-clouds"></a>Clouds nacionais

Nuvens nacionais são casos fisicamente isolados de Azure. Estas regiões de Azure destinam-se a garantir que os requisitos de residência, soberania e conformidade dos dados sejam honrados dentro das fronteiras geográficas.

Incluindo a nuvem global, o Azure Ative Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

As nuvens nacionais são únicas e um ambiente separado do Azure global. É importante estar atento às principais diferenças ao desenvolver a sua aplicação para estes ambientes. As diferenças incluem o registo de aplicações, a aquisição de fichas e a configuração de pontos finais.

## <a name="app-registration-endpoints"></a>Pontos finais de inscrição de aplicativos

Há um portal Azure separado para cada uma das nuvens nacionais. Para integrar aplicações com a plataforma de identidade da Microsoft numa nuvem nacional, é necessário registar a sua aplicação separadamente em cada portal Azure específico do ambiente.

A tabela seguinte lista os URLs base para os pontos finais da AD Azure utilizados para registar uma aplicação para cada nuvem nacional.

| Nuvem nacional | Ponto final do portal Azure AD |
|----------------|--------------------------|
| Azure AD para governo dos EUA | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operado pela 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos finais de autenticação da AD Azure

Todas as nuvens nacionais autenticam os utilizadores separadamente em cada ambiente e têm pontos finais de autenticação separados.

A tabela seguinte lista os URLs base para os pontos finais da AD Azure usados para adquirir fichas para cada nuvem nacional.

| Nuvem nacional | Ponto final de autenticação da AD Azure |
|----------------|-------------------------|
| Azure AD para governo dos EUA | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operado pela 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

Pode fornecer pedidos à autorização da AD Azure ou pontos finais simbólicos utilizando o URL de base específico da região apropriado. Por exemplo, para a Alemanha Azure:

  - Autorização ponto final comum é `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
  - Símbolo ponto final comum é `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

Para pedidos de inquilino único, substitua "comum" nos URLs anteriores pelo seu ID ou nome de inquilino. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Para aprender a chamar as APIs do Microsoft Graph num ambiente nacional de nuvem, vá ao [Microsoft Graph em implementações nacionais](https://developer.microsoft.com/graph/docs/concepts/deployments)de nuvem .

> [!IMPORTANT]
> Certos serviços e características que se encontram em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens nacionais. Para saber quais os serviços disponíveis, vá a [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)

Para aprender a construir uma aplicação utilizando a plataforma de identidade da Microsoft, siga o tutorial da [Microsoft Authentication Library (MSAL).](msal-national-cloud.md) Especificamente, esta aplicação irá assinar num utilizador e obter um sinal de acesso para ligar para a Microsoft Graph API.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
- [Fundamentos básicos de autenticação da AD Azure](authentication-scenarios.md)

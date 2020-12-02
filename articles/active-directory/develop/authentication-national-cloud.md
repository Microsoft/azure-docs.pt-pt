---
title: A autenticação da AD & nuvens nacionais Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o registo de aplicações e pontos finais de autenticação para nuvens nacionais.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: da1ea6462d22242d23629bcec192d7760314dfed
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444804"
---
# <a name="national-clouds"></a>Clouds nacionais

Nuvens nacionais são casos fisicamente isolados de Azure. Estas regiões de Azure destinam-se a garantir que os requisitos de residência, soberania e conformidade sejam respeitados dentro dos limites geográficos.

Incluindo a nuvem global, o Azure Ative Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure Alemanha
- Azure China 21Vianet

As nuvens nacionais são únicas e um ambiente separado do Azure global. É importante estar atento às principais diferenças ao desenvolver a sua aplicação para estes ambientes. As diferenças incluem o registo de aplicações, a aquisição de fichas e a configuração de pontos finais.

## <a name="app-registration-endpoints"></a>Pontos finais de registo de aplicativos

Há um portal Azure separado para cada uma das nuvens nacionais. Para integrar aplicações com a plataforma de identidade da Microsoft numa nuvem nacional, é-lhe exigido que registe a sua aplicação separadamente em cada portal Azure que seja específico para o ambiente.

A tabela que se segue lista os URLs de base para os pontos finais Azure AD utilizados para registar uma aplicação para cada nuvem nacional.

| Nuvem nacional | Ponto final do portal AD AZure |
|----------------|--------------------------|
| Azure AD para governo dos EUA | `https://portal.azure.us` |
| Azure AD Alemanha | `https://portal.microsoftazure.de` |
| Azure AD China operada pela 21Vianet | `https://portal.azure.cn` |
| Azure AD (serviço global) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Pontos finais de autenticação AD Azure

Todas as nuvens nacionais autenticam os utilizadores separadamente em cada ambiente e têm pontos finais de autenticação separados.

A tabela que se segue lista os URLs de base para os pontos finais Azure AD utilizados para adquirir fichas para cada nuvem nacional.

| Nuvem nacional | Ponto final de autenticação AD Azure |
|----------------|-------------------------|
| Azure AD para governo dos EUA | `https://login.microsoftonline.us` |
| Azure AD Alemanha| `https://login.microsoftonline.de` |
| Azure AD China operada pela 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (serviço global)| `https://login.microsoftonline.com` |

Pode formar pedidos para a autorização AD do Azure ou pontos finais simbólicos utilizando o URL de base específico da região apropriado. Por exemplo, para a Azure Alemanha:

- O ponto final comum de autorização é `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
- O ponto final comum simbólico é `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

Para aplicações de inquilino único, substitua o "comum" nos URLs anteriores com o seu ID ou nome do seu inquilino. Um exemplo é `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Para aprender a chamar apis do Microsoft Graph num ambiente nacional de nuvem, vá ao [Microsoft Graph em implementações nacionais em nuvem](/graph/deployments).

> [!IMPORTANT]
> Certos serviços e características que se encontram em regiões específicas do serviço global podem não estar disponíveis em todas as nuvens nacionais. Para saber que serviços estão disponíveis, vá aos [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)

Para aprender a construir uma aplicação utilizando a plataforma de identidade microsoft, siga o tutorial da [Microsoft Authentication Library (MSAL).](msal-national-cloud.md) Especificamente, esta aplicação irá iniciar seduca num utilizador e obter um token de acesso para ligar para a API do Gráfico microsoft.

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar a Biblioteca de Autenticação da [Microsoft (MSAL) num ambiente nacional em nuvem](msal-national-cloud.md).

Documentação nacional em nuvem:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Alemanha](../../germany/index.yml)

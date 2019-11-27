---
title: O que há de novo no Azure AD no Microsoft 365 governamental? | Microsoft Docs
description: Saiba mais sobre algumas alterações no Azure Active Directory (Azure AD) na instância de nuvem do governo Microsoft 365, que pode afetar você.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e7c6809810a78c81348bd4bbeb808e103f453f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805289"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>O que há de novo para Azure Active Directory no governo Microsoft 365

Fizemos algumas alterações no Azure Active Directory (Azure AD) na instância de nuvem do governo Microsoft 365, que é aplicável aos clientes que usam os seguintes serviços:

- Microsoft Azure Governo

- Governo Microsoft 365 – GCC alto

- Governo Microsoft 365 – DoD

Este artigo não se aplica a clientes Microsoft 365 do governo-GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações no nome de domínio inicial

Durante a inscrição inicial da sua organização para um serviço online governamental Microsoft 365, você foi solicitado a escolher o nome de domínio da sua organização, `<your-domain-name>.onmicrosoft.com`. Se você já tiver um nome de domínio com o sufixo. com, nada será alterado.

No entanto, se você estiver se inscrevendo para um novo serviço governamental Microsoft 365, será solicitado que você escolha um nome de domínio usando o sufixo `.us`. Portanto, será `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Essa alteração não se aplica a clientes que são gerenciados por CSPs (provedores de serviços de nuvem).

## <a name="changes-to-portal-access"></a>Alterações no acesso ao portal

Atualizamos os pontos de extremidade do portal para Microsoft Azure Governamental, Microsoft 365 governamental – GCC High e Microsoft 365 governamental – DoD, conforme mostrado na tabela de [mapeamento de ponto de extremidade](#endpoint-mapping).

Anteriormente, os clientes podiam entrar usando os portais mundiais do Azure (portal.azure.com) e do Office 365 (portal.office.com). Com essa atualização, os clientes agora devem entrar usando o Microsoft Azure Governamental específico, os portais de Microsoft 365 de governo-GCC alta e Microsoft 365 governamental-DoD.

## <a name="endpoint-mapping"></a>Mapeamento de ponto de extremidade

A tabela a seguir mostra os pontos de extremidade para todos os clientes:

| Nome | Detalhes do ponto de extremidade |
|------|------------------|
| Portals |Microsoft Azure Governamental: https://portal.azure.us<p>Governo Microsoft 365 – GCC alto: https://portal.office365.us<p>Governo Microsoft 365 – DoD: https://portal.apps.mil |
| Ponto de extremidade de autoridade de Azure Active Directory | https://login.microsoftonline.us |
| Graph API do Azure Active Directory | https://graph.windows.net |
| API de Microsoft Graph para Microsoft 365 governamental-GCC alto | https://graph.microsoft.us |
| API de Microsoft Graph para Microsoft 365 governamental-DoD | https://dod-graph.microsoft.us |
| Pontos de extremidade de serviços do Azure governamental | Para obter detalhes, consulte [Guia do desenvolvedor do Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 os pontos de extremidade mais poestados do governo de GCC | Para obter detalhes, consulte [pontos de extremidade altos do Office 365 no governo dos EUA](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Governo Microsoft 365-DoD | Para obter detalhes, consulte [pontos de extremidade do DoD do governo dos EUA do Office 365](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja estes artigos:

- [O que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Atualização do ponto de extremidade da autoridade do AAD do Azure governamental](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph pontos de extremidade na nuvem do governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 do governo dos EUA GCC alto e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
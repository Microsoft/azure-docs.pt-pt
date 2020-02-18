---
title: Quais as novidades para o Azure AD no Governo Microsoft 365? | Microsoft Docs
description: Saiba algumas alterações ao Azure Ative Directory (Azure AD) na instância de nuvem do Governo Microsoft 365, que poderá impactar-lhe.
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
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425549"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novidades para o Azure Ative Directory no Governo da Microsoft 365

Fizemos algumas alterações ao Azure Ative Directory (Azure AD) na instância de nuvem do Governo Microsoft 365, que é aplicável aos clientes que utilizam os seguintes serviços:

- Governo Microsoft Azure

- Microsoft 365 Governo – GCC High

- Microsoft 365 Governo - DoD

Este artigo não se aplica aos clientes do Microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações ao nome inicial do domínio

Durante a inscrição inicial da sua organização para um serviço online do Governo Microsoft 365, foi-lhe pedido que escolhesse o nome de domínio da sua organização, `<your-domain-name>.onmicrosoft.com`. Se já tiver um nome de domínio com o sufixo .com, nada mudará.

No entanto, se estiver a inscrever-se num novo serviço do Governo Microsoft 365, será-lhe pedido que escolha um nome de domínio usando o sufixo `.us`. Então, será `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Esta alteração não se aplica a nenhum cliente que seja gerido por fornecedores de serviços na nuvem (CSPs).

## <a name="changes-to-portal-access"></a>Alterações no acesso ao portal

Atualizámos os pontos finais do portal para o Governo microsoft Azure, o Governo microsoft 365 – GCC High e o Microsoft 365 Government – DoD, como mostra a tabela de [mapeamento Endpoint](#endpoint-mapping).

Anteriormente, os clientes podiam inscrever-se nos portais azure (portal.azure.com) e Office 365 (portal.office.com) em todo o mundo. Com esta atualização, os clientes devem agora assinar usando os portais específicos do Microsoft Azure Government, Microsoft 365 Government - GCC High e Microsoft 365 Government - DoD.

## <a name="endpoint-mapping"></a>Mapeamento endpoint

A tabela seguinte mostra os pontos finais para todos os clientes:

| Nome | Detalhes do ponto final |
|------|------------------|
| Portais |Governo do Microsoft Azure: https://portal.azure.us<p>Microsoft 365 Governo – GCC High: https://portal.office365.us<p>Microsoft 365 Governo – DoD: https://portal.apps.mil |
| Ponto final da Autoridade de Diretório Ativo Azure | https://login.microsoftonline.us |
| Microsoft Graph API para o Governo Microsoft 365 - GCC High | https://graph.microsoft.us |
| Microsoft Graph API para o Governo Microsoft 365 - DoD | https://dod-graph.microsoft.us |
| Pontos finais dos serviços do Governo azure | Para mais detalhes, consulte o guia de [desenvolvimento do Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| Microsoft 365 Governo - Pontos finais do GCC | Para mais detalhes, consulte o [Office 365 U.S. Government GCC High endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Governo - DoD | Para mais detalhes, consulte o [Office 365 U.S. Government DoD endpoints](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja estes artigos:

- [O que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Atualização do ponto final da Autoridade aad do Governo de Azure](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph termina pontos finais na nuvem do Governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Escritório 365 Governo dos EUA GCC Alto e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)

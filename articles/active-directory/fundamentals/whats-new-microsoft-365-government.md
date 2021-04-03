---
title: Quais as novidades para a Azure AD no Governo microsoft 365? | Microsoft Docs
description: Saiba mais sobre algumas alterações ao Azure Ative Directory (Azure AD) na instância cloud do Governo microsoft 365, o que pode impactá-lo.
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
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89318935"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Novidades para o Azure Ative Directory no Governo da Microsoft 365

Fizemos algumas alterações ao Azure Ative Directory (Azure AD) na instância cloud do Governo da Microsoft 365, que é aplicável aos clientes que usam os seguintes serviços:

- Governo microsoft Azure

- Microsoft 365 Government – GCC High

- Microsoft 365 Governo – DoD

Este artigo não se aplica aos clientes microsoft 365 Government – GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações ao nome de domínio inicial

Durante a inscrição inicial da sua organização para um serviço online do Governo Microsoft 365, foi-lhe pedido que escolhesse o nome de domínio da sua `<your-domain-name>.onmicrosoft.com` organização, . Se já tem um nome de domínio com o sufixo .com, nada mudará.

No entanto, se estiver a inscrever-se para um novo serviço do Governo Microsoft 365, será solicitado que escolha um nome de domínio usando o `.us` sufixo. Então, vai `<your-domain-name>.onmicrosoft.us` ser.

>[!Note]
>Esta alteração não se aplica a clientes que sejam geridos por prestadores de serviços na nuvem (CSPs).

## <a name="changes-to-portal-access"></a>Alterações no acesso ao portal

Atualizámos os pontos finais do portal para o Governo do Microsoft Azure, o Governo microsoft 365 – GCC High e o Governo microsoft 365 – DoD, como mostra a [tabela de mapeamento endpoint](#endpoint-mapping).

Anteriormente, os clientes podiam inscrever-se usando os portais Azure (portal.azure.com) e Office 365 (portal.office.com). Com esta atualização, os clientes devem agora iniciar súbb pouco através dos portais específicos do Microsoft Azure Government, Microsoft 365 Government - GCC High e Microsoft 365 Government - DoD.

## <a name="endpoint-mapping"></a>Mapeamento de ponto final

A tabela a seguir mostra os pontos finais para todos os clientes:

| Name | Detalhes do ponto final |
|------|------------------|
| Portais |Governo microsoft Azure: https://portal.azure.us<p>Microsoft 365 Government – GCC High: https://portal.office365.us<p>Microsoft 365 Governo – DoD: https://portal.apps.mil |
| Ponta final da Autoridade de Direção Ativa da Azure | https://login.microsoftonline.us |
| Microsoft Graph API para o Governo microsoft 365 - GCC High | https://graph.microsoft.us |
| Microsoft Graph API para o Governo microsoft 365 - DoD | https://dod-graph.microsoft.us |
| Pontos finais dos serviços do Governo de Azure | Para mais detalhes, consulte [o guia de desenvolvimento do Governo Azure](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 Government - GCC High endpoints | Para mais detalhes, consulte [o Office 365 U.S. Government GCC High endpoints](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Governo - DoD | Para mais detalhes, consulte o [Office 365 U.S. Government DoD pontos finais](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja estes artigos:

- [O que é o Azure Government?](../../azure-government/documentation-government-welcome.md)

- [A atualização de endpoint da Autoridade AAD do Governo de Azure](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Pontos finais do Microsoft Graph na nuvem do governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 US Government GCC High and DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
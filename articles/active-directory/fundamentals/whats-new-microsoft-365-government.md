---
title: O que há de novo para o Azure Active Directory no Microsoft 365 Government - Azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre algumas alterações ao Azure Active Directory (Azure AD) na instância de cloud do Microsoft 365 Government, que poderá afetar a.
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
ms.openlocfilehash: 5b077c7b5efbad2add971d42ff31938b56f6bc33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258898"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>O que há de novo para o Azure Active Directory no Microsoft 365 Government

Fizemos algumas alterações ao Azure Active Directory (Azure AD) na instância de cloud do Microsoft 365 Government, que se aplica aos clientes que utilizam os seguintes serviços:

- Microsoft Azure Governo

- O Microsoft 365 Government – GCC elevada

- O Microsoft 365 Government – DoD

Este artigo não se aplica a Microsoft 365 Government – clientes GCC.

## <a name="changes-to-the-initial-domain-name"></a>Alterações para o nome de domínio inicial

Durante à inscrição inicial sua organização para um serviço online do Microsoft 365 Government, foi pedido para escolher o nome de domínio da sua organização, `<your-domain-name>.onmicrosoft.com`. Se já tiver um nome de domínio com o sufixo de .com, nada mudará.

No entanto, se estiver a iniciar cópia de segurança para um novo serviço do Microsoft 365 Government, será solicitado a escolher um nome de domínio utilizando o `.us` sufixo. Por isso, é `<your-domain-name>.onmicrosoft.us`.

>[!Note]
>Esta alteração não se aplica a todos os clientes que são geridos pelo fornecedor de serviços cloud (CSPs).

## <a name="changes-to-portal-access"></a>Alterações para acesso ao portal

Atualizámos de pontos finais do portais para o Microsoft Azure Government, o Microsoft 365 Government – GCC alta e o Microsoft 365 Government – DoD, como mostra a [tabela de mapeamento de ponto final](#endpoint-mapping).

Anteriormente os clientes podem entrar usando os portais de Office 365 (portal.office.com) e em todo o mundo do Azure (portal.azure.com). Com esta atualização, os clientes agora tem de iniciar sessão com o Microsoft Azure Government, Microsoft 365 Government - GCC alta e Microsoft 365 Government - portais do DoD específico.

## <a name="endpoint-mapping"></a>Mapeamento de ponto final

A tabela seguinte mostra os pontos de extremidade para todos os clientes:

| Name | Detalhes do ponto final |
|------|------------------|
| Portais |Microsoft Azure Government: https://portal.azure.us<p>O Microsoft 365 Government – GCC elevada: https://portal.office365.us<p>O Microsoft 365 Government – DoD: https://portal.apps.mil |
| Ponto final da autoridade do Azure Active Directory | https://login.microsoftonline.us |
| Graph API do Azure Active Directory | https://graph.windows.net |
| Microsoft Graph API para o Microsoft 365 Government - GCC elevada | https://graph.microsoft.us |
| Microsoft Graph API para o Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Pontos de extremidade dos serviços do Azure Government | Para obter detalhes, consulte [Guia do programador do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) |
| O Microsoft 365 Government - pontos finais de GCC elevada | Para obter detalhes, consulte [dos Estados Unidos do Office 365 Pontos finais de GCC elevada de Governo](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government - DoD | Para obter detalhes, consulte [dos Estados Unidos do Office 365 Pontos de extremidade do Governo DoD](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja estes artigos:

- [O que é o Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Atualização de ponto final de autoridade AAD o Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Pontos de extremidade do Microsoft Graph na cloud do Governo dos EUA](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 administração pública dos EUA GCC alta e DoD](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
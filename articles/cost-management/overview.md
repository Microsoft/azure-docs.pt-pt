---
title: Descrição Geral da Cloudyn no Azure | Microsoft Docs
description: A Cloudyn é uma solução de gestão de custos e várias clouds que o ajuda a utilizar o Azure e outros recursos da cloud.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 70951bfdafb9e54a63c2645d4993233fc4b8446c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230005"
---
# <a name="what-is-the-cloudyn-service"></a>What is the Cloudyn service?

A Cloudyn, uma subsidiária da Microsoft, permite-lhe controlar a utilização da cloud e os gastos dos seus recursos do Azure e de outros fornecedores de cloud, incluindo AWS e Google. Os relatórios do dashboard fáceis de compreender também ajudam na alocação de custos e showbacks/estornos. A Cloudyn ajuda a otimizar os gastos da cloud ao identificar recursos subutilizados que pode gerir e ajustar.

Para ver um vídeo introdutório, veja [Introduction to Azure Cloudyn (Introdução à Azure Cloudyn)](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo).

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, consulte [Azure Cost Management](overview-cost-mgt.md).

Watch the [Azure Cost Management and Cloudyn video](https://www.youtube.com/watch?v=PmwFWwSluh8) to see recommendations when you should use either Azure Cost Management or Cloudyn, based on your business needs.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn features moving to Azure Cost Management

Microsoft acquired Cloudyn and is migrating its cost management features from the Cloudyn portal natively into Azure. To use the new features, sign-in to the Azure portal and navigate to [Cost Management and Billing](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) in the list of Azure services. Compared to Cloudyn, the native experience offers improved performance and lower data latency of about eight hours.

Key feature migration for Enterprise Agreement, Pay-As-You-Go, and MSDN offer categories to Azure Cost Management is complete. CSP subscriptions are in the process of being migrated over to Azure Cost Management.

If you have an offer category not yet migrated, you should continue to use the Cloudyn portal. Everyone else can use Azure Cost Management.

| Microsoft Azure offers and features | Recommended cost management service |
| --- | --- |
| Azure Enterprise Agreement | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Cross-cloud cost analysis support for AWS (in preview) | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS recommendations | [Cloudyn](https://azure.cloudyn.com) |

Some of the following features are available in Cloudyn, but all of them are available now in Azure Cost Management.

- APIs
- Azure compute recommendations
- Azure Reservation recommendations
- Orçamentos
- Análise de custos
- Export data to an Azure storage account
- Latências mais baixas
- Power BI content pack and connector
- Resource tag support

## <a name="monitor-usage-and-spending"></a>Monitorizar a utilização e os gastos

Monitorizar a utilização e os gastos é extremamente importante para infraestruturas da cloud porque as organizações pagam pelos recursos que consomem ao longo do tempo. Quando a utilização excede os limiares do contrato, podem ocorrer excessos de custos inesperados. A few important factors can make ad hoc monitoring difficult. Em primeiro lugar, a projeção de custos baseada na utilização média assume que o consumo permanece consistente num determinado período de faturação. Em segundo lugar, quando os custos estão próximos ou excedem o seu orçamento, é importante receber notificações de forma pró-ativa para ajustar os gastos. Além disso, os fornecedores de serviços cloud podem não oferecer projeção de custo de acordo com os limiares ou relatórios de comparação de períodos.

Os relatórios ajudam a monitorizar os gastos para analisar e controlar a utilização da cloud, os custos e as tendências. Ao utilizar relatórios ao Longo do Tempo, pode detetar anomalias diferentes das tendências normais. São visíveis ineficiências na sua implementação da cloud nos relatórios de otimização. Também pode reparar em ineficiências nos relatórios de análise de custos.

## <a name="manage-costs"></a>Gerir os custos

Os dados históricos podem ajudá-lo a gerir os custos, ao analisar os custos de utilização e ao longo do tempo para identificar tendências. As tendências são então utilizadas para prever despesas futuras. A Cloudyn também inclui relatórios de custos estimados úteis.

A alocação de custos gere os custos ao analisá-los com base na sua política de etiquetagem. Pode utilizar etiquetas nas suas contas personalizadas, recursos e entidades para refinar a alocação de custos. O Gestor de Categoria organiza as suas etiquetas para ajudar a oferecer governação adicional. E utiliza a alocação de custos para análise de custos/estorno para mostrar a utilização de recursos e os custos associados para influenciar os comportamentos de consumo ou cobrar clientes de inquilino.

O controlo de acesso ajuda a gerir os custos, ao garantir que os utilizadores e as equipas acedam apenas aos dados de gestão de custos que precisam. Utilize a estrutura de entidade, a gestão de utilizadores e os relatórios agendados com listas de destinatários para atribuir acesso.

O alerta ajuda a gerir os custos ao notificá-lo automaticamente quando ocorrerem despesas invulgares ou gastar demasiado. Os alertas também podem notificar outros intervenientes automaticamente sobre anomalias de gastos e riscos de grandes gastos. Vários relatórios suportam alertas com base nos limiares de custo e orçamento. No entanto, os alertas não são atualmente suportados para contas ou subscrições de parceiro CSP.

## <a name="improve-efficiency"></a>Melhorar a eficiência

Pode determinar a utilização da VM ideal e identificar as VM inativas ou removê-las, bem como os discos desligados com a Cloudyn. Através da utilização de informações nos relatórios de Ineficácia e Otimização de Dimensionamento, pode criar um plano para reduzir ou remover VMs inativas. No entanto, os relatórios de otimização não são atualmente suportados para contas ou subscrições de parceiro CSP.

Se aprovisionou Instâncias Reservadas do AWS, pode melhorar a sua utilização de instâncias reservadas com relatórios de Otimização, onde pode ver recomendações de compra, modificar reservas e planear o aprovisionamento.


## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com a Cloudyn, o passo seguinte é registar o seu ambiente cloud e começar a explorar os seus dados.

- [Registe uma subscrição do Azure individual](quick-register-azure-sub.md)

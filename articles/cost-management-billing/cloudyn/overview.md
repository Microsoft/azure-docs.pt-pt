---
title: Descrição Geral da Cloudyn no Azure
description: A Cloudyn é uma solução de gestão de custos e várias clouds que o ajuda a utilizar o Azure e outros recursos da cloud.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 74d171a4c694f6d720ec3882f97d18e43aecc63f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543292"
---
# <a name="what-is-the-cloudyn-service"></a>O que é o serviço da Cloudyn?

A Cloudyn, uma subsidiária da Microsoft, permite-lhe monitorizar a utilização da cloud e os gastos dos seus recursos do Azure. Os relatórios do dashboard fáceis de compreender também ajudam na alocação de custos e showbacks/estornos. A Cloudyn ajuda a otimizar os gastos da cloud ao identificar recursos subutilizados que pode gerir e ajustar.

Para ver um vídeo introdutório, veja [Introduction to Azure Cloudyn (Introdução à Azure Cloudyn)](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/).
 
O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, veja [Azure Cost Management](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="monitor-usage-and-spending"></a>Monitorizar a utilização e os gastos

Monitorizar a utilização e os gastos é extremamente importante para infraestruturas da cloud porque as organizações pagam pelos recursos que consomem ao longo do tempo. Quando a utilização excede os limiares do contrato, podem ocorrer excessos de custos inesperados. Alguns fatores importantes podem dificultar a monitorização ad hoc. Em primeiro lugar, a projeção de custos baseada na utilização média assume que o consumo permanece consistente num determinado período de faturação. Em segundo lugar, quando os custos estão próximos ou excedem o seu orçamento, é importante receber notificações de forma pró-ativa para ajustar os gastos. Além disso, os fornecedores de serviços cloud podem não oferecer projeção de custo de acordo com os limiares ou relatórios de comparação de períodos.

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

- [Rever a utilização e os custos](tutorial-review-usage.md)

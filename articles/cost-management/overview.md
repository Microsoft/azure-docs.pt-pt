---
title: Descrição Geral da Cloudyn no Azure | Microsoft Docs
description: A Cloudyn é uma solução de gestão de custos e várias clouds que o ajuda a utilizar o Azure e outros recursos da cloud.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 08a1980a8855fc119a5066e058e9bb4d2df6646e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998864"
---
# <a name="what-is-the-cloudyn-service"></a>O que é o serviço do Cloudyn?

A Cloudyn, uma subsidiária da Microsoft, permite-lhe controlar a utilização da cloud e os gastos dos seus recursos do Azure e de outros fornecedores de cloud, incluindo AWS e Google. Os relatórios do dashboard fáceis de compreender também ajudam na alocação de custos e showbacks/estornos. A Cloudyn ajuda a otimizar os gastos da cloud ao identificar recursos subutilizados que pode gerir e ajustar.

Para ver um vídeo introdutório, veja [Introduction to Azure Cloudyn (Introdução à Azure Cloudyn)](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo).

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, veja [Azure Cost Management](overview-cost-mgt.md).

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Funcionalidades do Cloudyn mudar para o Azure Cost Management

A Microsoft adquiriu a Cloudyn e é migrar seus recursos de gerenciamento de custo do portal do Cloudyn nativamente no Azure. Para utilizar as novas funcionalidades, iniciar sessão no portal do Azure e navegue para [gestão de custos e a faturação](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na lista de serviços do Azure. Em comparação ao Cloudyn, a experiência nativa oferece um desempenho melhorado e latência mais baixa de dados de cerca de oito horas.

Migração de funcionalidade-chave para categorias de oferta Enterprise Agreement, o pay as you go e o MSDN para o Azure Cost Management está concluída. Subscrições de CSP estão a ser migrados para o Azure Cost Management.

Se tiver uma categoria de oferta ainda não foram migrados, deve continuar a utilizar o portal do Cloudyn. Todos os outros podem utilizar o Azure Cost Management.

| Funcionalidades e ofertas do Microsoft Azure | Serviço de gestão de custos recomendada |
| --- | --- |
| Contrato Enterprise do Azure | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Web do Azure Direct (PAYG/MSDN) | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Entre Clouds custam de suporte de análise de AWS (em pré-visualização) | [Gestão de Custos do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Recomendações de AWS | [Cloudyn](https://azure.cloudyn.com) |

Algumas das seguintes funcionalidades estão disponíveis no Cloudyn, mas todos eles estão agora disponíveis no Azure Cost Management.

- APIs
- Recomendações de computação do Azure
- Recomendações de reserva do Azure
- Orçamentos
- Análise de custos
- Exportar dados para uma conta de armazenamento do Azure
- Latências mais baixas
- O pacote de conteúdos do Power BI e o conector
- Suporte de marca do recurso

## <a name="monitor-usage-and-spending"></a>Monitorizar a utilização e os gastos

Monitorizar a utilização e os gastos é extremamente importante para infraestruturas da cloud porque as organizações pagam pelos recursos que consomem ao longo do tempo. Quando a utilização excede os limiares do contrato, podem ocorrer excessos de custos inesperados. Alguns fatores importantes podem dificultar ad hoc de monitorização. Em primeiro lugar, a projeção de custos baseada na utilização média assume que o consumo permanece consistente num determinado período de faturação. Em segundo lugar, quando os custos estão próximos ou excedem o seu orçamento, é importante receber notificações de forma pró-ativa para ajustar os gastos. Além disso, os fornecedores de serviços cloud podem não oferecer projeção de custo de acordo com os limiares ou relatórios de comparação de períodos.

Os relatórios ajudam a monitorizar os gastos para analisar e controlar a utilização da cloud, os custos e as tendências. Ao utilizar relatórios ao Longo do Tempo, pode detetar anomalias diferentes das tendências normais. São visíveis ineficiências na sua implementação da cloud nos relatórios de otimização. Também pode reparar em ineficiências nos relatórios de análise de custos.

## <a name="manage-costs"></a>Gerir os custos

Os dados históricos podem ajudá-lo a gerir os custos, ao analisar os custos de utilização e ao longo do tempo para identificar tendências. As tendências são então utilizadas para prever despesas futuras. A Cloudyn também inclui relatórios de custos estimados úteis.

A alocação de custos gere os custos ao analisá-los com base na sua política de etiquetagem. Pode utilizar etiquetas nas suas contas personalizadas, recursos e entidades para refinar a alocação de custos. O Gestor de Categoria organiza as suas etiquetas para ajudar a oferecer governação adicional. E utiliza a alocação de custos para análise de custos/estorno para mostrar a utilização de recursos e os custos associados para influenciar os comportamentos de consumo ou cobrar clientes de inquilino.

O controlo de acesso ajuda a gerir os custos, ao garantir que os utilizadores e as equipas acedam apenas aos dados de gestão de custos que precisam. Utilize a estrutura de entidade, a gestão de utilizadores e os relatórios agendados com listas de destinatários para atribuir acesso.

O alerta ajuda a gerir os custos ao notificá-lo automaticamente quando ocorrerem despesas invulgares ou gastar demasiado. Os alertas também podem notificar outros intervenientes automaticamente sobre anomalias de gastos e riscos de grandes gastos. Vários relatórios suportam alertas com base nos limiares de custo e orçamento. No entanto, os alertas não são atualmente suportados para contas ou subscrições de parceiro CSP.

## <a name="improve-efficiency"></a>Melhorar a eficiência

Pode determinar a utilização da VM ideal e identificar as VM inativas ou removê-las, bem como os discos desligados com a Cloudyn. Através da utilização de informações nos relatórios de Ineficácia e Otimização de Dimensionamento, pode criar um plano para reduzir ou remover VMs inativas. No entanto, os relatórios de otimização não são atualmente suportados para contas ou subscrições de parceiro CSP.

Se aprovisionou Instâncias Reservadas do AWS, pode melhorar a sua utilização de instâncias reservadas com relatórios de Otimização, onde pode ver recomendações de compra, modificar reservas e planear o aprovisionamento.


## <a name="next-steps"></a>Passos Seguintes

Agora que está familiarizado com a Cloudyn, o passo seguinte é registar o seu ambiente cloud e começar a explorar os seus dados.

- [Registe uma subscrição do Azure individual](quick-register-azure-sub.md)

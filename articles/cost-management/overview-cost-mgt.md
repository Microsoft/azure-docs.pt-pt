---
title: Descrição geral do Azure Cost Management | Microsoft Docs
description: O Azure Cost Management é uma solução de gestão de custos que ajuda a monitorizar e a controlar os gastos do Azure, e a otimizar a utilização de recursos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management-billing
manager: benshy
ms.custom: ''
ms.openlocfilehash: 90d2646aa554a20a823d29cde06537d05415b603
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230028"
---
# <a name="what-is-azure-cost-management"></a>O que é o Azure Cost Management?

A gestão de custos é o processo que diz respeito ao planeamento e ao controlo eficazes dos custos envolvidos na sua empresa. Normalmente, as tarefas de gestão de custos são realizadas por equipas de finanças, gestão e aplicações. O gerenciamento de custos do Azure ajuda as organizações a planejar com o custo em mente. Ele também ajuda a analisar os custos com eficiência e tomar medidas para otimizar os gastos com a nuvem. Para saber mais sobre como abordar a gestão de custos como uma organização, veja o artigo [Melhores práticas do Azure Cost Management](cost-mgt-best-practices.md).

Assista ao [vídeo visão geral do gerenciamento de custos do Azure](https://www.youtube.com/watch?v=el4yN5cHsJ0) para obter uma visão geral rápida sobre como o gerenciamento de custos do Azure pode ajudá-lo a economizar dinheiro no Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Embora relacionada, a faturação é diferente da gestão de custos. A faturação é o processo de faturar clientes relativamente a bens ou serviços e gerir a relação comercial.  Normalmente, as equipas de aprovisionamento e finanças realizam tarefas de faturação.

O Cost Management apresenta padrões de utilização e custos da organização com uma análise avançada. Os relatórios no gerenciamento de custos mostram os custos com base no uso consumidos pelos serviços do Azure e ofertas do Marketplace de terceiros. Os custos são baseados em preços negociados e no fator de reserva e Benefício Híbrido do Azure descontos. Em conjunto, os relatórios apresentam os seus custos internos e externos relativamente à utilização e os custos do Azure Marketplace. Outros custos, como compras de reservas, suporte e impostos, ainda não são apresentados nos relatórios. Os relatórios ajudam-no a compreender os seus gastos e a utilização de recursos, e podem ajudar a detetar anomalias nos gastos. Também está disponível a análise preditiva. O Cost Management utiliza grupos de gestão, orçamentos e recomendações do Azure para mostrar claramente como as suas despesas estão organizadas e como poderá reduzir os custos.

Pode utilizar o portal do Azure ou várias APIs para a automatização de exportações, para integrar dados de custos em sistemas e processos externos. Também estão disponíveis a exportação automatizada de dados de faturação e o agendamento de relatórios.

## <a name="plan-and-control-expenses"></a>Planear e controlar despesas

As formas de o Cost Management o ajudar a planear e controlar os seus custos incluem a análise de custos, orçamentos, recomendações e a exportação de dados de gestão de custos.

Pode utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos são acumulados e identificar as tendências das despesas. Além disso, pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento.

Os orçamentos ajudam-no a planear e cumprir a responsabilidade financeira da sua organização. Ajudam a impedir que os limiares ou limites de custos sejam ultrapassados. Os orçamentos também podem ajudá-lo a informar outras pessoas sobre os seus gastos para gerir proativamente os custos. Além disso, com os orçamentos, pode ver o progresso dos gastos ao longo do tempo.

As recomendações mostram como pode otimizar e melhorar a eficiência ao identificar os recursos inativos e subutilizados. Em alternativa, podem mostrar opções de recursos menos dispendiosas. Ao tomar medidas com base nas recomendações, pode alterar a forma como utiliza os recursos para poupar dinheiro. Para tomar medidas, veja primeiro as recomendações de otimização de custos para identificar as potenciais ineficiências de utilização. Em seguida, tome medidas com base numa recomendação para mudar a sua utilização dos recursos do Azure para uma opção mais económica. Em seguida, verifique a ação para se certificar de que a alteração feita é bem-sucedida.

Se utilizar sistemas externos para analisar ou aceder a dados de gestão de custos, pode exportar facilmente os dados do Azure. Além disso, pode definir o agendamento diário de uma exportação no formato CSV e armazenar os ficheiros de dados no armazenamento do Azure. Em seguida, pode aceder aos dados a partir do seu sistema externo.

## <a name="consider-cloudyn"></a>Considere o Cloudyn

O [Cloudyn](overview.md) é um serviço do Azure relacionado com o Cost Management. No Cloudyn, pode controlar a utilização e os gastos com a cloud para os seus recursos do Azure. O Cloudyn também suporta outros fornecedores de serviços cloud, incluindo a AWS e a Google. Os relatórios do dashboard fáceis de compreender também ajudam na alocação de custos e showbacks/estornos. Atualmente, o Cost Management não suporta a análise de custos/estorno ou outros fornecedores de serviços cloud. No entanto, o Cloudyn é uma opção que _de facto_ os suporta. Atualmente, o gerenciamento de custos não dá suporte a Microsoft Cloud contas do CSP (provedor de serviços), mas o Cloudyn. Se você tiver contas de CSP ou se quiser usar o regressivo/chargeback, poderá usar o Cloudyn para ajudar a gerenciar seus custos.

Assista ao [vídeo de gerenciamento de custos e Cloudyn do Azure](https://www.youtube.com/watch?v=PmwFWwSluh8) para ver as recomendações quando você deve usar o gerenciamento de custos do Azure ou o Cloudyn, com base em suas necessidades de negócios.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de funcionalidades do Azure Cost Management. No entanto, eles desempenham um papel importante no processo de gerenciamento de custos. Para saber mais sobre estas ferramentas, veja as ligações seguintes.

- [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/): utilize esta ferramenta para estimar os seus custos iniciais com a cloud.
- [Azure Migrate](../migrate/migrate-overview.md): avalie a carga de trabalho do seu datacenter atual para obter informações sobre o que precisa numa solução de substituição do Azure.
- [Assistente do Azure](../advisor/advisor-overview.md): identifique VMs não utilizadas e obtenha recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/): utilize as suas licenças do Windows Server ou SQL Server atuais no local para que as VMs no Azure as guardem.


## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o Cost Management, o próximo passo é começar a utilizar o serviço.

- Comece a utilizar o Azure Cost Management para [analisar os custos](quick-acm-cost-analysis.md).
- Também pode saber mais sobre as [práticas recomendadas do Azure Cost Management](cost-mgt-best-practices.md).

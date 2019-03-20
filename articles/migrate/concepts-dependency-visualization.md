---
title: Visualização de dependências no Azure Migrate | Documentos da Microsoft
description: Fornece uma visão geral dos cálculos de avaliação no serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004088"
---
# <a name="dependency-visualization"></a>Visualização de dependência

O [do Azure Migrate](migrate-overview.md) serviços avalia os grupos de máquinas no local para migração para o Azure. Pode utilizar a funcionalidade de visualização de dependência no Azure Migrate para criar grupos. Este artigo fornece informações sobre esta funcionalidade.

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

## <a name="overview"></a>Descrição geral

Visualização de dependências no Azure Migrate permite-lhe criar grupos de confiança elevada para avaliações de migração. Usando a visualização de dependências, pode ver as dependências de rede de máquinas e identificar máquinas relacionadas que precisavam de ser migradas em conjunto para o Azure. Esta funcionalidade é útil em cenários em que não está totalmente ciente das máquinas que constituem a sua aplicação e tem de ser migradas em conjunto para o Azure.

## <a name="how-does-it-work"></a>Como funciona?

O Azure Migrate utiliza a [mapa de serviço](../operations-management-suite/operations-management-suite-service-map.md) solução em [registos do Azure Monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Para aproveitar a visualização de dependências, precisa associar área de trabalho do Log Analytics, nova ou existente, com um projeto do Azure Migrate.
- Só pode criar ou anexar uma área de trabalho na mesma subscrição em que é criado o projeto de migração.
- Para anexar uma área de trabalho do Log Analytics a um projeto, aceda a **Essentials** secção do projeto **descrição geral** página e clique em **requer configuração**

    ![Associar área de trabalho do Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Ao associar uma área de trabalho, obterá a opção para criar uma nova área de trabalho ou anexar um já existente:
  - Quando cria uma nova área de trabalho, tem de especificar um nome para a área de trabalho. A área de trabalho, em seguida, é criada numa região na mesma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) como o projeto de migração.
  - Quando anexa uma área de trabalho existente, pode escolher entre todas as áreas de trabalho disponíveis na mesma subscrição que o projeto de migração. Tenha em atenção que apenas essas áreas de trabalho estão listadas que foram criados numa região onde [mapa de serviço é suportado](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Para poder ligar uma área de trabalho, certifique-se de que tem acesso de "Reader" para a área de trabalho.

  > [!NOTE]
  > Depois de ter anexada uma área de trabalho para um projeto, não é possível alterá-lo mais tarde.

- O espaço de trabalho associado é marcado com a chave **projeto de migração**e o valor **nome do projeto**, que pode usar para pesquisar no portal do Azure.
- Para navegar para a área de trabalho associada ao projeto, pode aceder à **Essentials** secção do projeto **descrição geral** página e aceder a área de trabalho

    ![Navegue até a área de trabalho do Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Para utilizar a visualização de dependência, terá de transferir e instalar agentes em cada máquina no local que pretende analisar.  

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) tem de ser instalado em cada máquina.
- O [agente de dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) tem de ser instalado em cada máquina.
- Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar o gateway de Log Analytics nos mesmos.

Não precisa destes agentes nas máquinas que pretende avaliar a menos que está usando a visualização de dependência.

## <a name="do-i-need-to-pay-for-it"></a>É necessário para pagá-la?

O Azure Migrate está disponível sem custos adicionais. Utilização da funcionalidade de visualização de dependência no Azure Migrate requer o mapa de serviço e necessita que associe uma área de trabalho do Log Analytics, nova ou existente, com o projeto do Azure Migrate. A funcionalidade de visualização de dependência no Azure Migrate é gratuita durante os primeiros 180 dias no Azure Migrate.

1. Utilização de quaisquer soluções que não seja o mapa de serviço dentro desta área de trabalho do Log Analytics incorre [padrão do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) encargos.
2. Para suportar cenários de migração sem custos adicionais, a solução mapa de serviço não será cobrado qualquer custo para os primeiros 180 dias a partir do dia de associação de área de trabalho do Log Analytics com o projeto do Azure Migrate. Após 180 dias, serão aplicadas tarifas padrão do Log Analytics.

Quando registra agentes à área de trabalho, utilize o ID e a chave fornecida pelo projeto na página de passos de agente de instalação.

Quando o projeto do Azure Migrate é eliminado, a área de trabalho não é eliminada, juntamente com ele. Publicar a eliminação do projeto e a utilização de mapa de serviço não serão gratuita para cada nó será cobrada de acordo com o escalão pago de área de trabalho do Log Analytics.

> [!NOTE]
> A funcionalidade de visualização de dependência utiliza o mapa de serviço por meio de uma área de trabalho do Log Analytics. Desde a 28 de Fevereiro de 2018, com o anúncio da disponibilidade geral do Azure Migrate, a funcionalidade agora está disponível sem custos adicionais. Terá de criar um novo projeto para que utilize a área de trabalho de utilização gratuita. Áreas de trabalho existentes antes da disponibilidade geral são ainda faturáveis, por conseguinte, recomendamos que mover para um novo projeto.

Saiba mais sobre os preços do Azure Migrate [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Como posso gerir a área de trabalho?

Pode utilizar a área de trabalho do Log Analytics fora do Azure Migrate. Não é eliminado se eliminar o projeto de migração no qual foi criado. Se já não precisar de área de trabalho, [eliminá-lo](../azure-monitor/platform/manage-access.md) manualmente.

Não elimine a área de trabalho criada pelo Azure Migrate, a menos que excluir o projeto de migração. Se o fizer, a funcionalidade de visualização de dependência não funcionará conforme esperado.

## <a name="next-steps"></a>Passos Seguintes
- [Agrupar máquinas através de dependências de máquina](how-to-create-group-machine-dependencies.md)
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes sobre a visualização de dependência.

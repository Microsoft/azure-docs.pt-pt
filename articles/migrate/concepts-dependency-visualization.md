---
title: Visualização de dependência no Azure Migrate
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor em Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425685"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Este artigo descreve a funcionalidade de visualização da dependência em Azure Migrate: Server Assessment.

A visualização da dependência ajuda-o a compreender as dependências através de máquinas que pretende avaliar e migrar. Normalmente, utiliza-se mapeamento de dependência quando se quer avaliar máquinas com níveis de confiança mais elevados.

- Em Azure Migrate: Server Assessment, reúne máquinas em grupos para avaliação. Os grupos geralmente consistem em máquinas que você quer migrar em conjunto, e a visualização da dependência ajuda-o a cruzar as dependências das máquinas, para que você possa agrupar máquinas com precisão.
- Utilizando a visualização, pode descobrir sistemas interdependentes que precisam migrar juntos. Pode identificar se os sistemas de funcionamento ainda estão em uso, ou se os sistemas podem ser desativados em vez de migrados.
- Visualizar dependências ajuda a garantir que nada é deixado para trás, e evitar interrupções surpresa durante a migração.
- Esta funcionalidade é especialmente útil se não estiver completamente ciente de máquinas que fazem parte de apps, pelo que deve ser migrada em conjunto para o Azure.


> [!NOTE]
> A funcionalidade de visualização da dependência não está disponível no Governo do Azure.

## <a name="agent-based-and-agentless"></a>Baseado em agente e sem agente

Existem duas opções para implementar a visualização da dependência:

- **Visualização**da dependência sem agente : Esta opção encontra-se atualmente em pré-visualização e está disponível apenas para VMware VMs. Não requer que instale agentes em máquinas. 
    - Funciona capturando os dados de ligação TCP a partir de máquinas para as quais está ativado. [Saiba mais](how-to-create-group-machine-dependencies-agentless.md).
Após a descoberta da dependência, o aparelho recolhe dados de máquinas num intervalo de sondagens de cinco minutos.
    - São recolhidos os seguintes dados:
        - Ligações TCP
        - Nomes de processos que têm ligações ativas
        - Nomes de aplicações instaladas que executam os processos acima referidos
        - Não. de conexões detetadas em todos os intervalos de votação
- **Visualização**da dependência baseada no agente : Para utilizar a visualização da dependência baseada no agente, é necessário descarregar e instalar os seguintes agentes em cada máquina no local que pretende analisar.  
    - O [Agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa de ser instalado em cada computador. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sobre como instalar o agente MMA.
    - O [agente dependency](../azure-monitor/platform/agents-overview.md#dependency-agent) precisa de ser instalado em cada máquina. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sobre como instalar o agente de dependência.
    - Além disso, caso tenha computadores sem conectividade à Internet, terá de transferir e instalar o gateway do Log Analytics.

## <a name="agent-based-requirements"></a>Requisitos baseados no agente

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>O que preciso para implementar a visualização da dependência?

Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.

[Saiba mais](how-to-assess.md) sobre adicionar a ferramenta e implementar um aparelho para [hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.


### <a name="how-does-it-work"></a>Como funciona?

A Azure Migrate utiliza a solução [Service Map](../operations-management-suite/operations-management-suite-service-map.md) em [registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para visualização da dependência.

- Para alavancar a visualização da dependência, é necessário associar um espaço de trabalho log Analytics (novo ou existente), com um projeto Azure Migrate.
- O espaço de trabalho deve estar na mesma subscrição em que se cria o projeto Azure Migrate.
- A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto. Note também que o espaço de trabalho deve estar numa região em que o Mapa de [Serviços é suportado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
- O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
- No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.

    ![Navegar no espaço de trabalho do Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Preciso pagar por isso?

A visualização da dependência requer um Mapa de Serviço e um espaço de trabalho associado log Analytics. 

- A solução do Mapa de Serviço sem encargos nos primeiros 180 dias. Isto é a partir do dia em que associou o espaço de trabalho log Analytics ao projeto Azure Migrate.
- Após 180 dias, serão aplicados os encargos padrão do Log Analytics.
- A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em cargas padrão de [Log Analytics.](https://azure.microsoft.com/pricing/details/log-analytics/)
- Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é eliminado juntamente com ele. Após a exclusão do projeto, o uso do Mapa de Serviço sem graça e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics.

Saiba mais sobre os preços do Azure Migrate [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate em 28 de fevereiro de 2018, poderá ter incorrido em custos adicionais do Mapa de Serviços. Para garantir que só paga após 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da disponibilidade geral ainda são responsáveis.



### <a name="how-do-i-manage-the-workspace"></a>Como posso gerir o espaço de trabalho?

- Quando regista agentes no espaço de trabalho, utiliza a identificação e a chave fornecidas pelo projeto Azure Migrate.
- Pode utilizar o espaço de trabalho log Analytics fora do Azure Migrate.
- Se eliminar o projeto azure migrate associado, o espaço de trabalho não é eliminado automaticamente. Tem de [o apagar manualmente](../azure-monitor/platform/manage-access.md).
- Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que apague o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização da dependência não funcionará como esperado.

## <a name="next-steps"></a>Passos seguintes
- [Máquinas de grupo que usam dependências de máquinas](how-to-create-group-machine-dependencies.md)
- [Saiba mais](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre as FAQs sobre visualização da dependência.



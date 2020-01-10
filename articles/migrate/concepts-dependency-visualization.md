---
title: Visualização de dependência no Azure Migrate
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor nas migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 75b2120b9fef904114d532e83d571c08e1a1034d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772317"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Este artigo descreve o recurso de visualização de dependência em migrações para Azure: avaliação de servidor.

A visualização de dependências ajuda você a entender as dependências entre computadores que você deseja avaliar e migrar. Normalmente, você usa o mapeamento de dependência quando deseja avaliar computadores com níveis mais altos de confiança.

- Nas migrações para Azure: avaliação de servidor, você reúne computadores em grupos para avaliação. Os grupos geralmente consistem em computadores que você deseja migrar juntos, e a visualização de dependência ajuda a verificar dependências de máquina, para que você possa agrupar as máquinas com precisão.
- Usando a visualização, você pode descobrir sistemas interdependentes que precisam ser migrados juntos. Você pode identificar se os sistemas em execução ainda estão em uso ou se os sistemas podem ser descomissionados em vez de migrados.
- A visualização de dependências ajuda a garantir que nada seja deixado para trás e evitar interrupções surpresa durante a migração.
- Esse recurso é especialmente útil se você não estiver completamente ciente de computadores que fazem parte de aplicativos e, portanto, devem ser migrados juntos para o Azure.


> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure governamental.

## <a name="agent-based-and-agentless"></a>Com base em agente e sem agente

Há duas opções para implantar a visualização de dependência:

- **Visualização de dependência sem agente**: essa opção está atualmente em visualização e está disponível somente para VMs VMware. Ele não exige a instalação de agentes em computadores. 
    - Ele funciona capturando os dados de conexão TCP de computadores para os quais está habilitado. [Saiba mais](how-to-create-group-machine-dependencies-agentless.md).
Depois que a descoberta de dependência é iniciada, o dispositivo reúne dados de computadores em um intervalo de sondagem de cinco minutos.
    - Os seguintes dados são coletados:
        - Ligações TCP
        - Nomes de processos que têm conexões ativas
        - Nomes de aplicativos instalados que executam os processos acima
        - Não. de conexões detectadas em cada intervalo de sondagem
- **Visualização de dependência baseada em agente**: para usar a visualização de dependência baseada em agente, você precisa baixar e instalar os agentes a seguir em cada computador local que você deseja analisar.  
    - O [Agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa de ser instalado em cada computador. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sobre como instalar o agente do MMA.
    - O [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent) precisa ser instalado em cada computador. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sobre como instalar o Dependency Agent.
    - Além disso, caso tenha computadores sem conectividade à Internet, terá de transferir e instalar o gateway do Log Analytics.

## <a name="agent-based-requirements"></a>Requisitos baseados em agente

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>O que é necessário para implantar a visualização de dependência?

Antes de implantar a visualização de dependência, você deve ter um projeto de migrações para Azure em vigor, com a ferramenta migrações para Azure: Server Assessment adicionada ao projeto. Você implanta a visualização de dependência depois de configurar um dispositivo de migrações para Azure para descobrir seus computadores locais.

[Saiba mais](how-to-assess.md) sobre como adicionar a ferramenta e implantar um dispositivo para servidores [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)ou físicos.


### <a name="how-does-it-work"></a>Como funciona?

As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.

- Para aproveitar a visualização de dependência, você precisa associar um espaço de trabalho Log Analytics (novo ou existente), com um projeto de migrações para Azure.
- O espaço de trabalho deve estar na mesma assinatura que na qual você cria o projeto de migrações para Azure.
- As migrações para Azure dão suporte a espaços de trabalho que residem nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental. Espaços de trabalho em outras regiões não podem ser associados a um projeto. Além disso, observe que o espaço de trabalho deve estar em uma região na qual [mapa do serviço tem suporte](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- O espaço de trabalho para um projeto de migrações para Azure não pode ser modificado após ser adicionado.
- No Log Analytics, o espaço de trabalho associado à migração do Azure é marcado com a chave do projeto de migração e o nome do projeto.

    ![Navegar Log Analytics espaço de trabalho](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Preciso pagar por ele?

A visualização de dependência requer Mapa do Serviço e um espaço de trabalho Log Analytics associado. 

- A solução Mapa do Serviço não incorrerá em cobranças pelos primeiros 180 dias. Isso é a partir do dia em que você associou o espaço de trabalho Log Analytics com o projeto de migrações para Azure.
- Após 180 dias, os encargos de Log Analytics padrão serão aplicados.
- Usar qualquer solução que não seja Mapa do Serviço no espaço de trabalho Log Analytics associado incorrerá em encargos de [log Analytics padrão](https://azure.microsoft.com/pricing/details/log-analytics/) .
- Quando o projeto de migrações para Azure é excluído, o espaço de trabalho não é excluído junto com ele. Depois de excluir o projeto, Mapa do Serviço uso não é gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho Log Analytics.

Saiba mais sobre os preços do Azure Migrate [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Se você tiver projetos que criou antes que o Azure migre a disponibilidade geral em 28 de fevereiro de 2018, você poderá ter incorrido Mapa do Serviço encargos adicionais. Para garantir que você pague apenas após 180 dias, recomendamos que você crie um novo projeto, já que os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança.



### <a name="how-do-i-manage-the-workspace"></a>Como fazer gerenciar o espaço de trabalho?

- Ao registrar agentes no espaço de trabalho, você usa a ID e a chave fornecidas pelo projeto de migrações para Azure.
- Você pode usar o espaço de trabalho Log Analytics fora das migrações para Azure.
- Se você excluir o projeto de migrações do Azure associado, o espaço de trabalho não será excluído automaticamente. Você precisa [excluí-lo manualmente](../azure-monitor/platform/manage-access.md).
- Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.

## <a name="next-steps"></a>Passos seguintes
- [Agrupar computadores usando dependências de máquina](how-to-create-group-machine-dependencies.md)
- [Saiba mais](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre as perguntas frequentes sobre a visualização de dependência.



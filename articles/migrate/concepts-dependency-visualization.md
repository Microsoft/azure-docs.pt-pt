---
title: Análise de dependência em Azure Migrate Discovery e avaliação
description: Descreve como usar a análise de dependência para avaliação usando Azure Migrate Discovery e avaliação.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778394"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise da dependência em Azure Migrate: Descoberta e avaliação.

A análise da dependência identifica dependências entre servidores descobertos no local. Fornece estas vantagens:

- Pode reunir servidores em grupos para avaliação, com maior precisão, com maior confiança.
- Pode identificar servidores que devem ser migrados juntos. Isto é especialmente útil se não tiver a certeza de quais os servidores que fazem parte de uma implementação de aplicações que pretende migrar para a Azure.
- Pode identificar se os servidores estão a ser utilizados e quais os servidores que podem ser desativados em vez de migrados.
- Analisar as dependências ajuda a garantir que nada seja deixado para trás, evitando assim interrupções surpresa após a migração.
- [Reveja](common-questions-discovery-assessment.md#what-is-dependency-visualization) questões comuns sobre análise de dependência.

## <a name="analysis-types"></a>Tipos de análise

Existem duas opções para implementar a análise da dependência

**Opção** | **Detalhes** | **Cloud pública** | **Azure Government**
----  |---- | ----
**Sem agente** | Pesquisas de dados de servidores em VMware usando APIs vSphere.<br/><br/> Não é preciso instalar agentes nos servidores.<br/><br/> Esta opção encontra-se atualmente em pré-visualização, apenas para servidores em VMware. | Suportado. | Suportado.
**Análise baseada em agente** | Utiliza a [solução de Mapa de Serviço](../azure-monitor/vm/service-map.md) no Monitor Azure, para permitir a visualização e análise de dependência.<br/><br/> Tem de instalar agentes em cada servidor no local que pretende analisar. | Suportado | Não suportado.

## <a name="agentless-analysis"></a>Análise sem agente

A análise da dependência de agentes funciona capturando dados de ligação TCP a partir de servidores para os quais está ativado. Não há agentes instalados nos servidores. As ligações com o mesmo servidor de origem e processo, e servidor de destino, processo e porta são agrupadas logicamente numa dependência. Pode visualizar dados de dependência capturados numa vista de mapa, ou exportá-lo como um CSV. Não há agentes instalados nos servidores que pretende analisar.

### <a name="dependency-data"></a>Dados de dependência

Após o início da descoberta dos dados de dependência, começa a sondagem:

- O aparelho Azure Migrate recolhe dados de ligação TCP dos servidores a cada cinco minutos para recolher dados.
- Os dados são recolhidos dos servidores dos hóspedes através do vCenter Server, utilizando APIs vSphere.
- As sondagens reúnem estes dados:

    - Nome dos processos que têm ligações ativas.
    - Nome da aplicação que executam processos que têm ligações ativas.
    - Porta de destino nas ligações ativas.

- Os dados recolhidos são processados no aparelho Azure Migrate, para deduzir informações de identidade, e são enviados para Azure Migrate a cada seis horas


## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análise baseada em agente, Azure Migrate: A descoberta e avaliação utiliza a solução [de Mapa de Serviço](../azure-monitor/vm/service-map.md) no Monitor Azure. Instala o [agente Microsoft Monitoring Agent/Log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent) e o agente [Dependency](../azure-monitor/agents/agents-overview.md#dependency-agent)em cada servidor que pretende analisar.

### <a name="dependency-data"></a>Dados de dependência

A análise baseada em agentes fornece estes dados:

- Nome do servidor de origem, processo, nome da aplicação.
- Nome do servidor de destino, processo, nome da aplicação e porta.
- O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics.

## <a name="compare-agentless-and-agent-based"></a>Compare sem agente e baseado em agente

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseada em agente**
--- | --- | ---
**Suporte** | Em pré-visualização para servidores apenas em VMware. [Reveja](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operativos suportados. | Em disponibilidade geral (GA).
**Agente** | Não são necessários agentes nos servidores que queiras analisar. | Agentes necessários em cada servidor no local que pretende analisar.
**Log Analytics** | Não necessárias. | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/vm/service-map.md) nos [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md) para análise de dependência.<br/><br/> Associa um espaço de trabalho log Analytics a um projeto. O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
**Processo** | Captura dados de ligação TCP. Após a descoberta, recolhe dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados num servidor recolhem dados sobre processos TCP e ligações de entrada/saída para cada processo.
**Dados** | Nome do servidor de origem, processo, nome da aplicação.<br/><br/> Nome do servidor de destino, processo, nome da aplicação e porta. | Nome do servidor de origem, processo, nome da aplicação.<br/><br/> Nome do servidor de destino, processo, nome da aplicação e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
**Visualização** | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/>  O mapa pode ser visto apenas durante uma hora.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Os dados dos últimos 30 dias podem ser descarregados num formato CSV. | Os dados podem ser consultados com Log Analytics.



## <a name="next-steps"></a>Passos seguintes

- [Configurar a](how-to-create-group-machine-dependencies.md) visualização de dependência baseada em agente.
- [Experimente a](how-to-create-group-machine-dependencies-agentless.md) visualização de dependência sem agente para servidores em VMware.
- Reveja [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização de dependência.

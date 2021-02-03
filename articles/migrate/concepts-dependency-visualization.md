---
title: Análise de dependência na avaliação do servidor migratório Azure
description: Descreve como utilizar a análise de dependência para avaliação usando a Avaliação do Servidor Azure Migrate.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: 378a85ed77a6eedeb928dee541046db1909da553
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491996"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise de dependência em Azure Migrate: Avaliação do servidor.


A análise da dependência identifica as dependências entre máquinas descobertas no local. Fornece estas vantagens: 

- Pode reunir máquinas em grupos para avaliação, com maior precisão, com maior confiança.
- Pode identificar máquinas que devem ser migradas juntas. Isto é especialmente útil se não tiver a certeza de quais as máquinas que fazem parte de uma implementação de aplicações que pretende migrar para Azure.
- Pode identificar se as máquinas estão a ser utilizadas e quais as máquinas que podem ser desativadas em vez de migradas.
- Analisar as dependências ajuda a garantir que nada seja deixado para trás, evitando assim interrupções surpresa após a migração.
- [Reveja](common-questions-discovery-assessment.md#what-is-dependency-visualization) questões comuns sobre análise de dependência.


## <a name="analysis-types"></a>Tipos de análise

Existem duas opções para implementar a análise da dependência

**Opção** | **Detalhes** | **Cloud pública** | **Azure Government**
----  |---- | ---- 
**Sem agente** | Sondagens dados de VMware VMs usando APIs vSphere.<br/><br/> Não é preciso instalar agentes em VMs.<br/><br/> Esta opção encontra-se atualmente em pré-visualização, apenas para VMware VMs. | Suportado. | Suportado.
**Análise baseada em agente** | Utiliza a [solução de Mapa de Serviço](../azure-monitor/insights/service-map.md) no Monitor Azure, para permitir a visualização e análise de dependência.<br/><br/> Tem de instalar agentes em cada máquina no local que pretende analisar. | Suportado | Não suportado.


## <a name="agentless-analysis"></a>Análise sem agente

A análise da dependência de agentes funciona capturando dados de ligação TCP de máquinas para as quais está ativado. Não há agentes instalados em VMs. As ligações com o mesmo servidor de origem e processo, e servidor de destino, processo e porta são agrupadas logicamente numa dependência. Pode visualizar dados de dependência capturados numa vista de mapa, ou exportá-lo como um CSV. Não são instalados agentes em máquinas que queira analisar.

### <a name="dependency-data"></a>Dados de dependência

Após o início da descoberta dos dados de dependência, começa a sondagem:

- O aparelho Azure Migrate recolhe dados de ligação TCP de máquinas a cada cinco minutos para recolher dados.
- Os dados são recolhidos a partir de VMs de hóspedes através do vCenter Server, utilizando APIs vSphere.
- As sondagens reúnem estes dados:

    - Nome dos processos que têm ligações ativas.
    - Nome da aplicação que executam processos que têm ligações ativas.
    - Porta de destino nas ligações ativas.

- Os dados recolhidos são processados no aparelho Azure Migrate, para deduzir informações de identidade, e são enviados para Azure Migrate a cada seis horas


## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análises baseadas em agentes, a Avaliação do Servidor utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) no Monitor Azure. Instala o [agente Microsoft Monitoring Agent/Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o agente [Dependency](../azure-monitor/platform/agents-overview.md#dependency-agent)em cada máquina que pretende analisar.

### <a name="dependency-data"></a>Dados de dependência

A análise baseada em agentes fornece estes dados:

- Nome do servidor da máquina de origem, processo, nome da aplicação.
- Nome do servidor da máquina de destino, processo, nome da aplicação e porta.
- O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 



## <a name="compare-agentless-and-agent-based"></a>Compare sem agente e baseado em agente

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseada em agente**
--- | --- | ---
**Suporte** | Apenas na pré-visualização de VMware VMs. [Reveja](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) os sistemas operativos suportados. | Em disponibilidade geral (GA).
**Agente** | Não são necessários agentes em máquinas que queiras analisar. | Agentes necessários em cada máquina no local que você quer analisar.
**Log Analytics** | Não necessárias. | A Azure Migrate utiliza a solução [de Mapa de Serviço](../azure-monitor/insights/service-map.md) nos [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) para análise de dependência.<br/><br/> Associa um espaço de trabalho log Analytics a um projeto Azure Migrate. O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)
**Processo** | Captura dados de ligação TCP. Após a descoberta, recolhe dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre os processos TCP e ligações de entrada/saída para cada processo.
**Dados** | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
**Visualização** | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/>  O mapa pode ser visto apenas durante uma hora.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Os dados dos últimos 30 dias podem ser descarregados num formato CSV. | Os dados podem ser consultados com Log Analytics.



## <a name="next-steps"></a>Passos seguintes

- [Configurar a](how-to-create-group-machine-dependencies.md) visualização de dependência baseada em agente.
- [Experimente](how-to-create-group-machine-dependencies-agentless.md) a visualização de dependência sem agente para VMware VMs.
- Reveja [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização de dependência.

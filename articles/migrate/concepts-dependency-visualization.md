---
title: Análise de dependência na avaliação do servidor migratório Azure
description: Descreve como utilizar a análise de dependência para avaliação usando a Avaliação do Servidor Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b269322f5426a68b072452bc2f79531685be3742
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84447582"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise de dependência em Azure Migrate:Avaliação do servidor.

## <a name="overview"></a>Descrição geral

A análise da dependência ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. 

- Em Azure Migrate:Server Assessment, recolhe-se máquinas em grupo e, em seguida, avalia-se o grupo. A análise da dependência ajuda-o a agrupar as máquinas com mais precisão, com alta confiança para avaliação.
- A análise da dependência permite identificar máquinas que devem ser migradas em conjunto. Pode identificar se as máquinas estão a ser utilizadas ou se podem ser desativadas em vez de migradas.
- Analisar as dependências ajuda a garantir que nada seja deixado para trás e evitar interrupções surpresa durante a migração.
- A análise é especialmente útil se não tiver a certeza se as máquinas fazem parte de uma implementação de aplicações que pretende migrar para Azure.
- [Reveja](common-questions-discovery-assessment.md#what-is-dependency-visualization) questões comuns sobre análise de dependência.

Existem duas opções para implementar a análise da dependência

- **Baseado em agente:** A análise da dependência baseada em agentes requer que os agentes sejam instalados em cada máquina no local que pretende analisar.
- **Agenteless**: Com uma análise sem agente, não é necessário instalar agentes em máquinas que pretende verificar. Esta opção está atualmente em pré-visualização, e só está disponível para VMware VMs.

> [!NOTE]
> A análise da dependência baseada em agentes não está disponível no Governo de Azure. Pode utilizar a análise de dependência sem agente.

## <a name="agentless-analysis"></a>Análise sem agente

A análise da dependência de agentes funciona capturando dados de ligação TCP de máquinas para as quais está ativado. Não são instalados agentes em máquinas que queira analisar.

### <a name="collected-data"></a>Dados recolhidos

Após o início da descoberta da dependência, o aparelho recolhe dados de máquinas a cada cinco minutos para recolher dados. Estes dados são recolhidos a partir de VMs de hóspedes através do vCenter Server, utilizando APIs vSphere. Os dados recolhidos são processados no aparelho Azure Migrate, para deduzir informações de identidade, e são enviados para Azure Migrate de seis em seis horas.

As sondagens recolhem estes dados de máquinas: 
- Nome dos processos que têm ligações ativas.
- Nome da aplicação que executam processos que têm ligações ativas.
- Porta de destino nas ligações ativas.

## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análise baseada em agentes, a Avaliação do Servidor utiliza a [solução de Mapa de Serviço](../azure-monitor/insights/service-map.md) no Monitor Azure para permitir a visualização e análise da dependência. O [agente microsoft monitoring/log analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o agente [Dependency](../azure-monitor/platform/agents-overview.md#dependency-agent), devem ser instalados em cada máquina que pretende analisar.

### <a name="collected-data"></a>Dados recolhidos

Para análises baseadas em agentes, são recolhidos os seguintes dados:

- Nome do servidor da máquina de origem, processo, nome da aplicação.
- Nome do servidor da máquina de destino, processo, nome da aplicação e porta.
- O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Compare sem agente e baseado em agente

As diferenças entre visualização sem agente e visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseada em agente**
--- | --- | ---
Suporte | Esta opção está atualmente em pré-visualização, e só está disponível para VMware VMs. [Reveja](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os sistemas operativos suportados. | Em disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em máquinas que pretende verificar. | Agentes a instalar em cada máquina no local que pretende analisar: O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)e o [agente Desadependido](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Não é necessário. | A Azure Migrate utiliza a solução [de Mapa de Serviço](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) nos [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para análise de dependência. 
Como funciona | Captura dados de ligação TCP em máquinas ativadas para visualização de dependência. Após a descoberta, recolhe dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre processos TCP e ligações de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome do servidor da máquina de destino, processo, nome da aplicação e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa pode ser visto apenas durante uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Os dados dos últimos 30 dias podem ser descarregados num formato CSV. | Os dados podem ser consultados com Log Analytics.



## <a name="next-steps"></a>Próximos passos
- Reveja os requisitos para a configuração de análises baseadas em agentes para [VMware VMs,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e [VMs Hiper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Reveja](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os requisitos para a análise sem agente dos VMware VMs.
- [Configurar](how-to-create-group-machine-dependencies.md) visualização de dependência baseada em agente
- [Experimente](how-to-create-group-machine-dependencies-agentless.md) a visualização de dependência sem agente para VMware VMs.
- Reveja [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização de dependência.



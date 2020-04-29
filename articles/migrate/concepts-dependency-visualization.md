---
title: Análise de dependência na Avaliação do Servidor Migratório Azure
description: Descreve como usar a análise de dependência para avaliação usando a Avaliação do Servidor Migratório Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024766"
---
# <a name="dependency-analysis"></a>Análise de dependência

Este artigo descreve a análise da dependência em Azure Migrate:Server Assessment.

## <a name="overview"></a>Descrição geral

A análise da dependência ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. 

- Em Azure Migrate:Server Assessment, você recolhe máquinas em um grupo, e depois avalia o grupo. A análise da dependência ajuda-o a agrupar as máquinas com mais precisão, com alta confiança para a avaliação.
- A análise da dependência permite identificar máquinas que devem ser migradas em conjunto. Pode identificar se as máquinas estão a ser utilizadas, ou se podem ser desativadas em vez de emigrarem.
- Analisar as dependências ajuda a garantir que nada é deixado para trás, e evitar interrupções surpresa durante a migração.
- A análise é especialmente útil se não tiver a certeza se as máquinas fazem parte de uma implementação de aplicações que pretende migrar para o Azure.
- [Reveja](common-questions-discovery-assessment.md#what-is-dependency-visualization) questões comuns sobre análise de dependência.

Existem duas opções para implementar a análise da dependência

- Baseado no **agente:** A análise de dependência baseada no agente requer que os agentes sejam instalados em cada máquina no local que pretende analisar.
- **Agente:** Com análises sem agente, não precisa de instalar agentes em máquinas que pretende cruzar. Esta opção encontra-se atualmente em pré-visualização e só está disponível para VMware VMs.

> [!NOTE]
> A análise da dependência baseada em agentes não está disponível no Governo de Azure. Podeusar a análise de dependência sem agente.

## <a name="agentless-analysis"></a>Análise sem agente

A análise da dependência sem agente funciona capturando dados de ligação TCP de máquinas para as quais está ativado. Não são instalados agentes em máquinas que queira analisar.

### <a name="collected-data"></a>Dados recolhidos

Após o início da descoberta da dependência, o aparelho recolhe dados de máquinas a cada cinco minutos para recolher dados. Estes dados são recolhidos a partir de VMs convidados através do VCenter Server, utilizando APIs vSphere. Os dados recolhidos são processados no aparelho Migratório Azure, para deduzir informações de identidade, e são enviados para o Azure Migrate a cada seis horas.

As sondagens recolhem estes dados a partir de máquinas: 
- Nome dos processos que têm ligações ativas.
- Nome da aplicação que executa processos que têm ligações ativas.
- Porta de destino nas ligações ativas.

## <a name="agent-based-analysis"></a>Análise baseada em agente

Para análise baseada em agentes, a Avaliação do Servidor utiliza a [solução do Mapa](../azure-monitor/insights/service-map.md) de Serviços no Monitor Azure para permitir a visualização e análise da dependência. O agente de [monitorização da Microsoft/agente de Log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent) e o [agente Dependency](../azure-monitor/platform/agents-overview.md#dependency-agent)devem ser instalados em cada máquina que pretende analisar.

### <a name="collected-data"></a>Dados recolhidos

Para análise baseada no agente, são recolhidos os seguintes dados:

- Nome do servidor da máquina de origem, processo, nome da aplicação.
- Nome, processo, nome de servidor de máquina de destino, e porta.
- O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 


## <a name="compare-agentless-and-agent-based"></a>Compare agentes sem agente e baseados em agentes

As diferenças entre a visualização sem agente e a visualização baseada em agentes são resumidas na tabela.

**Requisito** | **Sem agente** | **Baseado em agente**
--- | --- | ---
Suporte | Esta opção encontra-se atualmente em pré-visualização e só está disponível para VMware VMs. [Reveja](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os sistemas operativos suportados. | Na disponibilidade geral (GA).
Agente | Não é necessário instalar agentes em máquinas que queira sondar. | Agentes a instalar em cada máquina no local que pretende analisar: O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows), e o [agente dependency](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | Não é necessário. | A Azure Migrate utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para análise de dependência. 
Como funciona | Captura dados de ligação TCP em máquinas habilitadas para visualização da dependência. Após a descoberta, reúne dados em intervalos de cinco minutos. | Os agentes do Mapa de Serviço instalados numa máquina recolhem dados sobre os processos de TCP e ligações de entrada/saída para cada processo.
Dados | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome, processo, nome de servidor de máquina de destino, e porta. | Nome do servidor da máquina de origem, processo, nome da aplicação.<br/><br/> Nome, processo, nome de servidor de máquina de destino, e porta.<br/><br/> O número de ligações, latência e informações de transferência de dados são recolhidos e disponíveis para consultas de Log Analytics. 
Visualização | O mapa de dependência de um único servidor pode ser visto durante uma hora a 30 dias. | Mapa de dependência de um único servidor.<br/><br/> O mapa só pode ser visto ao longo de uma hora.<br/><br/> Mapa de dependência de um grupo de servidores.<br/><br/> Adicione e remova os servidores num grupo a partir da vista do mapa.
Exportação de dados | Atualmente não pode ser descarregado em formato tabular. | Os dados podem ser consultados com log Analytics.



## <a name="next-steps"></a>Passos seguintes
- Reveja os requisitos para a criação de análises baseadas em agentes para [VMware VMs,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements) [servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e [VMs Hiper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- [Reveja](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os requisitos para a análise sem agente dos VMware VMs.
- [Configurar](how-to-create-group-machine-dependencies.md) a visualização da dependência baseada em agentes
- [Experimente](how-to-create-group-machine-dependencies-agentless.md) a visualização de dependência sem agente para VMware VMs.
- Reveja [questões comuns](common-questions-discovery-assessment.md#what-is-dependency-visualization) sobre visualização da dependência.



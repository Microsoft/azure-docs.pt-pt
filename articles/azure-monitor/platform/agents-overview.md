---
title: Visão geral dos agentes de monitorização do Azure Microsoft Docs
description: Este artigo fornece uma visão detalhada dos agentes Azure disponíveis que suportam a monitorização de máquinas virtuais hospedadas em ambiente Azure ou híbrido.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/17/2020
ms.openlocfilehash: 2a23f7bad45a45d629a45da82b09458f79121d1f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679571"
---
# <a name="overview-of-azure-monitor-agents"></a>Visão geral dos agentes do Monitor Azure

Máquinas virtuais e outros recursos de computação requerem que um agente recolha dados de monitorização necessários para medir o desempenho e disponibilidade do seu sistema operativo e cargas de trabalho. Este artigo descreve os agentes utilizados pelo Azure Monitor e ajuda-o a determinar quais os requisitos necessários para o seu ambiente específico.

> [!NOTE]
> A Azure Monitor tem atualmente vários agentes devido à recente consolidação do Azure Monitor e do Log Analytics. Embora possa haver sobreposição nas suas características, cada uma tem capacidades únicas. Dependendo dos seus requisitos, poderá precisar de um ou mais agentes nas suas máquinas virtuais. 

Você pode ter um conjunto específico de requisitos que não podem ser completamente cumpridos com um único agente para uma determinada máquina virtual. Por exemplo, pode querer utilizar alertas métricos que requerem extensão de diagnóstico Azure, mas também quer aproveitar a funcionalidade do Azure Monitor para VMs que requer o agente Log Analytics e o agente Dependency. Em casos como este, você pode usar vários agentes, e este é um cenário comum para clientes que requerem funcionalidade de cada um.

## <a name="summary-of-agents"></a>Resumo dos agentes

As tabelas seguintes proporcionam uma comparação rápida dos agentes do Monitor Azure para Windows e Linux. São fornecidos mais pormenores sobre cada um deles na secção abaixo.

> [!NOTE]
> O agente Azure Monitor está atualmente em pré-visualização com capacidades limitadas. Esta tabela será atualizada 

### <a name="windows-agents"></a>Agentes do Windows

| | Agente do Monitor Azure (pré-visualização) | Diagnóstico<br>extensão (WAD) | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|
| **Ambientes apoiados** | Azure<br>Outra nuvem (Arco azul)<br>No local (Azure Arc)  | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>No local | 
| **Requisitos do agente**  | Nenhum | Nenhum | Nenhum | Requer agente log analytics |
| **Dados recolhidos** | Registos de Eventos<br>Desempenho | Registos de Eventos<br>Eventos da ETW<br>Desempenho<br>Registos baseados em ficheiros<br>Registos do IIS<br>Registos de aplicações .NET<br>Informações de falha de sistema<br>Registos de diagnóstico de agentes | Registos de Eventos<br>Desempenho<br>Registos baseados em ficheiros<br>Registos do IIS<br>Insights e soluções<br>Outros serviços | Dependências de processos<br>Métricas de ligação de rede |
| **Dados enviados para** | Registos do Azure Monitor<br>Métricas do Azure Monitor | Storage do Azure<br>Métricas do Azure Monitor<br>Hub de Eventos | Registos do Azure Monitor | Registos do Azure Monitor<br>(através do agente Log Analytics) |
| **Serviços e**<br>**características**<br>**apoiado** | Log Analytics<br>Explorador de Métricas | Explorador de Métricas | Azure Monitor para VMs<br>Log Analytics<br>Automatização do Azure<br>Centro de Segurança do Azure<br>Azure Sentinel | Azure Monitor para VMs<br>Mapa de Serviços |

### <a name="linux-agents"></a>Agentes do Linux

| | Agente do Monitor Azure (pré-visualização) | Diagnóstico<br>extensão (LAD) | Telegrafa<br>agente | Log Analytics<br>agente | Dependência<br>agente |
|:---|:---|:---|:---|:---|:---|
| **Ambientes apoiados** | Azure<br>Outra nuvem (Arco azul)<br>No local (Arc Arc) | Azure | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>Local | Azure<br>Outra nuvem<br>No local |
| **Requisitos do agente**  | Nenhum | Nenhum | Nenhum | Nenhum | Requer agente log analytics |
| **Dados recolhidos** | Syslog<br>Desempenho | Syslog<br>Desempenho | Desempenho | Syslog<br>Desempenho| Dependências de processos<br>Métricas de ligação de rede |
| **Dados enviados para** | Registos do Azure Monitor<br>Métricas do Azure Monitor | Storage do Azure<br>Hub de Eventos | Métricas do Azure Monitor | Registos do Azure Monitor | Registos do Azure Monitor<br>(através do agente Log Analytics) |
| **Serviços e**<br>**características**<br>**apoiado** | Log Analytics<br>Explorador de Métricas | | Explorador de Métricas | Azure Monitor para VMs<br>Log Analytics<br>Automatização do Azure<br>Centro de Segurança do Azure<br>Azure Sentinel | Azure Monitor para VMs<br>Mapa de Serviços |


## <a name="azure-monitor-agent-preview"></a>Agente do Monitor Azure (pré-visualização)
O [agente Azure Monitor](azure-monitor-agent-overview.md) está atualmente em pré-visualização e substituirá o agente Log Analytics e o agente Telegraf para máquinas virtuais Windows e Linux. Pode enviar dados tanto para os Registos do Monitor Azure como para as Métricas do Monitor Azure e utiliza as Regras de Recolha de [Dados (DCR)](data-collection-rule-overview.md) que fornecem um método mais escalável de configurar a recolha de dados e destinos para cada agente.

Utilize o agente Azure Monitor se for necessário:

- Colete registos e métricas de hóspedes de qualquer máquina virtual em Azure, em outras nuvens ou no local. (Arco azul necessário para máquinas virtuais fora de Azure.) 
- Envie dados para Azure Monitor Logs e Azure Monitor Metrics para análise com o Azure Monitor. 
- Envie dados para o Azure Storage para arquivar.
- Enviar dados para ferramentas de terceiros utilizando [hubs de eventos Azure](diagnostics-extension-stream-event-hubs.md).
- Gerencie a segurança das suas máquinas virtuais utilizando o [Azure Security Center](../../security-center/security-center-introduction.md)  ou [o Azure Sentinel](../../sentinel/overview.md). (Não disponível em pré-visualização.)

As limitações do agente Azure Monitor incluem:

- Atualmente em pré-visualização pública. Consulte [as limitações atuais](azure-monitor-agent-overview.md#current-limitations) para uma lista de limitações durante a pré-visualização pública.

## <a name="log-analytics-agent"></a>Agente do Log Analytics

O [agente Do Log Analytics](log-analytics-agent.md) recolhe dados de monitorização do sistema operativo dos hóspedes e cargas de trabalho de máquinas virtuais em Azure, outros fornecedores de nuvem e no local. Envia dados para um espaço de trabalho log analytics. O agente Log Analytics é o mesmo agente utilizado pelo Gestor de Operações do System Center, e pode comunicar com o seu grupo de gestão e o Azure Monitor simultaneamente. Este agente também é exigido por determinadas informações no Azure Monitor e outros serviços em Azure.


> [!NOTE]
> O agente Log Analytics para windows é frequentemente referido como Agente de Monitorização da Microsoft (MMA). O agente Log Analytics para Linux é frequentemente referido como agente OMS.



Utilize o agente Log Analytics se for necessário:

* Recolher registos e dados de desempenho de máquinas virtuais ou físicas dentro ou fora de Azure. 
* Envie dados para um espaço de trabalho log analytics para tirar partido das funcionalidades suportadas por [Registos do Monitor Azure,](data-platform-logs.md) tais como [consultas de registo .](../log-query/log-query-overview.md)
* Utilize [o Azure Monitor para VMs](../insights/vminsights-overview.md) que lhe permite monitorizar as suas máquinas virtuais em escala e monitorizar os seus processos e dependências de outros recursos e processos externos..  
* Gerencie a segurança das suas máquinas virtuais utilizando o [Azure Security Center](../../security-center/security-center-introduction.md)  ou [o Azure Sentinel](../../sentinel/overview.md).
* Utilize [a gestão de Azure Automation Update](../../automation/update-management/overview.md), [Azure Automation State Configuration](../../automation/automation-dsc-overview.md), ou [Azure Automation Change Tracking and Inventory](../../automation/change-tracking/overview.md) para fornecer uma gestão abrangente dos seus VMs Azure
* Utilize [diferentes soluções](../monitor-reference.md#insights-and-core-solutions) para monitorizar um determinado serviço ou aplicação.

As limitações do agente Log Analytics incluem:

- Não é possível enviar dados para Azure Monitor Metrics, Azure Storage ou Azure Event Hubs.
- Difícil configurar definições únicas de monitorização para agentes individuais.
- Difícil de gerir em escala uma vez que cada máquina virtual tem uma configuração única.

## <a name="azure-diagnostics-extension"></a>Extensão de diagnóstico do Azure

A [extensão Azure Diagnostics](diagnostics-extension-overview.md) recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos computatórios. Recolhe principalmente dados no Azure Storage, mas também permite definir sumidouros de dados para também enviar dados para outros destinos, como Azure Monitor Metrics e Azure Event Hubs.

Utilize a extensão de diagnóstico Azure se for necessário:

- Envie dados para o Azure Storage para arquivar ou analisá-los com ferramentas como [o Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Envie dados para [a Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para tirar partido de funcionalidades como [alertas métricos próximos](./alerts-metric-overview.md) em tempo real e [autoescalação](autoscale-overview.md) (apenas Windows).
- Enviar dados para ferramentas de terceiros utilizando [hubs de eventos Azure](diagnostics-extension-stream-event-hubs.md).
- Colete [o Boot Diagnostics](../../virtual-machines/troubleshooting/boot-diagnostics.md) para investigar problemas de arranque VM.

As limitações da extensão de diagnósticos Azure incluem:

- Só pode ser usado com recursos Azure.
- Capacidade limitada de enviar dados para registos do Monitor Azure.

## <a name="telegraf-agent"></a>Agente telegraf

O [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) é utilizado para recolher dados de desempenho dos computadores Linux para as Métricas do Monitor Azure.

Utilize o agente Telegraf se for necessário:

* Envie dados para [a Azure Monitor Metrics](data-platform-metrics.md) para analisá-lo com o explorador de [métricas](metrics-getting-started.md) e para tirar partido de funcionalidades como [alertas métricos próximos](./alerts-metric-overview.md) em tempo real e [autoescalação](autoscale-overview.md) (apenas Linux). 



## <a name="dependency-agent"></a>Agente de Dependência

O agente de Dependência recolhe dados descobertos sobre processos em execução na máquina virtual e dependências de processos externos. 

Utilize o agente de dependência se for necessário:

* Utilize o Monitor Azure do Mapa [para VMs](../insights/vminsights-overview.md) ou a solução [de Mapa de Serviço.](../insights/service-map.md)

Considere o seguinte ao utilizar o agente Desadependição:

- O agente Desadependido requer que o agente Log Analytics seja instalado na mesma máquina virtual.
- Nos VMs Linux, o agente Log Analytics deve ser instalado antes da extensão de diagnóstico Azure.

## <a name="virtual-machine-extensions"></a>Extensões de máquinas virtuais

A extensão Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md) instala o agente Log Analytics em máquinas virtuais Azure. A extensão de dependência do Monitor Azure para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) instala o agente Dependency em máquinas virtuais Azure. Estes são os mesmos agentes descritos acima, mas permitem-lhe geri-los através [de extensões de máquinas virtuais.](../../virtual-machines/extensions/overview.md) Deve utilizar extensões para instalar e gerir os agentes sempre que possível.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As tabelas a seguir enumeram os sistemas operativos que são suportados pelos agentes do Azure Monitor. Consulte a documentação de cada agente para obter considerações únicas e para o processo de instalação. Consulte a documentação da Telegraf para os seus sistemas operativos suportados. Todos os sistemas operativos são assumidos como x64. x86 não é suportado para qualquer sistema operativo.

### <a name="windows"></a>Windows

| Sistema de operações | Agente do Azure Monitor | Agente do Log Analytics | Agente de Dependência | Extensão de diagnóstico | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Núcleo do Windows Server 2016                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(incluindo várias sessões) e Pro<br>(Apenas cenários de servidor)  | X | X | X | X |
| Windows 8 Enterprise e Pro<br>(Apenas cenários de servidor)  |   | X | X |   |
| Windows 7 SP1<br>(Apenas cenários de servidor)                 |   | X | X |   |


### <a name="linux"></a>Linux

| Sistema de operações | Agente do Azure Monitor | Agente do Log Analytics | Agente de Dependência | Extensão de diagnóstico | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X | X |   |
| CentOS Linux 7                                           | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5+                                        |   | X | X | X |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1+                                           |   |   |   | X |
| Oráculo Linux 8                                           |   | X |   |   |
| Oráculo Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4+                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X | X |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7+                     |   | X | X | X |
| SUSE Linux Enterprise Server 15.1                        |   | X |   |   |
| SUSE Linux Enterprise Server 15                          | X | X | X |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20.04 LTS                                         |   | X | X |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14.04 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Suporte de kernel do agente de dependência Linux
Uma vez que o agente de dependência trabalha ao nível do núcleo, o suporte também depende da versão kernel. A tabela que se segue lista a versão principal e menor do Linux OS e as versões de kernel suportadas para o agente Dependency.

| Distribuição | Versão do SO | Versão de kernel |
|:---|:---|:---|
|  Chapéu Vermelho Linux 8   | 8.2     | 4.18.0-193. \* el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147. \* el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80. \* el8_0.x86_64 |
|  Red Hat Linux 7   | 7,9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193. \* el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147. \* el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80. \* el8_0.x86_64 |
| CentOS Linux 7     | 7,9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20.04   | 5.4\* |
|                    | 18.04   | 5.3.0-1020<br>5.0 (inclui núcleo afinado aZure)<br>4.18 *<br> 4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| Servidor empresarial SUSE Linux 12 | 15     | 4.12.14-150\*
|                                 | 12 SP4 | 4.12.* (inclui núcleo afinado aZure) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4,9  | 


## <a name="next-steps"></a>Passos seguintes

Obtenha mais detalhes sobre cada um dos agentes no seguinte:

- [Visão geral do agente Log Analytics](log-analytics-agent.md)
- [Descrição geral da extensão do Diagnóstico do Azure](diagnostics-extension-overview.md)
- [Colete métricas personalizadas para um Linux VM com o agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)

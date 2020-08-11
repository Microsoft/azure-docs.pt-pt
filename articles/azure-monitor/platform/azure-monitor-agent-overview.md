---
title: Visão geral do agente do Azure Monitor
description: Visão geral do agente Azure Monitor (AMA), que recolhe dados de monitorização do sistema operativo convidado de máquinas virtuais.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: e38d59ff1eb31dd5fc3ecf6b7df6b12504141d5e
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083174"
---
# <a name="azure-monitor-agent-overview-preview"></a>Visão geral do agente do Azure Monitor (pré-visualização)
O agente Azure Monitor (AMA) recolhe dados de monitorização do sistema operativo convidado de máquinas virtuais e entrega-os ao Azure Monitor. Estes artigos fornecem uma visão geral do agente Azure Monitor, incluindo como instalá-lo e como configurar a recolha de dados.

## <a name="relationship-to-other-agents"></a>Relação com outros agentes
O Agente monitor Azure substitui os seguintes agentes que são atualmente utilizados pelo Azure Monitor para recolher dados dos hóspedes de máquinas virtuais:

- [Log Analytics agent](log-analytics-agent.md) - Envia dados para log analytics espaço de trabalho e suporta o Azure Monitor para VMs e soluções de monitorização.
- [Extensão de diagnóstico](diagnostics-extension-overview.md) - Envia dados para Azure Monitor Metrics (apenas Windows), Azure Event Hubs e Azure Storage.
- [Agente telegraf -](collect-custom-metrics-linux-telegraf.md) Envia dados para Azure Monitor Metrics (apenas Linux).

Além de consolidar esta funcionalidade num único agente, o Agente monitor Azure proporciona os seguintes benefícios sobre os agentes existentes:

- Âmbito de monitorização. Configurar centralmente a recolha para diferentes conjuntos de dados de diferentes conjuntos de VMs.  
- Linux multi-homing: Enviar dados de VMs Linux para vários espaços de trabalho.
- Filtragem de eventos do Windows: Utilize consultas XPATH para filtrar quais os eventos do Windows que são recolhidos.
- Melhor gestão da extensão: O agente Azure Monitor utiliza um novo método de tratamento da extensibilidade mais transparente e controlável do que os pacotes de gestão e plug-ins Linux nos atuais agentes do Log Analytics.

### <a name="changes-in-data-collection"></a>Alterações na recolha de dados
Os métodos para definir a recolha de dados para os agentes existentes são distintamente diferentes uns dos outros, e cada um tem desafios que são abordados com o agente Azure Monitor.

- O agente Log Analytics obtém a sua configuração a partir de um espaço de trabalho Log Analytics. Isto é fácil de configurar centralmente, mas difícil de definir definições independentes para diferentes máquinas virtuais. Só pode enviar dados para um espaço de trabalho do Log Analytics.

- A extensão de diagnóstico tem uma configuração para cada máquina virtual. Isto é fácil de definir definições independentes para diferentes máquinas virtuais, mas difíceis de gerir centralmente. Só pode enviar dados para Azure Monitor Metrics, Azure Event Hubs ou Azure Storage. Para os agentes Linux, o agente Telegraf de código aberto é obrigado a enviar dados para a Azure Monitor Metrics.

O agente Azure Monitor utiliza [as Regras de Recolha de Dados (DCR)](data-collection-rule-overview.md) para configurar dados para recolher de cada agente. As regras de recolha de dados permitem a gestão das configurações de recolha à escala, permitindo configurações únicas e com âmbito para subconjuntos de máquinas. São independentes do espaço de trabalho e independentes da máquina virtual, o que lhes permite ser definidos uma vez e reutilizados através de máquinas e ambientes. Consulte [a recolha de dados configurar para o agente Azure Monitor (pré-visualização)](data-collection-rule-azure-monitor-agent.md).



## <a name="current-limitations"></a>Limitações atuais
As seguintes limitações aplicam-se durante a visualização pública do Agente monitor Azure:

- O agente Azure Monitor não suporta soluções e insights como o Azure Monitor para VMs e o Azure Security Center. O único cenário atualmente suportado é a recolha de dados utilizando as regras de recolha de dados que configura. 
- As regras de recolha de dados devem ser criadas na mesma região que qualquer espaço de trabalho Log Analytics utilizado como destino.
- Apenas máquinas virtuais Azure são suportadas atualmente. Máquinas virtuais no local, conjuntos de escala de máquinas virtuais, Arco para Servidores, Serviço Azure Kubernetes e outros tipos de recursos computativos não são suportados atualmente.
- A máquina virtual deve ter acesso aos seguintes pontos finais HTTPS:
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Coexistência com outros agentes
O agente Azure Monitor pode coexistir com os agentes existentes para que possa continuar a utilizar a sua funcionalidade existente durante a avaliação ou migração. Isto é particularmente importante devido às limitações da visualização pública no apoio às soluções existentes. Deve ter cuidado na recolha de dados duplicados, uma vez que isso pode distorcer os resultados da consulta e resultar em encargos adicionais para a ingestão e retenção de dados.

Por exemplo, o Azure Monitor para VMs utiliza o agente Log Analytics para enviar dados de desempenho para um espaço de trabalho do Log Analytics. Também pode ter configurado o espaço de trabalho para recolher eventos Windows e eventos Syslog de agentes. Se instalar o agente Azure Monitor e criar uma regra de recolha de dados para estes mesmos eventos e dados de desempenho, resultará em dados duplicados.


## <a name="costs"></a>Custos
Não há custo para o agente Azure Monitor, mas pode incorrer em encargos para os dados ingeridos. Consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter mais detalhes sobre a recolha e retenção de dados do Log Analytics e para as métricas do cliente.

## <a name="data-sources-and-destinations"></a>Fontes de dados e destinos
A tabela que se segue lista os tipos de dados que pode recolher atualmente com o agente Azure Monitor utilizando regras de recolha de dados e para onde pode enviar esses dados. Ver [O que é monitorizado pelo Azure Monitor?](../monitor-reference.md)


O agente Azure Monitor envia dados para as Métricas do Monitor Azure ou para um espaço de trabalho log analytics que suporta registos do Monitor Azure.

| Origem de dados | Destinos | Descrição |
|:---|:---|:---|
| Desempenho        | Métricas do Monitor Azure<br>Área de trabalho do Log Analytics | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Registos do Evento Windows | Área de trabalho do Log Analytics | Informação enviada para o sistema de registo de eventos do Windows. |
| Syslog             | Área de trabalho do Log Analytics | Informação enviada para o sistema de registo de eventos Linux. |


## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Os seguintes sistemas operativos são atualmente suportados pelo agente Azure Monitor.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oráculo Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS

> [!IMPORTANT]
> <sup>1</sup> Para que estas distribuições enviem dados do Syslog, deve remover o rsyslog e instalar o syslog-ng.


## <a name="security"></a>Segurança
O agente Azure Monitor não necessita de chaves, mas requer uma [identidade gerida atribuída pelo sistema.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) Deve ter uma identidade gerida atribuída ao sistema ativada em cada máquina virtual antes de implantar o agente.


## <a name="install-the-azure-monitor-agent"></a>Instale o agente Azure Monitor
O Agente monitor Azure é implementado como uma [extensão Azure VM](../../virtual-machines/extensions/overview.md) com os detalhes na tabela seguinte. 

| Propriedade | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Tipo      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 0.9 |

Instale o agente Azure Monitor utilizando qualquer um dos métodos para instalar agentes de máquinas virtuais, incluindo o seguinte utilizando o PowerShell ou o CLI. Em alternativa, pode instalar o agente e configurar a recolha de dados em máquinas virtuais na sua assinatura Azure utilizando o portal com o procedimento descrito na [recolha de dados configure para o agente Azure Monitor (pré-visualização)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --version 1.0 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -Version 1.0 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --version 0.9 --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -Version 0.9 -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>Passos seguintes

- [Crie uma regra de recolha](data-collection-rule-azure-monitor-agent.md) de dados para recolher dados do agente e enviá-lo para o Azure Monitor.

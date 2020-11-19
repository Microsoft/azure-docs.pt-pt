---
title: Visão geral do agente do Azure Monitor
description: Visão geral do agente Azure Monitor (AMA), que recolhe dados de monitorização do sistema operativo convidado de máquinas virtuais.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: 76f541a45c56669d17103f16997f3d036955b773
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919693"
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

## <a name="should-i-switch-to-azure-monitor-agent"></a>Devo mudar para o agente do Azure Monitor?
O agente Azure Monitor coexiste com os [agentes geralmente disponíveis para o Azure Monitor,](agents-overview.md)mas pode considerar a transição dos seus VMs dos agentes atuais durante o período de pré-visualização do agente Azure Monitor. Considere os seguintes fatores ao fazer esta determinação.

- **Requisitos ambientais.** O agente Azure Monitor tem um conjunto mais limitado de sistemas operativos suportados, ambientes e requisitos de networking do que os agentes atuais. O futuro suporte ao ambiente, como novas versões do sistema operativo e tipos de requisitos de rede, será provavelmente fornecido apenas no agente Azure Monitor. Deve avaliar se o seu ambiente é apoiado pelo agente Azure Monitor. Se não, terá de ficar com o agente atual. Se o agente Azure Monitor apoiar o seu ambiente atual, então deve considerar a transição para ele.
- **Tolerância ao risco de pré-visualização pública.** Embora o agente do Azure Monitor tenha sido completamente testado para os cenários atualmente suportados, o agente ainda está em pré-visualização pública. As atualizações de versão e melhorias de funcionalidades ocorrerão com frequência e podem introduzir bugs. Deve avaliar o risco de um erro no agente nos seus VMs que possa impedir a recolha de dados. Se uma lacuna na recolha de dados não vai ter um impacto significativo nos seus serviços, então proceda com o agente Azure Monitor. Se tiver uma baixa tolerância a qualquer instabilidade, então deve ficar com os agentes geralmente disponíveis até que o agente do Azure Monitor atinja este estatuto.
- **Requisitos atuais e novos de funcionalidades.** O agente Azure Monitor introduz várias novas capacidades, como filtragem, scoping e multi-homing, mas ainda não está em paridade com os agentes atuais para outras funcionalidades, como a recolha de registos personalizados e a integração com soluções. A maioria das novas capacidades no Azure Monitor só serão disponibilizadas com o agente Azure Monitor, pelo que com o tempo mais funcionalidade só estará disponível no novo agente. Deve considerar se o agente do Azure Monitor tem as funcionalidades que necessita e se existem algumas funcionalidades que pode dispensar temporariamente sem obter outras funcionalidades importantes no novo agente. Se o agente Azure Monitor tiver todas as capacidades centrais que necessita, considere a transição para ele. Se houver características críticas que necessite, continue com o agente atual até que o agente do Azure Monitor atinja a paridade.
- **Tolerância para refazer o trabalho.** Se estiver a criar um novo ambiente com recursos como scripts de implementação e modelos de embarque, deve considerar se poderá retraê-los quando o agente do Azure Monitor estiver geralmente disponível. Se o esforço para esta remodelação for mínimo, então fique com os agentes atuais por enquanto. Se for preciso muito trabalho, então considere criar o seu novo ambiente com o novo agente. Espera-se que o agente do Azure Monitor fique geralmente disponível e uma data de depreciação publicada para os agentes do Log Analytics em 2021. Os agentes atuais serão apoiados por vários anos assim que a depreciação começar.



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
| Desempenho        | Métricas do Azure Monitor<br>Área de trabalho do Log Analytics | Valores numéricos que medem o desempenho de diferentes aspetos do sistema operativo e cargas de trabalho. |
| Registos do Evento Windows | Área de trabalho do Log Analytics | Informação enviada para o sistema de registo de eventos do Windows. |
| Syslog             | Área de trabalho do Log Analytics | Informação enviada para o sistema de registo de eventos Linux. |


## <a name="supported-operating-systems"></a>Sistemas operativos suportados
Consulte [os sistemas operativos suportados](agents-overview.md#supported-operating-systems) para uma lista das versões do sistema operativo Windows e Linux que são atualmente suportadas pelo agente Azure Monitor.



## <a name="security"></a>Segurança
O agente Azure Monitor não necessita de chaves, mas requer uma [identidade gerida atribuída pelo sistema.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) Deve ter uma identidade gerida atribuída ao sistema ativada em cada máquina virtual antes de implantar o agente.

## <a name="networking"></a>Redes
O agente Azure Monitor suporta tags de serviço Azure (são necessárias tags AzureMonitor e AzureResourceManager) mas ainda não funciona com Azure Monitor Private Link Scopes ou proxies diretos.

## <a name="install-the-azure-monitor-agent"></a>Instale o agente Azure Monitor
O Agente monitor Azure é implementado como uma [extensão Azure VM](../../virtual-machines/extensions/overview.md) com os detalhes na tabela seguinte. 

| Propriedade | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| Tipo      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1,0 | 1.5 |

Instale o agente Azure Monitor utilizando qualquer um dos métodos para instalar agentes de máquinas virtuais, incluindo o seguinte utilizando o PowerShell ou o CLI. Em alternativa, pode instalar o agente e configurar a recolha de dados em máquinas virtuais na sua assinatura Azure utilizando o portal com o procedimento descrito na [recolha de dados configure para o agente Azure Monitor (pré-visualização)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal).

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus -TypeHandlerVersion 1.5
```
---

## <a name="next-steps"></a>Passos seguintes

- [Crie uma regra de recolha](data-collection-rule-azure-monitor-agent.md) de dados para recolher dados do agente e enviá-lo para o Azure Monitor.

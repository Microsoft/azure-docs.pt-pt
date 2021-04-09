---
title: Insights VM de resolução de problemas
description: Instalação de insights VM de resolução de problemas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555654"
---
# <a name="troubleshoot-vm-insights"></a>Insights VM de resolução de problemas
Este artigo fornece informações de resolução de problemas para quando tiver problemas para ativar ou usar insights VM.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Não é possível ativar insights VM numa máquina
Ao embarcar uma máquina virtual Azure a partir do portal Azure, ocorrem os seguintes passos:

- Um espaço de trabalho de Log Analytics predefinido é criado se essa opção for selecionada.
- O agente Log Analytics é instalado em máquinas virtuais Azure utilizando uma extensão VM se o agente já estiver instalado.
- O agente Dependency é instalado em máquinas virtuais Azure utilizando uma extensão, se for determinado que é necessário.
  
Durante o processo de embarque, cada uma destas etapas é verificada e um estado de notificação apresentado no portal. A configuração do espaço de trabalho e da instalação do agente normalmente demora 5 a 10 minutos. Levará mais 5 a 10 minutos para que os dados fiquem disponíveis para visualização no portal.

Se receber uma mensagem de que a máquina virtual precisa de ser a bordo depois de ter realizado o processo de embarque, permita até 30 minutos para que o processo seja concluído. Se o problema persistir, consulte as seguintes secções para possíveis causas.

### <a name="is-the-virtual-machine-running"></a>A máquina virtual está a funcionar?
 Se a máquina virtual estiver desligada durante algum tempo, está desligada atualmente ou só recentemente ligada, então não terá dados para exibir até que os dados cheguem.

### <a name="is-the-operating-system-supported"></a>O sistema operativo está suportado?
Se o sistema operativo não estiver na lista de [sistemas operativos suportados,](vminsights-enable-overview.md#supported-operating-systems) então a extensão deixará de ser instalada e verá esta mensagem de que estamos à espera que os dados cheguem.

### <a name="did-the-extension-install-properly"></a>A extensão foi corretamente instalada?
Se ainda vir uma mensagem de que a máquina virtual precisa de ser a bordo, pode significar que uma ou ambas as extensões não foram instaladas corretamente. Consulte a página **Extensões** da sua máquina virtual no portal Azure para verificar se estão listadas as seguintes extensões.

| Sistema operativo | Agentes | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OmsagentForLinux<br>DependênciaAgentForLinux |

Se não vir as duas extensões do seu sistema operativo na lista de extensões instaladas, terá de ser instalada. Se as extensões forem listadas mas o seu estado não aparecer como *o Provisioning foi bem sucedido,* então a extensão deve ser removida e reinstalada.

### <a name="do-you-have-connectivity-issues"></a>Tem problemas de conectividade?
Para máquinas Windows, pode utilizar a ferramenta  *TestCloudConnectivity* para identificar o problema de conectividade. Esta ferramenta é instalada por padrão com o agente na pasta *%SystemRoot%\Ficheiros de programa\Microsoft Monitoring Agent \Agent*. Executar a ferramenta a partir de um pedido de comando elevado. Devolverá os resultados e realçará onde o teste falha. 

![Ferramenta TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Mais resolução de problemas de agente

Consulte os seguintes artigos para problemas de resolução de problemas com o agente Log Analytics:

- [Como resolver problemas com o agente do Log Analytics para Windows](../agents/agent-windows-troubleshoot.md)
- [How to troubleshoot issues with the Log Analytics agent for Linux](../agents/agent-linux-troubleshoot.md) (Como resolver problemas com o agente do Log Analytics para Linux)

## <a name="performance-view-has-no-data"></a>A vista de desempenho não tem dados
Se os agentes parecerem estar instalados corretamente, mas não ver quaisquer dados na vista Performance, consulte as seguintes secções para possíveis causas.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>O seu espaço de trabalho Log Analytics atingiu o seu limite de dados?
Verifique as [reservas de capacidade e os preços para a ingestão de dados.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>A sua máquina virtual está a enviar dados de registo e desempenho para registos do Monitor Azure?

Abrir Log Analytics a partir de **Registos** no menu Azure Monitor no portal Azure. Execute a seguinte consulta para o seu computador:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Se não vir nenhum dado, pode ter problemas com o seu agente. Consulte a secção acima para obter informações sobre resolução de problemas do agente.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>A máquina virtual não aparece na vista do mapa

### <a name="is-the-dependency-agent-installed"></a>O agente da Dependência está instalado?
 Utilize as informações nas secções acima para determinar se o agente Dependency está instalado e a funcionar corretamente.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Está no nível livre do Log Analytics?
O [nível gratuito do Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) Este é um plano de preços antigo que permite até cinco máquinas de mapa de serviço únicas. Quaisquer máquinas subsequentes não aparecerão no Mapa de Serviços, mesmo que as cinco anteriores já não estejam a enviar dados.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>A sua máquina virtual está a enviar dados de registo e desempenho para registos do Monitor Azure?
A utilização da consulta de registo na vista Performance não tem nenhuma secção [de dados](#performance-view-has-no-data) para determinar se os dados estão a ser recolhidos para a máquina virtual. Se não forem recolhidos dados, utilize a ferramenta TestCloudConnectivity acima descrita para determinar se tem problemas de conectividade.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>Máquina virtual aparece na vista do mapa mas tem dados em falta
Se a máquina virtual estiver na vista do mapa, então o agente Dependency está instalado e a funcionar, mas o condutor do núcleo não carregou. Verifique o ficheiro de registo nos seguintes locais:

| Sistema operativo | Registo | 
|:---|:---|
| Windows | C:\Ficheiros de programa\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

As últimas linhas do ficheiro devem indicar o motivo pelo qual kernel não carregou. Por exemplo, o kernel poderá não ser suportado pelo Linux se tiver atualizado o kernel.
## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre os agentes de insights VM de bordo, consulte [a visão geral dos insights de VM](vminsights-enable-overview.md).

---
title: Solução de problemas de desempenho genérico para máquina virtual do Azure executando Linux ou Windows
description: Este artigo descreve a solução de problemas de desempenho genérico da VM (máquina virtual) por meio do monitoramento e da observação de afunilamentos e fornece uma possível correção para problemas que possam ocorrer.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: fc8cc4834997033203376cd33670cc907e2911e7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170303"
---
# <a name="generic-performance-troubleshooting-for-azure-virtual-machine-running-linux-or-windows"></a>Resolução de problemas genéricos de desempenho da Máquina Virtual do Azure que executa o Linux ou o Windows

Este artigo descreve a solução de problemas de desempenho genérico da VM (máquina virtual) por meio do monitoramento e da observação de afunilamentos e fornece uma possível correção para problemas que possam ocorrer.

## <a name="enabling-monitoring"></a>Habilitando o monitoramento

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitoramento de máquinas virtuais IAAS do Azure

Para monitorar a VM convidada, use o monitoramento de VM do Azure, que irá alertá-lo para determinadas condições de recursos de alto nível. Para verificar se você tem o diagnóstico de VM habilitado, consulte [visão geral dos logs de recursos do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#collecting-resource-logs). Se você vir o seguinte, provavelmente não terá o diagnóstico habilitado:

![O monitoramento não está habilitado](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Habilitar o diagnóstico de VM por meio do Microsoft portal do Azure

Para habilitar o diagnóstico de VM, acesse a VM, clique em **configurações**e em **diagnóstico**.

![Clique em configurações e em diagnóstico](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)
 
### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Habilitar o diagnóstico da conta de armazenamento por meio do portal do Azure

Primeiro, identifique qual conta de armazenamento (ou contas) sua VM está usando selecionando a VM. Clique em **configurações**e em **discos**:

![Clique em configurações e em discos](media/troubleshoot-performance-virtual-machine-linux-windows/3-storage-disks-disks-selection.png)

No portal, vá para a conta de armazenamento (ou contas) para a VM e trabalhe com as seguintes etapas:

![Selecionar métricas de BLOB](media/troubleshoot-performance-virtual-machine-linux-windows/4-select-blob-metrics.png)
 
1. Selecione **todas as configurações**.
2. Ative o diagnóstico.
3. Selecione *métricas de *blob** * e defina a retenção para **30** dias.
4. Guarde as alterações.

## <a name="observing-bottlenecks"></a>Observando afunilamentos

### <a name="accessing-the-monitoring"></a>Acessando o monitoramento

Selecione a VM do Azure que você deseja investigar e selecione **monitoramento**.

![Selecionar monitoramento](media/troubleshoot-performance-virtual-machine-linux-windows/5-observe-monitoring.png)
 
### <a name="timelines-of-observation"></a>Cronogramas de observação

Para identificar se há gargalos de recursos, examine os dados. Se você descobrir que seu computador está funcionando bem, mas foi informado que o desempenho foi degradado recentemente, examine um intervalo de tempo de dados que abrange os dados de métrica de desempenho antes que o relatório seja alterado, durante e após o problema.

### <a name="check-for-cpu-bottleneck"></a>Verificar afunilamento de CPU

![Verificar afunilamento de CPU](media/troubleshoot-performance-virtual-machine-linux-windows/6-cpu-bottleneck-time-range.png)

1. Edite o grafo.
2. Defina o intervalo de tempo.
3. Em seguida, você precisa adicionar no contador: sistema operacional convidado do percentual de CPU
4. Guarde.

### <a name="cpu-observe-trends"></a>Tendências de observar a CPU

Ao examinar problemas de desempenho, esteja atento às tendências e entenda se elas afetam você. Nas próximas seções, usaremos os gráficos de monitoramento do portal para mostrar as tendências. Eles também podem ser úteis para comportamentos de recursos de diferença de referência cruzada no mesmo período de tempo. Para personalizar os grafos, clique em [Azure monitor plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Travado – travado pode estar relacionado a uma tarefa agendada/evento conhecido. Se você puder identificar a tarefa, determine se a tarefa é executada no nível de desempenho necessário. Se o desempenho for aceitável, talvez não seja necessário aumentar os recursos.

Pico e constante – geralmente indica uma nova carga de trabalho. Se não for uma carga de trabalho reconhecida, habilite o monitoramento na VM para descobrir qual processo (ou processos) causa o comportamento. Depois que o processo for reconhecido, determine se o aumento de consumo está sendo causado por código ineficiente ou consumo normal. Se o consumo normal, decida se o processo opera no nível de desempenho necessário.

Constante – determine se a VM sempre foi executada nesse nível ou se ela só está sendo executada nesse nível desde que o diagnóstico foi habilitado. Nesse caso, identifique o processo (ou processos) que está causando o problema e considere adicionar mais desse recurso.

Aumentando constantemente – um aumento constante no consumo geralmente é um código ineficiente ou um processo que assume mais carga de trabalho do usuário.

### <a name="high-cpu-utilization-remediation"></a>Alta correção de utilização da CPU

Se seu aplicativo ou processo não estiver em execução no nível de desempenho correto e você estiver vendo a constante de utilização de 95% + CPU, poderá executar uma das seguintes tarefas:

* Para a relevo imediata-aumente o tamanho da VM para um tamanho com mais núcleos
* Entenda o problema – localize o aplicativo/processo e solucione-o de acordo.

Se você tiver aumentado a VM e a CPU ainda estiver executando 95%, determine se essa configuração está oferecendo melhor desempenho ou maior taxa de transferência de aplicativo para um nível aceitável. Caso contrário, solucione o problema dessa application\process. individual

## <a name="check-for-memory-bottleneck"></a>Verificar afunilamento de memória

Para exibir as métricas:

1. Adicione uma seção.
2. Adicione um bloco.
3. Abra a galeria.
4. Selecione o uso de memória e arraste. Quando o bloco estiver encaixado, clique com o botão direito do mouse e selecione **6x4**.

### <a name="memory-observe-trends"></a>Tendências de observar memória

O uso de memória mostra a quantidade de memória que está sendo consumida com a VM. Entenda a tendência e se ela é mapeada para o momento em que você vê problemas. Você deve ter sempre mais de 100 MB de memória disponível.

Consumo estável e constante/constante-a utilização de memória alta pode não ser a causa do mau desempenho, já que alguns aplicativos, como os mecanismos de banco de dados relacional, alocam uma grande quantidade de memória, e essa utilização pode não ser significativa. No entanto, se houver vários aplicativos que exigem muita memória, você poderá ver um baixo desempenho da contenção de memória, causando corte e paginação/troca de disco. Esse baixo desempenho geralmente é uma causa perceptível do impacto no desempenho do aplicativo.

Consumo cada vez maior – um possível "aquecimento" do aplicativo, esse consumo é comum entre os mecanismos de banco de dados iniciando. No entanto, ele também pode ser um sinal de perda de memória em um aplicativo. Identifique o aplicativo e entenda se o comportamento é esperado.

Uso de página ou troca de arquivo – Verifique se você está usando o arquivo de paginação do Windows (localizado em D: \) ou o arquivo de permuta do Linux (localizado em `/dev/sdb`) estão sendo usados intensamente. Se você não tiver nada nesses volumes, exceto nesses arquivos, verifique se há altas leituras/gravações nesses discos. Esse problema é um indicativo de condições de memória insuficiente.

### <a name="high-memory-utilization-remediation"></a>Correção de utilização de memória alta

Para resolver a alta utilização de memória, execute qualquer uma das seguintes tarefas:

* Para reutilização imediata ou página ou uso de arquivo de permuta, aumente o tamanho da VM para uma com mais memória e, em seguida, monitore.
* Entender o problema – Localize aplicativos/processos e solucione problemas para identificar aplicativos de memória de alto consumo.
* Se você souber o aplicativo, veja se a alocação de memória pode ser limitada.

Se, após a atualização para uma VM maior, você descobrir que ainda tem um aumento constante entre 100%, identifique o aplicativo/processo e solucione problemas.

## <a name="check-for-disk-bottleneck"></a>Verificar afunilamento de disco

Para verificar o subsistema de armazenamento para a VM, verifique o diagnóstico no nível de VM do Azure usando os contadores em diagnóstico de VM e também o diagnóstico da conta de armazenamento.

Observe que não temos contadores para contas de armazenamento com redundância de zona e Premium. Para problemas relacionados a esses contadores, gere um caso de suporte.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Exibindo o diagnóstico da conta de armazenamento no monitoramento

Para trabalhar nos itens abaixo, vá para a conta de armazenamento da VM no Portal:

![Exibindo o diagnóstico da conta de armazenamento no monitoramento](media/troubleshoot-performance-virtual-machine-linux-windows/7-virtual-machine-storage-account.png)

1. Edite o grafo de monitoramento.
2. Defina o intervalo de tempo.
3. Adicione os contadores descritos nas etapas abaixo.
4. Guarde as alterações.

### <a name="disk-observe-trends-standard-storage-only"></a>O disco observa tendências (somente armazenamento Standard)

Para identificar problemas de armazenamento, examine as métricas de desempenho do diagnóstico da conta de armazenamento e do diagnóstico da VM.

Para cada verificação abaixo, procure tendências-chave quando os problemas ocorrem dentro do intervalo de tempo do problema.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Verificar a disponibilidade do armazenamento do Azure – adicionar a métrica da conta de armazenamento: disponibilidade

Se você vir uma queda na disponibilidade, pode haver um problema com a plataforma, verifique o [status do Azure](https://azure.microsoft.com/status/). Se nenhum problema for mostrado lá, gere uma nova solicitação de suporte.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Verificar o tempo limite do armazenamento do Azure-adicione as métricas da conta de armazenamento:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Os valores nas métricas * TimeOutError indicam que uma operação de e/s demorou muito e atingiu o tempo limite. O trabalho nas próximas etapas ajudará a identificar possíveis causas.

O AverageServerLatency aumenta ao mesmo tempo em que o TimeOutErrors pode ser um problema de plataforma. Gere uma nova solicitação de suporte nessa situação.

AverageE2ELatency representa a latência do cliente. Verifique como o IOPS está sendo executado pelo aplicativo. Procure uma métrica de TotalRequests de aumento ou constante alta. Essa métrica representa IOPS. Se você estiver começando a atingir os limites da conta de armazenamento ou do VHD único, a latência poderá estar relacionada à limitação.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Verificar a limitação do armazenamento do Azure – adicione as métricas da conta de armazenamento: ThrottlingError

Os valores para limitação indicam que você está sendo limitado no nível da conta de armazenamento, o que significa que você está atingindo o limite de IOPS da conta. Você pode determinar se está atingindo o limite de IOPs verificando a métrica **TotalRequests**.

Observe que cada VHD tem um limite de 500 IOPS ou 60 MBits, mas é associado pelo limite cumulativo de 20000 IOPS por conta de armazenamento.

Com essa métrica, você não pode saber qual blob está causando a limitação e quais são afetados por ela. No entanto, você está atingindo os limites de IOPS ou de entrada/saída da conta de armazenamento.

Para identificar se você está atingindo o limite de IOPS, acesse o diagnóstico da conta de armazenamento e verifique o TotalRequests, procurando ver se você está se aproximando do 20000 TotalRequests. Identifique uma alteração no padrão, independentemente de você estar vendo o limite pela primeira vez ou se esse limite ocorre em um determinado momento.

#### <a name="references"></a>Referências

* [Metas de escalabilidade para discos de máquina virtual](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

A largura de banda da conta de armazenamento é medida pelas métricas da conta de armazenamento: TotalIngress e TotalEgress. Você tem limites diferentes de largura de banda, dependendo do tipo de redundância e de regiões.

* [Metas de escalabilidade para BLOBs, filas, tabelas e arquivos](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

Verifique o TotalIngress e o TotalEgress nos limites de entrada e saída para a região e o tipo de redundância da conta de armazenamento.

Verifique os limites de taxa de transferência dos VHDs anexados à VM. Adicione as métricas de VM leitura e gravação do disco.

Cada VHD pode dar suporte a até 60 MB/s (IOPS não são expostos por VHD). Examine os dados para ver se você está atingindo os limites de taxa de transferência combinada de MB dos VHD no nível de VM usando leitura e gravação de disco e, em seguida, otimize sua configuração de armazenamento de VM para dimensionar os limites de VHD único.

### <a name="high-disk-utilizationlatency-remediation"></a>Alta correção de utilização/latência de disco

Reduzir a latência do cliente e otimizar a e/s de VM para dimensionar os limites de VHD passados

* [Otimizando a e/s para Windows no Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Otimizando a e/s para Linux no Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Reduzir a limitação

Se atingir limites superiores de contas de armazenamento, rebalanceie os VHDs entre as contas de armazenamento. Consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Aumentar a taxa de transferência e reduzir a latência

Se você tiver um aplicativo sensível à latência e exigir alta taxa de transferência, migre seus VHDs para o armazenamento Premium do Azure usando a VM da série DS e GS.

Estes artigos discutem os cenários específicos:

* [Migrar para o Armazenamento Premium do Azure](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Usar o armazenamento Premium do Azure com o SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/).

Como alternativa, arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

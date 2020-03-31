---
title: Performance de máquina virtual De Troubleshoot Azure em Linux ou Windows
description: Este artigo descreve a resolução genérica de problemas de desempenho da máquina virtual (VM) através da monitorização e observação de estrangulamentos e fornece uma possível reparação para questões que possam ocorrer.
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772623"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Performance de máquina virtual De Troubleshoot Azure em Linux ou Windows

Este artigo descreve a resolução genérica de problemas de desempenho da máquina virtual (VM) através da monitorização e observação de estrangulamentos e fornece uma possível reparação para questões que possam ocorrer. Além da monitorização, também pode utilizar perfinsights que podem fornecer um relatório com recomendações de boas práticas e estrangulamentos chave em torno da IO/CPU/Memória. Perfinsights está disponível tanto para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) como [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM's em Azure.

Este artigo passará por uma monitorização para diagnosticar estrangulamentos de desempenho.

## <a name="enabling-monitoring"></a>Habilitar a monitorização

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorização virtual de máquinas Azure IAAS

Para monitorizar o VM de hóspedes, utilize a Monitorização VM Azure, que irá alertá-lo para determinadas condições de recursos de alto nível. Para verificar se tem os diagnósticos VM ativados, consulte a visão geral dos [registos do Recurso Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs). Se vir o seguinte, então provavelmente não tem os diagnósticos ativados:

![A monitorização não está ativada](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Ativar diagnósticos VM através do portal Microsoft Azure

Para ativar diagnósticos VM:

1. Vá ao VM
2. Clique em **Definições de Diagnóstico**
3. Selecione a conta de armazenamento e clique **em ativar a monitorização ao nível do hóspede**.

   ![Clique em Definições, em seguida, Diagnósticos](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Pode verificar a conta de armazenamento utilizada para a configuração de Diagnósticos a partir do separador **Agente** em **definições**de diagnóstico .

![Verifique a conta de armazenamento](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Ativar diagnósticos de conta de armazenamento através do portal Azure

O armazenamento é um nível muito importante quando pretendemos analisar o desempenho da IO para uma Máquina Virtual em Azure. Para métricas relacionadas com o armazenamento, precisamos de permitir o diagnóstico como um passo adicional. Isto também poderia ser ativado, se quisermos apenas analisar os balcões relacionados com o armazenamento.

1. Identifique qual a conta de armazenamento (ou contas) que o seu VM está a utilizar selecionando o VM. Clique em **Definições**e, em seguida, clique em **Discos:**

   ![Clique em Definições, em seguida, discos](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. No portal, vá à conta de armazenamento (ou contas) para o VM e trabalhe através dos seguintes passos:

   1. Clique na visão geral da conta de Armazenamento que encontrou com passo acima.
   2. As métricas padrão seriam mostradas. 

    ![Métricas padrão](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Clique em qualquer uma das métricas, que mostrarão outra lâmina com mais opções para configurar e adicionar métricas.

   ![Adicionar métricas](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Para configurar estas opções:

1.  Selecione **Métricas**.
2.  Selecione o **Recurso** (conta de armazenamento).
3.  Selecione o **espaço de nome**
4.  Selecione **Métrica**.
5.  Selecione o tipo de **agregação**
6.  Pode fixar esta vista no painel de instrumentos.

## <a name="observing-bottlenecks"></a>Observando estrangulamentos

Uma vez que estejamos através do processo inicial de configuração para métricas necessárias, e postpermitindo os diagnósticos para VM e conta de Armazenamento relacionada, podemos passar para a fase de análise.

### <a name="accessing-the-monitoring"></a>Acesso à monitorização

Selecione o Azure VM que pretende investigar e selecionar **monitorização**.

![Selecione Monitorização](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Cronologias de observação

Para identificar se tem algum estrangulamento de recursos, reveja os seus dados. Se descobrir que a sua máquina está a funcionar bem, mas foi noticiado que o desempenho se degradou recentemente, reveja uma gama de dados que engloba dados métricos de desempenho antes da alteração do reportado, durante e após o problema.

### <a name="check-for-cpu-bottleneck"></a>Verifique se o estrangulamento da CPU

![Verifique se cpu bottleneck](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Editar o Gráfico.
2. Detete o intervalo de tempo.
3. Em seguida, você precisa adicionar no balcão: CPU Percentagem De HóspedeS OS
4. Guarde.

### <a name="cpu-observe-trends"></a>CpU observa tendências

Ao olhar para os problemas de desempenho, esteja atento às tendências e compreenda se elas o afetam. Nas próximas secções, usaremos os gráficos de Monitorização do portal para mostrar tendências. Também podem ser úteis para cruzar comportamentos de recursos de diferença no mesmo período de tempo. Para personalizar os gráficos, clique na plataforma de [dados Do Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking – Spiking pode estar relacionado com uma tarefa programada/evento conhecido. Se conseguir identificar a tarefa, determine se a tarefa funciona ao nível de desempenho necessário. Se o desempenho for aceitável, pode não precisar de aumentar os recursos.

Spike up e Constant – Muitas vezes indica uma nova carga de trabalho. Se não for uma carga de trabalho reconhecida, ative a monitorização no VM para descobrir que processo (ou processos) causa o comportamento. Uma vez reconhecido o processo, determine se o aumento do consumo está a ser causado por código ineficiente ou consumo normal. Se o consumo normal, decida se o processo funciona ao nível de desempenho exigido.

Constante – Determine se o seu VM sempre correu a este nível, ou se só está a funcionar a esse nível desde que os diagnósticos foram ativados. Em caso afirmativo, identifique o processo (ou processos) que causem o problema e considere adicionar mais desse recurso.

Aumento constante – Um aumento constante do consumo é muitas vezes um código ineficiente ou um processo que assume mais carga de trabalho do utilizador.

### <a name="high-cpu-utilization-remediation"></a>Remediação elevada da utilização da CPU

Se a sua aplicação ou processo não estiver a funcionar ao nível de desempenho correto, e estiver a ver uma constante de utilização de 95% + CPU, pode executar qualquer uma das seguintes tarefas:

* Para alívio imediato - Aumente o tamanho do VM para um tamanho com mais núcleos
* Compreender o problema – localizar a aplicação/processo e resolver problemas em conformidade.

Se tiver aumentado o VM, e o CPU ainda estiver a funcionar 95%, determine se esta definição está a oferecer um melhor desempenho ou uma maior entrada de aplicação para um nível aceitável. Caso contrário, problemas resolvem essa aplicação individual\processo.

Pode utilizar perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) para analisar que processo está a impulsionar o consumo de CPU. 

## <a name="check-for-memory-bottleneck"></a>Verifique se há estrangulamento de memória

Para ver as métricas:

1. Adicione uma secção.
2. Adicione um azulejo.
3. Abra a Galeria.
4. Selecione o Uso da Memória e arraste. Quando o azulejo estiver ancorado, clique à direita e selecione **6x4**.

### <a name="memory-observe-trends"></a>Memória observar tendências

O Uso da Memória mostra-lhe a quantidade de memória que está a ser consumida com o VM. Compreenda a tendência e se mapeia o momento em que vê problemas. Deve ter sempre mais de 100 MB de memória disponível.

Spike e Constant/Constant Constant Constant Consumption - A utilização elevada da memória pode não ser a causa de um mau desempenho, uma vez que algumas aplicações como os motores de base de dados relacionais alocam uma grande quantidade de memória, e esta utilização pode não ser significativa. No entanto, se existirem múltiplas aplicações com fome de memória, poderá ver um fraco desempenho da contenção da memória, causando aparar e paging/trocar para o disco. Este fraco desempenho é muitas vezes uma causa notável do impacto do desempenho da aplicação.

Aumento constante do consumo – Uma possível aplicação 'aquecimento', este consumo é comum entre os motores de base de dados que estão a iniciar. No entanto, também pode ser um sinal de fuga de memória numa aplicação. Identifique a aplicação e compreenda se o comportamento é esperado.

Utilização de ficheiros de página ou de troca – Verifique se\) está a utilizar o `/dev/sdb`ficheiro Desativação do Windows (localizado em D: ou ficheiro De troca de Linux (localizado) estão a ser fortemente utilizados. Se não tiver nada sobre estes volumes exceto estes ficheiros, verifique se há leitura/escrita saltada nesses discos. Esta questão é indicativa de baixas condições de memória.

### <a name="high-memory-utilization-remediation"></a>Remediação da utilização da memória alta

Para resolver a utilização elevada da memória, execute qualquer uma das seguintes tarefas:

* Para alívio imediato ou Utilização de Ficheiros de Página ou De Troca - Aumente o tamanho do VM para um com mais memória e, em seguida, monitorize.
* Compreender o problema – localizar aplicações/processo e resolução de problemas para identificar aplicações de memória de alta consumista.
* Se conhece a aplicação, veja se a atribuição de memória pode ser limitada.

Se depois de atualizar para um VM maior, descobre que ainda tem um aumento constante e constante até 100%, identificar a aplicação/processo e resolução de problemas.

Pode utilizar Perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) para analisar que processo está a impulsionar o consumo de Memória. 

## <a name="check-for-disk-bottleneck"></a>Verifique se o estrangulamento do disco

Para verificar o subsistema de armazenamento do VM, verifique os diagnósticos ao nível do VM Azure utilizando os contadores em Diagnósticos VM e também os Diagnósticos da Conta de Armazenamento.

Para dentro de uma resolução específica de problemas da VM, pode utilizar Perfinsights para [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), o que pode ajudar a analisar que processo está a impulsionar o IO's. 

Note que não temos balcões para Contas de Armazenamento Da Zona Redundante e Premium. Para questões relacionadas com estes balcões, levante um caso de apoio.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visualização de diagnósticos de contas de armazenamento na monitorização

Para trabalhar nos itens abaixo, entre na conta de armazenamento do VM no portal:

![Visualização de diagnósticos de conta de armazenamento em monitorização](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Editar o Gráfico de Monitorização.
2. Desmarcar o intervalo de tempo.
3. Adicione os contadores descritos nos degraus abaixo.
4. Guarde as alterações.

### <a name="disk-observe-trends-standard-storage-only"></a>O disco observa as tendências (apenas o armazenamento padrão)

Para identificar problemas com o armazenamento, veja as métricas de desempenho dos Diagnósticos da Conta de Armazenamento e dos Diagnósticos vM.

Para cada verificação abaixo, procure tendências-chave quando as questões ocorrem dentro do intervalo de tempo da emissão.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Consulte a disponibilidade de armazenamento do Azure – Adicione a métrica da conta de armazenamento: disponibilidade

Se vir uma diminuição da disponibilidade, pode haver um problema com a plataforma, consulte o [Estado do Azure](https://azure.microsoft.com/status/). Se não for mostrado qualquer problema, levante um novo pedido de apoio.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Verifique se o tempo de armazenamento do Azure - Adicione as métricas da conta de armazenamento:

* Erro timeout do cliente
* Error timeout do servidor
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Os valores nas métricas *TimeOutError indicam que uma operação iO demorou muito tempo e cronometrada. Trabalhar através dos próximos passos ajudará a identificar potenciais causas.

O aumento médio do ServerLatency ao mesmo tempo no TimeOutErrors pode ser um problema de plataforma. Levante um novo pedido de apoio nesta situação.

AverageE2ELatency representa a latência do cliente. Verifique como os IOPS estão a ser realizados pela aplicação. Procure uma métrica de aumento ou totalsolicitações constantemente elevadas. Esta métrica representa o IOPS. Se começar a atingir os limites da conta de armazenamento ou de um único VHD, a latência pode estar relacionada com estrangulamento.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Verifique se o estrangulamento do armazenamento Azure - Adicione as métricas da conta de armazenamento: ThrotlingError

Os valores para a aceleração indicam que está a ser estrangulado ao nível da conta de armazenamento, o que significa que está a atingir o limite iops da conta. Pode determinar se está a atingir o limiar dos IOPs verificando a métrica **TotalRequests**.

Note que cada VHD tem um limite de 500 IOPS ou 60 MBits, mas está vinculado ao limite acumulado de 20000 IOPS por conta de armazenamento.

Com esta métrica, não se pode dizer qual a bolha que está a causar a aceleração e que são afetadas por ela. No entanto, ou está a atingir os limites iops ou ingress/egress da conta de armazenamento.

Para identificar se está a atingir o limite do IOPS, vá aos diagnósticos da Conta de Armazenamento e verifique os TotalRequests, procurando ver se está a aproximar-se de 20 mil TotalRequests. Identifique ou uma mudança no padrão, quer esteja a ver o limite pela primeira vez, ou se este limite acontece a dada altura.

Com novas ofertas de discos no armazenamento Standard, os limites IOPS e De supor podem diferir, mas o limite acumulado da conta de Armazenamento Padrão é 20000 IOPS (O armazenamento premium tem limites diferentes a nível de conta ou de disco). Leia mais sobre diferentes ofertas padrão de discos de armazenamento e por limites de disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

#### <a name="references"></a>Referências

* [Metas de escalabilidade e desempenho para contas de armazenamento de blob de página premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

A largura de banda da conta de armazenamento é medida pelas Métricas da Conta de Armazenamento: TotalIngress e TotalEgress. Tem diferentes limiares para a largura de banda, dependendo do tipo de redundância e regiões.

* [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md)

Verifique os limites TotalIngress e TotalEgress contra os limites de Ingress e Egress para o tipo de redundância da conta de armazenamento e região.

Verifique os limites de entrada dos VHDs ligados ao VM. Adicione o disco de métricas VM Ler e Escrever.

As novas ofertas de discos sob armazenamento Standard têm diferentes iOPS e limites de entrada (os IOPS não são expostos por VHD). Veja os dados para ver se está a atingir os limites de entrada combinada MB do VHD(s) a nível VM usando a Leitura e Escrita do Disco e, em seguida, otimize a configuração de armazenamento VM para escalar limites de VHD únicos. Leia mais sobre diferentes ofertas padrão de discos de armazenamento e por limites de disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets).

### <a name="high-disk-utilizationlatency-remediation"></a>Remediação de utilização/latência de alto disco

Reduzir a latência do cliente e otimizar o VM IO para escalar limites vHD passados

* [Otimização de IO para Windows em Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Otimização de IO para Linux em Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Reduzir a estrangulamento

Se atingir os limites superiores das contas de armazenamento, reequilibre os VHDs entre as contas de armazenamento. Consulte a [escalabilidade de armazenamento azure e alvos de desempenho.](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)

### <a name="increase-throughput-and-reduce-latency"></a>Aumentar a entrada e reduzir a latência

Se tiver uma aplicação sensível à latência e necessitar de alta entrada, emigre os seus VHDs para o armazenamento Azure Premium utilizando o VM da série DS e GS.

Estes artigos discutem os cenários específicos:

* [Migrar para o Armazenamento Premium do Azure](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Utilize o Armazenamento Premium Azure com o Servidor SQL](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em algum ponto deste artigo, contacte os especialistas da Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, apresente um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

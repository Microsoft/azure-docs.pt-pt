---
title: Resolução de problemas Desempenho da máquina virtual Azure em Linux ou Windows
description: Este artigo descreve a resolução genérica de problemas de desempenho da máquina virtual (VM) através da monitorização e observação de estrangulamentos e fornece uma possível reparação para problemas que possam ocorrer.
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
ms.openlocfilehash: cde84a018d307bada6a6b6f3b5467b8addd007b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571438"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Resolução de problemas Desempenho da máquina virtual Azure em Linux ou Windows

Este artigo descreve a resolução genérica de problemas de desempenho da máquina virtual (VM) através da monitorização e observação de estrangulamentos e fornece uma possível reparação para problemas que possam ocorrer. Além da monitorização, também pode utilizar Perfinsights que pode fornecer um relatório com recomendações de boas práticas e estrangulamentos chave em torno de IO/CPU/Memória. Perfinsights está disponível tanto para [Windows](./how-to-use-perfinsights.md) como [Linux](./how-to-use-perfinsights-linux.md) VM's em Azure.

Este artigo passará pela monitorização para diagnosticar estrangulamentos de desempenho.

## <a name="enabling-monitoring"></a>Habilitação de monitorização

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitorização da máquina virtual Azure IAAS

Para monitorizar o VM do hóspede, utilize o Monitor Azure VM, que irá alertá-lo para determinadas condições de recursos de alto nível. Para verificar se tem os diagnósticos VM ativados, consulte [a visão geral dos registos de recursos do Azure](../../azure-monitor/essentials/tutorial-resource-logs.md). Se vir o seguinte, então provavelmente não tem os diagnósticos ativados:

![A monitorização não está ativada](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Ativar diagnósticos VM através do portal Microsoft Azure

Para ativar diagnósticos em VM:

1. Ir ao VM
2. Clique **em Definições de Diagnóstico**
3. Selecione a conta de armazenamento e clique **em Ativar a monitorização ao nível do hóspede.**

   ![Clique em Definições e, em seguida, Diagnósticos](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Pode verificar a conta de armazenamento utilizada para a configuração de Diagnóstico a partir do **separador agente** em **Definições de Diagnóstico**.

![Ver conta de armazenamento](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Ativar diagnósticos de conta de armazenamento através do portal Azure

O armazenamento é um nível muito importante quando pretendemos analisar o desempenho de IO para uma Máquina Virtual em Azure. Para métricas relacionadas com o armazenamento precisamos de permitir o diagnóstico como um passo adicional. Isto também poderia ser ativado, se apenas quisermos analisar os balcões relacionados com o armazenamento.

1. Identifique qual a conta de armazenamento (ou contas) que o seu VM está a utilizar selecionando o VM. Clique em **Definições** e, em seguida, clique em **Discos**:

   ![Clique em Definições e, em seguida, Discos](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. No portal, aceda à conta de armazenamento (ou contas) para o VM e trabalhe através dos seguintes passos:

   1. Clique na visão geral da conta de Armazenamento encontrada com o passo acima.
   2. As métricas predefinidos seriam mostradas. 

    ![Métricas padrão](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Clique em qualquer uma das métricas, que mostrará outra lâmina com mais opções para configurar e adicionar métricas.

   ![Adicionar métricas](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Para configurar estas opções:

1.  Selecione **Métricas**.
2.  Selecione o **Recurso** (conta de armazenamento).
3.  Selecione o **Espaço Nome**
4.  Selecione **métrica**.
5.  Selecione o tipo **de Agregação**
6.  Pode colocar esta vista no painel de instrumentos.

## <a name="observing-bottlenecks"></a>Observar estrangulamentos

Uma vez que estamos através do processo inicial de configuração para as métricas necessárias, e postando permitindo os diagnósticos para VM e conta de Armazenamento relacionado, podemos passar para fase de análise.

### <a name="accessing-the-monitoring"></a>Acesso à monitorização

Selecione o Azure VM que pretende investigar e selecione **Monitoring**.

![Selecione Monitorização](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Cronologias da observação

Para identificar se tem algum estrangulamento de recursos, reveja os seus dados. Se descobrir que a sua máquina está a correr bem, mas foi noticiado que o desempenho se degradou recentemente, reveja um intervalo de dados que engloba dados da métrica de desempenho antes da alteração reportada, durante e após o problema.

### <a name="check-for-cpu-bottleneck"></a>Verifique se o estrangulamento do CPU

![Verifique se o Gargalo-de-CPU](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Editar o Gráfico.
2. Desa parte do intervalo de tempo.
3. Em seguida, você precisa adicionar no balcão: CPU Percentage Guest OS
4. Guarde.

### <a name="cpu-observe-trends"></a>CPU observa tendências

Ao olhar para as questões de desempenho, esteja atento às tendências e compreenda se elas o afetam. Nas próximas secções, usaremos os gráficos de monitorização do portal para mostrar tendências. Também podem ser úteis para cruzar comportamentos de recursos de diferença no mesmo período de tempo. Para personalizar os gráficos, clique na [plataforma de dados do Azure Monitor](../../azure-monitor/data-platform.md).

Spiking – Spiking pode estar relacionado com uma tarefa/evento conhecido agendado. Se conseguir identificar a tarefa, determine se a tarefa é executado ao nível de desempenho exigido. Se o desempenho for aceitável, pode não precisar de aumentar recursos.

Aumentar e Constante – Muitas vezes indica uma nova carga de trabalho. Se não for uma carga de trabalho reconhecida, permita a monitorização no VM para descobrir que processo (ou processos) causa o comportamento. Uma vez reconhecido o processo, determine se o aumento do consumo está a ser causado por um código ineficiente ou pelo consumo normal. Se o consumo normal, decida se o processo funciona ao nível de desempenho exigido.

Constante – Determine se o seu VM sempre funcionou a este nível, ou se só tem funcionado a esse nível desde que os diagnósticos foram ativados. Em caso afirmativo, identifique o processo (ou processos) que cause o problema e considere adicionar mais desse recurso.

Aumentar constantemente – Um aumento constante do consumo é frequentemente um código ineficiente ou um processo que assume mais carga de trabalho do utilizador.

### <a name="high-cpu-utilization-remediation"></a>Remediação de alta utilização do CPU

Se a sua aplicação ou processo não estiver a decorrer ao nível de desempenho correto, e estiver a ver 95% + constante de utilização do CPU, pode executar qualquer uma das seguintes tarefas:

* Para alívio imediato - Aumente o tamanho do VM para um tamanho com mais núcleos
* Compreenda o problema – localize a aplicação/processo e a resolução de problemas em conformidade.

Se tiver aumentado o VM e o CPU ainda estiver a funcionar 95%, determine se esta definição está a oferecer um melhor desempenho ou uma produção de aplicação superior a um nível aceitável. Caso contrário, resolva o processo de aplicação individual.

Pode utilizar Perfinsights para [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md) para analisar que processo está a impulsionar o consumo de CPU. 

## <a name="check-for-memory-bottleneck"></a>Verifique se há estrangulamento de memória

Para ver as métricas:

1. Adicione uma secção.
2. Adicione um azulejo.
3. Abra a Galeria.
4. Selecione a Utilização da Memória e arraste. Quando o azulejo estiver ancorado, clique à direita e selecione **6x4**.

### <a name="memory-observe-trends"></a>Tendências de observação da memória

O Uso da Memória mostra-lhe a quantidade de memória que está a ser consumida com o VM. Compreenda a tendência e se mapeia para o momento em que vê problemas. Deve ter sempre mais de 100 MB de memória disponível.

Spike e Consumo Constante/Constante Constante - A utilização elevada da memória pode não ser a causa de um mau desempenho, uma vez que algumas aplicações como os motores de base de dados relacionais atribuem uma grande quantidade de memória, e esta utilização pode não ser significativa. No entanto, se existirem múltiplas aplicações com fome de memória, poderá ver um mau desempenho da contenção da memória causando aparas e paging/troca para o disco. Este mau desempenho é muitas vezes uma causa notável do impacto do desempenho da aplicação.

Aumentar constantemente o consumo – Uma possível aplicação 'aquecimento', este consumo é comum entre os motores de base de dados que estão a arrancar. No entanto, também pode ser um sinal de fuga de memória numa aplicação. Identifique a aplicação e compreenda se o comportamento é esperado.

Utilização de ficheiros de página ou troca – Verifique se está a utilizar o ficheiro De Paging do Windows (localizado no ficheiro D: \) ou Linux Swap (localizado `/dev/sdb` em) estão a ser fortemente utilizados. Se não tiver nada nestes volumes exceto estes ficheiros, verifique se há leitura/escritas elevadas nesses discos. Esta questão é indicativa de condições de memória baixas.

### <a name="high-memory-utilization-remediation"></a>Reparação de alta utilização da memória

Para resolver uma utilização de memória elevada, execute qualquer uma das seguintes tarefas:

* Para alívio imediato ou utilização de página ou swap - Aumente o tamanho VM para um com mais memória e monitorize.
* Compreenda o problema – localize aplicações/processos e resolução de problemas para identificar aplicações de memória altamente consumistas.
* Se souber da aplicação, veja se a alocação de memória pode ser limitada.

Se depois de atualizar para um VM maior, você descobre que ainda tem um aumento constante constante até 100%, identifique a aplicação/processo e resolução de problemas.

Pode utilizar Perfinsights para [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md) para analisar que processo está a impulsionar o consumo de Memória. 

## <a name="check-for-disk-bottleneck"></a>Verifique se há estrangulamento do disco

Para verificar o subsistema de armazenamento do VM, verifique os diagnósticos ao nível do VM Azure utilizando os contadores em VM Diagnostics e também os Diagnósticos de Conta de Armazenamento.

Para uma resolução específica de problemas em VM, pode utilizar Perfinsights para [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md), o que pode ajudar a analisar que processo está a conduzir o IO's. 

Note que não temos balcões para contas de armazenamento de zonas redundantes e premium. Para questões relacionadas com estes balcões, levante um caso de apoio.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visualização de diagnósticos de conta de armazenamento na monitorização

Para trabalhar nos itens abaixo, entre na conta de armazenamento do VM no portal:

![Visualização de diagnósticos de conta de armazenamento em monitorização](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Editar o Gráfico de Monitorização.
2. Desa parte do intervalo de tempo.
3. Adicione os contadores descritos nos passos abaixo.
4. Guarde as alterações.

### <a name="disk-observe-trends-standard-storage-only"></a>Tendências de observação de discos (apenas armazenamento padrão)

Para identificar problemas com armazenamento, veja as métricas de desempenho dos Diagnósticos de Conta de Armazenamento e dos Diagnósticos VM.

Para cada verificação abaixo, procure as principais tendências quando as questões ocorrem dentro do intervalo de tempo da emissão.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Verifique a disponibilidade de armazenamento Azure – Adicione a métrica da conta de armazenamento: disponibilidade

Se vir uma queda na disponibilidade, pode haver um problema com a plataforma, verifique o [Estado de Azure](https://azure.microsoft.com/status/). Se não houver qualquer questão, apresente um novo pedido de apoio.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Verifique o tempo limite de armazenamento da Azure - Adicione as métricas da conta de armazenamento:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

Os valores nas métricas *TimeOutError indicam que uma operação de IO demorou demasiado tempo e foi desatar. Trabalhar os próximos passos ajudará a identificar potenciais causas.

O aumento médio da Procuração de verão ao mesmo tempo nos TimeOutErrors pode ser um problema de plataforma. Levantar um novo pedido de apoio nesta situação.

A Média E2ELatency representa a latência do cliente. Verifique como o IOPS está a ser realizado pela aplicação. Procure uma métrica total de aumento ou constantemente alta. Esta métrica representa iOPS. Se você está começando a atingir os limites da conta de armazenamento ou vHD único, a latência pode estar relacionada com estrangulamento.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Verifique o estrangulamento do armazenamento Azure - Adicione as métricas da conta de armazenamento: ThrottlingError

Os valores para o estrangulamento indicam que está a ser estrangulado ao nível da conta de armazenamento, o que significa que está a atingir o limite do IOPS da conta. Pode determinar se está a atingir o limiar de IOPs verificando as **métricas TotalRequests**.

Note que cada VHD tem um limite de 500 IOPS ou 60 MBits, mas está vinculado ao limite acumulado de 20000 IOPS por conta de armazenamento.

Com esta métrica, não se sabe qual a bolha que está a causar o estrangulamento e que são afetadas por ela. No entanto, ou está a atingir os limites do IOPS ou ingress/Egress da conta de armazenamento.

Para identificar se está a atingir o limite do IOPS, entre nos diagnósticos da Conta de Armazenamento e verifique os TotalRequests, procurando ver se está a aproximar-se dos 20 mil TotalRequests. Identifique ou uma mudança no padrão, quer esteja a ver o limite pela primeira vez, ou se este limite acontece numa determinada altura.

Com novas ofertas de discos sob armazenamento standard, os limites de IOPS e Depute podem diferir, mas o limite acumulado da conta standard de armazenamento é 20000 IOPS (O armazenamento premium tem limites diferentes a nível da conta ou do disco). Leia mais sobre diferentes ofertas padrão de disco de armazenamento e por limites de disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](../disks-scalability-targets.md).

#### <a name="references"></a>Referências

* [Metas de escalabilidade e desempenho para contas de armazenamento de blob de página premium](../../storage/blobs/scalability-targets-premium-page-blobs.md)

A largura de banda da conta de armazenamento é medida pelas Métricas da Conta de Armazenamento: TotalIngress e TotalEgress. Tem diferentes limiares para a largura de banda, dependendo do tipo de redundância e regiões.

* [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md)

Verifique os limites totalings e TotalEgress contra os limites de Ingress e Egress para o tipo e região de redundância da conta de armazenamento.

Verifique os limites de produção dos VHDs ligados ao VM. Adicione as métricas VM Leitura e Escrita.

As novas ofertas de discos sob armazenamento standard têm diferentes IOPS e limites de produção (O IOPS não está exposto por VHD). Veja os dados para ver se está a atingir os limites de MB de produção combinada dos VHD(s) a nível VM utilizando a Leitura e a Escrita do Disco, em seguida, otimize a sua configuração de armazenamento VM para escalar os limites de VHD únicos. Leia mais sobre diferentes ofertas padrão de disco de armazenamento e por limites de disco:

* [Metas de escalabilidade e desempenho para discos VM no Windows](../disks-scalability-targets.md).

### <a name="high-disk-utilizationlatency-remediation"></a>Reparação de alta utilização/latência do disco

Reduzir a latência do cliente e otimizar o IO VM para escalar os limites de VHD passados

* [Otimização de IO para Windows em Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Otimização de IO para Linux em Azure](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>Reduzir o estrangulamento

Se atingir os limites superiores das contas de armazenamento, reequilibre os VHDs entre contas de armazenamento. Consulte os [objetivos de escalabilidade e desempenho do armazenamento Azure](../../storage/common/scalability-targets-standard-account.md).

### <a name="increase-throughput-and-reduce-latency"></a>Aumentar a produção e reduzir a latência

Se tiver uma aplicação sensível à latência e necessitar de alta produção, migrar os seus VHDs para o armazenamento Azure Premium utilizando a série DS e GS VM.

Estes artigos discutem os cenários específicos:

* [Migrar para o Armazenamento Premium do Azure](../windows/migrate-to-managed-disks.md)

* [Use armazenamento Azure Premium com servidor SQL](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, apresente um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

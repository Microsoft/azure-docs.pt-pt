---
title: Como traçar o desempenho com o Monitor Azure para VMs (pré-visualização) / Microsoft Docs
description: O desempenho é uma característica do Monitor Azure para VMs que descobre automaticamente componentes de aplicações nos sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como usá-lo em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 245d5c0fb0a54a6d129a193deaa9445bc8fefbfb
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670700"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Como traçar o desempenho com o Monitor Azure para VMs (pré-visualização)

O Monitor Azure para VMs inclui um conjunto de gráficos de desempenho que visam vários indicadores de desempenho chave (KPIs) para ajudá-lo a determinar o quão bem uma máquina virtual está a funcionar. Os gráficos mostram a utilização de recursos durante um período de tempo para que possa identificar estrangulamentos, anomalias ou mudar para uma perspetiva listando cada máquina para visualizar a utilização de recursos com base na métrica selecionada. Embora existam inúmeros elementos a considerar ao lidar com o desempenho, o Monitor Azure para VMs monitoriza os principais indicadores de desempenho do sistema operativo relacionados com processador, memória, adaptador de rede e utilização de discos. O desempenho complementa a funcionalidade de monitorização da saúde e ajuda a expor problemas que indicam uma possível falha na componente do sistema, afinação de suporte e otimização para alcançar eficiência, ou planeamento da capacidade de suporte.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspetiva multi-VM do Monitor Azure

Do Monitor Azure, a funcionalidade Performance proporciona uma visão de todos os VMs monitorizados implantados em grupos de trabalho nas suas subscrições ou no seu ambiente. Para aceder ao Monitor Azure, execute os seguintes passos. 

1. No portal Azure, selecione **Monitor**. 
2. Escolha **Máquinas Virtuais (pré-visualização)** na secção **Soluções.**
3. Selecione o separador **Performance.**

![VM insights Performance Top N List vista](./media/vminsights-performance/vminsights-performance-aggview-01.png)

No separador **Top N Charts,** se tiver mais de um espaço de trabalho log Analytics, escolha o espaço de trabalho ativado com a solução do seletor **workspace** no topo da página. O seletor **do Grupo** devolverá subscrições, grupos de recursos, [grupos informáticos](../platform/computer-groups.md)e conjuntos de computadores de escala virtual de computadores relacionados com o espaço de trabalho selecionado que pode utilizar para filtrar mais resultados apresentados nas tabelas desta página e nas outras páginas. A sua seleção aplica-se apenas à funcionalidade Performance e não passa para a Saúde ou mapa.  

Por padrão, os gráficos mostram as últimas 24 horas. Utilizando o seletor **TimeRange,** pode consultar intervalos históricos de tempo até 30 dias para mostrar como o desempenho foi feito no passado.

Os cinco gráficos de utilização de capacidade mostrados na página são:

* CPU Utilização % - mostra as cinco máquinas top com a utilização média mais alta do processador 
* Memória Disponível - mostra as cinco melhores máquinas com a menor quantidade média de memória disponível 
* Espaço lógico do disco usado % - mostra as cinco máquinas top com o espaço de disco médio mais elevado usado % em todos os volumes de disco 
* Bytes Sent Rate - mostra as cinco melhores máquinas com a média mais alta de bytes enviados 
* Bytes Receive Rate - mostra as cinco melhores máquinas com a média mais alta de bytes recebidos 

Clicar no ícone do pino no canto superior direito de qualquer uma das cinco tabelas fixará o gráfico selecionado para o último painel de instrumentos Azure que viu pela última vez.  A partir do tablier, pode redimensionar e reposicionar o gráfico. A seleção do gráfico do painel de instrumentos irá redirecioná-lo para o Monitor Azure para VMs e carregar o âmbito e a vista corretos.  

Clicar no ícone localizado à esquerda do ícone pinem em qualquer uma das cinco tabelas abre a vista **Top N List.**  Aqui você vê a utilização de recursos para essa métrica de desempenho por VM individual numa vista de lista e qual a máquina que está mais alta.  

![Vista top N list para uma métrica de desempenho selecionada](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando clica na máquina virtual, o painel **Properties** é expandido à direita para mostrar as propriedades do item selecionado, como informações do sistema reportadas pelo sistema operativo, propriedades do VM Azure, etc. Clicar numa das opções da secção **Quick Links** irá redirecioná-lo diretamente para essa funcionalidade a partir do VM selecionado.  

![Painel de propriedades de máquinas virtuais](./media/vminsights-performance/vminsights-properties-pane-01.png)

Mude para o separador **Gráficos Agregados** para ver as métricas de desempenho filtradas por medidas médias ou percentículas.  

![Visão de desempenho de insights vM](./media/vminsights-performance/vminsights-performance-aggview-02.png)

São fornecidos os seguintes gráficos de utilização da capacidade:

* CPU Utilização % - incumprimentos que mostram o percentil médio e 95º 
* Memória Disponível - incumprimentos que mostram a média, top 5 e 10º percentil 
* Espaço lógico do disco usado % - incumprimentos que mostram o percentil médio e 95º 
* Bytes Taxa Enviada - incumprimentos que mostram bytes médios enviados 
* Bytes Receive Rate - incumprimentos que mostram bytes médios recebidos

Também pode alterar a granularidade das tabelas dentro do intervalo de tempo, selecionando **Avg,** **Min,** **Max,** **50º,** **90º**e **95º** no seletor de percentil.

Para ver a utilização de recursos por VM individual numa visualização de lista e ver qual a máquina que está a evoluir com a maior utilização, selecione o separador **Lista De Topo N.**  A página **top N list** mostra as 20 máquinas mais sortidas pela mais utilizada pelo percentil 95 para a utilização métrica do *CPU %* .  Pode ver mais máquinas selecionando **Load More,** e os resultados expandem-se para mostrar as 500 melhores máquinas. 

>[!NOTE]
>A lista não pode mostrar mais de 500 máquinas de cada vez.  
>

![Exemplo da página da lista N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados numa máquina virtual específica da lista, introduza o seu nome de computador na caixa de texto **Search by name.**  

Se preferir ver a utilização de uma métrica de desempenho diferente, desde a lista **métrica** de drop-down selecione **Memória Disponível,** **Espaço lógico de disco usado %** , Rede Recebida **Byte/s,** ou **Rede Enviada Byte/s** e as atualizações da lista para mostrar utilização scoped para essa métrica.  

Selecionando uma máquina virtual da lista abre o painel **Propriedades** no lado direito da página e a partir daqui pode selecionar **o detalhe de Desempenho**.  A página **Virtual Machine Detail** abre e é direcionada a esse VM, semelhante na experiência ao aceder ao Desempenho vM Insights diretamente do VM VM.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Ver desempenho diretamente de um Azure VM

Para aceder diretamente a partir de uma máquina virtual, execute os seguintes passos.

1. No portal Azure, selecione **Máquinas Virtuais**. 
2. Na lista, escolha um VM e na secção **de Monitorização** escolha **Insights (pré-visualização)** .  
3. Selecione o separador **Performance.** 

Esta página não só inclui gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, a sua capacidade, utilização e média total por cada medida.  

São fornecidos os seguintes gráficos de utilização da capacidade:

* CPU Utilização % - incumprimentos que mostram o percentil médio e 95º 
* Memória Disponível - incumprimentos que mostram a média, top 5 e 10º percentil 
* Espaço lógico do disco usado % - incumprimentos que mostram o percentil médio e 95º 
* IOPS de disco lógico - incumprimentos que mostram o percentil médio e 95º
* Icteriquete lógico MB/s - incumprimentos que mostram o percentil médio e 95º
* Max Logical Disk Used % - incumprimentos que mostram o percentil médio e 95º
* Bytes Taxa Enviada - incumprimentos que mostram bytes médios enviados 
* Bytes Receive Rate - incumprimentos que mostram bytes médios recebidos

Clicar no ícone do pino no canto superior direito de qualquer um dos gráficos pins o gráfico selecionado para o último painel de instrumentos Azure que visualizaste. A partir do tablier, pode redimensionar e reposicionar o gráfico. A seleção da tabela a partir do painel redireciona-o para o Monitor Azure para VMs e carrega a vista de detalhes de desempenho para o VM.  

![VM insights Desempenho diretamente da vista VM](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Ver desempenho diretamente de um conjunto de escala de máquina virtual Azure

Para aceder diretamente a partir de um conjunto de escala de máquina virtual Azure, execute os seguintes passos.

1. No portal Azure, selecione **conjuntos**de escala de máquina virtual .
2. Na lista, escolha um VM e na secção **de Monitorização** escolha **Insights (pré-visualização)** para visualizar o separador **Performance.**

Esta página carrega a vista de desempenho do Monitor Azure, resumida ao conjunto de escala selecionado. Isto permite-lhe ver as Instâncias De Topo N na escala definida através do conjunto de métricas monitorizadas, ver o desempenho agregado em todo o conjunto de escala, e ver as tendências para métricas selecionadas em cada instância no conjunto de escala. A seleção de uma instância da vista da lista permite-lhe carregar o seu mapa ou navegar para uma visão detalhada de desempenho para esse exemplo.

Clicar no ícone do pino no canto superior direito de qualquer um dos gráficos pins o gráfico selecionado para o último painel de instrumentos Azure que visualizaste. A partir do tablier, pode redimensionar e reposicionar o gráfico. A seleção da tabela a partir do painel redireciona-o para o Monitor Azure para VMs e carrega a vista de detalhes de desempenho para o VM.  

![VM insights Desempenho diretamente da vista de conjunto de escala de máquina virtual](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Também pode aceder a uma visão detalhada de desempenho para uma instância específica da vista Instances para o seu conjunto de escala. Navegue para **Instâncias** sob a secção **Definições** e, em seguida, escolha **Insights (pré-visualização)** .

## <a name="alerts"></a>Alertas  

As métricas de desempenho ativadas como parte do Monitor Azure para VMs não incluem regras de alerta pré-configuradas. Existem alertas de [saúde correspondentes](vminsights-health.md#alerts) a problemas de desempenho detetados no seu VM Azure, tais como alta utilização de CPU, baixa memória disponível, espaço de disco baixo, etc.  No entanto, estes alertas de saúde aplicam-se apenas a todos os VMs habilitados para o Monitor Azure para VMs. 

No entanto, só podemos recolher e armazenar um subconjunto das métricas de desempenho que necessita no espaço de trabalho log Analytics. Se a sua estratégia de monitorização necessitar de análise ou alerta que inclua outras métricas de desempenho para avaliar eficazmente a capacidade ou a saúde da máquina virtual, ou precisar da flexibilidade para especificar os seus próprios critérios de alerta ou lógica, pode configurar a [recolha desses contadores de desempenho](../platform/data-sources-performance-counters.md) no Log Analytics e definir [alertas](../platform/alerts-log.md)de registo . Enquanto o Log Analytics permite realizar análises complexas com outros tipos de dados, e fornecer uma retenção mais longa para apoiar a análise de tendências, as métricas, por outro lado, são leves e capazes de suportar cenários próximos em tempo real. São recolhidos pelo agente de [diagnóstico Azure](../../virtual-machines/windows/monitor.md) e armazenados na loja de métricas Azure Monitor, permitindo criar alertas com menor latência e a um custo mais baixo.

Reveja a visão geral da [recolha de métricas e registos com](../platform/data-platform.md) o Monitor Azure para compreender melhor as diferenças fundamentais e outras considerações antes de configurar a recolha destas métricas adicionais e regras de alerta.  

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar [os livros](vminsights-workbooks.md) de trabalho que estão incluídos com o Monitor Azure para VMs para analisar ainda mais o desempenho e as métricas de rede.  

- Para saber sobre dependências de aplicações descobertas, consulte o View Azure Monitor para o Mapa de [VMs](vminsights-maps.md).

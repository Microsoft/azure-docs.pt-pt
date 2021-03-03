---
title: Como traçar o desempenho com insights VM
description: O desempenho é uma característica dos insights VM que descobre automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Este artigo fornece detalhes sobre como usá-lo em vários cenários.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/31/2020
ms.openlocfilehash: 9c69ea3da71063d7e20ebf31ae2eb3df9a51e2c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725446"
---
# <a name="how-to-chart-performance-with-vm-insights"></a>Como traçar o desempenho com insights VM

Os insights de VM incluem um conjunto de gráficos de desempenho que visam vários indicadores-chave de desempenho (KPI's) para ajudá-lo a determinar o quão bem uma máquina virtual está a funcionar. Os gráficos mostram a utilização de recursos durante um período de tempo para que possa identificar estrangulamentos, anomalias ou mudar para uma perspetiva listando cada máquina para ver a utilização do recurso com base na métrica selecionada. Embora existam inúmeros elementos a ter em conta quando se trata de desempenho, os insights VM monitorizam os principais indicadores de desempenho do sistema operativo relacionados com o processador, a memória, o adaptador de rede e a utilização do disco. O desempenho complementa a funcionalidade de monitorização da saúde e ajuda a expor problemas que indicam uma possível falha na componente do sistema, a sintonização de suporte e otimização para alcançar eficiência ou planeamento de capacidade de suporte.  

## <a name="limitations"></a>Limitações
Seguem-se limitações na recolha de desempenho com insights VM.

- **A memória disponível** não está disponível para máquinas virtuais que executam o Red Hat Linux (RHEL) 6. Esta métrica é calculada a partir do **MemAvailable** que foi introduzido na [versão kernel 3.14](http://www.man7.org/linux/man-pages/man1/free.1.html).
- As métricas só estão disponíveis para discos de dados em máquinas virtuais Linux utilizando o sistema de ficheiros XFS ou a família de ficheiros EXT (EXT2, EXT3, EXT4).

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspetiva multi-VM do Azure Monitor

A partir do Azure Monitor, a funcionalidade Performance fornece uma visão de todos os VMs monitorizados implantados em grupos de trabalho nas suas subscrições ou no seu ambiente. Para aceder a partir do Monitor Azure, execute os seguintes passos. 

1. No portal Azure, selecione **Monitor**. 
2. Escolha **Máquinas Virtuais** na secção **Soluções.**
3. Selecione o separador **Desempenho**.

![VM insights Performance Top N List](media/vminsights-performance/vminsights-performance-aggview-01.png)

No separador **Top N Charts,** se tiver mais de um espaço de trabalho log Analytics, escolha o espaço de trabalho ativado com a solução do seletor **workspace** no topo da página. O seletor **do Grupo** devolverá subscrições, grupos de recursos, [grupos de computadores](../logs/computer-groups.md)e conjuntos de escala de máquinas virtuais de computadores relacionados com o espaço de trabalho selecionado que pode utilizar para filtrar ainda mais os resultados apresentados nas tabelas desta página e nas outras páginas. A sua seleção aplica-se apenas à funcionalidade Performance e não transporta para saúde ou mapa.  

Por defeito, os gráficos mostram as últimas 24 horas. Utilizando o seletor **TimeRange,** pode consultar intervalos históricos de tempo de até 30 dias para mostrar como o desempenho parecia no passado.

Os cinco gráficos de utilização da capacidade mostrados na página são:

* CPU Utilização % - mostra as cinco melhores máquinas com a utilização média mais alta do processador 
* Memória Disponível - mostra as cinco melhores máquinas com a menor quantidade média de memória disponível 
* Logic Disk Space Used % - mostra as cinco melhores máquinas com o maior espaço médio de disco usado % em todos os volumes de discos 
* Bytes Sent Rate - mostra as cinco melhores máquinas com a média mais alta de bytes enviados 
* Bytes Receive Rate - mostra as cinco melhores máquinas com a média mais alta de bytes recebidos 

Clicar no ícone pino no canto superior direito de qualquer uma das cinco tabelas irá fixar a tabela selecionada para o último painel Azure que viu pela última vez.  A partir do painel de instrumentos, pode redimensionar e reposicionar a tabela. Selecionar o gráfico do painel de instrumentos irá redirecioná-lo para as introspeções de VM e carregar o âmbito e a vista corretos.  

Clicar no ícone localizado à esquerda do ícone pino em qualquer um dos cinco gráficos abre a vista **da Lista N Superior.**  Aqui você vê a utilização de recursos para essa métrica de desempenho por VM individual em uma vista de lista e qual a máquina que está tendência mais alta.  

![Vista top N List para uma métrica de desempenho selecionada](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando clica na máquina virtual, o painel **Propriedades** é expandido no direito de mostrar as propriedades do item selecionado, como informações do sistema reportadas pelo sistema operativo, propriedades do VM Azure, etc. Clicar numa das opções na secção **Links Rápidos** irá redirecioná-lo diretamente para essa função a partir do VM selecionado.  

![Painel de propriedades de máquinas virtuais](./media/vminsights-performance/vminsights-properties-pane-01.png)

Mude para o **separador Tabelas Agregadas** para ver as métricas de desempenho filtradas por medidas médias ou percentivas.  

![VM insights Performance Agregada vista](./media/vminsights-performance/vminsights-performance-aggview-02.png)

São fornecidos os seguintes gráficos de utilização da capacidade:

* CPU Utilização % - incumprimentos que mostram o percentil médio e superior do 95º 
* Memória Disponível - padrão que mostra a média, top 5 e percentil 10 
* Espaço lógico do disco usado % - padrão mostrando a média e 95º percentil 
* Bytes Sent Rate - incumprimentos que mostram bytes médios enviados 
* Bytes Receive Rate - incumprimentos que mostram bytes médios recebidos

Também pode alterar a granularidade das tabelas dentro do intervalo de tempo selecionando **Avg**, **Min**, **Max**, **50º**, **90º** e **95º** no seletor percentil.

Para visualizar a utilização do recurso por VM individual numa visualização de lista e ver qual a máquina que está a tendência com a utilização mais elevada, selecione o separador **Top N List.**  A página **top N List** mostra as 20 melhores máquinas classificadas pela mais utilizadas pelo percentil 95 para a métrica *CPU Utilização %*.  Pode ver mais máquinas selecionando **Load More**, e os resultados expandem-se para mostrar as 500 melhores máquinas. 

>[!NOTE]
>A lista não pode mostrar mais de 500 máquinas de cada vez.  
>

![Exemplo de página da lista N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados de uma máquina virtual específica na lista, introduza o seu nome de computador na caixa de texto **do nome Procura.**  

Se preferir visualizar a utilização a partir de uma métrica de desempenho diferente, a partir da lista **métrica** de drop-down selecione **Memória Disponível**, **Espaço Lógico Disco Usado %**, **Byte/s recebido** em rede, ou **Byte/s de rede** e as atualizações da lista para mostrar utilização telescópio para essa métrica.  

A seleção de uma máquina virtual da lista abre o painel **Propriedades** no lado direito da página e a partir daqui pode selecionar **o detalhe** de desempenho .  A página **Virtual Machine Detail** abre e é traçada para esse VM, semelhante em experiência ao aceder ao VM Insights Performance diretamente do Azure VM.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Ver desempenho diretamente de um Azure VM

Para aceder diretamente a partir de uma máquina virtual, execute os seguintes passos.

1. No portal Azure, selecione **Máquinas Virtuais.** 
2. Na lista, escolha um VM e na secção **de Monitorização** escolha **Insights**.  
3. Selecione o separador **Desempenho**. 

Esta página inclui não só gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, a sua capacidade, utilização e média total por cada medida.  

São fornecidos os seguintes gráficos de utilização da capacidade:

* CPU Utilização % - incumprimentos que mostram o percentil médio e superior do 95º 
* Memória Disponível - padrão que mostra a média, top 5 e percentil 10 
* Espaço lógico do disco usado % - padrão mostrando a média e 95º percentil 
* Logical Disk IOPS - padrão que mostra a média e o percentil 95
* Disco lógico MB/s - incumprimentos que mostram a média e o percentil 95
* Max Logical Disk Used % - incumprimentos que mostram a média e o percentil 95
* Bytes Sent Rate - incumprimentos que mostram bytes médios enviados 
* Bytes Receive Rate - incumprimentos que mostram bytes médios recebidos

Clicando no ícone do pino no canto superior direito de qualquer um dos gráficos pino o gráfico selecionado para o último painel Azure que viu. A partir do painel de instrumentos, pode redimensionar e reposicionar a tabela. A seleção do gráfico do dashboard redireciona-o para insights VM e carrega a vista de detalhes de desempenho para o VM.  

![VM insights Desempenho diretamente da vista VM](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Ver desempenho diretamente de um conjunto de escala de máquina virtual Azure

Para aceder diretamente a partir de um conjunto de balanças de máquina virtual Azure, execute os seguintes passos.

1. No portal Azure, selecione **conjuntos de escala de máquina virtual**.
2. Na lista, escolha um VM e na secção **de Monitorização** escolha **Insights** para visualizar o **separador Desempenho.**

Esta página carrega a vista de desempenho do Monitor Azure, definida para o conjunto de escala selecionado. Isto permite-lhe ver as Instâncias Top N na escala definida através do conjunto de métricas monitorizadas, ver o desempenho agregado através do conjunto de escalas, e ver as tendências para métricas selecionadas em cada instância no conjunto de escala. Selecionar uma instância a partir da visualização da lista permite-lhe carregar o seu mapa ou navegar para uma visão detalhada do desempenho para esse caso.

Clicando no ícone do pino no canto superior direito de qualquer um dos gráficos pino o gráfico selecionado para o último painel Azure que viu. A partir do painel de instrumentos, pode redimensionar e reposicionar a tabela. A seleção do gráfico do dashboard redireciona-o para insights VM e carrega a vista de detalhes de desempenho para o VM.  

![VM insights Desempenho diretamente da vista de conjunto de escala de máquina virtual](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Também pode aceder a uma visão de desempenho detalhada para uma instância específica a partir da vista Instâncias para o seu conjunto de escala. Navegue para **instâncias** sob a secção **Definições** e, em seguida, escolha **Insights**.



## <a name="next-steps"></a>Passos seguintes

- Aprenda a usar livros de trabalho que estão [incluídos](vminsights-workbooks.md) com insights VM para analisar mais aprofundadamente o desempenho e as métricas de rede.  

- Para saber mais sobre as dependências de aplicações descobertas, consulte [o Mapa de insights VM.](vminsights-maps.md)

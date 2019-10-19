---
title: Como exibir o desempenho de gráficos com Azure Monitor para VMs (visualização) | Microsoft Docs
description: O desempenho é um recurso do Azure Monitor para VMs que descobre automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre como usá-lo em uma variedade de cenários.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: f8879ac2d7827732112fa1a7504484209461b196
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555168"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Como exibir o desempenho de gráficos com Azure Monitor para VMs (versão prévia)

Azure Monitor para VMs inclui um conjunto de gráficos de desempenho que visam vários KPIs (indicadores chave de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Os gráficos mostram a utilização de recursos durante um período de tempo para que você possa identificar afunilamentos, anomalias ou alternar para uma perspectiva listando cada máquina para exibir a utilização de recursos com base na métrica selecionada. Embora haja inúmeros elementos a serem considerados ao lidar com o desempenho, o Azure Monitor para VMs monitora os principais indicadores de desempenho do sistema operacional relacionados ao processador, à memória, ao adaptador de rede e à utilização de disco. O desempenho complementa o recurso de monitoramento de integridade e ajuda a expor problemas que indicam uma possível falha de componente do sistema, o ajuste de suporte e a otimização para alcançar a eficiência ou o planejamento de capacidade de suporte.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Perspectiva de várias VMs de Azure Monitor

Do Azure Monitor, o recurso de desempenho fornece uma exibição de todas as VMs monitoradas implantadas em grupos de recursos em suas assinaturas ou em seu ambiente. Para acessar a partir do Azure Monitor, execute as etapas a seguir. 

1. Na portal do Azure, selecione **Monitor**. 
2. Escolha **máquinas virtuais (versão prévia)** na seção **soluções** .
3. Selecione a guia **desempenho** .

![Modo de exibição de lista Top N do desempenho de informações da VM](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Na guia **N gráficos superiores** , se você tiver mais de um espaço de trabalho log Analytics, escolha o espaço de trabalho habilitado com a solução no seletor de **espaço de trabalho** na parte superior da página. O seletor de **grupo** retornará assinaturas, grupos de recursos, [grupos](../platform/computer-groups.md)de computadores e conjuntos de dimensionamento de máquinas virtuais de computadores relacionados ao espaço de trabalho selecionado que você pode usar para filtrar ainda mais os resultados apresentados nos gráficos desta página e entre as outras páginas. Sua seleção só se aplica ao recurso de desempenho e não é transferida para a integridade ou o mapa.  

Por padrão, os gráficos mostram as últimas 24 horas. Usando o seletor de **intervalo** de tempo, você pode consultar intervalos históricos de até 30 dias para mostrar como o desempenho foi examinado no passado.

Os cinco gráficos de utilização de capacidade mostrados na página são:

* % De utilização da CPU-mostra os cinco principais computadores com a maior utilização média do processador 
* Memória disponível-mostra os cinco principais computadores com a menor quantidade média de memória disponível 
* % De espaço em disco lógico usado-mostra os cinco principais computadores com o maior espaço em disco médio usado em% em todos os volumes de disco 
* Taxa de bytes enviados-mostra os cinco principais computadores com a média mais alta de bytes enviados 
* Taxa de recebimento de bytes-mostra os cinco principais computadores com a média mais alta de bytes enviados 

Clicar no ícone de pino no canto superior direito de qualquer um dos cinco gráficos fixará o gráfico selecionado no último painel do Azure que você exibiu pela última vez.  No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel irá redirecioná-lo para Azure Monitor para VMs e carregar o escopo e a exibição corretos.  

Clicar no ícone localizado à esquerda do ícone de pino em qualquer um dos cinco gráficos abre a exibição de **lista N mais alta** .  Aqui você vê a utilização de recursos para essa métrica de desempenho por VM individual em uma exibição de lista e qual computador é a tendência mais alta.  

![Exibição de lista Top N para uma métrica de desempenho selecionada](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando você clica na máquina virtual, o painel **Propriedades** é expandido à direita para mostrar as propriedades do item selecionado, como informações do sistema relatadas pelo sistema operacional, propriedades da VM do Azure etc. Ao clicar em uma das opções na seção **links rápidos** , você será redirecionado para esse recurso diretamente da VM selecionada.  

![Painel de propriedades da máquina virtual](./media/vminsights-performance/vminsights-properties-pane-01.png)

Alterne para a guia **gráficos agregados** para exibir as métricas de desempenho filtradas por medidas médias ou percentuais.  

![Exibição agregada de desempenho do insights de VM](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Os seguintes gráficos de utilização de capacidade são fornecidos:

* % De utilização da CPU-padrões mostrando a média e a parte superior do 95 º percentil 
* Memória disponível-padrões mostrando a média, o 5 primeiros e o 10º percentil 
* % De espaço em disco lógico usado-padrões mostrando a média e 95 º percentil 
* Taxa de bytes enviados-padrões mostrando a média de bytes enviados 
* Taxa de recebimento de bytes-padrões mostrando a média de bytes recebidos

Você também pode alterar a granularidade dos gráficos dentro do intervalo de tempo selecionando **AVG**, **min**, **Max**, **50 º**, **90 º**e **95 º** no seletor de percentil.

Para exibir a utilização de recursos por VM individual em um modo de exibição de lista e ver qual computador está se tendência com maior utilização, selecione a guia **lista N superior** .  A página da **lista N superior** mostra os 20 principais computadores classificados pelo mais utilizado pelo 95 º percentil para a% de *utilização da CPU*de métrica.  Você pode ver mais computadores selecionando **carregar mais**e os resultados são expandidos para mostrar os principais computadores 500. 

>[!NOTE]
>A lista não pode mostrar mais de 500 computadores por vez.  
>

![Exemplo de página de lista N superior](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Para filtrar os resultados em uma máquina virtual específica na lista, insira o nome do computador na caixa de texto **Pesquisar por nome** .  

Se você preferir exibir a utilização de uma métrica de desempenho diferente, na lista suspensa **métrica** , selecione **memória disponível**, **espaço em disco lógico usado%** , **bytes recebidos**de rede ou **byte/s de rede enviados** e o listar atualizações para mostrar a utilização com escopo para essa métrica.  

A seleção de uma máquina virtual na lista abre o painel **Propriedades** no lado direito da página e, aqui, você pode selecionar **detalhes de desempenho**.  A página de **detalhes da máquina virtual** é aberta e tem o escopo definido para essa VM, semelhante em experiência ao acessar o desempenho da VM insights diretamente da VM do Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Exibir o desempenho diretamente de uma VM do Azure

Para acessar diretamente de uma máquina virtual, execute as etapas a seguir.

1. No portal do Azure, selecione **máquinas virtuais**. 
2. Na lista, escolha uma VM e, na seção **monitoramento** , escolha **insights (versão prévia)** .  
3. Selecione a guia **desempenho** . 

Esta página não inclui apenas gráficos de utilização de desempenho, mas também uma tabela que mostra cada disco lógico descoberto, sua capacidade, utilização e média total por cada medida.  

Os seguintes gráficos de utilização de capacidade são fornecidos:

* % De utilização da CPU-padrões mostrando a média e a parte superior do 95 º percentil 
* Memória disponível-padrões mostrando a média, o 5 primeiros e o 10º percentil 
* % De espaço em disco lógico usado-padrões mostrando a média e 95 º percentil 
* IOPS de disco lógico-padrões mostrando média e 95 º percentil
* Disco lógico MB/s-padrões mostrando a média e 95 º percentil
* % Máximo de disco lógico usado%-padrões mostrando média e 95 º percentil
* Taxa de bytes enviados-padrões mostrando a média de bytes enviados 
* Taxa de recebimento de bytes-padrões mostrando a média de bytes recebidos

Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixa o gráfico selecionado ao último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel redireciona você para Azure Monitor para VMs e carrega a exibição de detalhes de desempenho da VM.  

![Desempenho de informações da VM diretamente da exibição da VM](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Exibir o desempenho diretamente de um conjunto de dimensionamento de máquinas virtuais do Azure

Para acessar diretamente de um conjunto de dimensionamento de máquinas virtuais do Azure, execute as etapas a seguir.

1. Na portal do Azure, selecione **conjuntos de dimensionamento de máquinas virtuais**.
2. Na lista, escolha uma VM e, na seção **monitoramento** , escolha **insights (versão prévia)** para exibir a guia **desempenho** .

Esta página carrega a exibição de desempenho Azure Monitor, no escopo do conjunto de dimensionamento selecionado. Isso permite que você veja as primeiras N instâncias no conjunto de dimensionamento no conjunto de métricas monitoradas, exiba o desempenho agregado em todo o conjunto de dimensionamento e veja as tendências para métricas selecionadas em todas as instâncias individuais N o conjunto de dimensionamento. A seleção de uma instância na exibição de lista permite que você carregue o mapa ou navegue até um modo de exibição de desempenho detalhado para essa instância.

Clicar no ícone de pino no canto superior direito de qualquer um dos gráficos fixa o gráfico selecionado ao último painel do Azure exibido. No painel, você pode redimensionar e reposicionar o gráfico. A seleção do gráfico no painel redireciona você para Azure Monitor para VMs e carrega a exibição de detalhes de desempenho da VM.  

![Desempenho de informações da VM diretamente da exibição do conjunto de dimensionamento de máquinas virtuais](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Você também pode acessar um modo de exibição de desempenho detalhado para uma instância específica do modo de exibição de instâncias para seu conjunto de dimensionamento. Navegue até **instâncias** na seção **configurações** e, em seguida, escolha **insights (versão prévia)** .

## <a name="alerts"></a>Alertas  

As métricas de desempenho habilitadas como parte do Azure Monitor para VMs não incluem regras de alerta pré-configuradas. Há [alertas de integridade](vminsights-health.md#alerts) correspondentes aos problemas de desempenho detectados em sua VM do Azure, como alta utilização da CPU, memória insuficiente disponível, pouco espaço em disco, etc.  No entanto, esses alertas de integridade se aplicam somente a todas as VMs habilitadas para Azure Monitor para VMs. 

No entanto, só podemos coletar e armazenar um subconjunto das métricas de desempenho que você precisa no espaço de trabalho Log Analytics. Se sua estratégia de monitoramento exigir análise ou alertas que incluam outras métricas de desempenho para avaliar efetivamente a capacidade ou a integridade da máquina virtual, ou se você precisar da flexibilidade para especificar seus próprios critérios de alerta ou lógica, poderá Configure a [coleta desses contadores de desempenho](../platform/data-sources-performance-counters.md) no log Analytics e defina [alertas de log](../platform/alerts-log.md). Embora Log Analytics permita que você execute uma análise complexa com outros tipos de dados e forneça uma retenção mais longa para dar suporte à análise de tendência, as métricas por outro lado são leves e capazes de dar suporte a cenários quase em tempo real. Eles são coletados pelo [agente de diagnóstico do Azure](../../virtual-machines/windows/monitor.md) e armazenados no repositório de métricas Azure monitor, permitindo que você crie alertas com menor latência e a um custo mais baixo.

Examine a visão geral da [coleta de métricas e logs com Azure monitor](../platform/data-platform.md) para entender ainda mais as diferenças fundamentais e outras considerações antes de configurar a coleta dessas métricas adicionais e regras de alerta.  

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar [pastas de trabalho](vminsights-workbooks.md) incluídas com o Azure monitor para VMs para analisar ainda mais as métricas de desempenho e rede.  

- Para saber mais sobre dependências de aplicativo descobertas, consulte [exibir mapa de Azure monitor para VMs](vminsights-maps.md).

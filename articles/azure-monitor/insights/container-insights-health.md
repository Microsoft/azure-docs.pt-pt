---
title: Monitor Kubernetes cluster health with Azure Monitor for containers [ Microsoft Docs
description: Este artigo descreve como pode ver e analisar a saúde dos seus clusters AKS e não-AKS com o Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843995"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Compreender a saúde do cluster Kubernetes com o Monitor Azure para contentores

Com o Monitor Azure para contentores, monitoriza e reporta o estado de saúde dos componentes de infraestrutura geridos e todos os nós em funcionamento em qualquer cluster Kubernetes apoiado pelo Azure Monitor para contentores. Esta experiência [estende-se](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)para além do estado de saúde do cluster calculado e reportado na visão de vários clusters, onde agora pode entender se um ou mais nós no cluster estão limitados de recursos, ou um nó ou pod está indisponível que pode afetar uma aplicação de execução no cluster com base em métricas curadas.

>[!NOTE]
>A funcionalidade de Saúde está em pré-visualização pública neste momento.
>

Para obter informações sobre como ativar o Monitor Azure para contentores, consulte o [Monitor De Bordo Azure para obter recipientes](container-insights-onboard.md).

>[!NOTE]
>Para suportar os clusters aks engine, verifique se satisfaz o seguinte:
>- Está a utilizar a versão mais recente do [cliente HELM.](https://helm.sh/docs/using_helm/)
>- A versão do agente contentorizado é *microsoft/oms:ciprod11012019*. Para atualizar o agente, consulte o [agente de atualização no cluster Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Descrição geral

No Monitor Azure para contentores, a função Health (pré-visualização) fornece monitorização proativa da saúde do seu cluster Kubernetes para ajudá-lo a identificar e diagnosticar problemas. Dá-lhe a capacidade de ver problemas significativos detetados. Monitores avaliando a saúde do seu cluster executado no agente contentorizado no seu cluster, e os dados de saúde são escritos para a tabela **KubeHealth** no seu espaço de trabalho Log Analytics. 

A saúde do cluster kubernetes baseia-se numa série de cenários de monitorização organizados pelos seguintes objetos e abstrações kubernetes:

- Infraestrutura Kubernetes - fornece um rollup do servidor Kubernetes API, ReplicaSets e DaemonSets em execução em nós implantados no seu cluster avaliando CPU e utilização de memória, e uma disponibilidade de Pods

    ![Vista de rollup de saúde de infraestrutura Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nós - fornece um rollup das piscinas nóeias individuais em cada piscina, avaliando cpu e utilização de memória, e o estado de um nó como relatado por Kubernetes.

    ![Vista de rollup de saúde de nó](./media/container-insights-health/health-view-nodes-01.png)

Atualmente, apenas o estado de um kubelet virtual é suportado. O estado de saúde da CPU e a utilização da memória dos nós de kublet virtuais é relatado como **Desconhecido,** uma vez que não é recebido um sinal deles.

Todos os monitores são mostrados num layout hierárquico no painel da Hierarquia da Saúde, onde um monitor agregado que representa o objeto ou abstração kubernetes (isto é, infraestrutura kubernetes ou nós) são o monitor mais alto que reflete a saúde combinada de todos monitores de crianças dependentes. Os principais cenários de monitorização utilizados para obter a saúde são:

* Avalie a utilização do CPU a partir do nó e do recipiente.
* Avalie a utilização da memória a partir do nó e do recipiente.
* Estado de Pods e Nódosos com base no cálculo do seu estado pronto relatado pela Kubernetes.

Os ícones utilizados para indicar o estado são os seguintes:

|Ícone|Significado|  
|--------|-----------|  
|![Ícone em forma de visto a verde indica um bom estado de funcionamento](./media/container-insights-health/healthyicon.png)|Êxito, o estado de funcionamento está OK (verde)|  
|![Um triângulo amarelo acompanhado de um ponto de exclamação indica um aviso](./media/container-insights-health/warningicon.png)|Aviso (amarelo)|  
|![Um botão vermelho com um X branco indica um estado crítico](./media/container-insights-health/criticalicon.png)|Crítico (vermelho)|  
|![Ícone cinza-out](./media/container-insights-health/grayicon.png)|Desconhecido (cinza)|  

## <a name="monitor-configuration"></a>Configuração do monitor

Para compreender o comportamento e configuração de cada monitor que suporta o Monitor Azure para recipientes Saúde, consulte guia de configuração do monitor de [saúde](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Ver saúde de um cluster AKS ou não-AKS

O acesso ao Monitor Azure para recipientes A funcionalidade Saúde (pré-visualização) está disponível diretamente a partir de um cluster AKS, selecionando **Insights** do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Recipientes**. 

Para ver a saúde de um cluster não AKS, que é um cluster aks engine hospedado no local ou no Azure Stack, **selecione Monitor Azure** a partir do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Recipientes**.  Na página multi-cluster, selecione o cluster não-AKS da lista.

No Monitor Azure para recipientes, a partir da página **Cluster,** selecione **Health**.

![Exemplo de painel de saúde cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Rever a saúde do cluster

Quando a página de Saúde abre, por padrão, a **Infraestrutura Kubernetes** é selecionada na grelha **de Aspeto da Saúde.**  A grelha resume o estado atual de rollup de saúde da infraestrutura kubernetes e dos nós de cluster. A seleção de qualquer aspeto de saúde atualiza os resultados no painel da Hierarquia da Saúde (isto é, o painel intermédio) e mostra todos os monitores infantis num layout hierárquico, mostrando o seu estado de saúde atual. Para ver mais informações sobre qualquer monitor dependente, pode selecionar um e um painel de propriedade exibe automaticamente no lado direito da página. 

![Painel de propriedade de saúde cluster](./media/container-insights-health/health-view-property-pane.png)

No painel da propriedade, você aprende o seguinte:

- No separador **Overview,** mostra o estado atual do monitor selecionado, quando o monitor foi calculado pela última vez, e quando ocorreu a última alteração de estado. Informações adicionais são mostradas dependendo do tipo de monitor selecionado na hierarquia.

    Se selecionar um monitor agregado no painel da Hierarquia da Saúde, sob o separador **Overview** no painel de propriedades, mostra um rollup do número total de monitores infantis na hierarquia, e quantos monitores agregados estão em estado crítico, de alerta e saudável. 

    ![Separador de visão geral do painel de propriedades de saúde para monitor agregado](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se selecionar um monitor de unidade no painel da Hierarquia da Saúde, também mostra em **última alteração de estado** as amostras anteriores calculadas e reportadas pelo agente contentorizado nas últimas quatro horas. Isto baseia-se no cálculo dos monitores da unidade para comparar vários valores consecutivos para determinar o seu estado. Por exemplo, se selecionou o monitor de unidade *estatal pronto* do Pod, mostra as duas últimas amostras controladas pelo parâmetro *ConsecutiveSamplesForStateTransition*. Para mais informações, consulte a descrição detalhada dos [monitores](container-insights-health-monitors-config.md#unit-monitors)da unidade .
    
    ![Separador de visão geral do painel de propriedades de saúde](./media/container-insights-health/health-overview-unit-monitor.png)

    Se o tempo relatado pela **última mudança** de estado for um dia ou mais, é o resultado de nenhuma alteração de estado para o monitor. No entanto, se a última amostra recebida para um monitor de unidade tiver mais de quatro horas, isso indica que o agente contentorizado não tem enviado dados. Se o agente sabe que existe um determinado recurso, por exemplo um Nó, mas não recebeu dados do CPU do Nó ou monitores de utilização da memória (como exemplo), então o estado de saúde do monitor é definido para **Desconhecido**.  

- No separador**Config,** mostra as definições de parâmetrode configuração predefinida (apenas para monitores de unidade, não monitores agregados) e os seus valores.
- No separador **Conhecimento,** contém informações que explicam o comportamento do monitor e como avalia para a condição pouco saudável.

A monitorização dos dados nesta página não é atualizada automaticamente e é necessário selecionar **Refresh** no topo da página para ver o estado de saúde mais recente recebido do cluster.

## <a name="next-steps"></a>Passos seguintes

Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de registo para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

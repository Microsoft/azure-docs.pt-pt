---
title: Monitor Kubernetes cluster health with Azure Monitor for containers Microsoft Docs
description: Este artigo descreve como pode ver e analisar a saúde dos seus clusters AKS e não-AKS com O Azure Monitor para recipientes.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649574"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Compreenda a saúde do cluster Kubernetes com o Azure Monitor para contentores

Com o Azure Monitor para contentores, monitoriza e informa o estado de saúde dos componentes de infraestrutura geridos e de todos os nós em funcionamento em qualquer cluster Kubernetes suportado pelo Azure Monitor para contentores. Esta experiência estende-se para além do estado de saúde do cluster calculado e reportado na [vista multi-cluster,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)onde agora você pode entender se um ou mais nós no cluster são limitados por recursos, ou um nó ou vagem não está disponível que pode afetar uma aplicação de execução no cluster com base em métricas curadas.

>[!NOTE]
>A funcionalidade Saúde vai transitar para uma pré-estreia privada no final de junho de 2020. Para mais informações, reveja o [seguinte anúncio de atualizações do Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/).
>

Para obter informações sobre como permitir o Azure Monitor para contentores, consulte [o Monitor Azure a bordo para obter recipientes](container-insights-onboard.md).

>[!NOTE]
>Para suportar os clusters do motor AKS, verifique se cumpre o seguinte:
>- Está a utilizar a versão mais recente do [cliente HELM.](https://helm.sh/docs/using_helm/)
>- A versão do agente contentorizado é *microsoft/oms:ciprod11012019*. Para atualizar o agente, consulte [o agente de upgrade no cluster Kubernetes](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent).
>

## <a name="overview"></a>Descrição geral

No Azure Monitor para recipientes, a funcionalidade Health (pré-visualização) fornece uma monitorização proativa da saúde do seu cluster Kubernetes para ajudá-lo a identificar e diagnosticar problemas. Dá-lhe a capacidade de ver problemas significativos detetados. Monitores avaliando a saúde do seu cluster executado no agente contentorizado no seu cluster, e os dados de saúde são escritos para a tabela **KubeHealth** no seu espaço de trabalho Log Analytics. 

A saúde do cluster Kubernetes baseia-se numa série de cenários de monitorização organizados pelos seguintes objetos e abstrações de Kubernetes:

- Infraestrutura Kubernetes - fornece um rollup do servidor API de Kubernetes, ReplicaSets e DaemonSets em execução em nos nosmos implantados no seu cluster, avaliando cpu e utilização da memória, e uma disponibilidade de Pods

    ![Vista de rollup de saúde da infraestrutura de Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nós - fornece um rollup das piscinas de nó e estado de nó individuais em cada piscina, avaliando CPU e utilização da memória, e o estado de um nó como relatado por Kubernetes.

    ![Vista rollup de saúde de nódosa](./media/container-insights-health/health-view-nodes-01.png)

Atualmente, apenas o estado de um kubelet virtual é suportado. O estado de saúde da CPU e a utilização da memória dos nós de kublet virtuais é reportado como **Desconhecido**, uma vez que um sinal não é recebido deles.

Todos os monitores são mostrados em um layout hierárquico no painel da Hierarquia da Saúde, onde um monitor agregado que representa o objeto Kubernetes ou abstração (isto é, infraestrutura de Kubernetes ou Nó) são o monitor mais alto que reflete a saúde combinada de todos os monitores de crianças dependentes. Os principais cenários de monitorização utilizados para obter a saúde são:

* Avalie a utilização do CPU a partir do nó e do recipiente.
* Avalie a utilização da memória do nó e do recipiente.
* Estado de Pods e Nó com base no cálculo do seu estado pronto reportado pela Kubernetes.

Os ícones utilizados para indicar o estado são os seguintes:

|Ícone|Significado|  
|--------|-----------|  
|![Ícone em forma de visto a verde indica um bom estado de funcionamento](./media/container-insights-health/healthyicon.png)|Êxito, o estado de funcionamento está OK (verde)|  
|![Um triângulo amarelo acompanhado de um ponto de exclamação indica um aviso](./media/container-insights-health/warningicon.png)|Aviso (amarelo)|  
|![Um botão vermelho com um X branco indica um estado crítico](./media/container-insights-health/criticalicon.png)|Crítico (vermelho)|  
|![Ícone cinza-out](./media/container-insights-health/grayicon.png)|Desconhecido (cinza)|  

## <a name="monitor-configuration"></a>Configuração do monitor

Para compreender o comportamento e configuração de cada monitor de apoio ao Monitor Azure para os recipientes Função de saúde, consulte o guia de [configuração do monitor de saúde](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Ver saúde de um cluster AKS ou não-AKS

O acesso ao Monitor Azure para recipientes A funcionalidade Saúde (pré-visualização) está disponível diretamente a partir de um cluster AKS, selecionando **Insights** a partir do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Containers**. 

Para ver a saúde a partir de um cluster não-AKS, que é um cluster de motor AKS hospedado no local ou em Azure Stack, selecione **Azure Monitor** a partir do painel esquerdo no portal Azure. Na secção **Insights,** selecione **Containers**.  Na página multi-cluster, selecione o cluster não-AKS da lista.

No Monitor Azure para recipientes, a partir da página **Cluster,** selecione **Health**.

![Exemplo do painel de saúde do cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Rever a saúde do cluster

Quando a página Saúde abre, por defeito a **Infraestrutura kubernetes** é selecionada na grelha **De Aspeto de Saúde.**  A grelha resume o estado atual da saúde da infraestrutura de Kubernetes e dos nós de cluster. Selecionar qualquer um dos aspetos de saúde atualiza os resultados no painel da Hierarquia da Saúde (isto é, o painel intermédio) e mostra todos os monitores de crianças num layout hierárquico, mostrando o seu estado de saúde atual. Para ver mais informações sobre qualquer monitor dependente, pode selecionar um e um painel de propriedade automaticamente aparece no lado direito da página. 

![Painel de propriedade de saúde cluster](./media/container-insights-health/health-view-property-pane.png)

No painel de propriedade, aprende-se o seguinte:

- No **separador Visão Geral,** mostra o estado atual do monitor selecionado, quando o monitor foi calculado pela última vez, e quando ocorreu a última alteração de estado. Informações adicionais são mostradas dependendo do tipo de monitor selecionado na hierarquia.

    Se selecionar um monitor agregado no painel da Hierarquia da Saúde, sob o **separador Visão Geral** no painel de propriedade, mostra um rollup do número total de monitores de crianças na hierarquia, e quantos monitores agregados estão em estado crítico, de alerta e saudável. 

    ![Painel de visão geral do painel de propriedade de saúde para monitor agregado](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se selecionar um monitor de unidade no painel da Hierarquia da Saúde, também mostra que no **último estado alteram** as amostras anteriores calculadas e comunicadas pelo agente contentorizado nas últimas quatro horas. Isto baseia-se no cálculo dos monitores da unidade para comparar vários valores consecutivos para determinar o seu estado. Por exemplo, se selecionar o monitor de unidade *estatal pronto pod,* mostra as duas últimas amostras controladas pelo parâmetro *ConsecutiveSamplesForStateTransition*. Para obter mais informações, consulte a descrição detalhada dos [monitores de unidade.](container-insights-health-monitors-config.md#unit-monitors)
    
    ![Painel de visão geral do painel de propriedade de saúde](./media/container-insights-health/health-overview-unit-monitor.png)

    Se o tempo reportado pela **Última mudança de estado** for um dia ou mais, é o resultado de nenhuma alteração de estado para o monitor. No entanto, se a última amostra recebida para um monitor de unidade tiver mais de quatro horas de idade, isso provavelmente indica que o agente contentorizado não tem enviado dados. Se o agente sabe que existe um determinado recurso, por exemplo um nó, mas não recebeu dados do CPU do nó ou dos monitores de utilização da memória (como exemplo), então o estado de saúde do monitor está definido para **Desconhecido**.  

- No**separador Config,** mostra as definições de parâmetros de configuração predefinidos (apenas para monitores de unidade, não monitores agregados) e os seus valores.
- No **separador Conhecimento,** contém informações que explicam o comportamento do monitor e como avalia para a condição pouco saudável.

Os dados de monitorização desta página não se atualizam automaticamente e é necessário selecionar **Refresh** no topo da página para ver o estado de saúde mais recente recebido do cluster.

## <a name="next-steps"></a>Próximos passos

Consulte [exemplos de consulta de registos](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos predefinidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

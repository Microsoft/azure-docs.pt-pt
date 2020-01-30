---
title: Monitorar a integridade do cluster kubernetes com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode exibir e analisar a integridade de seus clusters AKS e não AKS com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843995"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Compreender o estado de funcionamento de um cluster do Kubernetes com o Azure Monitor para contentores

Com Azure Monitor para contêineres, ele monitora e relata o status de integridade dos componentes de infraestrutura gerenciada e todos os nós em execução em qualquer cluster kubernetes com suporte de Azure Monitor para contêineres. Essa experiência se estende além do status de integridade do cluster calculado e relatado na [exibição de vários clusters](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), em que agora você pode entender se um ou mais nós no cluster estão com restrição de recursos, ou se um nó ou Pod está indisponível que poderia afetar um aplicativo em execução no cluster com base em métricas organizadas.

>[!NOTE]
>O recurso de integridade está em visualização pública no momento.
>

Para obter informações sobre como habilitar o Azure Monitor para contêineres, consulte [Azure monitor integrado para contêineres](container-insights-onboard.md).

>[!NOTE]
>Para dar suporte a clusters do mecanismo AKS, verifique se ele atende ao seguinte:
>- Ele está usando a versão mais recente do [cliente Helm](https://helm.sh/docs/using_helm/).
>- A versão do agente em contêiner é *Microsoft/OMS: ciprod11012019*. Para atualizar o agente, consulte [atualizando o agente no cluster kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Visão geral

Em Azure Monitor para contêineres, o recurso de integridade (versão prévia) fornece monitoramento proativo de integridade do cluster kubernetes para ajudá-lo a identificar e diagnosticar problemas. Ele oferece a capacidade de exibir problemas significativos detectados. Monitora a avaliação da integridade da execução do cluster no agente em contêiner no seu cluster, e os dados de integridade são gravados na tabela **KubeHealth** em seu espaço de trabalho log Analytics. 

A integridade do cluster kubernetes é baseada em vários cenários de monitoramento organizados pelos seguintes objetos kubernetes e abstrações:

- Infraestrutura kubernetes – fornece um ROLLUP do servidor de API kubernetes, ReplicaSets e DaemonSets em execução em nós implantados em seu cluster avaliando a utilização de CPU e memória e uma disponibilidade de pods

    ![Exibição de Rollup de integridade da infraestrutura do kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nós – fornece um ROLLUP dos pools de nós e o estado de nós individuais em cada pool, avaliando a utilização de CPU e memória e o status de um nó conforme relatado por kubernetes.

    ![Exibição de Rollup de integridade dos nós](./media/container-insights-health/health-view-nodes-01.png)

Atualmente, há suporte apenas para o status de um kubelet virtual. O estado de integridade para utilização de CPU e memória de nós kublet virtuais é relatado como **desconhecido**, uma vez que um sinal não é recebido deles.

Todos os monitores são mostrados em um layout hierárquico no painel hierarquia de integridade, em que um monitor agregado que representa o objeto kubernetes ou a abstração (ou seja, infraestrutura kubernetes ou nós) são o monitor mais alto que reflete a integridade combinada de todos monitores filho dependentes. Os principais cenários de monitoramento usados para derivar a integridade são:

* Avalie a utilização da CPU do nó e do contêiner.
* Avalie a utilização de memória do nó e do contêiner.
* Status de pods e nós com base no cálculo de seu estado pronto relatado pelo kubernetes.

Os ícones usados para indicar o estado são os seguintes:

|Ícone|Significado|  
|--------|-----------|  
|![Ícone de verificação verde indica saudável](./media/container-insights-health/healthyicon.png)|Sucesso, saúde é OK (verde)|  
|![Triângulo amarelo e marca de exclamação é aviso](./media/container-insights-health/warningicon.png)|Aviso (amarelo)|  
|![Botão vermelho com X branco indica estado crítico](./media/container-insights-health/criticalicon.png)|Crítico (vermelho)|  
|![Ícone de cinza](./media/container-insights-health/grayicon.png)|Desconhecido (cinza)|  

## <a name="monitor-configuration"></a>Configuração do monitor

Para entender o comportamento e a configuração de cada monitor com suporte para Azure Monitor recurso de integridade de contêineres, consulte [Guia de configuração do Health Monitor](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Exibir a integridade de um cluster AKS ou não AKS

O acesso ao recurso de integridade do Azure Monitor para contêineres (versão prévia) está disponível diretamente de um cluster AKS selecionando **insights** no painel esquerdo na portal do Azure. Sob o **Insights** secção, selecione **contentores**. 

Para exibir a integridade de um cluster não AKS, que é um cluster do AKS Engine hospedado localmente ou em Azure Stack, selecione **Azure monitor** no painel esquerdo na portal do Azure. Sob o **Insights** secção, selecione **contentores**.  Na página de vários clusters, selecione o cluster não AKS na lista.

Em Azure Monitor para contêineres, na página **cluster** , selecione **integridade**.

![Exemplo de painel de integridade do cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Examinar a integridade do cluster

Quando a página de integridade é aberta, por padrão, a **infraestrutura do kubernetes** é selecionada na grade de **aspecto de integridade** .  A grade resume o estado atual de Rollup de integridade da infraestrutura kubernetes e dos nós de cluster. Selecionar o aspecto de integridade atualiza os resultados no painel hierarquia de integridade (ou seja, o painel central) e mostra todos os monitores filho em um layout hierárquico, exibindo seu estado de integridade atual. Para exibir mais informações sobre qualquer monitor dependente, você pode selecionar um e um painel de propriedades é exibido automaticamente no lado direito da página. 

![Painel de propriedades de integridade do cluster](./media/container-insights-health/health-view-property-pane.png)

No painel de propriedades, você aprende o seguinte:

- Na guia **visão geral** , ele mostra o estado atual do monitor selecionado, quando o monitor foi calculado pela última vez e quando ocorreu a última alteração de estado. Informações adicionais são mostradas dependendo do tipo de monitor selecionado na hierarquia.

    Se você selecionar um monitor agregado no painel hierarquia de integridade, na guia **visão geral** do painel Propriedade, ele mostrará um ROLLUP do número total de monitores filho na hierarquia e quantos monitores agregados estão em um estado crítico, de aviso e íntegro. 

    ![Guia Visão geral do painel de propriedades de integridade para o monitor agregado](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se você selecionar um monitor de unidade no painel hierarquia de integridade, ele também será exibido em **último estado altere** as amostras anteriores calculadas e relatadas pelo agente em contêiner nas últimas quatro horas. Isso se baseia no cálculo dos monitores de unidade para comparar vários valores consecutivos para determinar seu estado. Por exemplo, se você tiver selecionado o monitor de unidade de *estado pronto para o Pod* , ele mostrará as duas últimas amostras controladas pelo parâmetro *ConsecutiveSamplesForStateTransition*. Para obter mais informações, consulte a descrição detalhada dos [monitores de unidade](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Guia Visão geral do painel de propriedades de integridade](./media/container-insights-health/health-overview-unit-monitor.png)

    Se a hora relatada pela **última alteração de estado** for um dia ou mais, será o resultado de nenhuma alteração no estado do monitor. No entanto, se o último exemplo recebido para um monitor de unidade tiver mais de quatro horas de idade, isso provavelmente indicará que o agente em contêiner não enviou dados. Se o agente souber que existe um recurso específico, por exemplo, um nó, mas ele não recebeu dados dos monitores de utilização de CPU ou memória do nó (como exemplo), o estado de integridade do monitor será definido como **desconhecido**.  

- Na guia**configuração** , ele mostra as configurações de parâmetro de configuração padrão (somente para monitores de unidade, não para monitores agregados) e seus valores.
- Na guia **conhecimento** , ele contém informações explicando o comportamento do monitor e como ele é avaliado para a condição não íntegra.

Os dados de monitoramento nesta página não são atualizados automaticamente e você precisa selecionar **Atualizar** na parte superior da página para ver o estado de integridade mais recente recebido do cluster.

## <a name="next-steps"></a>Passos seguintes

Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.

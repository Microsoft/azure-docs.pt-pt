---
title: Alertas métricos do Monitor Azure para contentores
description: Este artigo analisa os alertas métricos recomendados do Azure Monitor para contentores em visualização pública.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: a81dfb3fab57b378a56bfa8ac8102d723a50dbbc
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695960"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Alertas métricos recomendados (pré-visualização) do Monitor Azure para contentores

Para alertar para problemas de recursos do sistema quando estão a sentir a procura máxima e a funcionar perto da capacidade, com o Azure Monitor para contentores criaria um alerta de registo com base nos dados de desempenho armazenados em Registos monitores Azure. O Azure Monitor para contentores inclui agora regras de alerta métrica pré-configuradas para o seu cluster AKS e Azure Arc ativados kubernetes, que está em pré-visualização pública.

Este artigo revê a experiência e fornece orientações sobre a configuração e gestão destas regras de alerta.

Se não estiver familiarizado com os alertas do Azure Monitor, consulte [a visão geral dos alertas no Microsoft Azure](../platform/alerts-overview.md) antes de começar. Para saber mais sobre alertas métricos, consulte [alertas métricos no Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme o seguinte:

* As métricas personalizadas só estão disponíveis num subconjunto de regiões de Azure. Uma lista de regiões apoiadas está documentada nas [regiões apoiadas.](../platform/metrics-custom-overview.md#supported-regions)

* Para suportar alertas métricos e a introdução de métricas adicionais, a versão de agente mínimo necessária é **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** para AKS e **mcr.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** para o cluster Kubernetes ativado pelo Arco Azure.

    Para verificar se o seu cluster está a executar a versão mais recente do agente, pode:

    * Executar o comando: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . No estado devolvido, note o valor em **Imagem** para omsagent na secção *Contentores* da saída. 
    * No **separador Nodes,** selecione o nó de cluster e no painel **de propriedades** à direita, observe o valor na Etiqueta de Imagem do **Agente**.

    O valor mostrado para aKS deve ser a versão **ciprod05262020** ou posterior. O valor mostrado para o cluster Azure Arc habilitado para Kubernetes deve ser a versão **ciprod09252020** ou mais tarde. Se o seu cluster tiver uma versão mais antiga, consulte [Como atualizar o Azure Monitor para o agente de contentores](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) para obter a versão mais recente.

    Para obter mais informações relacionadas com a libertação do agente, consulte o [histórico de lançamento do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). Para verificar se as métricas estão a ser recolhidas, pode utilizar o explorador de métricas do Azure Monitor e verificar a partir do **espaço de nome métrico** que os insights estão **listados.** Se for, pode ir em frente e começar a configurar os alertas. Se não vir nenhuma métrica recolhida, o cluster Service Principal ou MSI está a perder as permissões necessárias. Para verificar se o SPN ou o MSI são membros da função de Editor de **Métricas de Monitorização,** siga os passos descritos na secção [Upgrade per cluster utilizando O Azure CLI](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) para confirmar e definir a atribuição de funções.

## <a name="alert-rules-overview"></a>Visão geral das regras de alerta

Para alertar sobre o que importa, o Azure Monitor para contentores inclui os seguintes alertas métricos para os seus clusters AKS e Azure Arc habilitados:

|Nome| Descrição |Limiar padrão |
|----|-------------|------------------|
|CPU de contentor médio % |Calcula a média do CPU utilizado por contentor.|Quando o uso médio do CPU por contentor é superior a 95%.| 
|Memória média do conjunto de contentores definidos % |Calcula a memória média do conjunto de trabalho utilizada por recipiente.|Quando o uso médio da memória por recipiente é superior a 95%. |
|% Média da CPU |Calcula a média do CPU usado por nó. |Quando a utilização média do NÓ CPU é superior a 80% |
|Percentagem média de utilização do disco |Calcula o uso médio do disco para um nó.|Quando o uso do disco para um nó é superior a 80%. |
|Utilização média persistente do volume % |Calcula o uso médio de PV por vagem. |Quando o uso médio de PV por vagem é superior a 80%.|
|Memória média do conjunto de trabalho % |Calcula a memória média do conjunto de trabalho para um nó. |Quando a memória média do conjunto de trabalho para um nó é superior a 80%. |
|Contagem de recipientes de reinicio |Calcula o número de recipientes de reinicio. | Quando o recipiente recomeça é superior a 0. |
|Contagem de cápsulas falhadas |Calcula se alguma cápsula em estado falhado.|Quando um número de cápsulas em estado falhado é maior que 0. |
|Estado de Node NotReady |Calcula se algum nó estiver no estado de NotReady.|Quando um número de nós no estado de NotReady é maior que 0. |
|OOM matou contentores |Calcula o número de contentores mortos pela OOM. |Quando um número de contentores mortos da OOM é maior que 0. |
|Cápsulas prontas % |Calcula o estado médio pronto das cápsulas. |Quando o estado pronto das cápsulas é inferior a 80%.|
|Contagem de empregos concluída |Calcula o número de empregos concluídos há mais de seis horas. |Quando o número de empregos com mais de seis horas é superior a 0.|

Existem propriedades comuns em todas estas regras de alerta:

* Todas as regras de alerta são baseadas em métricas.

* Todas as regras de alerta são desativadas por defeito.

* Todas as regras de alerta são avaliadas uma vez por minuto e olham para os últimos 5 minutos de dados.

* As regras de alerta não têm um grupo de ação atribuído por padrão. Pode adicionar um [grupo de ação](../platform/action-groups.md) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação enquanto edita a regra de alerta.

* Pode modificar o limiar das regras de alerta editando-as diretamente. No entanto, consulte as orientações fornecidas em cada regra de alerta antes de modificar o seu limiar.

As seguintes métricas baseadas em alerta têm características de comportamento únicas em comparação com as outras métricas:

* *Métrica de Trabalho só* é enviada quando há trabalhos que são concluídos mais do que há seis horas atrás.

* *containerRestartCount* métrica só é enviada quando há recipientes reiniciando.

* a métrica *oomKilledContainerCount* só é enviada quando há contentores mortos da OOM.

* *cpuExceededPercentage*, *memoryRssExceededPercentage*, e *memorySingSetExceedPercentage* são enviados quando o CPU, o Rss de memória e os valores do conjunto de trabalho de memória excedem o limiar configurado (o limiar padrão é de 95%). Estes limiares são exclusivos do limiar de estado de alerta especificado para a regra de alerta correspondente. Ou seja, se quiser recolher estas métricas e analisá-las do [explorador Métricas,](../platform/metrics-getting-started.md)recomendamos que configures o limiar para um valor inferior ao limiar de alerta. A configuração relacionada com as definições de recolha dos seus limiares de utilização de recursos de contentores pode ser ultrapassada no ficheiro ConfigMaps na secção `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . Consulte a secção [Configurar métricas de alerta ConfigMaps](#configure-alertable-metrics-in-configmaps) para obter detalhes relacionados com a configuração do seu ficheiro de configuração ConfigMap.

* *pvUsageExceededPercentage* é enviada quando a percentagem de utilização do volume persistente excede o limiar configurado (o limiar de incumprimento é de 60%). Este limiar é exclusivo do limiar de estado de alerta especificado para a regra de alerta correspondente. Ou seja, se quiser recolher estas métricas e analisá-las do [explorador Métricas,](../platform/metrics-getting-started.md)recomendamos que configures o limiar para um valor inferior ao limiar de alerta. A configuração relacionada com as definições de recolha para os limiares de utilização persistente do volume pode ser ultrapassada no ficheiro ConfigMaps na secção `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` . Consulte a secção [Configurar métricas de alerta ConfigMaps](#configure-alertable-metrics-in-configmaps) para obter detalhes relacionados com a configuração do seu ficheiro de configuração ConfigMap. A recolha de métricas de volume persistentes com reclamações no espaço *de nomes do sistema kube* são excluídas por padrão. Para ativar a recolha neste espaço de nomes, utilize a secção `[metric_collection_settings.collect_kube_system_pv_metrics]` no ficheiro ConfigMap. Consulte [as definições de recolha métrica](./container-insights-agent-config.md#metric-collection-settings) para mais detalhes.

## <a name="metrics-collected"></a>Métricas recolhidas

As seguintes métricas são ativadas e recolhidas, salvo especificação em contrário, como parte desta característica:

|Espaço de nome métrico |Metric |Descrição |
|---------|----|------------|
|Insights.container/nós |cpuUsageMillicores |Utilização do CPU em millicores por hospedeiro.|
|Insights.container/nós |cpuUsagePercentage |Percentagem de utilização do CPU por nó.|
|Insights.container/nós |memóriaRssBytes |Utilização RSS de memória em bytes por hospedeiro.|
|Insights.container/nós |memóriaSpercentage |Percentagem de utilização RSS de memória por hospedeiro.|
|Insights.container/nós |memoryWorkingSetBytes |Utilização do conjunto de trabalho da memória em bytes por anfitrião.|
|Insights.container/nós |memóriaWorkingSetPercentage |Trabalho de memória Definir percentagem de utilização por hospedeiro.|
|Insights.container/nós |nodesCount |Conde de nós por estatuto.|
|Insights.container/nós |diskUsedPercentage |Percentagem de disco utilizado no nó por dispositivo.|
|Insights.container/pods |podCount |Contagem de cápsulas por controlador, espaço de nome, nó e fase.|
|Insights.container/pods |concluídoJobsCount |Os trabalhos concluídos contam o limiar configurável do utilizador mais antigo (o padrão é de seis horas) pelo controlador, o espaço de nomes Kubernetes. |
|Insights.container/pods |reiniciarContainerCount |Contagem de contentores reinicia pelo controlador, espaço de nome Kubernetes.|
|Insights.container/pods |oomKilledContainerCount |Contagem de contentores OOMkilled por controlador, espaço de nome Kubernetes.|
|Insights.container/pods |podReadyPercentage |Percentagem de cápsulas em estado pronto pelo controlador, espaço de nome Kubernetes.|
|Insights.contentor/contentores |cpuExceededPercentage |Percentagem de utilização do CPU para recipientes que excedam o limiar configurável do utilizador (o padrão é 95.0) por nome do recipiente, nome do controlador, espaço de nome Kubernetes, nome do casulo.<br> Recolhido  |
|Insights.contentor/contentores |memoryRssExceededPercentage |Percentagem de RSS de memória para recipientes que excedam o limiar configurável do utilizador (o padrão é 95.0) por nome do recipiente, nome do controlador, espaço de nome Kubernetes, nome do pod.|
|Insights.contentor/contentores |memoryWorkingSetExceededPercentage |Percentagem de conjunto de trabalho de memória para recipientes que excedam o limiar configurável do utilizador (padrão é 95.0) por nome do recipiente, nome do controlador, espaço de nome Kubernetes, nome do casulo.|
|Insights.container/persistentevolumes |pvUsageExceededPercentage |Percentagem de utilização de PV para volumes persistentes que excedam o limiar configurável do utilizador (o padrão é 60.0) pelo nome de reclamação, espaço de nome Kubernetes, nome de volume, nome do casulo e nome do nó.

## <a name="enable-alert-rules"></a>Ativar regras de alerta

Siga estes passos para ativar os alertas métricos no Monitor Azure a partir do portal Azure. Para ativar a utilização de um modelo de Gestor de Recursos, consulte [Ativar com um modelo de Gestor de Recursos](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>No portal do Azure

Esta secção percorre a ativação do Monitor Azure para alerta métrico de contentores (pré-visualização) a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. O acesso ao Monitor Azure para alerta de métricas de contentores (pré-visualização) está disponível diretamente a partir de um cluster AKS, selecionando **Insights** a partir do painel esquerdo no portal Azure.

3. A partir da barra de comando, **selecione alertas recomendados**.

    ![Opção de alerta recomendado no Monitor Azure para contentores](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. O **painel de propriedade recomendado alerta** automaticamente no lado direito da página. Por predefinição, todas as regras de alerta da lista estão desativadas. Depois de selecionar **Ativar,** a regra de alerta é criada e as atualizações do nome da regra para incluir um link para o recurso de alerta.

    ![Painel de propriedades de alerta recomendado](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Depois de selecionar o **alternador Enable/Disable** para ativar o alerta, é criada uma regra de alerta e as atualizações do nome da regra para incluir um link para o recurso de alerta real.

    ![Ativar regra de alerta](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. As regras de alerta não estão associadas a um [grupo de ação](../platform/action-groups.md) para notificar os utilizadores de que foi desencadeado um alerta. Selecione **Nenhum grupo de ação atribuído** e na página **grupos de ação,** especifique um grupo de ação existente ou crie um grupo de ação selecionando **Add** or **Create**.

    ![Selecione um grupo de ação](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Ative com um modelo de Gestor de Recursos

Pode utilizar um modelo e um ficheiro de parâmetros do Azure Resource Manager para criar os alertas métricos incluídos no Azure Monitor.

Os passos básicos são os seguintes:

1. Faça o download de um ou todos os modelos disponíveis que descrevem como criar o alerta a partir do [GitHub.](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM)

2. Crie e utilize um [ficheiro de parâmetros](../../azure-resource-manager/templates/parameter-files.md) como JSON para definir os valores necessários para criar a regra de alerta.

3. Implemente o modelo a partir do portal Azure, PowerShell ou Azure CLI.

#### <a name="deploy-through-azure-portal"></a>Implementar através do portal Azure

1. Faça o download e guarde para uma pasta local, o modelo do Gestor de Recursos Azure e o ficheiro de parâmetros, para criar a regra de alerta utilizando os seguintes comandos:

2. Para implementar um modelo personalizado através do portal, selecione **Criar um recurso** a partir do portal [Azure](https://portal.azure.com).

3. Procure o **modelo** e, em seguida, selecione **a implementação do modelo**.

4. Selecione **Criar**.

5. Você vê várias opções para criar um modelo, **selecione Construa o seu próprio modelo no editor.**

6. Na página do **modelo editar,** selecione **o ficheiro de carga** e, em seguida, selecione o ficheiro do modelo.

7. Na página do **modelo editar,** selecione **Guardar**.

8. Na página **de implementação personalizada,** especifique o seguinte e, em seguida, quando completar a **seleção De compra** para implementar o modelo e criar a regra de alerta.

    * O grupo de recursos
    * A localização
    * Nome do Alerta
    * ID de recursos de cluster

#### <a name="deploy-with-azure-powershell-or-cli"></a>Implementar com Azure PowerShell ou CLI

1. Faça o download e guarde para uma pasta local, o modelo do Gestor de Recursos Azure e o ficheiro de parâmetros, para criar a regra de alerta utilizando os seguintes comandos:

2. Pode criar o alerta métrico utilizando o ficheiro de modelo e parâmetros utilizando o PowerShell ou o Azure CLI.

    Utilizar o Azure PowerShell

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Utilizar a CLI do Azure

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Embora o alerta métrico possa ser criado num grupo de recursos diferente do recurso alvo, recomendamos a utilização do mesmo grupo de recursos que o seu recurso alvo.

## <a name="edit-alert-rules"></a>Editar regras de alerta

Pode ver e gerir o Azure Monitor para obter regras de alerta de contentores, para editar o seu limiar ou configurar um [grupo de ação](../platform/action-groups.md) para o seu cluster AKS. Embora possa executar estas ações a partir do portal Azure e do Azure CLI, também pode ser feito diretamente do seu cluster AKS no Azure Monitor para contentores.

1. A partir da barra de comando, **selecione alertas recomendados**.

2. Para modificar o limiar, no painel **de alertas recomendado,** selecione o alerta ativado. Na **regra Editar,** selecione os **critérios de Alerta** que pretende editar.

    * Para modificar o limiar da regra de alerta, selecione a **Condição**.
    * Para especificar um grupo de ação existente ou criar um grupo de ação, **selecione Adicionar** ou **Criar** no **grupo Action**

Para visualizar os alertas criados para as regras ativadas, no **painel de alertas recomendado** selecione Ver em **alertas**. Você é redirecionado para o menu de alerta para o cluster AKS, onde você pode ver todos os alertas atualmente criados para o seu cluster.

## <a name="configure-alertable-metrics-in-configmaps"></a>Configurar métricas alertáveis em ConfigMaps

Execute os seguintes passos para configurar o seu ficheiro de configuração ConfigMap para anular os limiares de utilização predefinidos. Estes passos são aplicáveis apenas para as seguintes métricas alertaveis:

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Editar o ficheiro ConfigMap YAML na secção `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` ou `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` .

   - Para modificar o limiar *cpuExceededPercentage* para 90% e iniciar a recolha desta métrica quando esse limiar for atingido e ultrapassado, configurar o ficheiro ConfigMap utilizando o seguinte exemplo:

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Para modificar o limiar *de pvUsageExceededPercentage* para 80% e iniciar a recolha desta métrica quando esse limiar for atingido e ultrapassado, configuure o ficheiro ConfigMap utilizando o seguinte exemplo:

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Executar o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um reinício rolante para todas as cápsulas omsagentes; Nem todos recomeçam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante ao seguinte exemplo e inclui o resultado: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Passos seguintes

- Consulte [exemplos de consulta](container-insights-log-search.md#search-logs-to-analyze-data) de registos para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.

- Para saber mais sobre o Azure Monitor e como monitorizar outros aspetos do seu cluster Kubernetes, consulte o desempenho do [cluster View Kubernetes](container-insights-analyze.md).
---
title: Como resolver o Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve como pode resolver problemas e resolver problemas com o Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403385"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Resolução de problemas do Monitor Azure para contentores

Quando configurar a monitorização do seu cluster azure Kubernetes Service (AKS) com o Azure Monitor para contentores, poderá encontrar um problema que impeça a recolha de dados ou o estado de reporte. Este artigo detalha algumas questões comuns e passos de resolução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante o embarque ou operação de atualização
Ao mesmo tempo que permite o Monitor Azure para contentores ou atualizar um cluster para suportar métricas de recolha, poderá receber um erro que se assemelhe ao seguinte - *o cliente <identidade> do utilizador" com o id de objeto '<objectid>' do utilizador não tem autorização para realizar a ação 'Microsoft.Authorization/roleAssignments/write' sobre* o âmbito

Durante o processo de embarque ou atualização, a atribuição da função de Editor de Métricas de **Monitorização** é tentada no recurso do cluster. O utilizador que inicie o processo para permitir o Monitor Azure para contentores ou a atualização para suportar a recolha de métricas deve ter acesso à **Microsoft.Autorizações/tarefas/autorização/autorização** de escrita no âmbito do recurso de cluster AKS. Apenas os membros das funções incorporadas do **Proprietário** e **do Administrador** de Acesso ao Utilizador têm acesso a esta permissão. Se as suas políticas de segurança exigirem a atribuição de permissões de nível granular, recomendamos que veja [funções personalizadas](../../role-based-access-control/custom-roles.md) e atribua-as aos utilizadores que o necessitem. 

Também pode conceder manualmente este papel do portal Azure executando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, digite **Kubernetes.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure Kubernetes**.
3. Na lista de clusters kubernetes, selecione um da lista.
2. A partir do menu à esquerda, clique no controlo de **acesso (IAM)**.
3. Selecione **+ Adicione** para adicionar uma atribuição de funções e selecione a função de Editor de **Métricas de Monitorização** e sob o tipo de caixa **Select** **AKS** para filtrar os resultados apenas nos principais de serviço de clusters definidos na subscrição. Selecione o da lista específico para esse cluster.
4. Selecione **Guardar** para terminar a atribuição da função. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>O Monitor Azure para contentores está habilitado, mas não reporta qualquer informação
Se o Monitor Azure para contentores estiver ativado e configurado com sucesso, mas não conseguir visualizar informações sobre o estado ou não fordevolvido nenhum resultado de uma consulta de registo, diagnostica o problema seguindo estes passos: 

1. Verifique o estado do agente executando o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve assemelhar-se à seguinte, o que indica que foi corretamente implantada:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o estado de implantação com a versão *06072018* do agente ou posteriormente utilizando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, o que indica que foi corretamente implantada:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o estado da cápsula para verificar se está a funcionar utilizando o comando:`kubectl get pods --namespace=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo com um estado de *execução* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os registos do agente. Quando o agente contentorizado é implantado, executa uma verificação rápida executando comandos OMI e exibe a versão do agente e do fornecedor. 

5. Para verificar se o agente foi destacado com sucesso, execute o comando:`kubectl logs omsagent-484hw --namespace=kube-system`

    O estatuto deve assemelhar-se ao seguinte exemplo:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela abaixo resume erros conhecidos que pode encontrar ao utilizar o Monitor Azure para contentores.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro`No data for selected filters`  | Poderá demorar algum tempo a estabelecer o fluxo de dados de monitorização para os clusters recentemente criados. Deixe pelo menos 10 a 15 minutos para que os dados apareçam para o seu cluster. |   
| Mensagem de erro`Error retrieving data` | Enquanto o cluster do Serviço Azure Kubernetes está a criar-se para monitorização da saúde e do desempenho, estabelece-se uma ligação entre o cluster e o espaço de trabalho Azure Log Analytics. Um espaço de trabalho log Analytics é usado para armazenar todos os dados de monitorização para o seu cluster. Este erro pode ocorrer quando o seu espaço de trabalho Log Analytics tiver sido eliminado. Verifique se o espaço de trabalho foi apagado e, se foi, terá de reativar a monitorização do seu cluster com o Monitor Azure para obter contentores e especificar um espaço de trabalho existente ou criar um novo espaço de trabalho. Para voltar a ativar, terá de [desativar](container-insights-optout.md) a monitorização do cluster e voltar a [ativar](container-insights-enable-new-cluster.md) o Monitor Azure para os contentores. |  
| `Error retrieving data`depois de adicionar O Monitor Azure para contentores através de az aks cli | Ao ativar `az aks cli`a utilização, o Monitor Azure para os contentores pode não estar corretamente implantado. Verifique se a solução está implantada. Para isso, vá ao seu espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **Soluções** do painel do lado esquerdo. Para resolver este problema, terá de recolocar a solução seguindo as instruções sobre [como implantar o Monitor Azure para contentores](container-insights-onboard.md) |  

Para ajudar a diagnosticar o problema, fornecemos um roteiro de resolução de problemas disponível [aqui.](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O Monitor Azure para contentores agente ReplicaSet Pods não está programado no cluster não-Azure Kubernetes

O Monitor Azure para o agente de contentores ReplicaSet Pods tem uma dependência dos seguintes selecionadores de nó nos nós do trabalhador (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os seus nós de trabalhador não tiverem etiquetas de nó anexadas, então o agente ReplicaSet Pods não será programado. Consulte os [selecionadores de etiquetas kubernetes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como fixar a etiqueta.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Gráficos de desempenho não mostram CPU ou memória de nós e contentores em um cluster não-Azure

O Monitor Azure para o agente de contentores Pods utiliza o ponto final do cAdvisor no agente do nó para recolher as métricas de desempenho. Verifique se o agente contentorizado no nó `cAdvisor port: 10255` está configurado para permitir a abertura de todos os nós do cluster para recolher métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Cluster kubernetes não-Azure não está a aparecer no Monitor Azure para contentores

Para ver o cluster não Azure Kubernetes no Monitor Azure para contentores, é necessário ler o acesso no espaço de trabalho log Analytics suportando esta Insight e no recurso de soluções Container Insights ***(espaço de trabalho*).**

## <a name="next-steps"></a>Passos seguintes

Com a monitorização habilitada a capturar métricas de saúde tanto para os nós e cápsulas do cluster AKS, estas métricas de saúde estão disponíveis no portal Azure. Para aprender a utilizar o Monitor Azure para contentores, consulte a [saúde do Serviço View Azure Kubernetes](container-insights-analyze.md).

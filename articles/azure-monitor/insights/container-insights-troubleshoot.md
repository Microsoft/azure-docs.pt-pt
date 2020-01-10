---
title: Como resolver problemas relacionados com o Azure Monitor para contentores | Documentos da Microsoft
description: Este artigo descreve como pode resolver problemas e resolver problemas com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403385"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Resolução de problemas do Azure Monitor para contentores

Quando configurar a monitorização do seu cluster do Azure Kubernetes Service (AKS) com o Azure Monitor para contentores, poderá ocorrer um problema que impede a recolha de dados ou comunicar o estado. Este artigo fornece detalhes sobre alguns problemas comuns e passos de resolução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de atualização ou integração
Ao habilitar Azure Monitor para contêineres ou atualizar um cluster para dar suporte à coleta de métricas, você pode receber um erro semelhante ao seguinte: *a identidade do usuário < cliente > ' with ID do objeto ' < o ObjectID do usuário > ' não tem autorização para executar a ação ' Microsoft. Authorization/roleAssignments/Write ' sobre o escopo*

Durante a integração ou o processo de atualização, é tentada a concessão da atribuição de função de **Editor de métricas de monitoramento** no recurso de cluster. O usuário que inicia o processo para habilitar a Azure Monitor para contêineres ou a atualização para dar suporte à coleção de métricas deve ter acesso à permissão **Microsoft. Authorization/roleAssignments/Write** no escopo de recurso de cluster AKs. Somente os membros das funções internas do **proprietário** e do **administrador de acesso do usuário** recebem acesso a essa permissão. Se suas políticas de segurança exigirem a atribuição de permissões de nível granular, recomendamos que você exiba [funções personalizadas](../../role-based-access-control/custom-roles.md) e atribua-as aos usuários que precisam dela. 

Você também pode conceder manualmente essa função da portal do Azure executando as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, digite **kubernetes**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure kubernetes**.
3. Na lista de clusters kubernetes, selecione um na lista.
2. No menu à esquerda, clique em **controle de acesso (iam)** .
3. Selecione **+ Adicionar** para adicionar uma atribuição de função e selecione a função de **Editor de métricas de monitoramento** e, na caixa **selecionar** , digite **AKs** para filtrar os resultados em apenas as entidades de serviço de clusters definidas na assinatura. Selecione aquele na lista específica para esse cluster.
4. Selecione **guardar** para concluir a atribuir a função. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Monitor do Azure para contentores está ativado mas não comunicam quaisquer informações
Se Azure Monitor para contêineres for habilitado e configurado com êxito, mas você não puder exibir informações de status ou nenhum resultado for retornado de uma consulta de log, você diagnosticará o problema seguindo estas etapas: 

1. Verifique o estado do agente ao executar o comando: 

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve assemelhar-se o seguinte, que indica que foi implementado devidamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Verifique o estado de implementação com a versão do agente *06072018* ou posteriormente utilizando o comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, o que indica que foi implementado devidamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Verifique o estado de pod para verificar se ele está em execução com o comando: `kubectl get pods --namespace=kube-system`

    A saída deve assemelhar-se o exemplo a seguir com o estado *em execução* para o omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Verifique os registos do agente. Quando o agente em contentores é implementado, ele executa uma verificação rápida ao executar comandos OMI e apresenta a versão do agente e fornecedor. 

5. Para verificar se o agente foi implantado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    O estado deve assemelhar-se o exemplo seguinte:

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

A tabela abaixo resume erros conhecidos que pode encontrar ao utilizar o Azure Monitor para contentores.

| Mensagens de erro  | Ação |  
| ---- | --- |  
| Mensagem de erro `No data for selected filters`  | Pode demorar algum tempo a estabelecer o fluxo de dados de monitorização para os clusters recém-criado. Aguarde pelo menos 10 a 15 minutos para que os dados sejam exibidos para o cluster. |   
| Mensagem de erro `Error retrieving data` | Enquanto o cluster do serviço kubernetes do Azure está configurando para monitoramento de integridade e desempenho, uma conexão é estabelecida entre o cluster e o espaço de trabalho do Azure Log Analytics. Uma área de trabalho do Log Analytics é utilizada para armazenar todos os dados de monitorização para o seu cluster. Esse erro pode ocorrer quando seu espaço de trabalho Log Analytics foi excluído. Verifique se o espaço de trabalho foi excluído e, se ele foi, será necessário reabilitar o monitoramento do cluster com Azure Monitor para contêineres e especificar um novo espaço de trabalho ou criar um existente. Para reabilitar, você precisará [desabilitar](container-insights-optout.md) o monitoramento do cluster e [habilitar](container-insights-enable-new-cluster.md) Azure monitor para contêineres novamente. |  
| `Error retrieving data` Depois de adicionar o Azure Monitor para contentores através da cli do az aks | Ao habilitar o monitoramento usando `az aks cli`, os Azure Monitor para contêineres podem não ser implantados corretamente. Verifique se a solução está implantada. Para tal, aceda à sua área de trabalho do Log Analytics e ver se a solução está disponível, selecionando **soluções** partir do painel no lado esquerdo. Para resolver este problema, terá de voltar a implementar a solução ao seguir as instruções [como implementar o Azure Monitor para contentores](container-insights-onboard.md) |  

Para ajudar a diagnosticar o problema, nós fornecemos um script de resolução de problemas disponível [aqui](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O Azure Monitor para os pods do agente de Réplicaset de contêineres não estão agendados no cluster kubernetes não Azure

Azure Monitor for containers o pods do agente de Réplicaset tem uma dependência nos seguintes seletores de nó nos nós de trabalho (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os nós de trabalho não tiverem rótulos de nó anexados, o pods de agente de Réplicaset não será agendado. Consulte [kubernetes atribuir rótulo seletores](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como anexar o rótulo.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram a CPU ou memória de nós e contêineres em um cluster não Azure

Azure Monitor para os pods de agente de contêineres usa o ponto de extremidade cAdvisor no agente de nó para coletar as métricas de desempenho. Verifique se o agente em contêineres no nó está configurado para permitir que `cAdvisor port: 10255` seja aberto em todos os nós no cluster para coletar métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>O cluster kubernetes não Azure não está aparecendo em Azure Monitor para contêineres

Para exibir o cluster kubernetes não Azure no Azure Monitor para contêineres, o acesso de leitura é necessário no espaço de trabalho Log Analytics que dá suporte a essa percepção e no recurso **ContainerInsights (*espaço de trabalho*)** da solução de informações do contêiner.

## <a name="next-steps"></a>Passos seguintes

Com a monitorização ativada para capturar métricas de estado de funcionamento para os nós de cluster do AKS e os pods, estas métricas de estado de funcionamento estão disponíveis no portal do Azure. Para saber como utilizar o Azure Monitor para contentores, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).

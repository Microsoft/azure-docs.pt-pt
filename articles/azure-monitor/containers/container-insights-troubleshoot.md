---
title: Como resolver problemas de informações sobre o contentor | Microsoft Docs
description: Este artigo descreve como pode resolver problemas e resolver problemas com insights do Contentor.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627120"
---
# <a name="troubleshooting-container-insights"></a>Insights do contentor de resolução de problemas

Ao configurar a monitorização do seu cluster Azure Kubernetes Service (AKS) com insights de Contentores, poderá encontrar um problema que impeça a recolha de dados ou o estado de reporte. Este artigo detalha algumas questões comuns e etapas de resolução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de integração ou atualização

Ao permitir insights do Contentor ou atualizar um cluster para suportar métricas de recolha, pode receber um erro semelhante ao seguinte - *O cliente <> de identidade do utilizador" com o id de objeto "<objectId>" não tem autorização para executar ação 'Microsoft.Autorização/funAsignments/write' sobre o âmbito*

Durante o processo de embarque ou atualização, a atribuição da função **de Editor de Métricas de Monitorização** é tentada no recurso cluster. O utilizador que inicia o processo para permitir insights do Contentor ou a atualização para suportar a recolha de métricas deve ter acesso à **microsoft.Autorização/funAsignments/write** permissão no âmbito do cluster AKS. Apenas os membros das funções incorporadas **do Proprietário** e **do Administrador** de Acesso ao Utilizador têm acesso a esta permissão. Se as suas políticas de segurança requerem a atribuição de permissões de nível granular, recomendamos que veja [as funções personalizadas](../../role-based-access-control/custom-roles.md) e a atribua aos utilizadores que o exijam.

Também pode conceder manualmente esta função a partir do portal Azure, realizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, **digite Kubernetes.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure Kubernetes**.
3. Na lista de agrupamentos de Kubernetes, selecione um da lista.
2. A partir do menu da esquerda, clique no **controlo de acesso (IAM)**.
3. Selecione **+ Adicione** para adicionar uma atribuição de função e selecione a função **de Editor de Métricas de Monitorização** e sob a caixa **Select** tipo **AKS** para filtrar os resultados apenas nos principais de serviço de clusters definidos na subscrição. Selecione o da lista específica para esse cluster.
4. **Selecione Guardar** para terminar a atribuição do papel.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Os insights do contentor estão ativados, mas não reportam qualquer informação

Se os insights do contentor estiverem ativados e configurados com sucesso, mas não conseguir visualizar informações sobre o estado ou não forem devolvidos resultados de uma consulta de registo, diagnostica o problema seguindo estes passos:

1. Verifique o estado do agente executando o comando:

    `kubectl get ds omsagent --namespace=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, que indica que foi corretamente implantada:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Se tiver nós do Windows Server, verifique o estado do agente executando o comando:

    `kubectl get ds omsagent-win --namespace=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, que indica que foi corretamente implantada:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Verifique o estado de implantação com a versão *06072018* ou posterior utilização do comando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo, que indica que foi corretamente implantada:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Verifique o estado da cápsula para verificar se está em funcionamento utilizando o comando: `kubectl get pods --namespace=kube-system`

    A saída deve assemelhar-se ao seguinte exemplo com um estatuto de *Running* for the omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Mensagens de erro

A tabela abaixo resume os erros conhecidos que pode encontrar durante a utilização de insights do Recipiente.

| Mensagens de erro  | Ação |
| ---- | --- |
| Mensagem de Erro `No data for selected filters`  | Poderá demorar algum tempo a estabelecer o fluxo de dados de monitorização para os clusters recentemente criados. Deixe pelo menos 10 a 15 minutos para que os dados apareçam para o seu cluster. |
| Mensagem de Erro `Error retrieving data` | Enquanto o cluster de serviço Azure Kubernetes está se estabelecendo para monitorização de saúde e desempenho, uma ligação é estabelecida entre o cluster e o espaço de trabalho Azure Log Analytics. Um espaço de trabalho Log Analytics é utilizado para armazenar todos os dados de monitorização do seu cluster. Este erro pode ocorrer quando o seu espaço de trabalho Log Analytics foi eliminado. Verifique se o espaço de trabalho foi apagado e se foi, terá de voltar a ativar a monitorização do seu cluster com insights de Contentores e especificar um espaço de trabalho existente ou criar um novo espaço de trabalho. Para voltar a ativar, terá de [desativar](container-insights-optout.md) a monitorização do cluster e [voltar a ativar](container-insights-enable-new-cluster.md) as informações do Contentor. |
| `Error retrieving data` depois de adicionar insights de recipiente através de az aks cli | Quando ativar a `az aks cli` monitorização, os insights do recipiente podem não ser corretamente implantados. Verifique se a solução está implantada. Para verificar, vá ao seu espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **Soluções** a partir do painel do lado esquerdo. Para resolver este problema, terá de recolocar a solução seguindo as instruções sobre [como implantar insights do Contentor](container-insights-onboard.md) |

Para ajudar a diagnosticar o problema, fornecemos um [roteiro de resolução de problemas.](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O agente de insights de contentores ReplicaSet Pods não está programado no cluster não-Azure Kubernetes

O agente de insights de contentores ReplicaSet Pods tem uma dependência dos seguintes nós de nó no trabalhador (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os seus nós de trabalhador não tiverem etiquetas de nó anexadas, então o agente ReplicaSet Pods não será programado. Consulte [os Kubernetes atribuir seletores de etiquetas](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como fixar a etiqueta.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram CPU ou memória de nódoas e contentores num cluster não-Azure

O agente de insights de contentores Pods usa o ponto final do cAdvisor no agente de nó para recolher as métricas de desempenho. Verifique se o agente contentorizado do nó está configurado para permitir que `cAdvisor port: 10255` sejam abertos em todos os nós do cluster para recolher métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>O cluster não-Azure Kubernetes não está a aparecer em insights de contentores

Para visualizar o cluster não-Azure Kubernetes em insights de contentores, é necessário ler o acesso no espaço de trabalho Log Analytics que suporta este Insight e no recurso de solução **ContainerInsights *(espaço de trabalho)*.**

## <a name="metrics-arent-being-collected"></a>As métricas não estão a ser recolhidas

1. Verifique se o cluster se encontra numa [região suportada para métricas personalizadas](../essentials/metrics-custom-overview.md#supported-regions).

2. Verifique se existe a atribuição de funções **do Editor de Métricas de Monitorização** utilizando o seguinte comando CLI:

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    Para clusters com MSI, o utilizador designado identificação do cliente para alterações omsagent cada vez que a monitorização é ativada e desativada, pelo que a atribuição de funções deve existir no id do cliente MSI atual. 

3. Para clusters com identidade de pod do Azure Ative Directory habilitados e utilizando o MSI:

   - Verifique a etiqueta requerida **kubernetes.azure.com/managedby: as aks**  estão presentes nas cápsulas omsagent utilizando o seguinte comando:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Verifique se as exceções são ativadas quando a identidade do casulo é ativada utilizando um dos métodos suportados em https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity .

        Executar o seguinte comando para verificar:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Deve receber uma saída semelhante à seguinte:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>Passos seguintes

Com a monitorização possibilitada para capturar métricas de saúde tanto para os nós do cluster AKS como para as cápsulas, estas métricas de saúde estão disponíveis no portal Azure. Para aprender a utilizar insights de recipiente, consulte [a saúde do Serviço Azure Kubernetes.](container-insights-analyze.md)

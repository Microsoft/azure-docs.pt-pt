---
title: Como resolver problemas monitor Azure Monitor para contentores Microsoft Docs
description: Este artigo descreve como pode resolver problemas com o Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5727702ff973523ce7ab6400c1c7748e0584acbf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010593"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Resolver problemas do Azure Monitor para contentores

Ao configurar a monitorização do seu cluster Azure Kubernetes Service (AKS) com o Azure Monitor para contentores, poderá encontrar um problema que impeça a recolha de dados ou o estado de reporte. Este artigo detalha algumas questões comuns e etapas de resolução de problemas.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Erro de autorização durante a operação de integração ou atualização

Ao permitir que o Azure Monitor para contentores ou a atualização de um cluster suporte a métricas de recolha, pode receber um erro semelhante ao seguinte - *O cliente <> de identidade do utilizador com o id de objeto "<objectId do utilizador>" não tem autorização para executar ação 'Microsoft.Autorização/funAsignments/write'*

Durante o processo de embarque ou atualização, a atribuição da função **de Editor de Métricas de Monitorização** é tentada no recurso cluster. O utilizador que inicia o processo para permitir o Azure Monitor para contentores ou a atualização para suportar a recolha de métricas deve ter acesso à **microsoft.Autorização/funAsignments/write** permission on the AKS cluster resource scope. Apenas os membros das funções incorporadas **do Proprietário** e **do Administrador** de Acesso ao Utilizador têm acesso a esta permissão. Se as suas políticas de segurança requerem a atribuição de permissões de nível granular, recomendamos que veja [as funções personalizadas](../../role-based-access-control/custom-roles.md) e a atribua aos utilizadores que o exijam.

Também pode conceder manualmente esta função a partir do portal Azure, realizando os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, **digite Kubernetes.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Azure Kubernetes**.
3. Na lista de agrupamentos de Kubernetes, selecione um da lista.
2. A partir do menu da esquerda, clique no **controlo de acesso (IAM)**.
3. Selecione **+ Adicione** para adicionar uma atribuição de função e selecione a função **de Editor de Métricas de Monitorização** e sob a caixa **Select** tipo **AKS** para filtrar os resultados apenas nos principais de serviço de clusters definidos na subscrição. Selecione o da lista específica para esse cluster.
4. **Selecione Guardar** para terminar a atribuição do papel.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>O Monitor Azure para contentores está ativado, mas não reporta qualquer informação

Se o Monitor Azure para os contentores estiver habilitado e configurado com sucesso, mas não conseguir visualizar informações sobre o estado ou não for devolvido nenhum resultado de uma consulta de registo, diagnostica o problema seguindo estes passos:

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

A tabela abaixo resume os erros conhecidos que pode encontrar durante a utilização do Azure Monitor para recipientes.

| Mensagens de erro  | Ação |
| ---- | --- |
| Mensagem de Erro `No data for selected filters`  | Poderá demorar algum tempo a estabelecer o fluxo de dados de monitorização para os clusters recentemente criados. Deixe pelo menos 10 a 15 minutos para que os dados apareçam para o seu cluster. |
| Mensagem de Erro `Error retrieving data` | Enquanto o cluster de serviço Azure Kubernetes está se estabelecendo para monitorização de saúde e desempenho, uma ligação é estabelecida entre o cluster e o espaço de trabalho Azure Log Analytics. Um espaço de trabalho Log Analytics é utilizado para armazenar todos os dados de monitorização do seu cluster. Este erro pode ocorrer quando o seu espaço de trabalho Log Analytics foi eliminado. Verifique se o espaço de trabalho foi apagado e se foi, terá de voltar a permitir a monitorização do seu cluster com o Azure Monitor para contentores e especificar um espaço de trabalho existente ou criar um novo espaço de trabalho. Para voltar a ativar, terá de [desativar](container-insights-optout.md) a monitorização do cluster e voltar a [ativar](container-insights-enable-new-cluster.md) o Monitor Azure para os contentores. |
| `Error retrieving data` depois de adicionar Azure Monitor para contentores através de az aks cli | Quando ativar a `az aks cli` monitorização, o Monitor Azure para recipientes pode não ser corretamente acionado. Verifique se a solução está implantada. Para verificar, vá ao seu espaço de trabalho Log Analytics e veja se a solução está disponível selecionando **Soluções** a partir do painel do lado esquerdo. Para resolver este problema, terá de recolocar a solução seguindo as instruções sobre como implantar o [Monitor Azure para contentores](container-insights-onboard.md) |

Para ajudar a diagnosticar o problema, fornecemos um [roteiro de resolução de problemas.](https://aka.ms/troubleshooting-script)

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>O Monitor Azure para o agente de contentores ReplicaSet Pods não está programado no cluster não-Azure Kubernetes

O Azure Monitor para o agente de contentores ReplicaSet Pods tem uma dependência dos seguintes nós de nó no trabalhador (ou agente) para o agendamento:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Se os seus nós de trabalhador não tiverem etiquetas de nó anexadas, então o agente ReplicaSet Pods não será programado. Consulte [os Kubernetes atribuir seletores de etiquetas](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) para obter instruções sobre como fixar a etiqueta.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Os gráficos de desempenho não mostram CPU ou memória de nódoas e contentores num cluster não-Azure

O Azure Monitor para o agente de contentores Pods utiliza o ponto final do cAdvisor no agente de nó para recolher as métricas de desempenho. Verifique se o agente contentorizado do nó está configurado para permitir que `cAdvisor port: 10255` sejam abertos em todos os nós do cluster para recolher métricas de desempenho.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>O cluster não-Azure Kubernetes não está a aparecer no Azure Monitor para contentores

Para visualizar o cluster não-Azure Kubernetes no Azure Monitor para contentores, é necessário ler o acesso no espaço de trabalho Log Analytics que suporta este Insight e no recurso de solução Container Insights **ContainerInsights *(espaço de trabalho)***.

## <a name="next-steps"></a>Passos seguintes

Com a monitorização possibilitada para capturar métricas de saúde tanto para os nós do cluster AKS como para as cápsulas, estas métricas de saúde estão disponíveis no portal Azure. Para aprender a utilizar o Azure Monitor para recipientes, consulte [a saúde do Serviço Azure Kubernetes](container-insights-analyze.md).

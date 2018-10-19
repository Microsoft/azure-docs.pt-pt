---
title: Ver registos de controlador do Azure Kubernetes Service (AKS)
description: Saiba como ativar e ver os registos para o nó principal do Kubernetes no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 07/26/2018
ms.author: iainfou
ms.openlocfilehash: 7caa98b65b35d1eb1a000f1e9099fbf8eb3f8861
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406046"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Ativar e rever o Kubernetes no Azure Kubernetes Service (AKS) de registos de nó principal

Com o Azure Kubernetes Service (AKS), os componentes principais, tais como o *kube apiserver* e *Gestor de controladores de kube* são fornecidos como um serviço gerido. Criar e gerir os nós que executam o *kubelet* e tempo de execução do contentor e implementar as suas aplicações através do servidor de API do Kubernetes gerido. Para ajudar a resolver problemas de seus aplicativos e serviços, poderá ver os registos gerados por esses componentes mestres. Este artigo mostra-lhe como utilizar o Log Analytics do Azure para ativar e consultar os registos dos componentes de mestres de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer a um cluster do AKS existente em execução na sua conta do Azure. Se ainda não tiver um cluster do AKS, crie uma utilizando o [CLI do Azure] [ cli-quickstart] ou [portal do Azure][portal-quickstart]. O log Analytics funciona com ambas as RBAC e não-RBAC ativado clusters do AKS.

## <a name="enable-diagnostics-logs"></a>Ativar registos de diagnóstico

Para ajudar a recolher e analisar dados de várias origens, o Log Analytics fornece um mecanismo de análise e linguagem de consulta que fornece informações para o seu ambiente. Uma área de trabalho é utilizada para agrupar e analisar os dados e pode ser integrado com outros serviços do Azure como o Application Insights e o Centro de segurança. Utilizar uma plataforma diferente para analisar os registos, em vez disso, pode optar por enviar registos de diagnóstico para um hub de evento ou a conta de armazenamento do Azure. Para obter mais informações, consulte [o que é o Azure Log Analytics?] [log-analytics-overview].

Log Analytics é ativado e gerenciado no portal do Azure. Para ativar a recolha de registos para os componentes de mestres de Kubernetes no seu cluster do AKS, abra o portal do Azure num navegador da web e conclua os seguintes passos:

1. Selecione o grupo de recursos para o seu cluster do AKS, como *myResourceGroup*. Não selecione o grupo de recursos que contém os recursos de cluster do AKS individuais, como *MC_myResourceGroup_myAKSCluster_eastus*.
1. No lado esquerdo, selecione **das definições de diagnóstico**.
1. Selecione o seu cluster do AKS, como *myAKSCluster*, em seguida, optar por **ativar diagnósticos**.
1. Introduza um nome, tal como *myAKSLogs*, em seguida, selecione a opção de **enviar para o Log Analytics**.
    * Optar por *configurar* Log Analytics, em seguida, selecione uma área de trabalho existente ou **criar nova área de trabalho**.
    * Se precisar de criar uma área de trabalho, forneça um nome, um grupo de recursos e uma localização.
1. Na lista de registos disponíveis, selecione os registos que pretende ativar, tal como *kube apiserver*, *Gestor de controladores de kube*, e *kube scheduler*. Pode regressar e alterar os registos recolhidos depois de ativar o Log Analytics.
1. Quando estiver pronto, selecione **guardar** para ativar a recolha dos registos selecionados.

O portal de captura de ecrã de exemplo seguinte mostra os *as definições de diagnóstico* janela e, em seguida, a opção para criar uma área de trabalho do Log Analytics:

![Ativar a área de trabalho do Log Analytics para o cluster do Log Analytics do AKS](media/view-master-logs/enable-oms-log-analytics.png)

>[!NOTE]
>Áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics. 

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agendar um pod de teste no cluster do AKS

Para gerar alguns registos, crie um novo pod no cluster do AKS. O manifesto YAML de exemplo seguinte pode ser utilizado para criar uma instância NGINX básica. Crie um ficheiro denominado `nginx.yaml` num editor à sua escolha e cole o seguinte conteúdo:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Criar o pod com o [criar kubectl] [ kubectl-create] de comando e especifique o ficheiro YAML, conforme mostrado no exemplo a seguir:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Ver registos recolhidos

Pode demorar alguns minutos para que os registos de diagnóstico para ser ativada e aparece na área de trabalho do Log Analytics. No portal do Azure, selecione o grupo de recursos para sua área de trabalho do Log Analytics, como *myResourceGroup*, em seguida, escolha o seu recurso do Log Analytics, tal como *myAKSLogs*.

![Selecione a área de trabalho do Log Analytics para o seu cluster do AKS](media/view-master-logs/select-log-analytics-workspace.png)

No lado esquerdo, selecione **pesquisa de registos**. Para ver os *kube apiserver*, introduza a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Provavelmente, muitos registos são devolvidos para o servidor de API. Para definir o âmbito para baixo a consulta para ver os registos sobre o pod NGINX criado no passo anterior, adicione um adicionais *em que* instrução para procurar *pods/nginx* conforme mostrado na seguinte consulta de exemplo:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Os registos específicos para seu pod NGINX são exibidos, conforme mostrado na captura de ecrã de exemplo seguinte:

![Os resultados de pod NGINX de exemplo de consulta do log Analytics](media/view-master-logs/log-analytics-query-results.png)

Para ver registos adicionais, pode atualizar a consulta para o *categoria* nome ao *Gestor de controladores de kube* ou *kube scheduler*, consoante o que registos adicionais Ative. Adicionais *onde* instruções, em seguida, podem ser utilizadas para refinar os eventos que está procurando.

Para obter mais informações sobre como consultar e filtrar os seus dados de registo, consulte [ver ou analisar os dados recolhidos com a pesquisa de registos do Log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Esquema de eventos de registo

Para ajudar a analisar os dados de registo, a tabela seguinte fornece detalhes sobre o esquema utilizado para cada evento:

| Nome do campo               | Descrição |
|--------------------------|-------------|
| *resourceId*             | Recursos do Azure que produziu o registo |
| *tempo*                   | Timestamp de quando o registo foi carregado |
| *category*               | Nome do contentor/componente gerar o registo |
| *operationName*          | Sempre *Microsoft.ContainerService/managedClusters/diagnositicLogs/Read* |
| *Properties.log*         | Texto completo do registo do componente do |
| *Properties.stream*      | *stderr* ou *stdout* |
| *Properties.pod*         | Nome de pod que vieram com o registo |
| *properties.containerID* | ID do contentor docker que provenientes deste registo |

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como ativar e rever os registos para os componentes de mestres de Kubernetes no seu cluster do AKS. Para monitorizar e resolver problemas ainda mais, pode também [ver os registos Kubelet] [ kubelet-logs] e [ativar o acesso de nó SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../log-analytics/log-analytics-tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md

---
title: Ver registos de comandos do Serviço Azure Kubernetes (AKS)
description: Saiba como ativar e ver os registos do nó mestre kubernetes no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 4d4485848bb81f9b745081bd999b3cd3e8101b41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299076"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Ativar e rever os registos de nó principal do Kubernetes no Azure Kubernetes Service (AKS)

Com o Azure Kubernetes Service (AKS), os componentes principais como o *kube-apiserver* e o *kube-controller-manager* são fornecidos como um serviço gerido. Cria e gere os nós que executam o tempo de execução do *kubelet* e do contentor e implementa as suas aplicações através do servidor API gerido de Kubernetes. Para ajudar a resolver problemas com a sua aplicação e serviços, poderá ter de visualizar os registos gerados por estes componentes principais. Este artigo mostra-lhe como utilizar registos do Azure Monitor para ativar e consultar os registos dos componentes principais de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer um cluster AKS existente na sua conta Azure. Se ainda não tiver um cluster AKS, crie um utilizando o portal [Azure CLI][cli-quickstart] ou [Azure][portal-quickstart]. Os registos do Azure Monitor funcionam com clusters AKS ativados por RBAC e não-RBAC.

## <a name="enable-resource-logs"></a>Ativar registos de recursos

Para ajudar a recolher e rever dados de várias fontes, os registos do Azure Monitor fornecem uma linguagem de consulta e motor de análise que fornece insights para o seu ambiente. Um espaço de trabalho é utilizado para coligir e analisar os dados, e pode integrar-se com outros serviços Azure, como Application Insights e Security Center. Para utilizar uma plataforma diferente para analisar os registos, pode optar por enviar registos de recursos para uma conta de armazenamento Azure ou centro de eventos. Para mais informações, veja [o que são os registos do Azure Monitor?][log-analytics-overview]

Os registos do Monitor Azure estão ativados e geridos no portal Azure. Para ativar a recolha de registos para os componentes principais de Kubernetes no seu cluster AKS, abra o portal Azure num navegador web e complete os seguintes passos:

1. Selecione o grupo de recursos para o seu cluster AKS, como *o myResourceGroup*. Não selecione o grupo de recursos que contém os seus recursos individuais de cluster AKS, como *MC_myResourceGroup_myAKSCluster_eastus*.
1. No lado esquerdo, escolha **as definições de Diagnóstico**.
1. Selecione o seu cluster AKS, como *o myAKSCluster,* e depois opte por adicionar a **definição de diagnóstico**.
1. Introduza um nome, como *myAKSClusterLogs,* selecione a opção de **Enviar para Registar analíticos**.
1. Selecione um espaço de trabalho existente ou crie um novo. Se criar um espaço de trabalho, forneça um nome de espaço de trabalho, um grupo de recursos e uma localização.
1. Na lista de registos disponíveis, selecione os registos que pretende ativar. Para este exemplo, ativar os registos *de auditoria de kube.* Os registos comuns incluem o *kube-apiserver,* *o controlador-controlador de kube*e *o kube-scheduler*. Pode retornar e alterar os registos recolhidos assim que os espaços de trabalho do Log Analytics estiverem ativados.
1. Quando estiver pronto, **selecione Guardar** para ativar a recolha dos registos selecionados.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agende uma cápsula de teste no cluster AKS

Para gerar alguns registos, crie uma nova cápsula no seu cluster AKS. O manifesto YAML, exemplo a seguir, pode ser usado para criar uma instância NGINX básica. Crie um ficheiro nomeado `nginx.yaml` num editor à sua escolha e cole os seguintes conteúdos:

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

Crie o pod com o [comando de criação de kubectl][kubectl-create] e especifique o seu ficheiro YAML, como mostra o seguinte exemplo:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Ver registos recolhidos

Pode levar alguns minutos para os registos de diagnóstico serem ativados e aparecer. No portal Azure, navegue para o seu cluster AKS e selecione **Logs** no lado esquerdo. Feche a janela *'Exemplo',* se aparecer.

No lado esquerdo, escolha **Logs**. Para ver os registos *de auditoria de kube,* insira a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Muitos registos são provavelmente devolvidos. Para examinar a consulta para visualizar os registos sobre o pod NGINX criado no passo anterior, adicione um adicional *onde* a declaração para procurar *nginx* como mostrado na seguinte consulta de exemplo:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Para obter mais informações sobre como consultar e filtrar os seus dados de registo, consulte [ver ou analisar dados recolhidos com pesquisa de registo de registo][analyze-log-analytics]de registos .

## <a name="log-event-schema"></a>Esquema de evento de log

AKS regista os seguintes eventos:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContentorInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContentorNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Heartbeat][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Rio Perf][log-schema-perf]

## <a name="log-roles"></a>Funções de Registo

| Função                     | Descrição |
|--------------------------|-------------|
| *aksService*             | O nome de exibição no registo de auditoria para a operação do plano de controlo (a partir do hcpService) |
| *masterclient*           | O nome de exibição no registo de auditoria do MasterClientCertificate, o certificado que obtém a az aks obter credenciais |
| *não declarado*             | O nome de exibição para ClientCertificate, que é usado por nós de agente |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e rever os registos dos componentes principais de Kubernetes no seu cluster AKS. Para monitorizar e resolver mais problemas, também pode [ver os registos de Kubelet][kubelet-logs] e [permitir o acesso ao nó SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf

---
title: Ver registos de controladores do Serviço Azure Kubernetes (AKS)
description: Saiba como ativar e ver os registos do nó principal kubernetes no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259386"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Ativar e rever os registos de nó mestre kubernetes no Serviço Azure Kubernetes (AKS)

Com o Serviço Azure Kubernetes (AKS), os principais componentes como o *kube-apiserver* e o gestor do *controlador kube* são fornecidos como um serviço gerido. Cria e gere os nós que executam o tempo de execução do *kubelet* e do contentor e implementa as suas aplicações através do servidor Kubernetes API gerido. Para ajudar a resolver problemas a sua aplicação e serviços, poderá ter de visualizar os registos gerados por estes componentes principais. Este artigo mostra-lhe como utilizar os registos do Monitor Azure para ativar e consultar os registos dos componentes principais kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer um cluster AKS existente na sua conta Azure. Se ainda não tiver um cluster AKS, crie um utilizando o portal [Azure CLI][cli-quickstart] ou [Azure][portal-quickstart]. Os registos do Monitor Azure funcionam com clusters AKS ativados por RBAC e não-RBAC.

## <a name="enable-diagnostics-logs"></a>Ativar registos de diagnóstico

Para ajudar a recolher e rever dados de várias fontes, os registos do Azure Monitor fornecem um motor de linguagem e análise de consulta que fornece informações sobre o seu ambiente. Um espaço de trabalho é usado para coligir e analisar os dados, e pode integrar-se com outros serviços Azure, como O Insights de Aplicação e Centro de Segurança. Para utilizar uma plataforma diferente para analisar os registos, pode optar por enviar registos de diagnóstico para uma conta de armazenamento Azure ou um centro de eventos. Para mais informações, consulte os registos do [Monitor Azure?][log-analytics-overview]

Os registos do Monitor Azure estão ativados e geridos no portal Azure. Para ativar a recolha de registos para os componentes principais kubernetes no seu cluster AKS, abra o portal Azure num navegador web e complete os seguintes passos:

1. Selecione o grupo de recursos para o seu cluster AKS, como *o myResourceGroup*. Não selecione o grupo de recursos que contém os seus recursos individuais de cluster AKS, como *MC_myResourceGroup_myAKSCluster_eastus*.
1. No lado esquerdo, escolha **as definições**de diagnóstico .
1. Selecione o seu cluster AKS, como *o myAKSCluster,* e depois opte por **adicionar a definição de diagnóstico**.
1. Introduza um nome, como *myAKSClusterLogs,* e, em seguida, selecione a opção de **Enviar para Registar Analytics**.
1. Selecione um espaço de trabalho existente ou crie um novo. Se criar um espaço de trabalho, forneça um nome de espaço de trabalho, um grupo de recursos e uma localização.
1. Na lista de registos disponíveis, selecione os registos que pretende ativar. Os registos comuns incluem o *kube-apiserver,* *o kube-controller-manager*e o *programador kube.* Pode ativar registos adicionais, tais como *a auditoria de kube* e o *cluster-autoscaler*. Pode devolver e alterar os registos recolhidos assim que os espaços de trabalho do Log Analytics estiverem ativados.
1. Quando estiver pronto, selecione **Guardar** para ativar a recolha dos registos selecionados.

A imagem do portal de exemplo seguinte mostra a janela de *definições de Diagnóstico:*

![Ativar o espaço de trabalho de Log Analytics para registos do Monitor Azure do cluster AKS](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Agende uma cápsula de teste no cluster AKS

Para gerar alguns registos, crie uma nova cápsula no seu cluster AKS. O seguinte exemplo de manifesto YAML pode ser usado para criar uma instância básica NGINX. Crie um ficheiro chamado `nginx.yaml` num editor à sua escolha e colhe o seguinte conteúdo:

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

Crie a cápsula com o [kubectl criar][kubectl-create] comando e especificar o seu ficheiro YAML, como mostra o seguinte exemplo:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Ver registos recolhidos

Pode levar alguns minutos para que os registos de diagnóstico estejam ativados e apareçam no espaço de trabalho do Log Analytics. No portal Azure, selecione o grupo de recursos para o seu espaço de trabalho Log Analytics, como o *myResourceGroup,* e depois escolha o seu recurso de análise de registo, como *myAKSLogs*.

![Selecione o espaço de trabalho log Analytics para o seu cluster AKS](media/view-master-logs/select-log-analytics-workspace.png)

Do lado esquerdo, escolha **Logs**. Para ver o *kube-apiserver,* introduza a seguinte consulta na caixa de texto:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Muitos registos são provavelmente devolvidos para o servidor API. Para examinar a consulta para ver os registos sobre a cápsula NGINX criada no passo anterior, adicione um adicional *onde* a declaração para procurar *cápsulas/nginx,* como mostra a seguinte consulta de exemplo:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

Os registos específicos da sua cápsula NGINX são apresentados, como mostra a seguinte imagem de exemplo:

![Resultados da consulta de análise de log para a amostra ngINX pod](media/view-master-logs/log-analytics-query-results.png)

Para visualizar registos adicionais, pode atualizar a consulta para o nome *categoria* para *kube-controller-manager* ou *kube-scheduler,* dependendo dos registos adicionais que ativar. Adicional *onde* as declarações podem então ser usadas para refinar os eventos que procura.

Para obter mais informações sobre como consultar e filtrar os seus dados de registo, consulte ['Ver' ou analisar dados recolhidos com pesquisa de registo slog analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Esquema de evento de log

Para ajudar a analisar os dados de registo, a tabela seguinte detalha o esquema utilizado para cada evento:

| Nome do campo               | Descrição |
|--------------------------|-------------|
| *recursosId*             | Recurso azure que produziu o tronco |
| *tempo*                   | Carimbo de tempo de quando o registo foi carregado |
| *categoria*               | Nome do recipiente/componente que gera o tronco |
| *operaçãoNome*          | Sempre *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *propriedades.log*         | Texto completo do registo do componente |
| *propriedades.stream*      | *stderr* ou *stdout* |
| *propriedades.pod*         | Nome de pod de que o tronco veio |
| *propriedades.containerID* | ID do recipiente de estivador este tronco veio de |

## <a name="log-roles"></a>Papéis de log

| Função                     | Descrição |
|--------------------------|-------------|
| *aksService*             | O nome do mostrador no registo de auditoria para a operação do plano de controlo (a partir do hcpService) |
| *cliente mestre*           | O nome de exibição no registo de auditoria do MasterClientCertificate, o certificado que obtém das credenciais get-credenciais az aks |
| *cliente de nó*             | O nome de exibição para ClientCertificate, que é usado por nós de agente |

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e rever os registos dos componentes principais da Kubernetes no seu cluster AKS. Para monitorizar e resolver ainda mais os problemas, também pode [ver os registos kubelet][kubelet-logs] e ativar o [acesso ao nó SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

---
title: Cápsulas AKS de escala automática com métricas de Gateway de aplicação Azure
description: Este artigo fornece instruções sobre como escalar as suas cápsulas de backend AKS utilizando métricas de Gateway de aplicação e adaptador métrico Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239454"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Calcifique automaticamente as suas cápsulas AKS utilizando métricas de gateway de aplicação (Beta)

À medida que o tráfego aumenta, torna-se crucial aumentar as suas aplicações com base na procura.

No seguinte tutorial, explicamos como pode usar `AvgRequestCountPerHealthyHost` a métrica do Application Gateway para aumentar a sua aplicação. `AvgRequestCountPerHealthyHost`mede os pedidos médios enviados para uma piscina específica de backend e combinação de definição http end.

Vamos usar dois componentes:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Usaremos o adaptador métrico para expor as métricas do Gateway aplicação através do servidor métrico. O Adaptador Métrico Azure Kubernetes é um projeto de código aberto no âmbito do Azure, semelhante ao Controlador de Entrada de Gateway de Aplicação. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Utilizaremos o HPA para utilizar as métricas do Gateway de aplicação e direcionar uma implementação para a escala.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Configuração do Adaptador Métrico Azure Kubernetes

1. Criaremos primeiro um diretor de serviço Azure `Monitoring Reader` AAD e atribuiremos-lhe acesso ao grupo de recursos da Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Agora, vamos implementar [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) o recurso ao principal de serviço AAD criado acima.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Criaremos `ExternalMetric` um recurso `appgw-request-count-metric`com nome. Este recurso instruirá o adaptador métrico para expor `AvgRequestCountPerHealthyHost` a métrica para `myApplicationGateway` o recurso no `myResourceGroup` grupo de recursos. Você pode `filter` usar o campo para direcionar uma piscina específica de backend e backend HTTP definição no Gateway de Aplicação.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Agora pode fazer um pedido ao servidor métrico para ver se a nossa nova métrica está a ser exposta:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Usando a nova métrica para aumentar a implantação

Assim que formos `appgw-request-count-metric` capazes de expor através [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) do servidor métrico, estamos prontos a usar para aumentar a nossa implementação de alvos.

Em exemplo, direcionaremos uma `aspnet`utilização da amostra. Escalaremos os Pods quando `appgw-request-count-metric` > 200 por `10` Pod até um máximo de Pods.

Substitua o nome de implementação do alvo e aplique a seguinte configuração de escala automática:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Teste a sua configuração utilizando uma ferramenta de teste de carga como o banco Apache:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Passos seguintes
- [**Problemas problemas do Controlador de Ingress:**](ingress-controller-troubleshoot.md)Problemas de resolução de problemas com o Controlador De Entrada.
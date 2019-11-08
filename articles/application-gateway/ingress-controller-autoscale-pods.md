---
title: Autoescalar AKS pods com métricas de gateway Aplicativo Azure
description: Este artigo fornece instruções sobre como dimensionar seus pods de back-end AKS usando métricas do gateway de aplicativo e o adaptador de métrica do kubernetes do Azure
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 0e1ba6d86778b40f96940c417050e242fde33845
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797585"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Dimensionamento automático de seu pods AKS usando métricas do gateway de aplicativo (beta)

À medida que o tráfego de entrada aumenta, ele se torna crucial para escalar verticalmente seus aplicativos com base na demanda.

No tutorial a seguir, explicamos como você pode usar a métrica de `AvgRequestCountPerHealthyHost` do gateway de aplicativo para escalar verticalmente seu aplicativo. `AvgRequestCountPerHealthyHost` mede as solicitações médias enviadas para um pool de back-end específico e uma combinação de configuração de HTTP de back-end

Vamos usar os dois componentes a seguir:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) – usaremos o adaptador de métrica para expor as métricas do gateway de aplicativo por meio do servidor de métrica. O adaptador do Azure kubernetes Metric é um projeto de software livre no Azure, semelhante ao controlador de entrada do gateway de aplicativo. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) – usaremos hPa para usar as métricas do gateway de aplicativo e direcionar uma implantação para dimensionamento.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Configurando o adaptador de métrica do Azure kubernetes

1. Primeiro, criaremos uma entidade de serviço do Azure AAD e atribuíremos `Monitoring Reader` acesso ao grupo de recursos do gateway de aplicativo. 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Agora, iremos implantar o [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) usando a entidade de serviço do AAD criada acima.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Criaremos um recurso de `ExternalMetric` com o nome `appgw-request-count-metric`. Esse recurso instruirá o adaptador de métrica a expor `AvgRequestCountPerHealthyHost` métrica para `myApplicationGateway` recurso no grupo de recursos `myResourceGroup`. Você pode usar o campo `filter` para direcionar um pool de back-end específico e uma configuração de HTTP de back-end no gateway de aplicativo.

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

Agora você pode fazer uma solicitação para o servidor de métrica para ver se nossa nova métrica está sendo exposta:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Usando a nova métrica para escalar verticalmente a implantação

Assim que pudermos expor `appgw-request-count-metric` por meio do servidor de métrica, estamos prontos para usar [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) para escalar verticalmente nossa implantação de destino.

No exemplo a seguir, iremos direcionar um exemplo de implantação `aspnet`. Dimensionaremos os pods quando `appgw-request-count-metric` > 200 por Pod até um máximo de `10` pods.

Substitua o nome da implantação de destino e aplique a seguinte configuração de dimensionamento automático:
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

Teste sua configuração usando uma ferramenta de teste de carga como o Apache bancada:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Passos seguintes
- [**Solucionar**](ingress-controller-troubleshoot.md)problemas do controlador de entrada: solucionar problemas com o controlador de entrada.
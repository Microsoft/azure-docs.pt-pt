---
title: Cápsulas AKS de escala automática com métricas de Gateway de aplicação Azure
description: Este artigo fornece instruções sobre como escalar as suas cápsulas de backend AKS usando métricas de Gateway de Aplicação e Adaptador Métrico Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397438"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Descalcificar automaticamente os seus pods AKS utilizando métricas de gateway de aplicação (Beta)

À medida que o tráfego de entrada aumenta, torna-se crucial aumentar as suas aplicações com base na procura.

No seguinte tutorial, explicamos como pode usar a métrica do Application Gateway `AvgRequestCountPerHealthyHost` para aumentar a sua aplicação. `AvgRequestCountPerHealthyHost` mede os pedidos médios enviados para uma combinação de definição http de backend específica e backend.

Vamos usar dois componentes:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) - Usaremos o adaptador métrico para expor as métricas do Gateway de Aplicação através do servidor métrico. O Adaptador Métrico Azure Kubernetes é um projeto de código aberto no âmbito do Azure, semelhante ao Controlador de Entradas de Gateway de Aplicação. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) - Utilizaremos o HPA para utilizar as métricas do Gateway de Aplicação e direcionamos uma implementação para dimensionamento.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Configuração do Adaptador Métrico Azure Kubernetes

1. Primeiro criaremos um diretor de serviço Azure AAD e atribuíremos-lhe `Monitoring Reader` acesso ao grupo de recursos da Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Agora, vamos implementar o [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) uso do principal de serviço AAD criado acima.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Criaremos um `ExternalMetric` recurso com `appgw-request-count-metric` nome. Este recurso instruirá o adaptador métrico a expor `AvgRequestCountPerHealthyHost` a métrica para `myApplicationGateway` o recurso no grupo de `myResourceGroup` recursos. Pode utilizar o `filter` campo para direcionar uma piscina de backend específica e uma definição HTTP de backend na definição de Gateway de aplicação.

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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Usando a nova métrica para escalar a implantação

Assim que formos capazes de expor `appgw-request-count-metric` através do servidor métrico, estamos prontos a usar [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) para escalar a nossa implementação de alvo.

Seguindo o exemplo, teremos como alvo uma implantação de `aspnet` amostras. Vamos escalar pods quando `appgw-request-count-metric` > 200 por Pod até um máximo de `10` Pods.

Substitua o nome de implantação do alvo e aplique a seguinte configuração de escala automática:
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

Teste a sua configuração utilizando uma ferramenta de teste de carga como banco apache:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Passos seguintes
- [**Problemas no controlador ingress :**](ingress-controller-troubleshoot.md)Resolução de problemas com o Controlador de Entradas.
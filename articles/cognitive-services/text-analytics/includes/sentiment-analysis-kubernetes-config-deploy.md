---
title: Análise de Sentimento Kubernetes config e implementar etapas
titleSuffix: Azure Cognitive Services
description: Análise de Sentimento Kubernetes config e implementar etapas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017901"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>Implementar o recipiente de Análise de Sentimento num cluster AKS

1. Abra o Azure CLI e inscreva-se em Azure.

    ```azurecli
    az login
    ```

1. Inscreva-se no cluster AKS. Substitua `your-cluster-name` e `your-resource-group` pelos valores adequados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Após este comando ser executado, reporta uma mensagem semelhante à seguinte:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se tiver várias subscrições disponíveis na sua conta Azure e o `az aks get-credentials` comando retornar com um erro, um problema comum é que está a utilizar a subscrição errada. Desconfie do contexto da sua sessão Azure CLI para utilizar a mesma subscrição com a que criou os recursos e tente novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto de eleição. Este exemplo utiliza o Código do Estúdio Visual.

    ```console
    code .
    ```

1. Dentro do editor de texto, crie um novo ficheiro chamado *sentiment.yaml,* e cole o yaML seguinte nele. Certifique-se de substituir `billing/value` e por sua própria `apikey/value` informação.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Guarde o ficheiro e feche o editor de texto.
1. Executar o comando Kubernetes `apply` com o ficheiro *sentiment.yaml* como alvo:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Após o comando aplicar com sucesso a configuração de implementação, uma mensagem aparece semelhante à seguinte saída:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Verifique se a cápsula foi implantada:

    ```console
    kubectl get pods
    ```

    A saída para o estado de funcionamento da cápsula:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída para o estado de funcionamento do serviço de *sentimento* na cápsula:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

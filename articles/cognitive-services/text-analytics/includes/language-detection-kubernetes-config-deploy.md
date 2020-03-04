---
title: Deteção de Idiomas Kubernetes config e desdobrar passos
titleSuffix: Azure Cognitive Services
description: Deteção de Idiomas Kubernetes config e desdobrar passos
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: addb044d82429a4471e7ecd302351dd7b7eada84
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262723"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Desloque o recipiente de deteção de idiomas para um cluster AKS

1. Abra o Azure CLI e inscreva-se no Azure.

    ```azurecli
    az login
    ```

1. Inscreva-se no aglomerado AKS. Substitua `your-cluster-name` e `your-resource-group` os valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Após a duração deste comando, reporta uma mensagem semelhante à seguinte:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se tiver várias subscrições disponíveis na sua conta Azure e o comando `az aks get-credentials` retorna com um erro, um problema comum é que está a usar a subscrição errada. Detete o contexto da sua sessão Azure CLI para usar a mesma subscrição com que criou os recursos e tentar novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto de eleição. Este exemplo utiliza o Código do Estúdio Visual.

    ```console
    code .
    ```

1. Dentro do editor de texto, crie um novo ficheiro chamado *language.yaml*, e cola o seguinte YAML nele. Certifique-se de substituir `billing/value` e `apikey/value` com as suas próprias informações.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
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
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. Guarde o ficheiro e feche o editor de texto.
1. Executar os Kubernetes `apply` comando com o ficheiro *language.yaml* como alvo:

    ```console
    kubectl apply -f language.yaml
    ```

    Após o comando aplicar com sucesso a configuração de implementação, uma mensagem aparece semelhante à seguinte saída:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. Verifique se a cápsula foi implantada:

    ```console
    kubectl get pods
    ```

    A saída para o estado de funcionamento da cápsula:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída para o estado de funcionamento do serviço *linguístico* na cápsula:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

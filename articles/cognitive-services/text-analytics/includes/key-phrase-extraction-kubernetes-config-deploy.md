---
title: Extração de frase-chave Kubernetes config e implementar etapas
titleSuffix: Azure Cognitive Services
description: Extração de frase-chave Kubernetes config e implementar etapas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 27c78566877f27e80ae5ae27c5250f228c7ae676
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779428"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Desloque o recipiente de extração de frases-chave para um cluster AKS

1. Abra o Azure CLI e inscreva-se em Azure.

    ```azurecli
    az login
    ```

1. Inscreva-se no cluster AKS. Substitua `your-cluster-name` e `your-resource-group` pelos valores adequados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Após este comando ser executado, reporta uma mensagem semelhante à seguinte:

    ```output
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

1. Dentro do editor de texto, crie um novo ficheiro chamado *keyphrase.yaml*, e cole o seguinte YAML nele. Certifique-se de substituir `billing/value` e por sua própria `apikey/value` informação.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Guarde o ficheiro e feche o editor de texto.
1. Executar o comando Kubernetes `apply` com o ficheiro *keyphrase.yaml* como alvo:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Após o comando aplicar com sucesso a configuração de implementação, uma mensagem aparece semelhante à seguinte saída:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Verifique se a cápsula foi implantada:

    ```console
    kubectl get pods
    ```

    A saída para o estado de funcionamento da cápsula:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída para o estado de funcionamento do serviço *de frases-chave* na cápsula:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

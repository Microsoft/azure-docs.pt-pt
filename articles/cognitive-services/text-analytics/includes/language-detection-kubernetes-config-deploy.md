---
title: Detecção de Idioma as etapas de configuração e implantação do kubernetes
titleSuffix: Azure Cognitive Services
description: Detecção de Idioma as etapas de configuração e implantação do kubernetes
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 2593f07ac30df77936c56785956b9e906ef683be
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051922"
---
## <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>Implantar o contêiner de Detecção de Idioma em um cluster AKS

1. Abra o CLI do Azure e entre no Azure.

    ```azurecli
    az login
    ```

1. Entre no cluster AKS. Substitua `your-cluster-name` e`your-resource-group` pelos valores apropriados.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Depois que esse comando é executado, ele relata uma mensagem semelhante à seguinte:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se você tiver várias assinaturas disponíveis em sua conta do Azure e o `az aks get-credentials` comando retornar com um erro, um problema comum é que você está usando a assinatura incorreta. Defina o contexto de sua sessão de CLI do Azure para usar a mesma assinatura com a qual você criou os recursos e tente novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto de sua escolha. Este exemplo usa Visual Studio Code.

    ```azurecli
    code .
    ```

1. No editor de texto, crie um novo arquivo chamado *Language. YAML*e cole o YAML a seguir nele. Certifique-se de `billing/value` substituir `apikey/value` e por suas próprias informações.

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

1. Salve o arquivo e feche o editor de texto.
1. Execute o comando `apply` kubernetes com o arquivo *Language. YAML* como seu destino:

    ```console
    kuberctl apply -f language.yaml
    ```

    Depois que o comando aplicar com êxito a configuração de implantação, uma mensagem será semelhante à seguinte saída:

    ```console
    deployment.apps "language" created
    service "language" created
    ```
1. Verifique se o Pod foi implantado:

    ```console
    kubectl get pods
    ```

    A saída do status de execução do pod:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verifique se o serviço está disponível e obtenha o endereço IP.

    ```console
    kubectl get services
    ```

    A saída do status de execução do serviço de *idioma* no pod:

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

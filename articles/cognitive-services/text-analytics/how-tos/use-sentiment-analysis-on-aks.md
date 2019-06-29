---
title: Executar serviços de Kubernetes do Azure
titleSuffix: Text Analytics - Azure Cognitive Services
description: Implementar os contentores de análise de texto com a imagem de análise de sentimentos, os serviços de Kubernetes do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454952"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>Implementar um contentor de análise de sentimentos para os serviços de Kubernetes do Azure (AKS)

Saiba como implementar os serviços cognitivos [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) contentor com a imagem de análise de sentimentos para os serviços de Kubernetes do Azure (AKS). Este procedimento exemplifica a criação de um recurso de análise de texto, a criação de uma imagem de análise de sentimentos associada e a capacidade de testar esta orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que tem de estar instaladas e executadas localmente. Não utilize o Azure Cloud shell.

* Utilize uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Editor de texto, por exemplo: [Visual Studio Code](https://code.visualstudio.com/download).
* Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Instalar o [CLI do Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
* Um recurso do Azure com o escalão de preço correto. Nem todos os escalões de preço trabalham com este contentor:
    * **Análise de texto** recurso com preços Standard ou de F0 camadas apenas.
    * **Os serviços cognitivos** recurso com o S0 escalão de preço.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>Implementar o contentor de análise de texto para um Cluster do AKS

1. Abra o CLI do Azure e o início de sessão no Azure

    ```azurecli
    az login
    ```

1. Inicie sessão para o cluster do AKS (substituir a `your-cluster-name` e `your-resource-group` com os valores adequados)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Após este comando é executado, ele relata uma mensagem semelhante ao seguinte:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se tiver várias subscrições disponíveis para si na sua conta do Azure e o `az aks get-credentials` comando devolve um erro, um problema comum é que está a utilizar uma subscrição errada. Basta definir o contexto da sessão da CLI do Azure para utilizar a mesma subscrição que criou os recursos com o e tente novamente.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Abra o editor de texto à escolha, (Este exemplo utiliza __Visual Studio Code__):

    ```azurecli
    code .
    ```

1. No editor de texto, crie um novo ficheiro designado _sentiment.yaml_ e cole o seguinte YAML na mesma:

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
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
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
1. Executar o Kubernetes `apply` comando com o _sentiment.yaml_ como seu destino:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    Depois do comando foi aplicada com êxito a configuração de implementação, saída é uma mensagem semelhante ao seguinte:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Certifique-se de que o POD foi implementado:

    ```console
    kubectl get pods
    ```

    Isto produzirá o status de execução do POD:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Certifique-se de que o serviço está disponível e obtenha o endereço IP:

    ```console
    kubectl get services
    ```

    Isto produzirá o status de execução do _sentimentos_ serviço no POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passos Seguintes

* Utilizar mais [contentores de serviços cognitivos](../../cognitive-services-container-support.md)
* Utilize o [serviço ligado de análise de texto](../vs-text-connected-service.md)
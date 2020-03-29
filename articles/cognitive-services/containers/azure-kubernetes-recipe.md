---
title: Executar recipiente de deteção de idiomas no serviço Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Desloque o recipiente de deteção de idiomas, com uma amostra em execução, para o Serviço Azure Kubernetes, e teste-o num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399166"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Implemente o recipiente de deteção de linguagem Text Analytics para o Serviço Azure Kubernetes

Aprenda a implementar o recipiente de deteção de linguagens. Este procedimento mostra como criar os contentores docker locais, empurrar os contentores para o seu próprio registo de contentores privados, executar o recipiente num cluster Kubernetes, e testá-lo num navegador web.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não utilize a concha Azure Cloud.

* Utilize uma assinatura Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Git](https://git-scm.com/downloads) para o seu sistema operativo para que possa clonar a [amostra](https://github.com/Azure-Samples/cognitive-services-containers-samples) utilizada neste procedimento.
* [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Docker motor](https://www.docker.com/products/docker-engine) e valide que o Docker CLI funciona numa janela de consola.
* [kubectl.](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)
* Um recurso Azure com o nível de preços correto. Nem todos os níveis de preços funcionam com este recipiente:
  * **Recurso de Análise** de Texto apenas com níveis de preços F0 ou Standard.
  * **Recursos dos Serviços Cognitivos** com o nível de preços S0.

## <a name="running-the-sample"></a>Executar o exemplo

Este procedimento carrega e executa a amostra do Recipiente de Serviços Cognitivos para deteção de linguagem. A amostra tem dois recipientes, um para a aplicação do cliente e outro para o recipiente de Serviços Cognitivos. Vamos levar estas duas imagens para o Registo de Contentores Azure. Uma vez que estejam no seu próprio registo, crie um Serviço Azure Kubernetes para aceder a estas imagens e executar os contentores. Quando os recipientes estiverem em funcionamento, utilize o **CLI kubectl** para observar o desempenho dos recipientes. Aceda à aplicação do cliente com um pedido HTTP e veja os resultados.

![Ideia conceptual de executar recipientes de amostra](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Os recipientes da amostra

A amostra tem duas imagens de contentores, uma para o site frontend. A segunda imagem é o recipiente de deteção de linguagem que devolve a linguagem (cultura) detetada do texto. Ambos os recipientes são acessíveis a partir de um IP externo quando estiver feito.

### <a name="the-language-frontend-container"></a>O recipiente de frente linguística

Este site é equivalente à sua própria aplicação do lado do cliente que faz pedidos do ponto final de deteção de idiomas. Quando o procedimento estiver concluído, obtém-se o idioma detetado de uma `http://<external-IP>/<text-to-analyze>`série de caracteres acedendo ao contentor do site num browser com . Um exemplo deste `http://132.12.23.255/helloworld!`URL é . O resultado no `English`navegador é .

### <a name="the-language-container"></a>O recipiente de línguas

O recipiente de deteção de linguagens, neste procedimento específico, está acessível a qualquer pedido externo. O recipiente não foi alterado de forma alguma, pelo que está disponível a API específica de deteção de linguagem específica dos Serviços Cognitivos.

Para este recipiente, essa API é um pedido post para deteção de idiomas. Tal como acontece com todos os recipientes dos Serviços Cognitivos, pode aprender mais sobre o recipiente a partir da sua informação de Swagger hospedada, `http://<external-IP>:5000/swagger/index.html`.

A porta 5000 é a porta padrão utilizada com os recipientes dos Serviços Cognitivos.

## <a name="create-azure-container-registry-service"></a>Criar serviço de registo de contentores Azure

Para implantar o recipiente no Serviço Azure Kubernetes, as imagens do recipiente precisam de ser acessíveis. Crie o seu próprio serviço de Registo de Contentores Azure para acolher as imagens.

1. Inscreva-se no Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Crie um `cogserv-container-rg` grupo de recursos nomeado para manter todos os recursos criados neste procedimento.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Crie o seu próprio Registo de Contentores `registry`Azure `pattyregistry`com o formato do seu nome então , como . Não utilize traços ou sublinhe caracteres no nome.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Guarde os resultados para obter a propriedade **loginServer.** Isto fará parte do endereço do contentor alojado, `language.yml` utilizado mais tarde no ficheiro.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Inscreva-se no registo do seu contentor. Tem de fazer login antes de poder empurrar as imagens para o seu registo.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Obtenha imagem do site Docker

1. O código de amostra utilizado neste procedimento está no repositório de amostras de recipientes dos Serviços Cognitivos. Clone o repositório para ter uma cópia local da amostra.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Uma vez que o repositório esteja no seu computador local, encontre o site no diretório [\dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Este site funciona como a aplicação do cliente chamando a API de deteção de idiomas alojada no recipiente de deteção de idiomas.  

1. Construa a imagem do Docker para este site. Certifique-se de que a consola está no diretório [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) onde o Dockerfile está localizado quando executa o seguinte comando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Para acompanhar a versão no registo do seu contentor, adicione `v1`a etiqueta com um formato de versão, como .

1. Empurre a imagem para o registo do seu contentor. Esta operação poderá demorar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Se tiver `unauthorized: authentication required` um erro, faça `az acr login --name <your-container-registry-name>` login com o comando. 

    Quando o processo estiver concluído, os resultados devem ser semelhantes a:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Obtenha imagem de Docker de deteção de linguagem

1. Puxe a versão mais recente da imagem do Docker para a máquina local. Esta operação poderá demorar alguns minutos. Se houver uma versão mais recente deste recipiente, altere o valor de `1.1.006770001-amd64-preview` para a versão mais recente.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Marque a imagem com o registo do seu contentor. Encontre a versão mais `1.1.006770001-amd64-preview` recente e substitua a versão se tiver uma versão mais recente. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Empurre a imagem para o registo do seu contentor. Esta operação poderá demorar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Obtenha credenciais de registo de contentores

São necessários os seguintes passos para obter as informações necessárias para ligar o registo de contentores ao Serviço Azure Kubernetes que cria mais tarde neste procedimento.

1. Crie o diretor de serviço.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Guarde `appId` o valor dos resultados para o `<appId>`parâmetro designado no passo 3, . Guarde `password` o parâmetro `<client-secret>`secreto do cliente da próxima secção.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Obtenha a sua identificação do registo de contentores.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Guarde a saída para o `<acrId>`valor do parâmetro de alcance, no passo seguinte. Parece que:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Guarde o valor total para o passo 3 nesta secção.

1. Para conceder o acesso correto para o cluster AKS utilizar imagens armazenadas no seu registo de contentores, crie uma atribuição de funções. `<appId>` Substitua `<acrId>` e com os valores recolhidos nos dois passos anteriores.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Criar serviço Azure Kubernetes

1. Crie o cluster do Kubernetes. Todos os valores do parâmetro são de secções anteriores, exceto o parâmetro de nome. Escolha um nome que indique quem o `patty-kube`criou e o seu propósito, como .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Este passo pode demorar alguns minutos. O resultado é:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    O serviço é criado mas ainda não tem o contentor do site ou recipiente de deteção de idiomas.  

1. Obtenha credenciais do aglomerado kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Carregue a definição de orquestração no seu serviço Kubernetes

Esta secção utiliza o **KUBectl** CLI para falar com o Serviço Azure Kubernetes.

1. Antes de carregar a definição de orquestração, verifique que **kubectl** tem acesso aos nós.

    ```console
    kubectl get nodes
    ```

    A resposta parece:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Copie o seguinte `language.yml`ficheiro e nomeie-o . O ficheiro `service` tem uma `deployment` secção e uma secção `language-frontend` para os `language` dois tipos de contentores, o contentor do site e o recipiente de deteção.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Altere as linhas de `language.yml` implementação do frontend linguístico com base na tabela seguinte para adicionar os nomes de imagens do registo de contentores, o segredo do cliente e as definições de análise de texto.

    Definições de implementação de frontend de idioma|Objetivo|
    |--|--|
    |Linha 32<br> `image`propriedade|Localização da imagem para a imagem frontal no seu Registo de Contentores<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Linha 44<br> `name`propriedade|Registo de contentores em segredo `<client-secret>` para a imagem, referido como numa secção anterior.|

1. Altere as linhas `language.yml` de implementação de idiomas com base na tabela seguinte para adicionar os nomes de imagens do registo de contentores, o segredo do cliente e as definições de análise de texto.

    |Definições de implementação de idiomas|Objetivo|
    |--|--|
    |Linha 78<br> `image`propriedade|Localização da imagem para a imagem linguística no seu Registo de Contentores<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Linha 95<br> `name`propriedade|Registo de contentores em segredo `<client-secret>` para a imagem, referido como numa secção anterior.|
    |Linha 91<br> `apiKey`propriedade|A sua chave de recursos de análise de texto|
    |Linha 92<br> `billing`propriedade|O ponto final da faturação para o seu recurso de análise de texto.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Como o **apiKey** e o ponto final de **faturação** são definidos como parte da definição de orquestração Kubernetes, o recipiente do site não precisa de saber sobre estes ou passá-los como parte do pedido. O recipiente do site refere-se ao recipiente `language`de deteção de idiomas pelo seu nome orquestrador.

1. Carregue o ficheiro de definição de orquestração para `language.yml`esta amostra a partir da pasta onde criou e guardou o .

    ```console
    kubectl apply -f language.yml
    ```

    A resposta é:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Obtenha IPs externos de recipientes

Para os dois contentores, verifique se os `language-frontend` serviços e `language` serviços estão em funcionamento e obtenha o endereço IP externo.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Se `EXTERNAL-IP` o serviço for apresentado como pendente, reecorra o comando até que o endereço IP seja mostrado antes de passar para o próximo passo.

## <a name="test-the-language-detection-container"></a>Testar o recipiente de deteção de linguagens

Abra um navegador e navegue `language` para o IP `http://<external-ip>:5000/swagger/index.html`externo do recipiente a partir da secção anterior: . Pode utilizar `Try it` a característica da API para testar o ponto final de deteção de idiomas.

![Veja a documentação do contentor](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testar o recipiente de aplicação do cliente

Altere o URL no navegador para `language-frontend` o IP externo `http://<external-ip>/helloworld`do recipiente utilizando o seguinte formato: . O texto da `helloworld` cultura inglesa `English`de é previsto como .

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com o cluster, elimine o grupo de recursos Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informações relacionadas

* [kubectl para Utilizadores de Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Recipientes de Serviços Cognitivos](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->
---
title: Executar o serviço do Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Implementar o contentor de deteção de idioma, com um exemplo em execução, o serviço de Kubernetes do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 1468b0a8a37ba1ec3455252b80139ae5db1e7af8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069189"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Implementar o contentor de deteção de idioma para o Azure Kubernetes Service

Saiba como implementar o contentor de deteção de idioma. Este procedimento mostra como criar os contentores de Docker locais, envie os contentores para o seu próprio registo privado de contentor, execute o contentor num cluster do Kubernetes e testá-la num navegador da web. 

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que tem de estar instaladas e executadas localmente. Não utilize o Azure Cloud shell. 

* Utilize uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Git](https://git-scm.com/downloads) para o seu sistema operativo para que pode clonar a [exemplo](https://github.com/Azure-Samples/cognitive-services-containers-samples) utilizado neste procedimento. 
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
* [Motor do docker](https://www.docker.com/products/docker-engine) e validar que a CLI do Docker funciona numa janela de consola.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe). 
* Um recurso do Azure com o escalão de preço correto. Nem todos os escalões de preço trabalham com este contentor:
    * **Análise de texto** recurso com preços Standard ou de F0 camadas apenas.
    * **Os serviços cognitivos** recurso com o S0 escalão de preço.

## <a name="running-the-sample"></a>Executar o exemplo

Este procedimento carrega e executa o exemplo de contentor de serviços cognitivos para deteção de idioma. O exemplo tem dois contentores, um para o aplicativo cliente e outro para o contentor de serviços cognitivos. Terá de enviar ambas estas imagens para o seu próprio registo de contentor do Azure. Assim que estão no seu próprio registo, crie um serviço de Kubernetes do Azure para aceder estas imagens e execute os contentores. Quando os contentores estão em execução, utilize o **kubectl** CLI para ver o desempenho de contentores. Aceder à aplicação de cliente com uma solicitação HTTP e ver os resultados. 

![Idéia conceitual da execução de contentores de exemplo](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Os contentores de exemplo

O exemplo tem duas imagens de contentor, um para o site de front-end. A segunda imagem é o contentor de deteção de idioma retornando o idioma detetado (cultura) de texto. Ambos os contentores são acessíveis a partir de um IP externo quando tiver terminado. 

### <a name="the-language-frontend-container"></a>O contentor de idioma-front-end

Este Web site é equivalente a sua própria aplicação do lado do cliente que faça pedidos do ponto final de deteção de idioma. Quando o procedimento estiver concluído, pode obter o idioma detetado de uma cadeia de caracteres ao aceder ao contentor do Web site num navegador com `http://<external-IP>/<text-to-analyze>`. Um exemplo deste URL é `http://132.12.23.255/helloworld!`. O resultado no browser é `English`.

### <a name="the-language-container"></a>O contentor de idioma

O contentor de deteção de idioma, este procedimento específico, está acessível a qualquer pedido externo. O contentor não foi alterado de qualquer forma, para que a deteção de linguagem específica do contentor de serviços cognitivos API padrão está disponível. 

Para este contentor, essa API é um pedido POST para deteção de idioma. Como com todos os contentores de serviços cognitivos, pode saber mais sobre o contentor a partir de suas informações de Swagger alojados, `http://<external-IP>:5000/swagger/index.html`. 

Porta 5000 é a porta predefinida utilizada com os contentores de serviços cognitivos. 

## <a name="create-azure-container-registry-service"></a>Criar serviço de registo de contentor do Azure

Para implementar o contentor para o serviço de Kubernetes do Azure, as imagens de contentor tem de estar acessíveis. Crie seu próprio serviço de registo de contentor do Azure para alojar as imagens. 

1. Entrar para a CLI do Azure 

    ```azurecli
    az login
    ```

1. Criar um grupo de recursos com o nome `cogserv-container-rg` para conter todos os recursos criados neste procedimento.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Criar, em seguida, o seu Azure Container Registry com o formato do nome da sua `registry`, tais como `pattyregistry`. Não utilize travessões ou sublinhado no nome da.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Guardar os resultados para obter o **loginServer** propriedade. Isso fará parte do endereço do contentor alojado, utilizado mais adiante a `language.yml` ficheiro.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Inicie sessão no seu registo de contentor. Tem de iniciar sessão antes de poder enviar imagens para o seu registo.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Obter imagem de Docker do Web site 

1. O código de exemplo utilizado neste procedimento está no repositório de exemplos de contentores de serviços cognitivos. Clone o repositório para ter uma cópia local do exemplo.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Assim que o repositório no seu computador local, encontre o Web site na [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) diretório. Este Web site funciona como a aplicação de cliente chamar a API alojada no contentor de deteção de idioma de deteção de idioma.  

1. Crie a imagem do Docker para este Web site. Certifique-se de que a consola está no [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) diretório onde o Dockerfile está localizado quando executar o seguinte comando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    Para controlar a versão no registo de contentores, adicione a etiqueta com um formato de versão, como `v1`. 

1. Envie a imagem para o seu registo de contentor. Esta operação poderá demorar alguns minutos. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Se obtiver um `unauthorized: authentication required` erro, o início de sessão com o `az acr login --name <your-container-registry-name>` comando. 

    Quando o processo for concluído, os resultados devem ser semelhantes a:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Obter imagem de Docker de deteção de idioma 

1. Obter a versão mais recente da imagem do Docker no computador local. Esta operação poderá demorar alguns minutos. Se existir uma versão mais recente desse contêiner, altere o valor de `1.1.006770001-amd64-preview` para a versão mais recente. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Imagem de marca com o seu registo de contentor. Localizar a versão mais recente e substituir a versão `1.1.006770001-amd64-preview` se tiver uma versão mais recente. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Envie a imagem para o seu registo de contentor. Esta operação poderá demorar alguns minutos. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Obter as credenciais do registo de contentor

Os seguintes passos são necessários para obter as informações necessárias para ligar o seu registo de contentor com o serviço de Kubernetes do Azure que cria mais tarde neste procedimento.

1. Crie principal de serviço.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Guardar os resultados `appId` valor para o parâmetro de detentor no passo 3, `<appId>`. Guardar a `password` para o parâmetro de segredo do cliente a próxima seção `<client-secret>`.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Obter o seu ID de registo de contentor.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Guarde a saída para o valor do parâmetro de âmbito, `<acrId>`, no próximo passo. Ele é semelhante a:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Guarde o valor total para o passo 3 nesta secção. 

1. Para conceder o acesso correto para o cluster do AKS utilizar imagens armazenadas no seu registo de contentor, crie uma atribuição de função. Substitua `<appId>` e `<acrId>` com os valores recolhidos nos dois passos anteriores.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Criar serviço Kubernetes do Azure

1. Crie o cluster do Kubernetes. Todos os valores de parâmetro são nas seções anteriores, exceto o parâmetro name. Escolha um nome que indica quem criou ele e sua finalidade, como `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Este passo pode demorar alguns minutos. O resultado é: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
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

    O serviço é criado, mas ele ainda não tiver o contentor de Web site ou o contentor de deteção de idioma.  

1. Obter credenciais de cluster de Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Carregar a definição de orquestração para o serviço do Kubernetes

Esta secção utiliza o **kubectl** CLI para falar com o serviço de Kubernetes do Azure. 

1. Antes de carregar a definição de orquestração, verifique **kubectl** tem acesso a nós.

    ```console
    kubectl get nodes
    ```

    A resposta é semelhante a:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Copie o seguinte ficheiro e nomeie- `language.yml`. O ficheiro tem um `service` secção e um `deployment` secção cada um para os tipos de dois contentor, o `language-frontend` contentor Web site e o `language` contentor de deteção. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Alterar as linhas de implementação de idioma-front-end de `language.yml` com base na tabela seguinte para adicionar seus próprios nomes de imagens do registo de contentor, o segredo do cliente e a definições de análise de texto.

    Definições de implementação de idioma-front-end|Objetivo|
    |--|--|
    |Line 32<br> `image` Propriedade|Localização de imagem para a imagem de front-end no seu registo de contentor<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Line 44<br> `name` Propriedade|Segredo de registo de contentor para a imagem, conhecido como `<client-secret>` numa secção anterior.|

1. Alterar as linhas de implementação de linguagem de `language.yml` com base na tabela seguinte para adicionar seus próprios nomes de imagens do registo de contentor, o segredo do cliente e a definições de análise de texto.

    |Definições de implementação de idioma|Objetivo|
    |--|--|
    |Linha 78<br> `image` Propriedade|Localização de imagem para a imagem de idioma no seu registo de contentor<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |95 de linha<br> `name` Propriedade|Segredo de registo de contentor para a imagem, conhecido como `<client-secret>` numa secção anterior.|
    |Line 91<br> `apiKey` Propriedade|A chave de recurso de análise de texto|
    |Line 92<br> `billing` Propriedade|O ponto final de faturação para o seu recurso de análise de texto.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Uma vez que o **apiKey** e **ponto final de faturação** estão definidas como parte da definição de orquestração de Kubernetes, o contentor de Web site não precisa saber sobre estes ou passá-los como parte do pedido. O contentor de Web site refere-se para o contentor de deteção de idioma por seu nome de orchestrator `language`. 

1. Carregar o ficheiro de definição de orquestração para este exemplo a partir da pasta onde criou e guardou o `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    A resposta é:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Obter IPs externos de contentores

Para os dois contentores, verifique se o `language-frontend` e `language` serviços estão em execução e obter o endereço IP externo. 

```console
kubectl get all
```

```console
> kubectl get all
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

Se o `EXTERNAL-IP` para o serviço é mostrado como pendente, volte a executar o comando até que o endereço IP é mostrado antes de passar para o passo seguinte. 

## <a name="test-the-language-detection-container"></a>Testar o contentor de deteção de idioma

Abra um browser e navegue para o IP externo do `language` contentor da secção anterior: `http://<external-ip>:5000/swagger/index.html`. Pode utilizar o `Try it` recurso da API para testar o ponto final de deteção de idioma. 

![Ver documentação de swagger do contentor](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testar o contentor da aplicação de cliente

Altere o URL no browser para o IP externo do `language-frontend` contentor com o seguinte formato: `http://<external-ip>/helloworld`. Os inglês cultura texto de `helloworld` se previr como `English`.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o cluster, elimine o grupo de recursos do Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informações relacionadas

* [kubectl para utilizadores do Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Passos Seguintes 

* Utilizar mais [contentores de serviços cognitivos](../../cognitive-services-container-support.md)
* Utilize a análise de texto de serviço ligado] (... / vs-texto-ligado-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->

---
title: Recipiente de deteção de idiomas executado no serviço Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Implemente o recipiente de deteção de idiomas, com uma amostra de execução, para o Serviço Azure Kubernetes e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6918218d8434c06f59b0738e60cad53b94b0a0b5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939844"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Implementar o recipiente de deteção de linguagem Text Analytics para o Serviço Azure Kubernetes

Saiba como implantar o recipiente de deteção de linguagem. Este procedimento mostra-lhe como criar os contentores estivadores locais, empurrar os contentores para o seu próprio registo de contentores privados, executar o contentor num cluster Kubernetes e testá-lo num navegador web.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não utilize a casca de Azure Cloud.

* Utilize uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.
* [Git](https://git-scm.com/downloads) para o seu sistema operativo para que possa clonar a [amostra](https://github.com/Azure-Samples/cognitive-services-containers-samples) utilizada neste procedimento.
* [Azure CLI](/cli/azure/install-azure-cli).
* [Docker motor](https://www.docker.com/products/docker-engine) e validar que o Docker CLI funciona numa janela de consola.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Um recurso Azure com o nível de preços correto. Nem todos os níveis de preços funcionam com este contentor:
  * **Recurso de análise** de texto apenas com níveis de preços F0 ou Standard.
  * **Recurso de Serviços Cognitivos** com o nível de preços S0.

## <a name="running-the-sample"></a>Executar o exemplo

Este procedimento carrega e executa a amostra do Recipiente de Serviços Cognitivos para deteção de linguagem. A amostra tem dois contentores, um para a aplicação do cliente e outro para o contentor dos Serviços Cognitivos. Vamos levar estas duas imagens para o Registo de Contentores de Azure. Uma vez que estejam no seu próprio registo, crie um Serviço Azure Kubernetes para aceder a estas imagens e executar os contentores. Quando os recipientes estiverem em funcionamento, utilize o **CLI de kubectl** para observar o desempenho dos recipientes. Aceda à aplicação do cliente com um pedido HTTP e veja os resultados.

![Ideia conceptual de correr recipientes de amostra](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Os recipientes de amostra

A amostra tem duas imagens de contentores, uma para o site frontend. A segunda imagem é o recipiente de deteção de linguagem que devolve a linguagem detetada (cultura) do texto. Ambos os recipientes estão acessíveis a partir de um IP externo quando terminar.

### <a name="the-language-frontend-container"></a>O recipiente de frente para a linguagem

Este site é equivalente à sua própria aplicação do lado do cliente que faz pedidos do ponto final de deteção de idiomas. Quando o procedimento estiver concluído, obtém-se o idioma detetado de uma série de caracteres acedendo ao recipiente do site num browser com `http://<external-IP>/<text-to-analyze>` . Um exemplo deste URL `http://132.12.23.255/helloworld!` é. O resultado no navegador é `English` .

### <a name="the-language-container"></a>O recipiente de linguagem

O recipiente de deteção de idiomas, neste procedimento específico, é acessível a qualquer pedido externo. O contentor não foi alterado de forma alguma, pelo que está disponível a API de deteção linguística específica dos Serviços Cognitivos padrão.

Para este recipiente, a API é um pedido de Deteção de Idiomas. Tal como acontece com todos os recipientes dos Serviços Cognitivos, pode saber mais sobre o recipiente a partir da sua informação swagger hospedada, `http://<external-IP>:5000/swagger/index.html` .

A porta 5000 é a porta padrão utilizada com os contentores dos Serviços Cognitivos.

## <a name="create-azure-container-registry-service"></a>Criar serviço de registo de contentores Azure

Para colocar o recipiente no Serviço Azure Kubernetes, as imagens do contentor devem estar acessíveis. Crie o seu próprio serviço de Registo de Contentores Azure para hospedar as imagens.

1. Inscreva-se no Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Crie um grupo de recursos nomeado `cogserv-container-rg` para deter todos os recursos criados neste procedimento.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Crie o seu próprio Registo de Contentores Azure com o formato do seu `registry` nome, por exemplo, como `pattyregistry` . Não utilize traços ou caracteres de sublinhado no nome.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Guarde os resultados para obter a propriedade **loginServer.** Isto fará parte do endereço do recipiente hospedado, usado mais tarde no `language.yml` ficheiro.

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

## <a name="get-website-docker-image"></a>Obtenha a imagem do site Docker

1. O código de amostra utilizado neste procedimento encontra-se no repositório de amostras de recipientes de serviços cognitivos. Clone o repositório para ter uma cópia local da amostra.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Assim que o repositório estiver no seu computador local, encontre o website no [diretório \dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Este site funciona como a aplicação do cliente que chama a API de deteção de idiomas hospedada no recipiente de deteção de idiomas.  

1. Construa a imagem do Docker para este site. Certifique-se de que a consola está no [diretório \FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) onde o Dockerfile está localizado quando executar o seguinte comando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Para acompanhar a versão no registo do seu contentor, adicione a etiqueta com um formato de versão, como `v1` .

1. Empurre a imagem para o registo do seu contentor. Esta operação poderá demorar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Se tiver um `unauthorized: authentication required` erro, faça login com o `az acr login --name <your-container-registry-name>` comando. 

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

## <a name="get-language-detection-docker-image"></a>Obtenha a deteção de linguagem Imagem docker

1. Puxe a última versão da imagem do Docker para a máquina local. Esta operação poderá demorar alguns minutos. Se houver uma versão mais recente deste recipiente, altere o valor para `1.1.006770001-amd64-preview` a versão mais recente.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Marque a imagem com o registo do seu contentor. Encontre a versão mais recente e substitua a versão `1.1.006770001-amd64-preview` se tiver uma versão mais recente. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Empurre a imagem para o registo do seu contentor. Esta operação poderá demorar alguns minutos.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Obtenha credenciais de registo de contentores

São necessárias as seguintes etapas para obter as informações necessárias para ligar o seu registo de contentores ao Serviço Azure Kubernetes que cria mais tarde neste procedimento.

1. Criar o principal de serviço.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Guarde o valor dos resultados `appId` para o parâmetro do destinatário no passo 3, `<appId>` . Guarde o `password` parâmetro secreto do cliente da próxima `<client-secret>` secção.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Obtenha a sua identificação de registo de contentores.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Guarde a saída para o valor do parâmetro de âmbito, `<acrId>` no passo seguinte. Parece que:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Guarde o valor total para o passo 3 nesta secção.

1. Para conceder o acesso correto para o cluster AKS utilizar imagens armazenadas no seu registo de contentores, crie uma atribuição de funções. Substitua `<appId>` e `<acrId>` pelos valores recolhidos nos dois passos anteriores.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Criar serviço Azure Kubernetes

1. Crie o cluster do Kubernetes. Todos os valores dos parâmetros são de secções anteriores, exceto o parâmetro do nome. Escolha um nome que indique quem o criou e o seu propósito, como `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Este passo pode levar alguns minutos. O resultado é:

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

    O serviço foi criado mas ainda não tem o recipiente do site ou o recipiente de deteção de idiomas.  

1. Obtenha credenciais do aglomerado de Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Carregue a definição de orquestração no seu serviço Kubernetes

Esta secção utiliza o **CLI kubectl** para falar com o Serviço Azure Kubernetes.

1. Antes de carregar a definição de orquestração, verifique **se kubectl** tem acesso aos nós.

    ```console
    kubectl get nodes
    ```

    A resposta parece:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Copie o ficheiro que se segue e dê-lhe o nome `language.yml` . O ficheiro tem uma `service` secção e uma secção cada para os dois tipos de `deployment` contentores, o contentor do `language-frontend` site e o recipiente de `language` deteção.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Altere as linhas de implementação de frontend de linguagem `language.yml` com base na tabela seguinte para adicionar os seus próprios nomes de imagem de registo de contentores, segredo do cliente e definições de análise de texto.

    Definições de implementação de frontend de linguagem|Objetivo|
    |--|--|
    |Linha 32<br> `image` propriedade|Localização da imagem para a imagem frontal no seu registo de contentores<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Linha 44<br> `name` propriedade|Registo de contentores secreto para a imagem, referido como `<client-secret>` numa secção anterior.|

1. Altere as linhas de implementação do `language.yml` idioma com base na tabela seguinte para adicionar os nomes de imagem do registo do seu próprio contentor, o segredo do cliente e as definições de análise de texto.

    |Definições de implementação de idiomas|Objetivo|
    |--|--|
    |Linha 78<br> `image` propriedade|Localização da imagem da imagem do idioma no seu registo de contentores<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Linha 95<br> `name` propriedade|Registo de contentores secreto para a imagem, referido como `<client-secret>` numa secção anterior.|
    |Linha 91<br> `apiKey` propriedade|A chave de recursos de análise de texto|
    |Linha 92<br> `billing` propriedade|O ponto final de faturação para o seu recurso de análise de texto.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Como o **apiKey** e o ponto final de **faturação** são definidos como parte da definição de orquestração de Kubernetes, o recipiente do site não precisa de saber sobre estes ou passá-los como parte do pedido. O recipiente do site refere-se ao recipiente de deteção de idiomas pelo seu nome de `language` orquestrador.

1. Carregue o ficheiro de definição de orquestração para esta amostra da pasta onde criou e guarde o `language.yml` .

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

## <a name="get-external-ips-of-containers"></a>Obter IPs externos de contentores

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

Se `EXTERNAL-IP` o serviço for apresentado como pendente, reexecute o comando até que o endereço IP seja mostrado antes de passar para o passo seguinte.

## <a name="test-the-language-detection-container"></a>Testar o recipiente de deteção linguística

Abra um browser e navegue para o IP externo do `language` recipiente a partir da secção anterior: `http://<external-ip>:5000/swagger/index.html` . Pode utilizar a `Try it` funcionalidade da API para testar o ponto final de deteção de idiomas.

![Veja a documentação arrojada do recipiente](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Teste o recipiente de aplicação do cliente

Altere o URL no navegador para o IP externo do `language-frontend` recipiente utilizando o seguinte formato: `http://<external-ip>/helloworld` . O texto da cultura inglesa `helloworld` é previsto como `English` .

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar o cluster, elimine o grupo de recursos Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informações relacionadas

* [kubectl para utilizadores docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Contentores dos Serviços Cognitivos](../cognitive-services-container-support.md)

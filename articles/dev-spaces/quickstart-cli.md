---
title: Desenvolver uma aplicação em Kubernetes
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar a Azure Dev Spaces e a linha de comando para desenvolver uma aplicação no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 82e7e73a91b07770881353465af063497deae085
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414807"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>Quickstart: Desenvolver uma aplicação em Kubernetes - Azure Dev Spaces
Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolver e executar código em recipientes utilizando a linha de comando.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Você precisa criar um cluster AKS em uma [região apoiada][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar espaços Azure Dev no seu cluster AKS

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações. O comando abaixo permite espaços Dev no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev *predefinido.*

> [!NOTE]
> O `use-dev-spaces` comando também instalará o CLI dos Espaços Azure Dev se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Concha da Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Obtenha o código de aplicação da amostra

Neste artigo, utiliza a aplicação de [amostra Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar a utilização de Espaços Azure Dev.

Clone a aplicação do GitHub e navegue nos *espaços dev/samples/nodejs/getting-started/webfrontend* direy:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparar a aplicação

Para executar a sua aplicação em Azure Dev Spaces, precisa de um gráfico de Dockerfile e Helm. Para alguns idiomas, como [Java][java-quickstart], [.NET core][netcore-quickstart]e [Node.js, ][nodejs-quickstart]a ferramenta de cliente Azure Dev Spaces pode gerar todos os ativos de que necessita. Para muitas outras línguas, como Go, PHP e Python, a ferramenta do cliente pode gerar o gráfico Helm desde que você possa fornecer um Dockerfile válido.

Gere os ativos do gráfico Docker e Helm para executar a aplicação em Kubernetes utilizando o `azds prep` comando:

```cmd
azds prep --enable-ingress
```

Você deve executar o `prep` comando a partir de *dev-spaces/samples/nodejs/getting-started/webfrontend* direy para gerar corretamente os ativos do gráfico Docker e Helm.

> [!TIP]
> O `prep` comando tenta gerar um gráfico de [Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto. O Azure Dev Spaces utiliza estes ficheiros para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes

Construa e execute o seu código em AKS utilizando o `azds up` comando:

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at `http://webfrontend.1234567890abcdef1234.eus.azds.io/`
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Pode ver o serviço a funcionar abrindo o URL público, que é apresentado na saída a partir do `azds up` comando. Neste exemplo, a URL pública é *`http://webfrontend.1234567890abcdef1234.eus.azds.io/`* .

> [!NOTE]
> Quando navega para o seu serviço durante `azds up` a execução, os vestígios de pedido HTTP também são apresentados na saída do `azds up` comando. Estes vestígios podem ajudá-lo a resolver problemas e depurar o seu serviço. Pode desativar estes vestígios `--disable-http-traces` utilizando-os durante a execução `azds up` .

Se parar o `azds up` comando utilizando *ctrl+c,* o serviço continuará a funcionar em AKS, e o URL público permanecerá disponível.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro no seu projeto e refazer o `azds up` comando. Por exemplo:

1. Se `azds up` ainda estiver em funcionamento, prima *Ctrl+c*.
1. Atualizar [a linha `server.js` 13](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) para:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Guarde as alterações.
1. Re-fazer o `azds up` comando:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navegue para o seu serviço de funcionamento e observe as suas alterações.
1. Pressione *Ctrl+c* para parar o `azds up` comando.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximos passos

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipas em Espaços Azure Dev][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
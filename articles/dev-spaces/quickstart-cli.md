---
title: Implementar uma aplicação no Kubernetes através dos espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Implementar um microsserviço no AKS com os espaços de desenvolvimento do Azure
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 39fb7658140a2eda948cd0dc0e58d71b0b9a053b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706273"
---
# <a name="quickstart-develop-an-application-on-kubernetes-using-azure-dev-spaces"></a>Início rápido: Desenvolver uma aplicação no Kubernetes através dos espaços de desenvolvimento do Azure
Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolva e execute o código em contentores com a linha de comandos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Tem de criar um cluster do AKS numa [suportada região][supported-regions]. Os comandos abaixo, crie um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar os espaços de desenvolvimento do Azure no seu cluster do AKS

Utilize o `use-dev-spaces` comando para ativar os espaços de desenvolvimento no seu cluster do AKS e siga as instruções. O comando abaixo permite que os espaços de desenvolvimento no *MyAKS* cluster no *MyResourceGroup* agrupar e cria um *padrão* espaço de desenvolvimento.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Obter o código de aplicativo de exemplo

Neste artigo, vai utilizar o [aplicação de exemplo do Azure Dev espaços](https://github.com/Azure/dev-spaces) para demonstrar a utilização de espaços de desenvolvimento do Azure.

Clonar a aplicação a partir do GitHub e navegue para o *dev-espaços/exemplos/nodejs/obter-iniciada/webfrontend* diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Para preparar a aplicação

Para executar a aplicação em espaços de desenvolvimento do Azure, terá de um gráfico Dockerfile e Helm. Para alguns idiomas, como [Java][java-quickstart], [.NET core][netcore-quickstart], e [node. js][nodejs-quickstart], o cliente de espaços de desenvolvimento do Azure, as ferramentas pode gerar todos os recursos que precisa. Para outros idiomas, como o Go, PHP e Python, o cliente de ferramentas pode gerar o gráfico Helm, desde que pode fornecer um Dockerfile válido.

Gerar os ativos de gráfico do Docker e o Helm para executar a aplicação no Kubernetes com o `azds prep` comando:

```cmd
azds prep --public
```

Tem de executar o `prep` comando a partir do *dev-espaços/exemplos/nodejs/obter-iniciada/webfrontend* diretório ao gerar corretamente os ativos de gráfico do Docker e o Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes

Criar e executar seu código no AKS com o `azds up` comando:

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
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

Pode ver o serviço em execução, abrindo a URL pública, o que é apresentada no resultado do `azds up` comando. Neste exemplo, é o URL público *http://webfrontend.1234567890abcdef1234.eus.azds.io/* .

Se parar a `azds up` através de comandos *Ctrl + c*, o serviço continua em execução no AKS e público URL permanecerão disponível.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer arquivo em seu projeto e execute novamente o `azds up` comando. Por exemplo:

1. Se `azds up` ainda está em execução, prima *Ctrl + c*.
1. Atualização [linha 13 no `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) para:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Guarde as alterações.
1. Volte a executar o `azds up` comando:

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navegue para o serviço em execução e observe as alterações.
1. Prima *Ctrl + c* para parar o `azds up` comando.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os espaços de desenvolvimento do Azure ajuda-o a desenvolver aplicativos mais complexos em vários contentores, e como pode simplificar o desenvolvimento colaborativo, trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe nos espaços de desenvolvimento do Azure][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations
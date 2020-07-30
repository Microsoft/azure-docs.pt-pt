---
title: Desenvolver uma aplicação com um gráfico helm existente em Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar Azure Dev Spaces e a linha de comando para desenvolver uma aplicação com um gráfico helm existente no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: devx-track-javascript
ms.openlocfilehash: 7d1d782203401607d3909d79d1f0f3a499712c32
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87414314"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Quickstart: Desenvolver uma aplicação com um gráfico helm existente em Kubernetes - Azure Dev Spaces
Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Executar uma aplicação com um gráfico helm existente em AKS usando Azure Dev Spaces na linha de comando.

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

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações. O comando abaixo permite espaços Dev no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev chamado *dev*.

> [!NOTE]
> O `use-dev-spaces` comando também instalará o CLI dos Espaços Azure Dev se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Concha da Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obtenha o código de aplicação da amostra

Neste artigo, utiliza a aplicação de [amostra Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar a utilização de Espaços Azure Dev.

Clone a aplicação do GitHub e navegue nos *espaços dev/samples/python/getting-started/webfrontend* direy:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparar a aplicação

Para executar a sua aplicação em Azure Dev Spaces, precisa de um gráfico de Dockerfile e Helm. Para alguns idiomas, como [Java][java-quickstart], [.NET core][netcore-quickstart]e [Node.js, ][nodejs-quickstart]a ferramenta de cliente Azure Dev Spaces pode gerar todos os ativos de que necessita. Para muitas outras línguas, como Go, PHP e Python, a ferramenta do cliente pode gerar o gráfico Helm desde que você possa fornecer um Dockerfile válido. Neste caso, a aplicação da amostra tem um gráfico existente de Dockerfile e Helm

Gere a configuração para executar a aplicação com Azure Dev Spaces com o gráfico helm existente e Dockerfile usando o `azds prep` comando:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Deve executar o `prep` comando a partir do *dev-spaces/samples/python/getting-started/webfrontend* directy e especificar a localização do gráfico Helm utilizando `--chart` .

> [!NOTE]
> Pode ver o aviso: *AVISO: O ficheiro de estivador não pôde ser gerado devido a uma linguagem não suportada.* quando estiver a correr `azds prep` . O `azds prep` comando tenta gerar um gráfico de [Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto, mas não substituirá quaisquer gráficos existentes de Dockerfiles ou Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes

Construa e execute o seu código em AKS utilizando o `azds up` comando:

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

Pode ver o serviço a funcionar abrindo o URL público, que é apresentado na saída a partir do `azds up` comando. Neste exemplo, a URL pública é `http://dev.service.1234567890abcdef1234.eus.azds.io/` .

> [!NOTE]
> Quando navega para o seu serviço durante `azds up` a execução, os vestígios de pedido HTTP também são apresentados na saída do `azds up` comando. Estes vestígios podem ajudá-lo a resolver problemas e depurar o seu serviço. Pode desativar estes vestígios `--disable-http-traces` utilizando-os durante a execução `azds up` .

Se parar o `azds up` comando utilizando *ctrl+c,* o serviço continuará a funcionar em AKS, e o URL público permanecerá disponível.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro no seu projeto e refazer o `azds up` comando. Por exemplo:

1. Se `azds up` ainda estiver em funcionamento, prima *Ctrl+c*.
1. Atualizar [a linha `webfrontend.py` 13](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13) para:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Guarde as alterações.
1. Re-fazer o `azds up` comando:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
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
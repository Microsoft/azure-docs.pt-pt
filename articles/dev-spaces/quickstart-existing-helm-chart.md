---
title: Desenvolver uma aplicação com um gráfico helm existente em Kubernetes
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar o Azure Dev Spaces e a linha de comando para desenvolver uma aplicação com um gráfico Helm existente no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82033565"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>Quickstart: Desenvolver uma aplicação com um gráfico helm existente em Kubernetes - Azure Dev Spaces
Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Execute uma aplicação com um gráfico helm existente em AKS usando Espaços Azure Dev na linha de comando.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces no seu cluster AKS

Utilize `use-dev-spaces` o comando para ativar os Espaços Dev no seu cluster AKS e siga as instruções. O comando abaixo permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev chamado *dev*.

> [!NOTE]
> O `use-dev-spaces` comando também instalará o Azure Dev Spaces CLI se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Obter código de aplicação de amostra

Neste artigo, utiliza a aplicação de amostra seleção [Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar a utilização de Espaços Azure Dev.

Clone a aplicação do GitHub e navegue para o diretório *de v-spaces/samples/python/start-start/webfrontend:*

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>Preparar a aplicação

Para executar a sua aplicação em Espaços Azure Dev, você precisa de um gráfico de Dockerfile e Helm. Para algumas línguas, como [Java][java-quickstart], [.NET core][netcore-quickstart], e [Node.js,][nodejs-quickstart]a ferramenta de cliente Azure Dev Spaces pode gerar todos os ativos que você precisa. Para muitas outras línguas, como Go, PHP e Python, a ferramenta do cliente pode gerar o gráfico Helm desde que possa fornecer um Dockerfile válido. Neste caso, a aplicação da amostra tem um gráfico de Dockerfile e Helm existente

Gere a configuração para executar a aplicação com o Azure Dev Spaces com o gráfico Helm existente e Dockerfile utilizando o `azds prep` comando:

```cmd
azds prep --enable-ingress --chart webfrontend/
```

Deve executar `prep` o comando a partir do *dev-spaces/samples/python/start-start/webfrontend* diretório `--chart`e especificar a localização do gráfico Helm utilizando .

> [!NOTE]
> Pode ver o aviso: *AVISO: O Dockerfile não pode ser gerado devido a uma linguagem não suportada.* ao `azds prep`correr. O `azds prep` comando tenta gerar [um gráfico de Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto, mas não irá substituir quaisquer ficheiros Docker existentes ou gráficos Helm.

## <a name="build-and-run-code-in-kubernetes"></a>Criar e executar códigos no Kubernetes

Construa e execute o `azds up` seu código em AKS usando o comando:

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

Pode ver o serviço a funcionar abrindo o URL público, `azds up` que é exibido na saída a partir do comando. Neste exemplo, o URL *http://dev.service.1234567890abcdef1234.eus.azds.io/* público é .

> [!NOTE]
> Quando navega para o `azds up`seu serviço durante a execução, os vestígios de pedido http também são apresentados na saída do `azds up` comando. Estes vestígios podem ajudá-lo a resolver problemas e a depurar o seu serviço. Pode desativar `--disable-http-traces` estes `azds up`vestígios utilizando durante a execução .

Se parar `azds up` o comando utilizando *Ctrl+c,* o serviço continuará a funcionar em AKS, e o URL público permanecerá disponível.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar `azds up` qualquer ficheiro do seu projeto e reexecutar o comando. Por exemplo:

1. Se `azds up` ainda estiver em funcionamento, prima *Ctrl+c*.
1. Atualizar [a linha `webfrontend.py` 13 em:](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Guarde as alterações.
1. Reexecutar `azds up` o comando:

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. Navegue para o seu serviço de funcionamento e observe as suas alterações.
1. Pressione *Ctrl+c* `azds up` para parar o comando.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
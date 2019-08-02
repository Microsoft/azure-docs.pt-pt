---
title: Depurar e iterar com Visual Studio Code e node. js em kubernetes usando Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Desenvolvimento rápido de kubernetes com contêineres, microservices e node. js no Azure
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 4ee11b4bebe32ff4a9af38a0789823178f388e10
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725772"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-code-and-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Início rápido: Depurar e iterar com Visual Studio Code e node. js em kubernetes usando Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolver o código em contêineres iterativamente usando Visual Studio Code.
- Depure o código em seu espaço de desenvolvimento de Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Visual Studio Code instalado](https://code.visualstudio.com/download).
- A extensão de [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) para Visual Studio Code instalada.
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Você precisa criar um cluster AKS em uma [região com suporte][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado MyResource Group e um cluster AKs chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Use o `use-dev-spaces` comando para habilitar espaços de desenvolvimento em seu cluster AKs e siga os prompts. O comando abaixo habilita espaços de desenvolvimento no cluster *MyAKS* no grupo *MyResource* Group e cria um espaço de desenvolvimento *padrão* .

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

## <a name="get-sample-application-code"></a>Obter código do aplicativo de exemplo

Neste artigo, você usa o [Azure dev Spaces aplicativo de exemplo](https://github.com/Azure/dev-spaces) para demonstrar o uso de Azure dev Spaces.

Clone o aplicativo do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Preparar o aplicativo de exemplo no Visual Studio Code

Abra Visual Studio Code, clique em *arquivo* e, em seguida, *abra...* , navegue até o diretório *dev-Spaces/Samples/NodeJS/getfront-Started/WebFrontEnd* e clique em *abrir*.

Agora você tem o projeto de WebFrontEnd aberto no Visual Studio Code. Para executar o aplicativo em seu espaço de desenvolvimento, gere os ativos do gráfico do Docker e do Helm usando a extensão Azure Dev Spaces na paleta de comandos.

Para abrir a paleta de comandos no Visual Studio Code, clique em *Exibir* na *paleta de comandos*. Comece a `Azure Dev Spaces` digitar e clique `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`em.

![Preparar arquivos de configuração para Azure Dev Spaces](./media/common/command-palette.png)

Quando Visual Studio Code também solicita que você configure seu ponto de extremidade público, `Yes` escolha habilitar um ponto de extremidade público.

![Selecionar ponto de extremidade público](media/common/select-public-endpoint.png)

Este comando prepara seu projeto para ser executado no Azure Dev Spaces gerando um gráfico Dockerfile e Helm. Ele também gera um diretório *. vscode* com a configuração de depuração na raiz do seu projeto.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Compilar e executar código no kubernetes da Visual Studio Code

Clique no ícone de *depuração* à esquerda e clique em *Iniciar servidor (AZDS)* na parte superior.

![Servidor de inicialização](media/get-started-node/debug-configuration-nodejs.png)

Esse comando cria e executa seu serviço no Azure Dev Spaces. A janela do *terminal* na parte inferior mostra a saída da compilação e as URLs para o serviço que está executando Azure dev Spaces. O *console de depuração* mostra a saída do log.

> [!Note]
> Se você não vir nenhum comando Azure Dev Spaces na *paleta de comandos*, certifique-se de ter instalado a [extensão de Visual Studio Code para Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se você abriu o diretório *dev-Spaces/Samples/NodeJS/getfront-Started/WebFrontEnd* no Visual Studio Code.

Você pode ver o serviço em execução abrindo a URL pública.

Clique em *depurar* e em *parar depuração* para interromper o depurador.

## <a name="update-code"></a>Atualizar código

Para implantar uma versão atualizada do serviço, você pode atualizar qualquer arquivo em seu projeto e executar novamente o *servidor de inicialização*. Por exemplo:

1. Se seu aplicativo ainda estiver em execução, clique em *depurar* e *interrompa a depuração* para interrompê-la.
1. Atualize a [linha 13 `server.js` em](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) :
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Guarde as alterações.
1. Execute novamente o *servidor de inicialização*.
1. Navegue até o serviço em execução e observe as alterações.
1. Clique em *depurar* e em *parar depuração* para parar o aplicativo.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Configurando e usando pontos de interrupção para depuração

Inicie o serviço usando o *servidor de inicialização (AZDS)* .

Navegue de volta para o modo de exibição do *Explorer* clicando em *Exibir* e em *Gerenciador*. Abra `server.js` e clique em algum lugar na linha 13 para colocar o cursor lá. Para definir um ponto de interrupção, pressione *F9* ou clique em *depurar* e *alternar ponto de interrupção*.

Abra seu serviço em um navegador e observe que nenhuma mensagem é exibida. Retorne para Visual Studio Code e observe que a linha 13 está realçada. O ponto de interrupção que você definiu pausou o serviço na linha 13. Para retomar o serviço, pressione *F5* ou clique em *depurar* e *continuar*. Retorne ao seu navegador e observe que a mensagem agora é exibida.

Ao executar o serviço no kubernetes com um depurador anexado, você tem acesso completo para depurar informações como a pilha de chamadas, variáveis locais e informações de exceção.

Remova o ponto de interrupção colocando o cursor na linha 13 `server.js` em e pressionando *F9*.

Clique em *depurar* e em *parar depuração* para interromper o depurador.

## <a name="update-code-from-visual-studio-code"></a>Atualizar código de Visual Studio Code

Altere o modo de depuração para *anexar a um servidor (AZDS)* e inicie o serviço:

![](media/get-started-node/attach-nodejs.png)

Esse comando cria e executa seu serviço no Azure Dev Spaces. Ele também inicia um [](https://nodemon.io) processo nodemon no contêiner do serviço e anexa vs Code a ele. O processo nodemon permite reinicializações automáticas quando são feitas alterações no código-fonte, permitindo um desenvolvimento de loop interno mais rápido semelhante ao desenvolvimento em seu computador local.

Depois que o serviço for iniciado, navegue até ele usando seu navegador e interaja com ele.

Enquanto o serviço estiver em execução, retorne para VS Code e atualize a `server.js`linha 13 em. Por exemplo:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Salve o arquivo e retorne ao seu serviço em um navegador. Interaja com o serviço e observe que a mensagem atualizada é exibida.

Ao executarnodemon, o processo de nó é reiniciado automaticamente assim que qualquer alteração de código é detectada. Esse processo de reinicialização automática é semelhante à experiência de edição e reinicialização do serviço em seu computador local, fornecendo uma experiência de desenvolvimento de loop interno.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: about.md#supported-regions-and-configurations

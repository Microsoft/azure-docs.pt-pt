---
title: Programar com node. js no Kubernetes com espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 03/22/2019
ms.topic: quickstart
description: Desenvolvimento rápido de Kubernetes com contentores, microsserviços e node. js no Azure
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: bc18a06405c0fe620136642a409df576c8e8d8b3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361710"
---
# <a name="quickstart-develop-with-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Início rápido: Programar com node. js no Kubernetes com espaços de desenvolvimento do Azure

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolva iterativamente código em contentores através do Visual Studio Code e a linha de comandos.
- Depure o código no seu espaço de desenvolvimento do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Visual Studio Code instalado](https://code.visualstudio.com/download).
- O [do Azure Dev espaços](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) extensão para o Visual Studio Code instalado.
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Tem de criar um cluster do AKS numa [suportada região](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Os comandos abaixo, crie um grupo de recursos chamado *MyResourceGroup* e um cluster do AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-count 1 --generate-ssh-keys
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

Pode ver o serviço em execução, abrindo a URL pública, o que é apresentada no resultado do `azds up` comando. Neste exemplo, é o URL público *http://webfrontend.1234567890abcdef1234.eus.azds.io/*.

Se parar a `azds up` através de comandos *Ctrl + c*, o serviço continua em execução no AKS e público URL permanecerão disponível.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer arquivo em seu projeto e execute novamente o `azds up` comando. Por exemplo:

1. Se `azds up` ainda está em execução, prima *Ctrl + c*.
1. Atualização [linha 10 `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L10) para:
    
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

## <a name="initialize-code-for-debugging-in-kubernetes-with-visual-studio-code"></a>Inicializar o código para depuração no Kubernetes com o Visual Studio Code

Abra o Visual Studio Code, clique em *arquivo* , em seguida, *abra...* , navegue para o *dev-espaços/exemplos/nodejs/obter-iniciada/webfrontend* diretório e clique em *aberto*.

Agora tem o *webfrontend* projeto aberto no Visual Studio Code, que é o mesmo serviço que foi executada com o `azds up` comando. Para este serviço no AKS com o Visual Studio Code, em vez do uso de depurar `azds up` diretamente, tem de preparar este projeto para utilizar o Visual Studio Code para comunicar com o seu espaço de desenvolvimento.

Para abrir a paleta de comandos no Visual Studio Code, clique em *View* , em seguida, *paleta de comandos*. Comece a escrever `Azure Dev Spaces` e clique em `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![](./media/common/command-palette.png)

Esse comando Prepare seu projeto para executar nos espaços de desenvolvimento do Azure diretamente a partir do Visual Studio Code. Ele também gera uma *.vscode* diretório com a depuração de configuração na raiz do seu projeto.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Criar e executar o código no Kubernetes a partir do Visual Studio Code

Clique nas *depurar* ícone à esquerda e clique em *iniciar servidor (AZDS)* na parte superior.

![](media/get-started-node/debug-configuration-nodejs.png)

Este comando é compilada e executada o seu serviço nos espaços de desenvolvimento do Azure no modo de depuração. O *Terminal* janela na parte inferior mostra os URLs e de saída da compilação para o seu serviço com espaços de desenvolvimento do Azure. O *consola de depuração* mostra a saída de registo.

> [!Note]
> Se não vir quaisquer comandos de espaços de desenvolvimento do Azure no *paleta de comandos*, certifique-se de que instalou o [extensão do Visual Studio Code para espaços de desenvolvimento do Azure](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se tiver aberto o *dev-espaços/exemplos/nodejs/obter-iniciada/webfrontend* diretório no Visual Studio Code.

Clique em *depurar* , em seguida, *parar depuração* para parar o depurador.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Configuração e seu uso de pontos de interrupção para depuração

Começar a utilizar o serviço *iniciar o servidor (AZDS)*.

Navegue de volta para o *Explorer* vista clicando *vista* , em seguida, *Explorer*. Abra `server.js` e clique em algum lugar na linha 10 para colocar o cursor aqui. Para definir um ponto de interrupção atingido *F9* ou clique em *depurar* , em seguida, *Ativar/desativar ponto de interrupção*.

Abra o serviço num navegador e que nenhuma mensagem será exibida. Regresse ao Visual Studio Code e observe a linha 10 é realçada. Definir o ponto de interrupção foi colocado em pausa o serviço na linha 10. Para retomar o serviço, pressionar *F5* ou clique em *depurar* , em seguida, *continuar*. Regresse ao seu navegador e tenha em atenção que agora é apresentada a mensagem.

Enquanto executa o seu serviço no Kubernetes com um depurador anexado, tem acesso total ao depurar informações como a pilha de chamadas, variáveis locais e informações de exceção.

Remover o ponto de interrupção ao colocar o cursor na linha 10 na `server.js` e pressionando *F9*.

Clique em *depurar* , em seguida, *parar depuração* para parar o depurador.

## <a name="update-code-from-visual-studio-code"></a>Atualizar o código do Visual Studio Code

Alterar o modo de depuração para *anexar a um servidor (AZDS)* e iniciar o serviço:

![](media/get-started-node/attach-nodejs.png)

Este comando é compilada e executada o seu serviço nos espaços de desenvolvimento do Azure. Ele também é iniciado um [nodemon](https://nodemon.io) processos no contentor e anexa o VS Code para o mesmo do seu serviço. O *nodemon* processo permite reinícios automáticos quando forem feitas alterações no código-fonte, permitindo o desenvolvimento mais rápido ciclo interno semelhante ao desenvolvimento no seu computador local.

Depois do serviço é iniciado, navegar até ele usando o seu navegador e interagir com ele.

Enquanto o serviço está em execução, regressar à linha VS Code e atualização 10 em `server.js`. Por exemplo:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Guarde o ficheiro e regresse ao seu serviço num navegador. Interagir com o serviço e que a sua mensagem atualizada é exibida.

Ao ser executado *nodemon*, o processo de nó é reiniciado automaticamente assim que forem detetadas alterações de código. Este processo de reinício automático é semelhante à experiência de edição e reiniciar o serviço no seu computador local, fornecendo uma experiência de desenvolvimento do ciclo interno.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os espaços de desenvolvimento do Azure ajuda-o a desenvolver aplicativos mais complexos em vários contentores, e como pode simplificar o desenvolvimento colaborativo, trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-nodejs.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)

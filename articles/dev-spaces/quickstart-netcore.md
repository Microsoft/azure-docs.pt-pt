---
title: Programar com .NET Core no Kubernetes com espaços de desenvolvimento do Azure (Visual Studio Code)
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: cc41e268678872910113c8e198bdaaac34232458
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706310"
---
# <a name="quickstart-develop-with-net-core-on-kubernetes-using-azure-dev-spaces-visual-studio-code"></a>Início rápido: Programar com .NET Core no Kubernetes com espaços de desenvolvimento do Azure (Visual Studio Code)

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Iterativamente desenvolva o código em contentores com o Visual Studio Code.
- Depure o código no seu espaço de desenvolvimento do Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Visual Studio Code instalado](https://code.visualstudio.com/download).
- O [do Azure Dev espaços](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) extensões para Visual Studio Code instalado.
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

Clone a aplicação a partir do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Preparar o aplicativo de exemplo no Visual Studio Code

Abra o Visual Studio Code, clique em *arquivo* , em seguida, *abra...* , navegue para o *dev-espaços/exemplos/dotnetcore/obter-iniciada/webfrontend* diretório e clique em *aberto*.

Agora tem o *webfrontend* projeto aberto no Visual Studio Code. Para executar a aplicação no seu espaço de desenvolvimento, gere os ativos de gráfico de Docker e o Helm, com a extensão de espaços de desenvolvimento do Azure no Pallette comando.

Para abrir a paleta de comandos no Visual Studio Code, clique em *View* , em seguida, *paleta de comandos*. Comece a escrever `Azure Dev Spaces` e clique em `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Preparar ficheiros de configuração para espaços de desenvolvimento do Azure](./media/common/command-palette.png)

Quando o Visual Studio Code também pede-lhe para configurar o ponto final público, escolha `Yes` para ativar um ponto final público.

![Selecione o ponto final público](media/common/select-public-endpoint.png)

Esse comando Prepare seu projeto para executar nos espaços de desenvolvimento do Azure através da geração de um gráfico Dockerfile e Helm. Ele também gera uma *.vscode* diretório com a depuração de configuração na raiz do seu projeto.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Criar e executar o código no Kubernetes a partir do Visual Studio

Clique nas *depurar* ícone à esquerda e clique em *.NET Core iniciar (AZDS)* na parte superior.

![](media/get-started-netcore/debug-configuration.png)

Este comando é compilada e executada o seu serviço nos espaços de desenvolvimento do Azure no modo de depuração. O *Terminal* janela na parte inferior mostra os URLs e de saída da compilação para o seu serviço com espaços de desenvolvimento do Azure. O *consola de depuração* mostra a saída de registo.

> [!Note]
> Se não vir quaisquer comandos de espaços de desenvolvimento do Azure no *paleta de comandos*, certifique-se de que instalou o [extensão do Visual Studio Code para espaços de desenvolvimento do Azure](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se tiver aberto o *dev-espaços/exemplos/dotnetcore/obter-iniciada/webfrontend* diretório no Visual Studio Code.

Pode ver o serviço em execução ao abrir o URL público.

Clique em *depurar* , em seguida, *parar depuração* para parar o depurador.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer arquivo em seu projeto e volte a executar *.NET Core iniciar (AZDS)* . Por exemplo:

1. Se seu aplicativo ainda está em execução, clique em *depurar* , em seguida, *parar depuração* interrompê-lo.
1. Atualização [linha 22 no `Controllers/HomeController.cs` ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22) para:
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Guarde as alterações.
1. Volte a executar *.NET Core lançamento (AZDS)* .
1. Navegue para o serviço em execução e clique em *sobre*.
1. Observe as alterações.
1. Clique em *depurar* , em seguida, *parar depuração* para parar a aplicação.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Configuração e seu uso de pontos de interrupção para depuração

Inicie o serviço no usando o modo de depuração *.NET Core iniciar (AZDS)* .

Navegue de volta para o *Explorer* vista clicando *vista* , em seguida, *Explorer*. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 22 para colocar o cursor aqui. Para definir um ponto de interrupção atingido *F9* ou clique em *depurar* , em seguida, *Ativar/desativar ponto de interrupção*.

Abra o serviço num navegador e que nenhuma mensagem será exibida. Regresse ao Visual Studio Code e observe a linha 20 é realçada. Definir o ponto de interrupção foi colocado em pausa o serviço na linha 20. Para retomar o serviço, pressionar *F5* ou clique em *depurar* , em seguida, *continuar*. Regresse ao seu navegador e tenha em atenção que agora é apresentada a mensagem.

Enquanto executa o seu serviço no Kubernetes com um depurador anexado, tem acesso total ao depurar informações como a pilha de chamadas, variáveis locais e informações de exceção.

Remover o ponto de interrupção ao colocar o cursor na linha 22 em `Controllers/HomeController.cs` e pressionando *F9*.

## <a name="update-code-from-visual-studio-code"></a>Atualizar o código do Visual Studio Code

Enquanto o serviço está em execução no modo de depuração, atualizar linha 22 no `Controllers/HomeController.cs`. Por exemplo:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Guarde o ficheiro. Clique em *depurar* , em seguida, *reiniciar depuração* ou no *barra de ferramentas de depuração*, clique no *reiniciar depuração* botão.

![](media/common/debug-action-refresh.png)

Abra o serviço num navegador e que a sua mensagem atualizada é exibida.

Em vez de reconstruir e Reimplementar uma nova imagem de contentor sempre que o código edições são feitas, espaços de desenvolvimento do Azure de forma incremental as recompilações código dentro do contentor existente para fornecer um loop de editar/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como os espaços de desenvolvimento do Azure ajuda-o a desenvolver aplicativos mais complexos em vários contentores, e como pode simplificar o desenvolvimento colaborativo, trabalhando com diferentes versões ou ramos do seu código em diferentes espaços. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: about.md#supported-regions-and-configurations
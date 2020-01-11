---
title: 'Depurar e iterar em kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Este guia de início rápido mostra como usar Azure Dev Spaces e Visual Studio Code para depurar e iterar rapidamente um aplicativo Java no serviço kubernetes do Azure
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Java, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 9237137f86bf7b6b30eb908e0acbe3b8b0e624d9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867474"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Início rápido: Depurar e iterar em kubernetes com Visual Studio Code e Java-Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolver o código em contêineres iterativamente usando Visual Studio Code.
- Depure o código em seu espaço de desenvolvimento de Visual Studio Code.

Azure Dev Spaces também permite depurar e iterar usando:
- [Node. js e Visual Studio Code](quickstart-nodejs.md)
- [.NET Core e Visual Studio Code](quickstart-netcore.md)
- [.NET Core e Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Visual Studio Code instalado](https://code.visualstudio.com/download).
- O depurador [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [Java para Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) Extensions para Visual Studio Code instalado.
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Maven instalado e configurado](https://maven.apache.org).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do serviço kubernetes do Azure

Você precisa criar um cluster AKS em uma [região com suporte][supported-regions]. Os comandos abaixo criam um grupo de recursos chamado *MyResource* Group e um cluster AKs chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Habilitar Azure Dev Spaces em seu cluster AKS

Use o comando `use-dev-spaces` para habilitar espaços de desenvolvimento em seu cluster AKS e siga os prompts. O comando abaixo habilita espaços de desenvolvimento no cluster *MyAKS* no grupo *MyResource* Group e cria um espaço de desenvolvimento *padrão* .

> [!NOTE]
> O comando `use-dev-spaces` também instalará a CLI do Azure Dev Spaces, se ainda não estiver instalada. Não é possível instalar a CLI do Azure Dev Spaces no Azure Cloud Shell.

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

Abra Visual Studio Code, clique em *arquivo* e, em seguida, *abra...* , navegue até o diretório *dev-Spaces/samples/java/Getting-Started/WebFrontEnd* e clique em *abrir*.

Agora você tem o projeto de *WebFrontEnd* aberto no Visual Studio Code. Para executar o aplicativo em seu espaço de desenvolvimento, gere os ativos do gráfico do Docker e do Helm usando a extensão Azure Dev Spaces na paleta de comandos.

Para abrir a paleta de comandos no Visual Studio Code, clique em *Exibir* na *paleta de comandos*. Comece digitando `Azure Dev Spaces` e clique em `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Preparar arquivos de configuração para Azure Dev Spaces](./media/common/command-palette.png)

Quando Visual Studio Code também solicita que você configure suas imagens base, porta exposta e ponto de extremidade público, escolha `Azul Zulu OpenJDK for Azure (Free LTS)` para a imagem base, `8080` para a porta exposta e `Yes` para habilitar um ponto de extremidade público.

![Selecionar imagem base](media/get-started-java/select-base-image.png)

![Selecionar porta exposta](media/get-started-java/select-exposed-port.png)

![Selecionar ponto de extremidade público](media/get-started-java/select-public-endpoint.png)

Este comando prepara seu projeto para ser executado no Azure Dev Spaces gerando um gráfico Dockerfile e Helm. Ele também gera um diretório *. vscode* com a configuração de depuração na raiz do seu projeto.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works.md#prepare-your-code) para seu projeto é usado pelo Azure dev Spaces para compilar e executar seu código, mas você pode modificar esses arquivos se quiser alterar a forma como o projeto é compilado e executado.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>Compilar e executar código no kubernetes por meio do Visual Studio

Clique no ícone de *depuração* à esquerda e clique em *Iniciar programa Java (AZDS)* na parte superior.

![Iniciar programa Java](media/get-started-java/debug-configuration.png)

Esse comando cria e executa seu serviço no Azure Dev Spaces. A janela do *terminal* na parte inferior mostra a saída da compilação e as URLs para o serviço que está executando Azure dev Spaces. O *console de depuração* mostra a saída do log.

> [!Note]
> Se você não vir nenhum comando Azure Dev Spaces na *paleta de comandos*, certifique-se de ter instalado a [extensão de Visual Studio Code para Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se você abriu o diretório *dev-Spaces/samples/java/guia de introdução/WebFrontEnd* no Visual Studio Code.

Você pode ver o serviço em execução abrindo a URL pública.

Clique em *depurar* e em *parar depuração* para interromper o depurador.

## <a name="update-code"></a>Atualizar código

Para implantar uma versão atualizada do serviço, você pode atualizar qualquer arquivo em seu projeto e executar novamente o *programa Java de inicialização (AZDS)* . Por exemplo:

1. Se seu aplicativo ainda estiver em execução, clique em *depurar* e *interrompa a depuração* para interrompê-la.
1. Atualize a [linha 19 em `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) para:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Guarde as alterações.
1. Execute novamente o *programa Java de inicialização (AZDS)* .
1. Navegue até o serviço em execução e observe as alterações.
1. Clique em *depurar* e em *parar depuração* para parar o aplicativo.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Configurando e usando pontos de interrupção para depuração

Inicie o serviço usando *Iniciar programa Java (AZDS)* . Isso também executa o serviço no modo de depuração.

Navegue de volta para o modo de exibição do *Explorer* clicando em *Exibir* e em *Gerenciador*. Abra `src/main/java/com/ms/sample/webfrontend/Application.java` e clique em algum lugar na linha 19 para colocar o cursor lá. Para definir um ponto de interrupção, pressione *F9* ou clique em *depurar* e *alternar ponto de interrupção*.

Abra seu serviço em um navegador e observe que nenhuma mensagem é exibida. Retorne para Visual Studio Code e observe que a linha 19 está realçada. O ponto de interrupção que você definiu pausou o serviço na linha 19. Para retomar o serviço, pressione *F5* ou clique em *depurar* e *continuar*. Retorne ao seu navegador e observe que a mensagem agora é exibida.

Ao executar o serviço no kubernetes com um depurador anexado, você tem acesso completo para depurar informações como a pilha de chamadas, variáveis locais e informações de exceção.

Remova o ponto de interrupção colocando o cursor na linha 19 em `src/main/java/com/ms/sample/webfrontend/Application.java` e pressionando *F9*.

## <a name="update-code-from-visual-studio-code"></a>Atualizar código de Visual Studio Code

Enquanto o serviço estiver sendo executado no modo de depuração, atualize a linha 19 em `src/main/java/com/ms/sample/webfrontend/Application.java`. Por exemplo:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Guarde o ficheiro. Clique em *depurar* e *reinicie a depuração* ou, na barra de *ferramentas depurar*, clique no botão *reiniciar depuração* .

![Atualizar depuração](media/common/debug-action-refresh.png)

Abra seu serviço em um navegador e observe que a mensagem atualizada é exibida.

Em vez de recompilar e reimplantar uma nova imagem de contêiner cada vez que são feitas edições de código, Azure Dev Spaces recompila incrementalmente o código dentro do contêiner existente para fornecer um loop de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-java.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

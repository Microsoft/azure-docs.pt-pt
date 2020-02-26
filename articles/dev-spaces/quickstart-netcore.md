---
title: 'Debug e iterado em Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar o Azure Dev Spaces e o Visual Studio Code para depurar e iterar rapidamente uma aplicação .NET Core no Serviço Azure Kubernetes
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: c2d84e823d028f542c5ab852be2ea68a5abafe93
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605283"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Quickstart: Debug e iterate em Kubernetes: Visual Studio Code e .NET Core - Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Itterativamente desenvolver código em recipientes usando o Código do Estúdio Visual.
- Desinver o código no seu espaço de dev do Visual Studio Code.

A Azure Dev Spaces também permite depurar e iterar usando:
- [Código do Estúdio Java e Visual](quickstart-java.md)
- [Node.js e Código de Estúdio Visual](quickstart-nodejs.md)
- [.NET Core e Estúdio Visual](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita](https://azure.microsoft.com/free).
- [Código de estúdio visual instalado.](https://code.visualstudio.com/download)
- Os [Espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) extensões para Código de Estúdio Visual instalados.
- A [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Os comandos abaixo criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces no seu cluster AKS

Utilize o comando `use-dev-spaces` para ativar os Espaços Dev no seu cluster AKS e seguir as instruções. O comando abaixo permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço de dev *padrão.*

> [!NOTE]
> O comando `use-dev-spaces` também instalará o Azure Dev Spaces CLI se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure.

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

## <a name="get-sample-application-code"></a>Obter código de aplicação de amostra

Neste artigo, utiliza a aplicação de amostra seleção [Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar a utilização de Espaços Azure Dev.

Clone a aplicação do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Prepare a aplicação da amostra no Código do Estúdio Visual

Abra o Código do Estúdio Visual, clique em *File* e *abra...* , navegue para o *dev-spaces/samples/dotnetcore/start-start/webfrontend* diretório, e clique em *Open*.

Tem agora o projeto *webfrontend* aberto no Código do Estúdio Visual. Para executar a aplicação no seu espaço de v, gere os ativos da tabela Docker e Helm utilizando a extensão dos Espaços Azure Dev na Paleta de Comando.

Para abrir a Paleta de Comando no Código do Estúdio Visual, clique em *Ver* e depois paleta de *comando*. Comece a digitar `Azure Dev Spaces` e clique em `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Prepare ficheiros de configuração para espaços Azure Dev](./media/common/command-palette.png)

Quando o Visual Studio Code também o incita a configurar o seu ponto final público, escolha `Yes` para ativar um ponto final público.

![Selecione ponto final público](media/common/select-public-endpoint.png)

Este comando prepara o seu projeto para funcionar em Espaços Azure Dev gerando um gráfico de Dockerfile e Helm. Também gera um diretório *.vscode* com configuração de depuração na raiz do seu projeto.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Construir e executar código em Kubernetes do Visual Studio Code

Clique no ícone *Debug* à esquerda e clique no *lançamento do Núcleo .NET (AZDS)* na parte superior.

![](media/get-started-netcore/debug-configuration.png)

Este comando constrói e executa o seu serviço em Espaços Azure Dev em modo de depuração. A janela *terminal* na parte inferior mostra a saída de construção e URLs para o seu serviço em funcionamento em Espaços Azure Dev. A *Consola Debug* mostra a saída de registo.

> [!Note]
> Se não vir nenhum comando da Azure Dev Spaces na Paleta de *Comando,* certifique-se de ter instalado a extensão do Código do [Estúdio Visual para espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também que abriu o *dev-spaces/samples/dotnetcore/start-start/webfrontend* diretório no Visual Studio Code.

Pode ver o serviço a funcionar abrindo o URL público.

Clique em *Debug* e *pare de depuração* para parar o debugger.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro do seu projeto e reexecutar *o Lançamento do Núcleo net (AZDS)* . Por exemplo:

1. Se a sua aplicação ainda estiver em execução, clique em *Debug* e, em seguida, *pare de depuração* para detê-la.
1. Atualizar [linha 22 em `Controllers/HomeController.cs`:](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Guarde as alterações.
1. Reexecutar *.NET Lançamento do Núcleo (AZDS)* .
1. Navegue para o seu serviço de execução e clique *em Cerca*de .
1. Observe as suas mudanças.
1. Clique em *Debug* *e pare de depuração* para parar a sua aplicação.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Inicie o seu serviço no modo de depuração utilizando *o Lançamento do Núcleo .NET (AZDS)* .

Navegue de volta à vista *Explorer* clicando em *Ver* e depois *Explorer*. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 22 para colocar o cursor lá. Para definir um ponto de *rutura,* clique em *Debug* e *toggle Breakpoint*.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. O Regresso ao Código do Estúdio Visual e a linha 20 de observação estão em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 20. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e note que a mensagem está agora apresentada.

Enquanto executa o seu serviço em Kubernetes com um debugger anexado, você tem acesso total a informações dedepuração tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o cursor na linha 22 em `Controllers/HomeController.cs` e batendo *em F9*.

## <a name="update-code-from-visual-studio-code"></a>Código de atualização do Código do Estúdio Visual

Enquanto o serviço estiver em funcionamento no modo de depuração, atualize a linha 22 em `Controllers/HomeController.cs`. Por exemplo:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Guarde o ficheiro. Clique *em Depurar* e *reiniciar depuração* ou na barra de *ferramentas Debug,* clique no botão *Restart Debugging.*

![](media/common/debug-action-refresh.png)

Abra o seu serviço num browser e note que a sua mensagem atualizada está visualizada.

Em vez de reconstruir e reimplantar uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recompila gradualmente o código dentro do recipiente existente para fornecer um ciclo de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

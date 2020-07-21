---
title: 'Debug e iterato em Kubernetes: Visual Studio Code & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar Azure Dev Spaces e Visual Studio Code para depurar e iterar rapidamente uma aplicação .NET Core no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b8ddf2e484b3d1525825f8df3b738091564b0a78
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512457"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Quickstart: Debug e iterate em Kubernetes: Visual Studio Code e .NET Core - Azure Dev Spaces

Neste guia, vai aprender a:

- Configurar os Espaços de Programador do Azure com um cluster Kubernetes gerido no Azure.
- Desenvolve iterativamente código em recipientes utilizando o Código do Estúdio Visual.
- Depurar o código no seu espaço dev do Código do Estúdio Visual.

A azure Dev Spaces também permite depurar e iterar usando:
- [Código de estúdio java e visual](quickstart-java.md)
- [CódigoNode.js e Estúdio Visual](quickstart-nodejs.md)
- [.NET Core e Estúdio Visual](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
- [Código de Estúdio Visual instalado](https://code.visualstudio.com/download).
- As extensões [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Código de Estúdio Visual instaladas.
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

Clone a aplicação do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Preparar a aplicação da amostra no Código do Estúdio Visual

Abrir código de estúdio visual, clicar *em Arquivo* e depois *abrir...*, navegar para os *espaços dev/samples/dotnetcore/getting-started/webfrontend* directy, e clicar *em Abrir*.

Tem agora o projeto *webfrontend* aberto no Código do Estúdio Visual. Para executar a aplicação no seu espaço dev, gere os ativos do gráfico Docker e Helm utilizando a extensão Azure Dev Spaces na Paleta de Comando.

Para abrir a Paleta de Comando no Código do Estúdio Visual, clique *em Ver e* depois Paleta de *Comando*. Comece a escrever `Azure Dev Spaces` e clique `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` em .

![Preparar ficheiros de configuração para espaços Azure Dev](./media/common/command-palette.png)

Quando o Código do Estúdio Visual também lhe pedir para configurar o seu ponto final público, opte por `Yes` ativar um ponto final público.

![Selecione ponto final público](media/common/select-public-endpoint.png)

Este comando prepara o seu projeto para funcionar em Azure Dev Spaces gerando um gráfico dockerfile e Helm. Também gera um *diretório .vscode* com configuração de depuragem na raiz do seu projeto.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Construa e execute código em Kubernetes a partir do Código do Estúdio Visual

Clique no ícone *Debug* à esquerda e clique em *.NET Core Launch (AZDS)* na parte superior.

![A imagem é do canto superior esquerdo da janela Visual Studio Code. O ícone de depurado é destacado, o painel esquerdo é intitulado "DEBUG", e uma lista à direita do título mostra "dot NET Core Launch (A Z D S).](media/get-started-netcore/debug-configuration.png)

Este comando constrói e executa o seu serviço em Azure Dev Spaces em modo de depuração. A janela *do Terminal* na parte inferior mostra a saída de construção e URLs para o seu serviço em execução em Azure Dev Spaces. A *Consola Debug* mostra a saída de registo.

> [!Note]
> Se não vir nenhum comando Azure Dev Spaces na *Paleta de Comandos,* certifique-se de que instalou a [extensão do Código do Estúdio Visual para Espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se abriu o *dev-spaces/samples/dotnetcore/getting-started/webfrontend* directy no Visual Studio Code.

Pode ver o serviço em execução abrindo a URL pública.

> [!Note]
> Inicialmente, o URL público pode mostrar um erro *de Bad Gateway.* Aguarde alguns segundos antes de refrescar a página web e deverá ver o seu serviço a funcionar.

Clique em *Debug* e, em seguida, *pare de depurar* para parar o depurante.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro no seu projeto e refazer *o .NET Core Launch (AZDS)*. Por exemplo:

1. Se a sua aplicação ainda estiver em execução, clique em *Debug* e, em seguida, *pare de depurar* para o impedir.
1. Atualizar [a linha `Controllers/HomeController.cs` 22 para:](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Guarde as alterações.
1. Rerun *.NET Core Launch (AZDs)*.
1. Navegue para o seu serviço de execução e clique em *Cerca de*.
1. Observe as suas mudanças.
1. Clique em *Debug* e pare *de depurar* para parar a sua aplicação.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Inicie o seu serviço em modo de depuramento utilizando *.NET Core Launch (AZDS)*.

Navegue de volta para a vista *do Explorer* clicando *em Ver* e depois *Explorador*. Abra `Controllers/HomeController.cs` e clique em algum lugar na linha 22 para colocar o seu cursor lá. Para definir um breakpoint, clique em *Debug* e depois *F9* *Toggle Breakpoint*.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. Destaque para o Código do Estúdio Visual e observa a linha 20. O ponto de rutura que definiu fez uma pausa no serviço na linha 20. Para retomar o serviço, clique em *F5* ou clique em *Debug* *e*continue . Volte ao seu navegador e note que a mensagem já está exibida.

Enquanto executa o seu serviço em Kubernetes com um depurador anexado, tem acesso total a informações de depurar, tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o cursor na linha 22 `Controllers/HomeController.cs` e atingindo *F9*.

## <a name="update-code-from-visual-studio-code"></a>Código de atualização do Código do Estúdio Visual

Enquanto o serviço está em modo de depuramento, atualize a linha 22 em `Controllers/HomeController.cs` . Por exemplo:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Guarde o ficheiro. Clique em *Debug* e *reinicie depuração* ou na *barra de ferramentas Debug,* clique no botão *Restart Debugging.*

![A barra de ferramentas Debug é uma pequena vidraça no centro superior da página (logo abaixo do título da página). O botão Reiniciar exibe uma seta circular e é realçado. A imagem do botão hover é "Restart (Ctrl+Shift+F5)".](media/common/debug-action-refresh.png)

Abra o seu serviço num browser e note que a sua mensagem atualizada está exposta.

Em vez de reconstruir e redistribuir uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recomplesca gradualmente o código dentro do recipiente existente para fornecer um loop de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximos passos

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários contentores e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços. 

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-netcore.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

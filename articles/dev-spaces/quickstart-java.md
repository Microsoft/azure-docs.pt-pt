---
title: 'Debug e iterado em Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar o Azure Dev Spaces e o Visual Studio Code para depurar e iterar rapidamente uma aplicação Java no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Java, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 5dcfff94d3a5296ed4462360ce85732c07b21cf5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245105"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Quickstart: Debug e iterate em Kubernetes com Visual Studio Code e Java - Azure Dev Spaces

Neste arranque rápido, você configura os Espaços Azure Dev com um cluster Kubernetes gerido, e usa uma aplicação Java em Visual Studio Code para desenvolver iterativamente código de depuração em recipientes. A Azure Dev Spaces permite-lhe depurar e testar todos os componentes da sua aplicação no Serviço Azure Kubernetes (AKS) com a configuração mínima da máquina de desenvolvimento. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Kit de [Desenvolvimento Java (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Os [Espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e [Java Debugger para Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) extensões para Visual Studio Code.
- [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster de serviço Azure Kubernetes

É preciso criar um aglomerado AKS numa [região apoiada.][supported-regions] Os seguintes comandos criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Enable Azure Dev Spaces no seu cluster AKS

Utilize o comando `use-dev-spaces` para ativar os Espaços Dev no seu cluster AKS e seguir as instruções. O seguinte comando permite a Dev Spaces no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço de v *padrão.*

> [!NOTE]
> O comando `use-dev-spaces` também instalará o Azure Dev Spaces CLI se ainda não estiver instalado. Não é possível instalar o Azure Dev Spaces CLI na Casca de Nuvem Azure.

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

## <a name="get-sample-application-code"></a>Obter código de aplicação de amostra

Neste artigo, utiliza a aplicação de amostra seleção [Azure Dev Spaces](https://github.com/Azure/dev-spaces) para demonstrar a utilização de Espaços Azure Dev.

Clone a aplicação do GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Prepare a aplicação da amostra no Código do Estúdio Visual

Open Visual Studio Code, selecione **File** em **seguida, Abra,** navegue para o *dev-spaces/samples/java/getting-start/webfrontend* diretório, e selecione **Open**.

Tem agora o projeto *webfrontend* aberto no Código do Estúdio Visual. Para executar a aplicação no seu espaço de v, gere os ativos da tabela Docker e Helm utilizando a extensão dos Espaços Azure Dev na Paleta de Comando.

Para abrir a Paleta de Comando no Código do Estúdio Visual, selecione **Ver** e ntão **Paleta de Comando**. Comece a digitar `Azure Dev Spaces` e **selecione Espaços Azure Dev: Prepare ficheiros de configuração para espaços Azure Dev**.

![Prepare ficheiros de configuração para espaços Azure Dev](./media/common/command-palette.png)

Quando o Visual Studio Code também o incita a configurar as suas imagens base, o porto exposto e o ponto final público, escolha `Azul Zulu OpenJDK for Azure (Free LTS)` para a imagem base, `8080` para a porta exposta e `Yes` para permitir um ponto final público.

![Selecione imagem base](media/get-started-java/select-base-image.png)

![Selecione porta exposta](media/get-started-java/select-exposed-port.png)

![Selecione ponto final público](media/get-started-java/select-public-endpoint.png)

Este comando prepara o seu projeto para funcionar em Espaços Azure Dev gerando um gráfico de Dockerfile e Helm. Também gera um diretório *.vscode* com configuração de depuração na raiz do seu projeto.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Construir e executar código em Kubernetes do Visual Studio Code

Selecione o ícone **Debug** à esquerda e selecione **Launch Java Program (AZDS)** na parte superior.

![Lançar programa Java](media/get-started-java/debug-configuration.png)

Este comando constrói e executa o seu serviço em Espaços Azure Dev. A janela **terminal** na parte inferior mostra a saída de construção e URLs para o seu serviço funcionando os Espaços Azure Dev. A **Consola Debug** mostra a saída de registo.

> [!Note]
> Se não vir nenhum comando da Azure Dev Spaces na Paleta de **Comando,** certifique-se de ter instalado a extensão do Código do [Estúdio Visual para espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também que abriu o *dev-spaces/samples/java/start-start/webfrontend* diretório no Visual Studio Code.

Pode ver o serviço a funcionar abrindo o URL público.

Selecione **Debug** e, em seguida, **pare de depuração** para parar o debugger.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro do seu projeto e reexecutar o **Programa Java de Lançamento (AZDS)** . Por exemplo:

1. Se a sua aplicação ainda estiver em funcionamento, selecione **Debug** e, em seguida, **pare de depuração** para o impedir.
1. Atualizar [linha 19 em `src/main/java/com/ms/sample/webfrontend/Application.java`:](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19)
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Guarde as alterações.
1. Reexecutar programa Java de **lançamento (AZDS)** .
1. Navegue para o seu serviço de funcionamento e observe as suas alterações.
1. Selecione **Debug** e, em seguida, **pare de depuração** para parar a sua aplicação.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Inicie o seu serviço utilizando o **Programa Launch Java (AZDS)** . Isto também executa o seu serviço em modo de depuração.

Navegue de volta à vista **Explorer** selecionando **Vista** e **explorer**. Abra *o src/main/java/java/com/ms/sample/webfrontend/Application.java* e clique em algum lugar na linha 19 para colocar o cursor lá. Para definir um ponto de rutura, prima **F9** ou selecione **Debug** e, em seguida, **Toggle Breakpoint**.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. O Regresso ao Código do Estúdio Visual e a linha 19 de observação estão em destaque. O ponto de rutura que definiu fez uma pausa no serviço na linha 19. Para retomar o serviço, prima **F5** ou selecione **Debug** **e,** em seguida, Continue . Volte ao seu navegador e note que a mensagem está agora apresentada.

Enquanto executa o seu serviço em Kubernetes com um debugger anexado, você tem acesso total a informações dedepuração tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o cursor na linha 19 na *linha sRC/main/java/com/ms/sample/webfrontend/Application.java* e pressionando **F9**.

## <a name="update-code-from-visual-studio-code"></a>Código de atualização do Código do Estúdio Visual

Enquanto o serviço estiver em execução no modo de depuração, atualize a linha 19 no *src/main/java/com/ms/sample/webfrontend/Application.java*. Por exemplo:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Guarde o ficheiro. Selecione **Debug** e, em seguida, **reiniciar depuração** ou na barra de **ferramentas Debug,** selecione o botão **Restart Debugging.**

![Refresh Debugging](media/common/debug-action-refresh.png)

Abra o seu serviço num browser e note que a sua mensagem atualizada está visualizada.

Em vez de reconstruir e reimplantar uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recompila gradualmente o código dentro do recipiente existente para fornecer um ciclo de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Working with multiple containers and team development](multi-service-java.md) (Trabalhar com vários contentores e o desenvolvimento em equipa)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

---
title: 'Debug e iterato em Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Este quickstart mostra-lhe como usar Azure Dev Spaces e Visual Studio Code para depurar e iterar rapidamente uma aplicação Java no Serviço Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Java, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: 53d7b602166ae463ddb821f5a9992de39f6e1644
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078721"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Quickstart: Debug e iterate em Kubernetes com Visual Studio Code e Java - Azure Dev Spaces

Neste quickstart, você configura Azure Dev Spaces com um cluster kubernetes gerido, e use uma app Java em Visual Studio Code para desenvolver e depurar código em recipientes. A Azure Dev Spaces permite depurar e testar todos os componentes da sua aplicação no Serviço Azure Kubernetes (AKS) com a configuração mínima da máquina de desenvolvimento. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Kit de Desenvolvimento de Java (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- Os [espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) e Java Debugger para extensões [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) para Código de Estúdio Visual.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git.](https://www.git-scm.com/downloads)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Criar um cluster do Azure Kubernetes Service

Você precisa criar um cluster AKS em uma [região apoiada][supported-regions]. Os seguintes comandos criam um grupo de recursos chamado *MyResourceGroup* e um cluster AKS chamado *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Ativar espaços Azure Dev no seu cluster AKS

Utilize o `use-dev-spaces` comando para ativar os Espaços Dev no seu cluster AKS e siga as indicações. O seguinte comando permite espaços Dev no cluster *MyAKS* no grupo *MyResourceGroup* e cria um espaço dev *predefinido.*

> [!NOTE]
> O `use-dev-spaces` comando também instalará o CLI dos Espaços Azure Dev se ainda não estiver instalado. Não é possível instalar o CLI Azure Dev Spaces na Azure Cloud Shell.

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

Abrir código de estúdio visual, selecionar **File** e depois **Abrir,** navegar para os *espaços dev/amostras/java/começar/webfrontend* diretório, e selecione **Open**.

Tem agora o projeto *webfrontend* aberto no Código do Estúdio Visual. Para executar a aplicação no seu espaço dev, gere os ativos do gráfico Docker e Helm utilizando a extensão Azure Dev Spaces na Paleta de Comando.

Para abrir a Paleta de Comando no Código do Estúdio Visual, **selecione Ver** e depois **Paleta de Comando**. Comece a escrever `Azure Dev Spaces` e selecione **Azure Dev Spaces: Prepare ficheiros de configuração para espaços Azure Dev**.

![Preparar ficheiros de configuração para espaços Azure Dev](./media/common/command-palette.png)

Quando o Código do Estúdio Visual também lhe pedir para configurar as suas imagens base, porta exposta e ponto final público, escolha `Azul Zulu OpenJDK for Azure (Free LTS)` a imagem base, `8080` para a porta exposta e para `Yes` ativar um ponto final público.

![Selecione a imagem base](media/get-started-java/select-base-image.png)

![Selecione a porta exposta](media/get-started-java/select-exposed-port.png)

![Selecione ponto final público](media/get-started-java/select-public-endpoint.png)

Este comando prepara o seu projeto para funcionar em Azure Dev Spaces gerando um gráfico dockerfile e Helm. Também gera um *diretório .vscode* com configuração de depuragem na raiz do seu projeto.

> [!TIP]
> O [gráfico Dockerfile e Helm](how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Construa e execute código em Kubernetes a partir do Código do Estúdio Visual

Selecione o ícone **Debug** à esquerda e selecione **Launch Java Program (AZDS)** na parte superior.

![Programa De Lançamento Java](media/get-started-java/debug-configuration.png)

Este comando constrói e executa o seu serviço em Azure Dev Spaces. A janela **do Terminal** na parte inferior mostra a saída de construção e URLs para o seu serviço que executa Azure Dev Spaces. A **Consola Debug** mostra a saída de registo.

> [!Note]
> Se não vir nenhum comando Azure Dev Spaces na **Paleta de Comandos,** certifique-se de que instalou a [extensão do Código do Estúdio Visual para Espaços Azure Dev](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Verifique também se abriu o *dev-spaces/samples/java/getting-started/webfrontend* directy no Visual Studio Code.

Pode ver o serviço em execução abrindo a URL pública.

Selecione **Debug** e, em **seguida, pare de depurar** para parar o depurante.

## <a name="update-code"></a>Atualizar código

Para implementar uma versão atualizada do seu serviço, pode atualizar qualquer ficheiro no seu projeto e refazer **o Programa De Lançamento java (AZDS)**. Por exemplo:

1. Se a sua aplicação ainda estiver em execução, selecione **Debug** e, em seguida, **pare de depurar** para o impedir.
1. Atualizar [a linha `src/main/java/com/ms/sample/webfrontend/Application.java` 19](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) para:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Guarde as alterações.
1. Rerun **Launch Java Program (AZDS)**.
1. Navegue para o seu serviço de funcionamento e observe as suas alterações.
1. Selecione **Debug** e, em seguida, **pare de depurar** para parar a sua aplicação.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Definição e utilização de pontos de rutura para depuração

Inicie o seu serviço utilizando **o Programa De Lançamento Java (AZDS)**. Isto também executa o seu serviço em modo de depuramento.

Navegue de volta para a vista **do Explorer** selecionando **Ver** e depois **Explorer**. Abra *src/main/java/com/ms/sample/webfrontend/Application.java* e clique em algum lugar na linha 19 para colocar o cursor lá. Para definir uma pressão de breakpoint **F9** ou selecione **Debug,** em **seguida, Toggle Breakpoint**.

Abra o seu serviço num browser e note que não é apresentada nenhuma mensagem. Destaque para o Código do Estúdio Visual e observa a linha 19. O ponto de rutura que definiu fez uma pausa no serviço na linha 19. Para retomar o serviço, prima **F5** ou selecione **Debug** e, em seguida, **Continue**. Volte ao seu navegador e note que a mensagem já está exibida.

Enquanto executa o seu serviço em Kubernetes com um depurador anexado, tem acesso total a informações de depurar, tais como a pilha de chamadas, variáveis locais e informações de exceção.

Retire o ponto de rutura colocando o cursor na linha 19 em *src/main/java/com/ms/sample/webfrontend/Application.java* e pressionando **F9**.

## <a name="update-code-from-visual-studio-code"></a>Código de atualização do Código do Estúdio Visual

Enquanto o serviço estiver em funcionamento no modo de depuramento, atualize a linha 19 em *src/main/java/com/ms/sample/webfrontend/Application.java*. Por exemplo:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Guarde o ficheiro. Selecione **Debug,** em seguida, **reinicie depuração** ou na **barra de ferramentas Debug**, selecione o botão **Restart Debugging.**

![Refresh Debugging](media/common/debug-action-refresh.png)

Abra o seu serviço num browser e note que a sua mensagem atualizada está exposta.

Em vez de reconstruir e redistribuir uma nova imagem de recipiente cada vez que são feitas edições de código, a Azure Dev Spaces recomplesca gradualmente o código dentro do recipiente existente para fornecer um loop de edição/depuração mais rápido.

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

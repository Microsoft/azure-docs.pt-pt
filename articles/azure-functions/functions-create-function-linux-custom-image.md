---
title: Criar Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 1865b1b96b5b8794f1518d639825ccd2f1dcd090
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773140"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Criar uma função no Linux usando uma imagem personalizada

As Funções do Azure permitem-lhe alojar as suas funções no Linux no seu próprio contentor personalizado. Também pode [alojar num contentor predefinido do Serviço de Aplicações do Azure](functions-create-first-azure-function-azure-cli-linux.md). Essa funcionalidade requer [o tempo de execução do Functions 2. x](functions-versions.md).

Neste tutorial, irá aprender a implementar as suas funções no Azure como uma imagem personalizada do Docker. Esse padrão é útil quando você precisa personalizar a imagem de contêiner interna. Pode querer utilizar uma imagem personalizada quando as suas funções precisarem de uma versão de idioma ou dependência específicas, ou de uma configuração não fornecida na imagem incorporada. As imagens base com suporte para Azure Functions são encontradas no [repositório de imagens base Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base). 

Este tutorial explica como utilizar o Azure Functions Core Tools para criar uma função numa imagem do Linux personalizada. Publique esta imagem numa aplicação de funções no Azure, que foi criada com a CLI do Azure. Posteriormente, você atualiza sua função para se conectar ao armazenamento de filas do Azure. Você também habilita o.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de funções e o Dockerfile com Ferramentas de Núcleo.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar uma conta de Armazenamento do Azure.
> * Crie um plano de hospedagem Premium.
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Habilite a implantação contínua.
> * Adicionar Application Insights monitoramento.

São suportados os seguintes passos num computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

* Instale a [versão 2.x das Ferramentas Base do Azure](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual.  
Pode também utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicação de funções local

Execute o seguinte comando na linha de comandos para criar um projeto de aplicação de funções na pasta `MyFunctionProj` do diretório local atual. Para um projeto Python, você [deve estar executando o em um ambiente virtual](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

Quando inclui a opção `--docker`, é gerado um dockerfile para o projeto. Este ficheiro é utilizado para criar um contentor personalizado para executar o projeto. A imagem base utilizada depende do idioma do runtime do trabalho escolhido.  

Quando lhe for pedido, escolha um runtime do trabalho entre os seguintes idiomas:

* `dotnet`: cria um projeto de biblioteca de classes do .NET Core (. csproj).
* `node`: cria um projeto do JavaScript.
* `python`: cria um projeto Python.  

Quando o comando for executado, verá algo parecido com o seguinte resultado:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Utilize o comando seguinte para navegar para a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Compilar a imagem a partir do Dockerfile

Veja o _Dockerfile_ no diretório de raiz do projeto. Este ficheiro descreve o ambiente que é necessário para executar a aplicação de funções no Linux. O exemplo seguinte é um Dockerfile que cria um contentor que executa uma aplicação de funções no runtime do trabalho do JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> A lista completa de imagens base com suporte para Azure Functions pode ser encontrada na [página Azure Functions imagem base](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Execute o comando `build`
Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e indique um nome, `mydockerimage`, e uma etiqueta, `v1.0.0`. Substitua `<docker-id>` pelo ID da sua conta do Docker Hub. Este comando cria a imagem do Docker para o contentor.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando o comando for executado, verá algo parecido com o seguinte resultado, que é, neste caso, para um runtime de trabalho do JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testar a imagem localmente
Verifique se a imagem que incorpora funciona ao executar a imagem do Docker num contentor local. Emita o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e transmita o nome e a etiqueta da imagem ao mesmo. Certifique-se de que utiliza o argumento `-p` para especificar a porta.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução num contentor local do Docker, certifique-se de que a aplicação de funções e o contentor estão a funcionar corretamente ao navegar para <http://localhost:8080>.

![Teste a aplicação de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Neste ponto, ao tentar chamar sua função HTTP específica, você obtém uma resposta de erro HTTP 401. Isso ocorre porque sua função é executada no contêiner local como faria no Azure, o que significa que a chave de função é necessária. Como o contêiner ainda não foi publicado em um aplicativo de funções, não há nenhuma chave de função disponível. Você verá posteriormente que, quando usar as ferramentas principais para publicar seu contêiner, as teclas de função serão mostradas a você. Se você quiser testar sua função em execução no contêiner local, poderá alterar a chave de [autorização](functions-bindings-http-webhook.md#authorization-keys) para `anonymous`. 

Depois de ter verificado a aplicação de funções no contentor, pare a execução. Agora, pode enviar a imagem personalizada para a sua conta do Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Enviar a imagem personalizada para o Docker Hub

Um registo de contentor é uma aplicação que aloja imagens e disponibiliza serviços de imagens e contentores. Para partilhar a sua imagem, tem de enviá-la para um registo. O Docker Hub é um registo de contentores para imagens do Docker que lhe permite alojar os seus próprios repositórios, sejam públicos ou privados.

Antes de poder enviar uma imagem, tem de iniciar sessão no Docker Hub com o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome da sua conta e introduza a palavra-passe na consola, quando lhe for pedido. Para outras opções de palavra-passe do Docker Hub, veja a [documentação do comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Uma mensagem de "logon bem-sucedido" confirma que você está conectado. Depois de iniciar sessão, envie a imagem para o Docker Hub com o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Veja o resultado do comando para confirmar se o envio foi bem-sucedido.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Agora, pode utilizar esta imagem como origem de implementação para uma nova aplicação de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Criar um plano Premium

Hospedagem do Linux para contêineres de funções personalizadas com suporte em [planos dedicados (serviço de aplicativo)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Este tutorial usa um plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md).

O exemplo a seguir cria um plano Premium `myPremiumPlan` chamado no tipo de preço **elástico Premium 1** (`--sku EP1`), na região oeste dos EUA`-location WestUS`() e em um contêiner do Linux`--is-linux`().

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>Criar e implementar a imagem personalizada

O aplicativo de funções gerencia a execução de suas funções em seu plano de hospedagem. Utilize o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar uma aplicação de funções a partir de uma imagem do Docker Hub.

No comando seguinte, substitua o nome da sua aplicação de funções exclusivo onde vir o marcador de posição `<app_name>` e o nome da conta de armazenamento para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. Como anteriormente, `<docker-id>` é o nome da sua conta do Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

O parâmetro _deployment-container-image-name_ indica a imagem alojada no Docker Hub a utilizar para criar a aplicação de funções. Use o comando [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para exibir informações sobre a imagem usada para implantação. Use o comando [AZ functionapp config container Set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar de uma imagem diferente.

## <a name="configure-the-function-app"></a>Configurar a aplicação de funções

A função precisa da cadeia de ligação para ligar à conta de armazenamento predefinida. Quando você estiver publicando sua imagem personalizada em uma conta de contêiner privada, defina essas configurações de aplicativo como variáveis de ambiente no Dockerfile usando a [instrução env](https://docs.docker.com/engine/reference/builder/#env)ou algo semelhante.

Neste caso, `<storage_name>` é o nome da conta de armazenamento que criou. Obtenha a cadeia de ligação com o comando [az storage account show-connection-string](/cli/azure/storage/account). Adicione estas definições de aplicação na aplicação de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se o contêiner for privado, você também precisará definir as seguintes configurações de aplicativo  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Você precisará parar e iniciar seu aplicativo de funções para que esses valores sejam selecionados

Agora, pode testar as suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>Habilitar implantação contínua

Um dos benefícios de usar contêineres é a capacidade de implantar atualizações automaticamente quando os contêineres são atualizados no registro. Habilite a implantação contínua com o comando de [configuração de contêiner AZ functionapp Deployment](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Esse comando retorna a URL de webhook de implantação após a implantação contínua ser habilitada. Você também pode usar o comando [AZ functionapp Deployment contêiner show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) para retornar essa URL. 

Copie a URL de implantação e navegue até o repositório DockerHub, escolha a guia **WebHooks** , digite um **nome de webhook** para o webhook, Cole a URL na **URL do webhook**e, em seguida, **+** escolha o sinal de adição ().

![Adicionar o webhook em seu repositório DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Com o webhook definido, todas as atualizações da imagem vinculada no DockerHub resultam no aplicativo de funções baixando e instalando a imagem mais recente.

## <a name="enable-application-insights"></a>Ativar o Application Insights

A maneira recomendada para monitorar a execução de suas funções é integrar seu aplicativo de funções com o Aplicativo Azure insights. Quando você cria um aplicativo de funções no portal do Azure, essa integração é feita para você por padrão. No entanto, quando você cria seu aplicativo de funções usando o CLI do Azure, a integração em seu aplicativo de funções no Azure não é feita.

Para habilitar Application Insights para seu aplicativo de funções:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [monitorar Azure Functions](functions-monitoring.md).

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de funções e o Dockerfile com Ferramentas de Núcleo.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar uma conta de Armazenamento do Azure.
> * Crie um plano Premium do Linux.
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Habilite a implantação contínua.
> * Adicionar Application Insights monitoramento.

> [!div class="nextstepaction"] 
> [Saiba mais sobre as opções de implantação de funções no Azure](functions-deployment-technologies.md)

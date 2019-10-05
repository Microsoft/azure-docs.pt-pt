---
title: Criar Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 54d7dc4e57991f6b773169f539a86fdc8451cbba
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950379"
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
> * Habilite conexões SSH para o contêiner.
> * Adicione uma associação de saída de armazenamento de fila. 
> * Adicionar Application Insights monitoramento.

São suportados os seguintes passos num computador Mac, Windows ou Linux. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

* Instale a [versão 2.x das Ferramentas Base do Azure](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual.  
Pode também utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Criar o projeto local

Execute o seguinte comando na linha de comandos para criar um projeto de aplicação de funções na pasta `MyFunctionProj` do diretório local atual. Para um projeto Python, você [deve estar executando o em um ambiente virtual](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

Quando inclui a opção `--docker`, é gerado um dockerfile para o projeto. Este ficheiro é utilizado para criar um contentor personalizado para executar o projeto. A imagem base utilizada depende do idioma do runtime do trabalho escolhido.  

Quando lhe for pedido, escolha um runtime do trabalho entre os seguintes idiomas:

* `dotnet`: cria um projeto de biblioteca de classes do .NET Core (. csproj).
* `node`: cria um projeto do JavaScript.
* `python`: cria um projeto Python.  

Utilize o comando seguinte para navegar para a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Compilar a partir do arquivo do Docker

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

Quando o comando for concluído, você poderá executar o novo contêiner localmente.

### <a name="run-the-image-locally"></a>Executar a imagem localmente
Verifique se a imagem que incorpora funciona ao executar a imagem do Docker num contentor local. Emita o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e transmita o nome e a etiqueta da imagem ao mesmo. Certifique-se de que utiliza o argumento `-p` para especificar a porta.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução num contentor local do Docker, certifique-se de que a aplicação de funções e o contentor estão a funcionar corretamente ao navegar para <http://localhost:8080>.

![Execute o aplicativo de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Neste ponto, ao tentar chamar sua função HTTP específica, você obtém uma resposta de erro HTTP 401. Isso ocorre porque sua função é executada no contêiner local como faria no Azure, o que significa que a chave de função é necessária. Como o contêiner ainda não foi publicado em um aplicativo de funções, não há nenhuma chave de função disponível. Você verá posteriormente que, quando usar as ferramentas principais para publicar seu contêiner, as teclas de função serão mostradas a você. Se você quiser testar sua função em execução no contêiner local, poderá alterar a chave de [autorização](functions-bindings-http-webhook.md#authorization-keys) para `anonymous`. 

Depois de ter verificado a aplicação de funções no contentor, pare a execução. Agora, pode enviar a imagem personalizada para a sua conta do Docker Hub.

## <a name="push-to-docker-hub"></a>Enviar por push para o Hub do Docker

Um registo de contentor é uma aplicação que aloja imagens e disponibiliza serviços de imagens e contentores. Para partilhar a sua imagem, tem de enviá-la para um registo. O Docker Hub é um registo de contentores para imagens do Docker que lhe permite alojar os seus próprios repositórios, sejam públicos ou privados.

Antes de poder enviar uma imagem, tem de iniciar sessão no Docker Hub com o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome da sua conta e introduza a palavra-passe na consola, quando lhe for pedido. Para outras opções de palavra-passe do Docker Hub, veja a [documentação do comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Uma mensagem de "logon bem-sucedido" confirma que você está conectado. Depois de iniciar sessão, envie a imagem para o Docker Hub com o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Depois que o push for bem sucedido, você poderá usar a imagem como a origem de implantação para um novo aplicativo de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Criar um plano Premium

Hospedagem do Linux para contêineres de funções personalizadas com suporte em [planos dedicados (serviço de aplicativo)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Este tutorial usa um plano Premium, que pode ser dimensionado conforme necessário. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md).

O exemplo a seguir cria um plano Premium chamado `myPremiumPlan` no tipo de preço **elástico Premium 1** (`--sku EP1`), na região oeste dos eua (`-location WestUS`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Criar um aplicativo a partir da imagem

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

## <a name="verify-your-functions"></a>Verificar suas funções

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

A função disparada por HTTP que você criou requer uma [chave de função](functions-bindings-http-webhook.md#authorization-keys) ao chamar o ponto de extremidade. Neste momento, a maneira mais fácil de obter a URL da função, incluindo a chave, é da [Azure portal]. 

> [!TIP]
> Você também pode obter suas chaves de função usando as [APIs de gerenciamento de chaves](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), o que exige que você apresente um [token de portador para autenticação](/cli/azure/account#az-account-get-access-token).

Localize seu novo aplicativo de funções no [Azure portal] digitando o nome do aplicativo de funções na caixa de **pesquisa** na parte superior da página e selecionando o recurso do **serviço de aplicativo** .

Selecione a função **MyHttpTrigger** , selecione **</> URL de função Get** > **padrão (chave de função)**  > **cópia**.

![Copiar o URL da função a partir do portal do Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

Nessa URL, a chave de função é o parâmetro de consulta `code`. 

> [!NOTE]  
> Como seu aplicativo de funções é implantado como um contêiner, você não pode fazer alterações no seu código de função no Portal. Em vez disso, você deve atualizar o projeto no contêiner local e publicá-lo novamente no Azure.

Cole o URL da função na barra de endereço do navegador. Adicione o valor da cadeia de consulta `&name=<yourname>` ao final deste URL e prima a tecla `Enter` no teclado para executar o pedido. Deverá ver a resposta devolvida pela função apresentada no browser.

O exemplo seguinte mostra a resposta no browser:

![Resposta da função no browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

O URL do pedido inclui uma chave que é necessária, por predefinição, para aceder à sua função através de HTTP. 

## <a name="enable-continuous-deployment"></a>Habilitar implantação contínua

Um dos benefícios de usar contêineres é o suporte para implantação contínua. O Functions permite que você implante atualizações automaticamente quando o contêiner é atualizado no registro. Habilite a implantação contínua com o comando de [configuração de contêiner AZ functionapp Deployment](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Esse comando retorna a URL de webhook de implantação após a implantação contínua ser habilitada. Você também pode usar o comando [AZ functionapp Deployment contêiner show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) para retornar essa URL. 

Copie a URL de implantação e navegue até o repositório DockerHub, escolha a guia **WebHooks** , digite um **nome de webhook** para o webhook, Cole a URL na **URL do webhook**e, em seguida, escolha o sinal de adição ( **+** ).

![Adicionar o webhook em seu repositório DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Com o webhook definido, todas as atualizações da imagem vinculada no DockerHub resultam no aplicativo de funções baixando e instalando a imagem mais recente.

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Com o SSH habilitado, você pode se conectar ao seu contêiner usando as ferramentas avançadas do serviço de aplicativo (kudu). Para facilitar a conexão com o contêiner usando SSH, as funções fornecem uma imagem base que tem o SSH já habilitado. 

### <a name="change-the-base-image"></a>Alterar a imagem base

Em seu dockerfile, acrescente a cadeia de caracteres `-appservice` à imagem base na instrução `FROM`, que para um projeto JavaScript é semelhante ao seguinte.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

As diferenças nas duas imagens base permitem conexões SSH em seu contêiner. Essas diferenças são detalhadas neste [tutorial de serviços de aplicativos](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Recompilar e reimplantar a imagem

Na pasta raiz, execute o comando [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) novamente, como antes, substitua `<docker-id>` pela ID da conta do Hub do Docker. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Envie a imagem atualizada de volta para o Hub do Docker.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

A imagem atualizada é reimplantada para seu aplicativo de funções.

### <a name="connect-to-your-container-in-azure"></a>Conectar-se ao seu contêiner no Azure

No navegador, navegue até o seguinte kudu (ferramentas avançadas) `scm.` ponto de extremidade para o contêiner do aplicativo de funções, substituindo `<app_name>` pelo nome do seu aplicativo de funções.

```
https://<app_name>.scm.azurewebsites.net/
```

Entre em sua conta do Azure e, em seguida, selecione a guia **SSH** para criar uma conexão SSH em seu contêiner.

Depois que a conexão for estabelecida, execute o comando `top` para exibir os processos em execução no momento. 

![Comando superior do Linux em execução em uma sessão SSH.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Gravar no armazenamento de fila

O Functions permite que você conecte os serviços do Azure e outros recursos a funções sem precisar escrever seu próprio código de integração. Essas *associações*, que representam entrada e saída, são declaradas dentro da definição da função. Os dados das associações são fornecidos para a função como parâmetros. Um *gatilho* é um tipo especial de associação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e saída. Para saber mais, confira [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

Esta seção mostra como integrar sua função a uma fila de armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados de uma solicitação HTTP em uma mensagem na fila.

### <a name="download-the-function-app-settings"></a>Baixar as configurações do aplicativo de funções

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

Como você está usando uma associação de saída de armazenamento de fila, você deve ter a extensão de associações de armazenamento instalada antes de executar o projeto. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Com exceção dos gatilhos HTTP e Timer, as associações são implementadas como pacotes de extensão. Execute o comando [dotnet adicionar pacote](/dotnet/core/tools/dotnet-add-package) a seguir na janela do terminal para adicionar o pacote de extensão de armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> Ao usar o Visual Studio, você também pode usar o Gerenciador de pacotes NuGet para adicionar esse pacote.

---

Agora, você pode adicionar uma associação de saída de armazenamento ao seu projeto.

### <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em funções, cada tipo de associação requer um `direction`, `type` e um @no__t exclusivo a ser definido no arquivo function. JSON. A maneira como você define esses atributos depende do idioma do seu aplicativo de funções.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

Depois que a associação é definida, você pode usar o `name` da Associação para acessá-la como um atributo na assinatura da função. Usando uma associação de saída, você não precisa usar o código do SDK de armazenamento do Azure para autenticação, obter uma referência de fila ou gravar dados. O tempo de execução de funções e a associação de saída de fila executam essas tarefas para você.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Atualizar o contêiner hospedado

Na pasta raiz, execute novamente o comando [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) e, desta vez, atualize a versão na marca para `v1.0.2`. Como antes, substitua `<docker-id>` pela ID da conta do Hub do Docker. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Envie a imagem atualizada de volta para o repositório.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verificar as atualizações no Azure

Use a mesma URL de antes do navegador para disparar sua função. Você deve ver a mesma resposta. No entanto, desta vez, a cadeia de caracteres que você passa como o parâmetro `name` é gravada na fila de armazenamento `outqueue`.

### <a name="set-the-storage-account-connection"></a>Definir a conexão da conta de armazenamento

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Consultar a fila de armazenamento

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou com êxito seu contêiner personalizado em um aplicativo de funções no Azure, considere ler mais sobre os seguintes tópicos:

+ [Funções de monitoramento](functions-monitoring.md)
+ [Opções de escala e Hospedagem](functions-scale.md)
+ [Hospedagem sem servidor baseada em kubernetes](functions-kubernetes-keda.md)

[Azure portal]: https://portal.azure.com

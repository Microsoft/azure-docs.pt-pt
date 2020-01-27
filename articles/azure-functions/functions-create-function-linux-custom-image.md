---
title: Criar Azure Functions no Linux usando uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions01
ms.openlocfilehash: 67d9ff3d4a69ed12d1fd023085eb33a68d02d37b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711083"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Criar uma função no Linux usando um recipiente personalizado

Neste tutorial, cria e implanta o código Python para as Funções Azure como um recipiente Docker personalizado usando uma imagem base linux. Normalmente, utiliza-se uma imagem personalizada quando as suas funções requerem uma versão idioma específica ou tem uma dependência ou configuração específica que não é fornecida pela imagem incorporada.

Também pode utilizar um recipiente de serviço de aplicações Azure padrão, tal como descrito no [Create your first function hospedado no Linux](functions-create-first-azure-function-azure-cli-linux.md). As imagens de base suportadas para funções azure são encontradas no repo de [imagens base funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de função e dockerfile utilizando as Ferramentas Core funções do Azure.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar recursos de apoio em Azure para a app de funções
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Habilite a implantação contínua.
> * Habilite conexões SSH para o contêiner.
> * Adicione uma associação de saída de armazenamento de fila. 

Pode seguir este tutorial em qualquer computador que execute Windows, Mac OS ou Linux. A conclusão do tutorial incorrerá em custos de alguns dólares americanos na sua conta Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- As [funções azure Core Tools](./functions-run-local.md#v2) versão 2.7.1846 ou um posterior
- A versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.77 ou posterior
- As [Funções Azure 2.x tempo](functions-versions.md) de funcionamento
- Os seguintes componentes de tempo de execução da linguagem:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x ou mais tarde](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bit](https://www.python.org/downloads/release/python-3610/) ou [Python 3.7 - 64 bit](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Uma [identificação do Docker](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Verificação pré-requisito

1. Numa janela de terminais ou comando, faça `func --version` para verificar se as Ferramentas Core funções Do Azure são a versão 2.7.1846 ou posterior.
1. Execute `az --version` para verificar se a versão Azure CLI é de 2.0.76 ou mais tarde.
1. Execute `az login` para iniciar sessão no Azure e verificar uma subscrição ativa.
1. Corra `docker login` para se inscrever no Docker. Este comando falha se o Docker não estiver a funcionar, caso em que começar a tentar o comando.

## <a name="create-and-test-the-local-functions-project"></a>Criar e testar o projeto de funções locais

1. Num aviso de terminal ou comando, crie uma pasta para este tutorial num local apropriado e, em seguida, navegue para essa pasta.

1. Siga as instruções sobre [Criar e ativar um ambiente virtual](functions-create-first-function-python.md#create-and-activate-a-virtual-environment) para criar um ambiente virtual para uso com este tutorial.

1. Execute o seguinte comando para o seu idioma escolhido para criar um projeto de aplicação de função numa pasta chamada `LocalFunctionsProject`. A opção `--docker` gera uma `Dockerfile` para o projeto, que define um recipiente personalizado adequado para utilização com funções Azure e o tempo de execução selecionado.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Navegue na pasta do projeto:

    ```
    cd LocalFunctionsProject
    ```
    
1. Adicione uma função ao seu projeto utilizando o seguinte comando, onde o argumento `--name` é o nome único da sua função e o `--template` argumento especifica o gatilho da função. `func new` criar uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração chamado *função.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Para testar a função localmente, inicie o hospedeiro local funções Azure na pasta *LocalFunctionsProject:*
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. Assim que vir o ponto final `HttpExample` aparecer na saída, navegue para `http://localhost:7071/api/HttpExample?name=Functions`. O navegador deve apresentar uma mensagem como "Olá, Funções" (variado ligeiramente dependendo da sua linguagem de programação escolhida).

1. Utilize **o Ctrl**-**C** para parar o hospedeiro.

## <a name="build-the-container-image-and-test-locally"></a>Construa a imagem do recipiente e teste localmente

1. (Opcional) Examine o *Dockerfile" na pasta *LocalFunctionsProj.* O Dockerfile descreve o ambiente necessário para executar a aplicação de função no Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > A lista completa de imagens base suportadas para funções azure pode ser encontrada na página de [imagem base funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. Na pasta *LocalFunctionsProject,* execute o comando de construção do [estivador](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, `azurefunctionsimage`, e etiqueta, `v1.0.0`. Substitua `<docker_id>` pelo ID da sua conta do Docker Hub. Este comando cria a imagem do Docker para o contentor.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    Quando o comando for concluído, você poderá executar o novo contêiner localmente.
    
1. Para testar a construção, faça a imagem num contentor local utilizando o comando de execução do [estivador,](https://docs.docker.com/engine/reference/commandline/run/) substituindo novamente `<docker_id>` com o seu Docker ID e adicionando o argumento das portas, `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Uma vez que a imagem esteja em execução num contentor local, abra um navegador para `http://localhost:8080`, que deve exibir a imagem do espaço reservado mostrada abaixo. A imagem aparece neste ponto porque a sua função está a funcionar no contentor local, como em Azure, o que significa que está protegida por uma chave de acesso, tal como definida na *função.json* com a propriedade `"authLevel": "function"`. O contentor ainda não foi publicado numa aplicação de funções no Azure, no entanto, a chave ainda não está disponível. Se quiser testar localmente, pare o estivador, altere a propriedade de autorização para `"authLevel": "anonymous"`, reconstrua a imagem e reinicie o estivador. Em seguida, redefinir `"authLevel": "function"` em *função.json*. Para mais informações, consulte [as chaves de autorização](functions-bindings-http-webhook.md#authorization-keys).

    ![Imagem de espaço reservado indicando que o recipiente está funcionando localmente](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. Depois de verificar a aplicação de função no recipiente, pare o estivador com **ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Empurre a imagem para Docker Hub

Docker Hub é um registo de contentores que acolhe imagens e fornece serviços de imagem e contentores. Para partilhar a sua imagem, que inclui a implantação para o Azure, deve empurrá-la para um registo.

1. Se ainda não assinou com o Docker, faça-o com o comando de login do [estivador,](https://docs.docker.com/engine/reference/commandline/login/) substituindo `<docker_id>` pela sua identificação do Docker. Este comando solicita-lhe o seu nome de utilizador e palavra-passe. Uma mensagem "Login Succeeded" confirma que está inscrito.

    ```
    docker login
    ```
    
1. Depois de ter assinado, empurre a imagem para o Docker Hub usando o comando de pressão do [estivador,](https://docs.docker.com/engine/reference/commandline/push/) substituindo novamente `<docker_id>` com a sua identificação do Docker.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Dependendo da velocidade da rede, empurrar a imagem pela primeira vez pode demorar alguns minutos (impulsionar alterações subsequentes é muito mais rápido). Enquanto espera, pode seguir para a próxima secção e criar recursos Azure em outro terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de apoio para a sua função

Para implementar o seu código de função para o Azure, precisa de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento Azure, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de funções Azure, que fornece o ambiente para executar o seu código de função. Uma aplicação de função mapeia o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação e partilha de recursos.

Usa comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

1. Inscreva-se no Azure com o comando [de login az:](/cli/azure/group#az-login)

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos chamado `AzureFunctionsContainers-rg` na região `westeurope`. (Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível a partir do comando `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Você não pode hospedar aplicativos Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente chamado `AzureFunctionsContainers-rg` com uma aplicação de função Windows ou aplicação web, deve utilizar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento geral no seu grupo de recursos e região utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, substitua `<storage_name>` por um nome globalmente único adequado a si. Os nomes devem conter três a 24 caracteres e apenas letras minúsculas. `Standard_LRS` especifica uma conta típica de uso geral.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento incorre apenas alguns cêntimos de USD para este tutorial.
    
1. Utilize o comando para criar um plano Premium para funções Azure denominado `myPremiumPlan` no nível de preços **Elástico Premium 1** (`--sku EP1`), na região da Europa Ocidental (`-location westeurope`, ou utilize uma região adequada perto de si) e num recipiente Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    O alojamento linux para recipientes de funções personalizadas é suportado em [planos dedicados (App Service)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Utilizamos aqui o plano Premium, que pode escalar conforme necessário. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md). Para calcular os custos, consulte a página de preços das [Funções](https://azure.microsoft.com/pricing/details/functions/).

    O comando também prevê uma instância de Insights de Aplicação Azure associada no mesmo grupo de recursos, com o qual pode monitorizar a sua aplicação de função e visualizar registos. Para mais informações, consulte as [Funções Monitor Azure](functions-monitoring.md). A instância não incorre em custos até que o ative.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Criar e configurar uma aplicação de função no Azure com a imagem

Uma aplicação de função no Azure gere a execução das suas funções no seu plano de hospedagem. Nesta secção, utiliza os recursos Azure da secção anterior para criar uma aplicação de função a partir de uma imagem no Docker Hub e configurá-la com uma cadeia de ligação ao Armazenamento Azure.

1. Criar a aplicação Funções utilizando o [app de funções az criar](/cli/azure/functionapp#az-functionapp-create) comando. No exemplo seguinte, substitua `<storage_name>` pelo nome utilizado na secção anterior para a conta de armazenamento. Substitua também `<app_name>` por um nome globalmente único apropriado para si, e `<docker_id>` com o seu Docker ID.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    O parâmetro de nome de *imagem de contentor de implantação* especifica a imagem a utilizar para a aplicação de função. Pode utilizar o comando do recipiente de visualização de [config az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para visualizar informações sobre a imagem utilizada para a implantação. YOu também pode usar o comando de conjunto de conjunto de [recipientes de config az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar a partir de uma imagem diferente.

1. Recupere a cadeia de ligação para a conta de armazenamento que criou utilizando o comando de cadeia de conexão de conta [de armazenamento az,](/cli/azure/storage/account) atribuindo-o a uma variável de concha `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    
1. Add this setting to the function app by using the [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) command. In the following command, replace `<app_name>` with the name of your function app, and replace `<connection_string>` with the connection string from the previous step (a long encoded string that begins with "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. A função pode agora utilizar esta cadeia de ligação para aceder à conta de armazenamento.

> [!TIP]
> Em bash, você pode usar uma variável de concha para capturar a cadeia de ligação em vez de usar a área de reparação. Em primeiro lugar, utilize o seguinte comando para criar uma variável com a cadeia de ligação:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Em seguida, consulte a variável no segundo comando:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Se publicar a sua imagem personalizada numa conta de contentores privados, deve utilizar variáveis ambientais no Dockerfile para a cadeia de ligação. Para mais informações, consulte a [instrução env](https://docs.docker.com/engine/reference/builder/#env). Também deve definir as variáveis `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD`. Para utilizar os valores, então, deve reconstruir a imagem, empurrar a imagem para o registo e, em seguida, reiniciar a aplicação de função no Azure.

## <a name="verify-your-functions-on-azure"></a>Verifique as suas funções no Azure

Com a imagem implementada na aplicação de funções no Azure, pode agora invocar a função através de pedidos HTTP. Como a definição *fun.json* inclui a propriedade `"authLevel": "function"`, você deve primeiro obter a chave de acesso (também chamada de "chave de função") e incluí-la como um parâmetro DE URL em quaisquer pedidos para o ponto final.

1. Recupere o URL de função com a tecla de acesso (função) utilizando o portal Azure, ou utilizando o Azure CLI com o comando `az rest`.)

    # <a name="portaltabportal"></a>[Portal](#tab/portal)

    1. Inscreva-se no portal Azure e, em seguida, localize a sua aplicação de função, inserindo o nome da sua aplicação de função na caixa **de pesquisa** no topo da página. Nos resultados, selecione o recurso **do Serviço de Aplicações.**

    1. No painel de navegação esquerdo, em **funções (Ler Apenas)** , selecione o nome da sua função.

    1. No painel de detalhes, **selecione <> Obtenha URL de função:**
    
        ![O comando URL da função Get no portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. No popup, selecione **predefinido (Tecla de função)** e, em seguida, **Copiar**. A chave é a cadeia de caracteres que se segue `?code=`.

        ![Copiar o URL de função do portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Como seu aplicativo de funções é implantado como um contêiner, você não pode fazer alterações no seu código de função no Portal. Em vez disso, deve atualizar o projeto à imagem local, empurrar a imagem para o registo novamente e, em seguida, recolocar para Azure. Pode configurar uma implantação contínua numa secção posterior.
    
    # <a name="azure-clitabazurecli"></a>[CLI do Azure](#tab/azurecli)

    1. Construa uma cadeia DE URL no seguinte formato, substituindo `<subscription_id>`, `<resource_group>`e `<app_name>` com o seu ID de subscrição Azure, o grupo de recursos da sua aplicação de função e o nome da sua aplicação de função, respectivamente:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Por exemplo, o URL pode olhar para o seguinte endereço:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Por conveniência, pode, em vez disso, atribuir o URL a uma variável ambiental e usá-lo no comando `az rest`.
    
    1. Executar o seguinte comando `az rest` (disponível na versão Azure CLI 2.0.77 e mais tarde), substituindo `<uri>` com a corda URI do último passo, incluindo as cotações:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A saída do comando é a chave de função. O URL de função completa é então `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, substituindo `<app_name>`, `<function_name>`, e `<key>` com os seus valores específicos.
    
        > [!NOTE]
        > A chave aqui recuperada é a chave do *hospedeiro* que funciona para todas as funções na aplicação de funções; o método mostrado para o portal recupera a chave apenas para uma função.

    ---

1. Colá-lo na barra de endereços do seu navegador, adicionando o parâmetro `&name=Azure` ao final deste URL. Textocomo "Hello Azure" deve aparecer no navegador.

    ![Resposta da função no browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Para testar a autorização, retire o parâmetro code= do URL e verifique se não obtém resposta da função.


## <a name="enable-continuous-deployment-to-azure"></a>Permitir a implantação contínua para o Azure

Pode ativar as Funções Azure para atualizar automaticamente a sua implementação de uma imagem sempre que atualizar a imagem no registo.

1. Ativar a implantação contínua utilizando o comando config do recipiente de implementação do recipiente de implementação de placas [az,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) substituindo `<app_name>` com o nome da sua aplicação de funções:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Este comando permite a implementação contínua e devolve o URL de webhook de implementação. (Pode recuperar este URL mais tarde utilizando o comando de implementação do contentor de implementação de app de [funções az.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Copie o URL de webhook de implementação para a área de reparação.

1. Open [Docker Hub,](https://hub.docker.com/)inscreva-se e selecione **Repositórios** na barra de navegação. Localizar e selecionar imagem, selecionar o separador **Webhooks,** especificar um **nome Webhook,** colar o URL no **URL do Webhook,** e, em seguida, selecionar **Criar:**

    ![Adicionar o webhook em seu repositório DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Com o conjunto webhook, o Azure Functions reimplementa a sua imagem sempre que a atualiza no Docker Hub.

## <a name="enable-ssh-connections"></a>Habilitar conexões SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Com o SSH habilitado, você pode se conectar ao seu contêiner usando as ferramentas avançadas do serviço de aplicativo (kudu). Para facilitar a ligação ao seu recipiente utilizando o SSH, a Azure Functions fornece uma imagem de base que já tem SSH ativado. Só precisa editar o seu Dockerfile, depois reconstruir e reimplantar a imagem. Em seguida, pode ligar-se ao recipiente através das Ferramentas Avançadas (Kudu)

1. No seu Dockerfile, acomode a cadeia `-appservice` à imagem base na sua instrução `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    As diferenças entre as imagens base são descritas no Tutorial de Imagens de [App - Imagens personalizadas do estivador.](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)

1. Reconstruir a imagem usando o comando `docker build` novamente, substituindo `<docker_id>` pelo seu Id Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Empurre a imagem atualizada para o Docker Hub, que deve demorar consideravelmente menos tempo do que o primeiro empurrão apenas os segmentos atualizados da imagem precisam de ser carregados.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. As Funções Azure reimplantam automaticamente a imagem para a sua aplicação de funções; o processo ocorre em menos de um minuto.

1. Num navegador, abra `https://<app_name>.scm.azurewebsites.net/`, substituindo `<app_name>` pelo seu nome único. Este URL é o ponto final das Ferramentas Avançadas (Kudu) para o seu recipiente de aplicação de funções.

1. Inscreva-se na sua conta Azure e, em seguida, selecione o **SSH** para estabelecer uma ligação com o recipiente. A ligação pode demorar alguns momentos se o Azure ainda estiver em processo de atualização da imagem do contentor.

1. Depois de estabelecer uma ligação com o seu recipiente, execute o comando `top` para ver os processos em curso. 

    ![Comando de topo linux correndo em uma sessão SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Escreva para uma fila de armazenamento azure

As Funções Azure permitem ligar as suas funções a outros serviços e recursos Azure tendo de escrever o seu próprio código de integração. Estas *encadernações*, que representam tanto a entrada como a saída, são declaradas dentro da definição de função. Os dados das encadernações são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de encadernação de entrada. Embora uma função tenha apenas um gatilho, ela pode ter várias associações de entrada e saída. Para saber mais, consulte as [Funções Azure desencadeia e encaderna conceitos.](functions-triggers-bindings.md)

Esta seção mostra como integrar sua função a uma fila de armazenamento do Azure. A associação de saída que você adiciona a essa função grava dados de uma solicitação HTTP em uma mensagem na fila.

## <a name="retrieve-the-azure-storage-connection-string"></a>Recupere a cadeia de ligação de armazenamento azure

Anteriormente, criou uma conta de Armazenamento Azure para utilização pela aplicação de funções. A cadeia de ligação para esta conta é armazenada de forma segura nas definições da aplicação em Azure. Ao descarregar a definição no ficheiro *local.settings.json,* pode utilizar essa ligação escrever para uma fila de armazenamento na mesma conta ao executar a função localmente. 

1. A partir da raiz do projeto, execute o seguinte comando, substituindo `<app_name>` com o nome da sua aplicação de função a partir do quickstart anterior. Este comando irá substituir quaisquer valores existentes no ficheiro.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Abra *local.settings.json* e localize o valor denominado `AzureWebJobsStorage`, que é a cadeia de ligação da conta de armazenamento. Usa o nome `AzureWebJobsStorage` e a cadeia de ligação noutras secções deste artigo.

> [!IMPORTANT]
> Como *local.settings.json contém segredos descarregados* do Azure, exclua sempre este ficheiro do controlo de origem. O ficheiro *.gitignore* criado com um projeto de funções locais exclui o ficheiro por defeito.

### <a name="add-an-output-binding-to-functionjson"></a>Adicione uma ligação de saída ao função.json

Nas Funções Azure, cada tipo de encadernação requer um `direction`, `type`, e um `name` único a ser definido no ficheiro *função.json.* A sua *função.json* já inclui uma encadernação de entrada para o tipo "httpTrigger" e uma ligação de saída para a resposta HTTP. Para adicionar uma ligação a uma fila de armazenamento, modifique o ficheiro da seguinte forma, o que adiciona uma ligação de saída para o tipo de "fila", onde a fila aparece no código como um argumento de entrada chamado `msg`. A ligação da fila também requer o nome da fila para usar, neste caso `outqueue`, e o nome das definições que detém a corda de ligação, neste caso `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

Num C# projeto de biblioteca de classes, as encadernações são definidas como atributos vinculativos no método da função. O ficheiro *função.json* é então autogerado com base nestes atributos.

1. Para a ligação da fila, execute o seguinte comando [de pacote de dotnet](/dotnet/core/tools/dotnet-add-package) para adicionar o pacote de extensão de armazenamento ao seu projeto.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Abra o ficheiro *HttpTrigger.cs* e adicione a seguinte declaração `using`:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Adicione o seguinte parâmetro à definição de método `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    A definição de método `Run` deve agora coincidir com o seguinte código:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

O parâmetro `msg` é um tipo `ICollector<T>`, que representa uma coleção de mensagens que são escritas para uma ligação de saída quando a função completa. Neste caso, a saída é uma fila de armazenamento chamada `outqueue`. A cadeia de ligação para a conta de armazenamento é definida pelo `StorageAccountAttribute`. Este atributo indica a definição que contém a cadeia de ligação da conta de armazenamento e pode ser aplicada ao nível de classe, método ou parâmetro. Neste caso, pode omitir `StorageAccountAttribute` porque já está a usar a conta de armazenamento predefinida.

::: zone-end

::: zone pivot="programming-language-javascript"

Atualização *função.json* para combinar com o seguinte adicionando a ligação da fila após a ligação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Atualização *função.json* para combinar com o seguinte adicionando a ligação da fila após a ligação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Atualização *função.json* para combinar com o seguinte adicionando a ligação da fila após a ligação HTTP:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Atualização *função.json* para combinar com o seguinte adicionando a ligação da fila após a ligação HTTP:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para usar a ligação de saída

Após a definição da ligação, o nome da ligação, neste caso `msg`, aparece no código de função como argumento (ou no objeto `context` em JavaScript e TypeScript). Em seguida, pode usar essa variável para escrever mensagens para a fila. É necessário escrever qualquer código para autenticação, obter uma referência na fila ou escrever dados. Todas estas tarefas de integração são convenientemente tratadas no tempo de funcionamento das Funções Azure e na ligação de saída da fila.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Atualizar a imagem no registo

1. Na pasta raiz, volte a repetir `docker build` e desta vez atualize a versão na etiqueta para `v1.0.1`. Como antes, substitua `<docker_id>` com o id da sua conta Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Empurre a imagem atualizada de volta para o repositório com `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Uma vez que configurau a entrega contínua, atualizando a imagem no registo novamente atualiza automaticamente a sua aplicação de função no Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila do Armazenamento Azure

Num browser, use o mesmo URL de antes para invocar a sua função. O navegador deve apresentar a mesma resposta que antes, porque não modificou essa parte do código de função. O código adicionado, no entanto, escreveu uma mensagem usando o parâmetro URL `name` para a `outqueue` fila de armazenamento.

Pode ver a fila no [portal Azure](../storage/queues/storage-quickstart-queues-portal.md) ou no [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Pode também ver a fila no Azure CLI conforme descrito nos seguintes passos:

1. Abra o ficheiro *local.definição.json* do projeto de função e copie o valor da cadeia de ligação. Numa janela de terminais ou comando, execute o seguinte comando para criar uma variável ambiental chamada `AZURE_STORAGE_CONNECTION_STRING`, colando a sua cadeia de ligação específica no lugar de `<connection_string>`. (Esta variável ambiental significa que não precisa de fornecer a cadeia de ligação a cada comando subsequente utilizando o argumento `--connection-string`.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Opcional) Utilize o comando [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) para visualizar as filas de armazenamento na sua conta. A saída deste comando deve incluir uma fila chamada `outqueue`, que foi criada quando a função escreveu a sua primeira mensagem para aquela fila.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Utilize o comando [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) para visualizar as mensagens nesta fila, que deve ser o primeiro nome utilizado ao testar a função anteriormente. O comando recupera a primeira mensagem na fila na [codificação base64,](functions-bindings-storage-queue.md#encoding)pelo que também deve descodificar a mensagem para visualizar como texto.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    Porque precisa de desreferenciar a recolha de mensagens e descodificar a partir do base64, executar powerShell e utilizar o comando PowerShell.

    ---

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com a Azure Function utilizando os recursos que criou neste tutorial, pode deixar todos esses recursos no lugar. Como criou um Plano Premium para Funções Azure, incorrerá num ou dois USD por dia em custos contínuos.

Para evitar custos contínuos, elimine o grupo de recursos `AzureFunctionsContainer-rg` para limpar todos os recursos desse grupo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Passos seguintes

+ [Funções de monitorização](functions-monitoring.md)
+ [Opções de escala e hospedagem](functions-scale.md)
+ [Hospedagem sem servidor esfumadas com base em Kubernetes](functions-kubernetes-keda.md)

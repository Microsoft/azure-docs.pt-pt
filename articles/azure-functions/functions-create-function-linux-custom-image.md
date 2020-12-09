---
title: Criar funções Azure no Linux utilizando uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 2ee26bdc713cb2b5b2a158797e3ae7ace31c97b8
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904096"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Criar uma função no Linux com um contentor personalizado

Neste tutorial, cria e implementa o seu código para Azure Functions como um recipiente personalizado do Docker utilizando uma imagem base linux. Normalmente usa uma imagem personalizada quando as suas funções requerem uma versão específica do idioma ou têm uma dependência ou configuração específica que não é fornecida pela imagem incorporada.

::: zone pivot="programming-language-other"
As Funções Azure suportam qualquer idioma ou tempo de execução utilizando [manipuladores personalizados](functions-custom-handlers.md). Para algumas línguas, como a linguagem de programação R utilizada neste tutorial, é necessário instalar o tempo de funcionamento ou bibliotecas adicionais como dependências que requerem a utilização de um recipiente personalizado.
::: zone-end

A implementação do seu código de função num recipiente Linux personalizado requer [um plano Premium](functions-premium-plan.md#features) ou um plano de hospedagem dedicado [(Serviço de Aplicações).](functions-scale.md#app-service-plan) Completar este tutorial incorre em custos de alguns dólares americanos na sua conta Azure, que pode minimizar através da [limpeza de recursos](#clean-up-resources) quando terminar.

Também pode utilizar um recipiente de Serviço de Aplicações Azure padrão, conforme descrito no [Criar a sua primeira função hospedada no Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). As imagens base suportadas para funções Azure encontram-se nas imagens base do [Azure Functions repo](https://hub.docker.com/_/microsoft-azure-functions-base).

Neste tutorial, ficará a saber como:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Crie uma aplicação de função e dockerfile utilizando as Ferramentas Principais de Funções Azure.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar recursos de apoio em Azure para a aplicação de função
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Ativar a implantação contínua.
> * Ativar as ligações SSH ao recipiente.
> * Adicione uma ligação de saída de armazenamento de fila. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Crie uma aplicação de função e dockerfile utilizando as Ferramentas Principais de Funções Azure.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar recursos de apoio em Azure para a aplicação de função
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Ativar a implantação contínua.
> * Ativar as ligações SSH ao recipiente.
::: zone-end

Pode seguir este tutorial em qualquer computador que execute Windows, macOS ou Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Uma [ID docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Corre `docker login` para assinar com o Docker. Este comando falha se o Docker não estiver a funcionar, caso em que começa o Docker e recandidam o comando.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Criar e testar o projeto de funções locais

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Numa solicitação de terminal ou comando, executar o seguinte comando para o seu idioma escolhido para criar um projeto de aplicação de função numa pasta chamada `LocalFunctionsProject` .  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

O `-DjavaVersion` parâmetro indica ao tempo de execução funções qual versão de Java a utilizar. Utilize `-DjavaVersion=11` se quiser que as suas funções funcionem em Java 11. Quando não `-DjavaVersion` especifica, Maven falha em Java 8. Para mais informações, consulte as [versões Java.](functions-reference-java.md#java-versions)

> [!IMPORTANT]
> A `JAVA_HOME` variável ambiente deve ser definida para a localização de instalação da versão correta do JDK para completar este artigo.

A Maven pede-lhe valores necessários para terminar a geração do projeto na implantação.   
Fornecer os seguintes valores quando solicitado:

| Prompt | Valor | Descrição |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as [regras de nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) de pacotes para a Java. |
| **artefactoId** | `fabrikam-functions` | Um valor que é o nome do jarro, sem um número de versão. |
| **Versão** | `1.0-SNAPSHOT` | Escolha o valor predefinido. |
| **pacote** | `com.fabrikam.functions` | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. |

Digite `Y` ou prima Enter para confirmar.

A Maven cria os ficheiros do projeto numa nova pasta com o nome de _artifactId_, que neste exemplo é `fabrikam-functions` . 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

A `--docker` opção gera um `Dockerfile` para o projeto, que define um recipiente personalizado adequado para uso com Funções Azure e o tempo de execução selecionado.

Navegue na pasta do projeto:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adicione uma função ao seu projeto utilizando o seguinte comando, onde o `--name` argumento é o nome único da sua função e o argumento especifica o gatilho da `--template` função. `func new` cria uma sub-dobradeira que corresponde ao nome da função que contém um ficheiro de código adequado à língua escolhida do projeto e um ficheiro de configuração denominado *function.jsem*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Adicione uma função ao seu projeto utilizando o seguinte comando, onde o `--name` argumento é o nome único da sua função e o argumento especifica o gatilho da `--template` função. `func new` cria uma sub-página que corresponde ao nome da função que contém um ficheiro de configuração denominado *function.jsem*.

```console
func new --name HttpExample --template "HTTP trigger"
```

Num editor de texto, crie um ficheiro na pasta do projeto chamada *handler. R*. Adicione o seguinte como o seu conteúdo.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT = strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)
http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) list(body="Hello world")
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Em *host.js,* modifique a `customHandler` secção para configurar o comando de arranque do manipulador personalizado.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Para testar a função localmente, inicie o hospedeiro local Azure Functions na raiz da pasta do projeto: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
Assim que vir o `HttpExample` ponto final aparecer na saída, navegue para `http://localhost:7071/api/HttpExample?name=Functions` . O navegador deve apresentar uma mensagem de "olá" que ecoa de `Functions` volta, o valor fornecido ao `name` parâmetro de consulta.
::: zone-end
::: zone pivot="programming-language-other"
Assim que vir o `HttpExample` ponto final aparecer na saída, navegue para `http://localhost:7071/api/HttpExample` . O navegador deve exibir uma mensagem "Olá mundo".
::: zone-end
Use **ctrl** - **C** para parar o hospedeiro.

## <a name="build-the-container-image-and-test-locally"></a>Construa a imagem do recipiente e teste localmente

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Opcional) Examine o *Dockerfile* na raiz da pasta do projeto. O Dockerfile descreve o ambiente necessário para executar a aplicação de função no Linux.  A lista completa de imagens base suportadas para funções Azure pode ser encontrada na página de imagem base do [Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Examine o *Dockerfile* na raiz da pasta do projeto. O Dockerfile descreve o ambiente necessário para executar a aplicação de função no Linux. As aplicações de manipulador personalizado usam a `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` imagem como base.

Modifique o *Dockerfile* para instalar R. Substitua o conteúdo do *Dockerfile* pelo seguinte.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Na pasta do projeto raiz, executar o [estivador constroem](https://docs.docker.com/engine/reference/commandline/build/) comando, e fornecer um `azurefunctionsimage` nome, e tag, `v1.0.0` . Substitua `<DOCKER_ID>` pelo ID da sua conta do Docker Hub. Este comando cria a imagem do Docker para o contentor.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Quando o comando terminar, pode executar o novo recipiente localmente.
    
Para testar a construção, coloque a imagem num recipiente local utilizando o comando de execução do [estivador,](https://docs.docker.com/engine/reference/commandline/run/) substituindo novamente `<DOCKER_ID` o seu Estivador de Identificação e adicionando o argumento das portas: `-p 8080:80`

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Uma vez que a imagem esteja a ser em execução num recipiente local, abra um browser para `http://localhost:8080` , que deve exibir a imagem do espaço reservado abaixo mostrada. A imagem aparece neste ponto porque a sua função está a funcionar no contentor local, como faria em Azure, o que significa que está protegida por uma chave de acesso, tal como definida em *function.js* com a `"authLevel": "function"` propriedade. O contentor ainda não foi publicado numa aplicação de função em Azure, no entanto, por isso a chave ainda não está disponível. Se quiser testar contra o contentor local, pare o estivador, altere a propriedade de autorização para `"authLevel": "anonymous"` reconstruir a imagem e reinicie o estivador. Em seguida, reinicie `"authLevel": "function"` *function.js.* Para mais informações, consulte [as teclas de autorização.](functions-bindings-http-webhook-trigger.md#authorization-keys)

![Imagem de espaço reservado indicando que o recipiente está funcionando localmente](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Uma vez que a imagem esteja a correr num recipiente local, `http://localhost:8080/api/HttpExample?name=Functions` procure, que deve exibir a mesma mensagem de "olá" da anterior. Como o arquétipo Maven gera uma função http desencadeada que utiliza uma autorização anónima, ainda pode chamar a função mesmo que esteja a funcionar no recipiente. 
::: zone-end  

Depois de verificar a aplicação de função no recipiente, pare o estivador com **o Ctrl** + **C**.

## <a name="push-the-image-to-docker-hub"></a>Empurre a imagem para Docker Hub

Docker Hub é um registo de contentores que acolhe imagens e fornece serviços de imagem e contentores. Para partilhar a sua imagem, que inclui a implantação para Azure, deve empurrá-la para um registo.

1. Se ainda não assinou contrato com o Docker, [faça-o](https://docs.docker.com/engine/reference/commandline/login/) com o comando de login do docker, substituindo `<docker_id>` o seu Estivador. Este comando solicita-lhe o seu nome de utilizador e senha. Uma mensagem "Login Com sucesso" confirma que está inscrito.

    ```console
    docker login
    ```
    
1. Depois de ter assinado, empurre a imagem para Docker Hub usando o comando [de push docker,](https://docs.docker.com/engine/reference/commandline/push/) substituindo novamente `<docker_id>` com o seu Estivador ID.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Dependendo da velocidade da sua rede, empurrar a imagem pela primeira vez pode demorar alguns minutos (empurrar as alterações subsequentes é muito mais rápido). Enquanto espera, pode seguir para a próxima secção e criar recursos Azure em outro terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Crie recursos Azure de suporte para a sua função

Para implementar o seu código de função para Azure, precisa de criar três recursos:

- Um grupo de recursos, que é um recipiente lógico para recursos relacionados.
- Uma conta de Armazenamento Azure, que mantém informações estatais e outras sobre os seus projetos.
- Uma aplicação de funções Azure, que fornece o ambiente para executar o seu código de função. Uma aplicação de função mapeia para o seu projeto de função local e permite-lhe agrupar funções como uma unidade lógica para uma gestão, implementação e partilha mais fácil de recursos.

Utiliza comandos Azure CLI para criar estes itens. Cada comando fornece saída JSON após a conclusão.

1. Inscreva-se no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos nomeado `AzureFunctionsContainers-rg` na `westeurope` região. (Geralmente cria o seu grupo de recursos e recursos numa região próxima de si, utilizando uma região disponível a partir do `az account list-locations` comando.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Não é possível hospedar aplicações Linux e Windows no mesmo grupo de recursos. Se tiver um grupo de recursos existente nomeado `AzureFunctionsContainers-rg` com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento para fins gerais no seu grupo de recursos e região utilizando a [conta de armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, `<storage_name>` substitua-o por um nome globalmente único, adequado a si. Os nomes devem conter apenas três a 24 caracteres e letras minúsculas. `Standard_LRS` especifica uma conta típica de fins gerais.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento incorre apenas alguns cêntimos usd para este tutorial.
    
1. Utilize o comando para criar um plano Premium para funções Azure nomeado `myPremiumPlan` no nível de preços **Elástico Premium 1** `--sku EP1` (), na região da Europa Ocidental ( `-location westeurope` ou use uma região adequada perto de si), e num recipiente Linux `--is-linux` ( ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Utilizamos aqui o plano Premium, que pode escalar conforme necessário. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md). Para calcular os [custos,](https://azure.microsoft.com/pricing/details/functions/)consulte a página de preços das funções .

    O comando também fornece uma instância Azure Application Insights associada no mesmo grupo de recursos, com a qual pode monitorizar a sua aplicação de função e visualizar registos. Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). O caso não incorre em custos até que o ative.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Criar e configurar uma aplicação de função no Azure com a imagem

Uma aplicação de função no Azure gere a execução das suas funções no seu plano de hospedagem. Nesta secção, utiliza os recursos Azure da secção anterior para criar uma aplicação de função a partir de uma imagem no Docker Hub e configurá-la com uma cadeia de ligação ao Azure Storage.

1. Crie a aplicação Funções utilizando o comando [az functionapp.](/cli/azure/functionapp#az-functionapp-create) No exemplo seguinte, `<storage_name>` substitua-o pelo nome utilizado na secção anterior para a conta de armazenamento. Substitua também `<app_name>` um nome globalmente único, adequado a si, e `<docker_id>` pelo seu Docker ID.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    O parâmetro *de designação-imagem-contentor* especifica a imagem a utilizar para a aplicação de função. Pode utilizar o comando de demonstração do recipiente de [config do az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para visualizar informações sobre a imagem utilizada para a implantação. Também pode utilizar o comando conjunto de conjunto de conjunto de conjuntos de [config do az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para implantar a partir de uma imagem diferente.

1. Exiba o fio de ligação para a conta de armazenamento que criou utilizando o comando [de série de ligação de ligação da conta az.](/cli/azure/storage/account) `<storage-name>`Substitua-o pelo nome da conta de armazenamento que criou acima:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adicione esta definição à aplicação de função utilizando o comando de configuração de configurações de [appsettings az functionapp.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) No seguinte comando, `<app_name>` substitua-o pelo nome da sua aplicação de função e substitua-a `<connection_string>` pela cadeia de ligação do passo anterior (uma longa cadeia codificada que começa por "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > Em Bash, pode utilizar uma variável de concha para capturar a cadeia de ligação em vez de utilizar a área de transferência. Primeiro, utilize o seguinte comando para criar uma variável com a cadeia de ligação:
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

1. A função pode agora utilizar este fio de ligação para aceder à conta de armazenamento.

> [!NOTE]    
> Se publicar a sua imagem personalizada numa conta de contentores privados, deverá utilizar variáveis ambientais no Dockerfile para a cadeia de ligação. Para mais informações, consulte a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env). Também deve definir as variáveis `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD` . Para utilizar os valores, então, deve reconstruir a imagem, empurrar a imagem para o registo e, em seguida, reiniciar a aplicação de função no Azure.

## <a name="verify-your-functions-on-azure"></a>Verifique as suas funções no Azure

Com a imagem implantada na aplicação de função no Azure, pode agora invocar a função através de pedidos HTTP. Como o *function.jsna* definição inclui a `"authLevel": "function"` propriedade, primeiro deve obter a chave de acesso (também chamada de "chave de função") e incluí-la como parâmetro URL em quaisquer pedidos para o ponto final.

1. Recupere o URL de função com a tecla de acesso (função) utilizando o portal Azure, ou utilizando o CLI Azure com o `az rest` comando.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Inscreva-se no portal Azure e, em seguida, procure e selecione **App de função.**

    1. Selecione a função que pretende verificar.

    1. No painel de navegação esquerdo, selecione **Funções** e, em seguida, selecione a função que pretende verificar.

        ![Escolha a sua função no portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Selecione **Obter Url de função**.

        ![Obtenha o URL de função do portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Na janela pop-up, selecione **predefinição (tecla de função)** e, em seguida, copie o URL para a área de transferência. A chave é a sequência de caracteres que se `?code=` seguem.

        ![Escolha a chave de acesso à função predefinida](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Como a sua aplicação de função é implantada como um recipiente, não pode escoar alterações ao seu código de função no portal. Em vez disso, deve atualizar o projeto na imagem local, empurrar a imagem para o registo novamente e, em seguida, reenfectar para Azure. Pode configurar uma implantação contínua numa secção posterior.
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

    1. Construa uma cadeia DE URL no seguinte formato, `<subscription_id>` substituindo, e com o seu `<resource_group>` `<app_name>` ID de subscrição Azure, o grupo de recursos da sua aplicação de função, e o nome da sua aplicação de função, respectivamente:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Por exemplo, o URL pode olhar para o seguinte endereço:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Por conveniência, pode atribuir o URL a uma variável ambiental e usá-lo no `az rest` comando.
    
    1. Executar o seguinte `az rest` comando (disponível na versão Azure CLI 2.0.77 e posterior), substituindo `<uri>` a cadeia URI do último passo, incluindo as citações:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A saída do comando é a chave de função. O URL de função completa `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>` é, então, `<app_name>` `<function_name>` substituído, e `<key>` pelos seus valores específicos.
    
        > [!NOTE]
        > A chave recuperada aqui é a chave *hospedeira* que funciona para todas as funções na aplicação de funções; o método mostrado para o portal recupera a chave apenas para uma função.

    ---

1. Cole o URL de função na barra de endereços do seu navegador, adicionando o parâmetro `&name=Azure` ao final deste URL. Texto como "Olá, Azure" deve aparecer no navegador.

    ![Resposta da função no browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Para testar a autorização, retire o `code=` parâmetro do URL e verifique se não obtém qualquer resposta da função.


## <a name="enable-continuous-deployment-to-azure"></a>Permitir a implantação contínua para Azure

Pode ativar as Funções Azure para atualizar automaticamente a sua implementação de uma imagem sempre que atualizar a imagem no registo.

1. Ativar a implementação contínua utilizando o comando [config do contentor config de implementação do az functionapp,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) `<app_name>` substituindo-o pelo nome da sua aplicação de função:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Este comando permite a implementação contínua e devolve o URL webhook de implementação. (Pode recuperar este URL em qualquer momento posterior, utilizando o comando de exibição de [show-cd-url do contentor de funções az.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Copie o URL webhook de implementação para a área de transferência.

1. Abra [o Docker Hub,](https://hub.docker.com/)inscreva-se e selecione **repositórios** na barra de navegação. Localizar e selecionar a imagem, selecionar o separador **Webhooks,** especificar um **nome Webhook,** colar o url no **URL webhook** e, em seguida, selecionar **Criar**:

    ![Adicione o webhook no seu repo DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Com o conjunto webhook, a Azure Functions reimplantia a sua imagem sempre que a atualiza no Docker Hub.

## <a name="enable-ssh-connections"></a>Ativar ligações SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Com o SSH ativado, pode ligar-se ao seu recipiente utilizando Ferramentas Avançadas de Serviço de Aplicação (Kudu). Para facilitar a ligação ao seu recipiente utilizando o SSH, o Azure Functions fornece uma imagem base que tem SSH já ativada. Só precisas de editar o teu Dockerfile, depois reconstruir e redistribuir a imagem. Em seguida, pode ligar-se ao recipiente através das Ferramentas Avançadas (Kudu)

1. No seu Dockerfile, apencha a cadeia `-appservice` à imagem base na sua `FROM` instrução:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
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
    
1. Reconstrua a imagem utilizando novamente o `docker build` comando, `<docker_id>` substituindo-a pelo seu Estivador ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Empurre a imagem atualizada para o Docker Hub, que deve demorar consideravelmente menos tempo do que o primeiro impulso apenas os segmentos atualizados da imagem precisam de ser carregados.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. As Funções Azure reimplantia automaticamente a imagem para a aplicação de funções; o processo ocorre em menos de um minuto.

1. Num browser, `https://<app_name>.scm.azurewebsites.net/` abra, substituindo `<app_name>` pelo seu nome único. Este URL é o ponto final das Ferramentas Avançadas (Kudu) para o seu recipiente de aplicações de função.

1. Inscreva-se na sua conta Azure e, em seguida, selecione o **SSH** para estabelecer uma ligação com o recipiente. A ligação pode demorar alguns momentos se o Azure ainda estiver a atualizar a imagem do recipiente.

1. Depois de ter sido estabelecida uma ligação com o seu recipiente, executa o `top` comando para visualizar os processos atualmente em execução. 

    ![Comando de topo do Linux em execução numa sessão de SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Escreva para uma fila de armazenamento Azure

As Funções Azure permitem-lhe ligar as suas funções a outros serviços e recursos da Azure sem ter de escrever o seu próprio código de integração. Estas *ligações*, que representam tanto a entrada como a saída, são declaradas dentro da definição de função. Os dados de enlaces são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de encadernação de entrada. Embora uma função tenha apenas um gatilho, pode ter múltiplas ligações de entrada e saída. Para saber mais, consulte [Azure Functions triggers e conceitos de encadernação.](functions-triggers-bindings.md)

Esta secção mostra-lhe como integrar a sua função com uma fila de Armazenamento Azure. A vinculação de saída que adiciona a esta função escreve dados de um pedido HTTP para uma mensagem na fila.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para utilizar a ligação de saída

Com a ligação da fila definida, pode agora atualizar a sua função para receber o `msg` parâmetro de saída e escrever mensagens para a fila.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Atualizar a imagem no registo

1. Na pasta raiz, volte a correr `docker build` e desta vez atualize a versão na etiqueta para `v1.0.1` . Como antes, `<docker_id>` substitua-o pela sua conta Docker Hub:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Empurre a imagem atualizada de volta para o repositório `docker push` com:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Como configuraste a entrega contínua, atualizando a imagem no registo novamente atualiza automaticamente a sua aplicação de função em Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila de armazenamento do Azure

Num browser, utilize o mesmo URL de antes para invocar a sua função. O navegador deve apresentar a mesma resposta de antes, porque não modificou essa parte do código de função. O código adicionado, no entanto, escreveu uma mensagem usando o `name` parâmetro URL para a fila de `outqueue` armazenamento.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser continuar a trabalhar com a Azure Function utilizando os recursos que criou neste tutorial, pode deixar todos esses recursos no lugar. Como criou um Plano Premium para Funções Azure, incorrerá num ou dois USD por dia em custos contínuos.

Para evitar custos contínuos, elimine o `AzureFunctionsContainer-rg` grupo de recursos para limpar todos os recursos desse grupo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Passos seguintes

+ [Funções de monitorização](functions-monitoring.md)
+ [Opções de escala e hospedagem](functions-scale.md)
+ [Hospedagem sem servidor baseada em Kubernetes](functions-kubernetes-keda.md)

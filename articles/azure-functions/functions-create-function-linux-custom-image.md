---
title: Criar funções Azure em Linux usando uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: fee4e16bd77664e541eeb36cb807a77d13191899
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82165727"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Criar uma função no Linux usando um recipiente personalizado

Neste tutorial, cria e implementa o seu código para as Funções Azure como recipiente Docker personalizado utilizando uma imagem base linux. Normalmente, utiliza-se uma imagem personalizada quando as suas funções requerem uma versão idioma específica ou tem uma dependência ou configuração específica que não é fornecida pela imagem incorporada.

Também pode utilizar um recipiente de serviço de aplicações Azure padrão, tal como descrito no [Create your first function hospedado no Linux](functions-create-first-azure-function-azure-cli-linux.md). As imagens de base suportadas para funções azure são encontradas no repo de [imagens base funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de função e dockerfile utilizando as Ferramentas Core funções do Azure.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar recursos de apoio em Azure para a app de funções
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Ativar a implantação contínua.
> * Ative as ligações SSH ao recipiente.
> * Adicione uma encadernação de saída de armazenamento de fila. 

Pode seguir este tutorial em qualquer computador que execute Windows, macOS ou Linux. A conclusão do tutorial incorrerá em custos de alguns dólares americanos na sua conta Azure.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Estivador](https://docs.docker.com/install/)  

+ Uma [identificação do Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Corra `docker login` para assinar com Docker. Este comando falha se o Docker não estiver a funcionar, caso em que começar a tentar o comando.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Criar e testar o projeto de funções locais

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Num aviso de terminal ou comando, execute o seguinte comando para o `LocalFunctionsProject`seu idioma escolhido para criar um projeto de aplicação de função numa pasta chamada .  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Numa pasta vazia, execute o seguinte comando para gerar o projeto das Funções a partir de um [arquétipo do Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven pede-lhe valores necessários para terminar gerando o projeto na implantação.   
Forneça os seguintes valores quando solicitado:

| Mensagem | Valor | Descrição |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Um valor que identifica exclusivamente o seu projeto em todos os projetos, seguindo as regras de [nomeação](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) do pacote para a Java. |
| **artefactoId** | `fabrikam-functions` | Um valor que é o nome do jarro, sem um número de versão. |
| **Versão** | `1.0-SNAPSHOT` | Escolha o valor padrão. |
| **pacote** | `com.fabrikam.functions` | Um valor que é o pacote Java para o código de função gerado. Utilize a predefinição. |

Digite `Y` ou prima Introduza para confirmar.

Maven cria os ficheiros do projeto numa nova pasta com `fabrikam-functions`um nome de _artefactoId_, que neste exemplo é . 
::: zone-end
A `--docker` opção `Dockerfile` gera um para o projeto, que define um recipiente personalizado adequado para utilização com funções Azure e o tempo de execução selecionado.

Navegue na pasta do projeto:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Adicione uma função ao seu projeto utilizando `--name` o seguinte comando, onde `--template` o argumento é o nome único da sua função e o argumento especifica o gatilho da função. `func new`criar uma subpasta que contenha um ficheiro de código adequado ao idioma escolhido do projeto e um ficheiro de configuração denominado *função.json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Para testar a função localmente, inicie o hospedeiro local funções Azure na raiz da pasta do projeto: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
Assim que `HttpExample` vir o ponto final aparecer `http://localhost:7071/api/HttpExample?name=Functions`na saída, navegue para . O navegador deve apresentar uma mensagem "olá" que ecoa, `Functions`o valor fornecido ao parâmetro de `name` consulta.

Use **ctrl**-**C** para parar o hospedeiro.

## <a name="build-the-container-image-and-test-locally"></a>Construa a imagem do recipiente e teste localmente

(Opcional) Examine o *Dockerfile" na raiz da pasta do projeto. O Dockerfile descreve o ambiente necessário para executar a aplicação de função no Linux.  A lista completa de imagens base suportadas para funções azure pode ser encontrada na página de [imagem base funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base).
    
Na pasta do projeto raiz, execute o comando `azurefunctionsimage`de construção do [estivador,](https://docs.docker.com/engine/reference/commandline/build/) e forneça um nome, e etiqueta, `v1.0.0`. Substitua `<DOCKER_ID>` pelo ID da sua conta do Docker Hub. Este comando cria a imagem do Docker para o contentor.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Quando o comando estiver concluído, pode executar o novo contentor localmente.
    
Para testar a construção, faça a imagem num contentor local `<DOCKER_ID` utilizando o comando de execução `-p 8080:80`do [estivador,](https://docs.docker.com/engine/reference/commandline/run/) substituindo novamente o seu Docker ID e adicionando o argumento das portas:

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Uma vez que a imagem esteja em `http://localhost:8080`execução num contentor local, abra um browser para, que deve exibir a imagem do espaço reservado mostrada abaixo. A imagem aparece neste ponto porque a sua função está a funcionar no contentor local, como em Azure, o que significa `"authLevel": "function"` que está protegida por uma chave de acesso, tal como definida na *função.json* com a propriedade. O contentor ainda não foi publicado numa aplicação de funções no Azure, no entanto, a chave ainda não está disponível. Se quiser testar contra o contentor local, pare o `"authLevel": "anonymous"`estivador, altere a propriedade de autorização para, reconstruir a imagem e reiniciar o estivador. Em seguida, reset `"authLevel": "function"` em *função.json*. Para mais informações, consulte [as chaves de autorização](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Imagem de espaço reservado indicando que o recipiente está funcionando localmente](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Uma vez que a imagem esteja a `http://localhost:8080/api/HttpExample?name=Functions`correr num contentor local, navegue para, que deve apresentar a mesma mensagem de "olá" que antes. Como o arquétipo Maven gera uma função ativada em HTTP que utiliza autorização anónima, ainda pode chamar a função mesmo estando a funcionar no contentor. 
::: zone-end  

Depois de verificar a aplicação de função no recipiente, pare o estivador com **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Empurre a imagem para Docker Hub

Docker Hub é um registo de contentores que acolhe imagens e fornece serviços de imagem e contentores. Para partilhar a sua imagem, que inclui a implantação para o Azure, deve empurrá-la para um registo.

1. Se ainda não assinou com o Docker, fá-lo com o `<docker_id>` comando de login do [Docker,](https://docs.docker.com/engine/reference/commandline/login/) substituindo a tua identificação do Docker. Este comando solicita-lhe o seu nome de utilizador e palavra-passe. Uma mensagem "Login Succeeded" confirma que está inscrito.

    ```
    docker login
    ```
    
1. Depois de ter assinado, empurre a imagem para o Docker Hub `<docker_id>` usando o comando de pressão do [estivador,](https://docs.docker.com/engine/reference/commandline/push/) substituindo novamente o seu Docker ID.

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

1. Inscreva-se no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria `AzureFunctionsContainers-rg` um `westeurope` grupo de recursos nomeado na região. (Geralmente cria o seu grupo de recursos e recursos numa `az account list-locations` região próxima de si, utilizando uma região disponível a partir do comando.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Não é possível hospedar aplicações linux e Windows no mesmo grupo de recursos. Se tiver um grupo de `AzureFunctionsContainers-rg` recursos existente nomeado com uma aplicação de função Windows ou uma aplicação web, deve utilizar um grupo de recursos diferente.
    
1. Crie uma conta de armazenamento geral no seu grupo de recursos e região utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az-storage-account-create) comando. No exemplo seguinte, `<storage_name>` substitua-o por um nome globalmente único adequado a si. Os nomes devem conter três a 24 caracteres e apenas letras minúsculas. `Standard_LRS`especifica uma conta típica de uso geral.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    A conta de armazenamento incorre apenas alguns cêntimos de USD para este tutorial.
    
1. Utilize o comando para criar um plano `myPremiumPlan` Premium para funções Azure`--sku EP1`nomeados no nível de preços **Elástico Premium 1** (`--is-linux`), na região da Europa Ocidental (`-location westeurope`ou utilize uma região adequada perto de si) e num recipiente Linux ().

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    O alojamento linux para recipientes de funções personalizadas é suportado em [planos dedicados (App Service)](functions-scale.md#app-service-plan) e [planos Premium](functions-premium-plan.md#features). Utilizamos aqui o plano Premium, que pode escalar conforme necessário. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md). Para calcular os custos, consulte a página de preços das [Funções](https://azure.microsoft.com/pricing/details/functions/).

    O comando também prevê uma instância de Insights de Aplicação Azure associada no mesmo grupo de recursos, com o qual pode monitorizar a sua aplicação de função e visualizar registos. Para mais informações, consulte as [Funções Monitor Azure](functions-monitoring.md). A instância não incorre em custos até que o ative.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Criar e configurar uma aplicação de função no Azure com a imagem

Uma aplicação de função no Azure gere a execução das suas funções no seu plano de hospedagem. Nesta secção, utiliza os recursos Azure da secção anterior para criar uma aplicação de função a partir de uma imagem no Docker Hub e configurá-la com uma cadeia de ligação ao Armazenamento Azure.

1. Criar a aplicação Funções utilizando o [app de funções az criar](/cli/azure/functionapp#az-functionapp-create) comando. No exemplo seguinte, `<storage_name>` substitua-o pelo nome utilizado na secção anterior para a conta de armazenamento. Substitua `<app_name>` também por um nome globalmente `<docker_id>` único apropriado para si, e com o seu Id Docker.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    O parâmetro de nome de *imagem de contentor de implantação* especifica a imagem a utilizar para a aplicação de função. Pode utilizar o comando do recipiente de visualização de [config az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) para visualizar informações sobre a imagem utilizada para a implantação. Também pode utilizar o comando de conjunto de conjunto de recipientes de [config az functionapp](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) para desdobrar a partir de uma imagem diferente.

1. Recupere a cadeia de ligação para a conta de armazenamento que criou utilizando o comando `storageConnectionString`de cadeia de conexão de conta de armazenamento [az,](/cli/azure/storage/account) atribuindo-o a uma variável de concha:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Adicione esta definição à aplicação de funções utilizando as definições de definição de definição de definições de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de definição de [definição](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) No comando seguinte, `<app_name>` substitua-o pelo nome `<connection_string>` da sua aplicação de função e substitua-o pela cadeia de ligação do passo anterior (uma longa cadeia codificada que começa com "DefaultEndpointProtocol="):
 
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
> Se publicar a sua imagem personalizada numa conta de contentores privados, deve utilizar variáveis ambientais no Dockerfile para a cadeia de ligação. Para mais informações, consulte a [instrução env](https://docs.docker.com/engine/reference/builder/#env). Também deve definir as `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD`variáveis e . Para utilizar os valores, então, deve reconstruir a imagem, empurrar a imagem para o registo e, em seguida, reiniciar a aplicação de função no Azure.

## <a name="verify-your-functions-on-azure"></a>Verifique as suas funções no Azure

Com a imagem implementada na aplicação de funções no Azure, pode agora invocar a função através de pedidos HTTP. Como a definição *fun.json* inclui a propriedade, `"authLevel": "function"`você deve primeiro obter a chave de acesso (também chamada de "chave de função") e incluí-la como um parâmetro DE URL em qualquer pedido para o ponto final.

1. Recupere o URL de função com a tecla de acesso (função) utilizando `az rest` o portal Azure, ou utilizando o Azure CLI com o comando.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Inscreva-se no portal Azure e, em seguida, localize a sua aplicação de função, inserindo o nome da sua aplicação de função na caixa **de pesquisa** no topo da página. Nos resultados, selecione o recurso **do Serviço de Aplicações.**

    1. No painel de navegação esquerdo, em **funções (Ler Apenas)**, selecione o nome da sua função.

    1. No painel de detalhes, selecione **</> Obtenha URL de função:**
    
        ![O comando URL da função Get no portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. No popup, selecione **predefinido (Tecla de função)** e, em seguida, **Copiar**. A chave é a `?code=`cadeia de caracteres que se seguem.

        ![Copiar o URL de função do portal Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Como a sua aplicação de função é implementada como um recipiente, não pode fazer alterações ao seu código de função no portal. Em vez disso, deve atualizar o projeto à imagem local, empurrar a imagem para o registo novamente e, em seguida, recolocar para Azure. Pode configurar uma implantação contínua numa secção posterior.
    
    # <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli)

    1. Construa uma cadeia DE URL `<subscription_id>`no `<resource_group>`seguinte `<app_name>` formato, substituindo, e com o seu ID de subscrição Azure, o grupo de recursos da sua aplicação de função, e o nome da sua app de funções, respectivamente:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Por exemplo, o URL pode olhar para o seguinte endereço:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Por conveniência, pode, em vez disso, atribuir o `az rest` URL a uma variável ambiental e usá-lo no comando.
    
    1. Executar o `az rest` seguinte comando (disponível na versão 2.0.77 do `<uri>` Azure CLI) e posteriormente), substituindo a cadeia URI do último passo, incluindo as cotações:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. A saída do comando é a chave de função. O URL de `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`função `<app_name>`completa `<function_name>`é `<key>` então, substituindo, e com os seus valores específicos.
    
        > [!NOTE]
        > A chave aqui recuperada é a chave do *hospedeiro* que funciona para todas as funções na aplicação de funções; o método mostrado para o portal recupera a chave apenas para uma função.

    ---

1. Colhe o URL de função na barra de `&name=Azure` endereços do seu navegador, adicionando o parâmetro ao fim deste URL. Textocomo "Hello Azure" deve aparecer no navegador.

    ![Resposta da função no browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Para testar a autorização, retire o parâmetro code= do URL e verifique se não obtém resposta da função.


## <a name="enable-continuous-deployment-to-azure"></a>Permitir a implantação contínua para o Azure

Pode ativar as Funções Azure para atualizar automaticamente a sua implementação de uma imagem sempre que atualizar a imagem no registo.

1. Ativar a implantação contínua utilizando o comando config `<app_name>` do recipiente de implementação do recipiente de implementação de [placas az,](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) substituindo o nome da sua aplicação de funções:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Este comando permite a implementação contínua e devolve o URL de webhook de implementação. (Pode recuperar este URL mais tarde utilizando o comando de implementação do contentor de implementação de app de [funções az.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Copie o URL de webhook de implementação para a área de reparação.

1. Open [Docker Hub,](https://hub.docker.com/)inscreva-se e selecione **Repositórios** na barra de navegação. Localizar e selecionar imagem, selecionar o separador **Webhooks,** especificar um **nome Webhook,** colar o URL no **URL do Webhook,** e, em seguida, selecionar **Criar:**

    ![Adicione o webhook no seu repo DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Com o conjunto webhook, o Azure Functions reimplementa a sua imagem sempre que a atualiza no Docker Hub.

## <a name="enable-ssh-connections"></a>Ativar ligações SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Com o SSH ativado, pode ligar-se ao seu recipiente utilizando ferramentas avançadas do Serviço app (Kudu). Para facilitar a ligação ao seu recipiente utilizando o SSH, a Azure Functions fornece uma imagem de base que já tem SSH ativado. Só precisa editar o seu Dockerfile, depois reconstruir e reimplantar a imagem. Em seguida, pode ligar-se ao recipiente através das Ferramentas Avançadas (Kudu)

1. No seu Dockerfile, acomode a `-appservice` cadeia `FROM` à imagem base na sua instrução:

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

1. Reconstruir a imagem `docker build` usando novamente `<docker_id>` o comando, substituindo-o pelo seu Id Docker:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Empurre a imagem atualizada para o Docker Hub, que deve demorar consideravelmente menos tempo do que o primeiro empurrão apenas os segmentos atualizados da imagem precisam de ser carregados.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. As Funções Azure reimplantam automaticamente a imagem para a sua aplicação de funções; o processo ocorre em menos de um minuto.

1. Num browser, `https://<app_name>.scm.azurewebsites.net/`aberto, `<app_name>` substituindo pelo seu nome único. Este URL é o ponto final das Ferramentas Avançadas (Kudu) para o seu recipiente de aplicação de funções.

1. Inscreva-se na sua conta Azure e, em seguida, selecione o **SSH** para estabelecer uma ligação com o recipiente. A ligação pode demorar alguns momentos se o Azure ainda estiver a atualizar a imagem do contentor.

1. Depois de estabelecer uma ligação `top` com o seu recipiente, execute o comando para ver os processos em funcionamento atualmente. 

    ![Comando de topo linux correndo em uma sessão SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Escreva para uma fila de armazenamento azure

As Funções Azure permitem ligar as suas funções a outros serviços e recursos Azure tendo de escrever o seu próprio código de integração. Estas *encadernações*, que representam tanto a entrada como a saída, são declaradas dentro da definição de função. Os dados de enlaces são fornecidos à função como parâmetros. Um *gatilho* é um tipo especial de encadernação de entrada. Embora uma função tenha apenas um gatilho, pode ter várias ligações de entrada e saída. Para saber mais, consulte as [Funções Azure desencadeia e encaderna conceitos.](functions-triggers-bindings.md)

Esta secção mostra-lhe como integrar a sua função com uma fila de Armazenamento Azure. O encadernação de saída que adiciona a esta função escreve dados de um pedido HTTP para uma mensagem na fila.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Adicione código para usar a ligação de saída

Com a ligação da fila definida, pode `msg` agora atualizar a sua função para receber o parâmetro de saída e escrever mensagens para a fila.

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

### <a name="update-the-image-in-the-registry"></a>Atualizar a imagem no registo

1. Na pasta raiz, `docker build` volte a ser executada e desta `v1.0.1`vez atualize a versão na etiqueta para . Como antes, `<docker_id>` substitua-o pelo ID da sua conta Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Empurre a imagem atualizada de volta `docker push`para o repositório com:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Uma vez que configurau a entrega contínua, atualizando a imagem no registo novamente atualiza automaticamente a sua aplicação de função no Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Veja a mensagem na fila do Armazenamento Azure

Num browser, use o mesmo URL de antes para invocar a sua função. O navegador deve apresentar a mesma resposta que antes, porque não modificou essa parte do código de função. O código adicionado, no entanto, escreveu uma mensagem usando o `name` parâmetro URL para a fila de `outqueue` armazenamento.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com a Azure Function utilizando os recursos que criou neste tutorial, pode deixar todos esses recursos no lugar. Como criou um Plano Premium para Funções Azure, incorrerá num ou dois USD por dia em custos contínuos.

Para evitar custos `AzureFunctionsContainer-rg` contínuos, elimine o grupo de recursos para limpar todos os recursos desse grupo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Passos seguintes

+ [Funções de monitorização](functions-monitoring.md)
+ [Opções de escala e hospedagem](functions-scale.md)
+ [Hospedagem sem servidor esfumadas com base em Kubernetes](functions-kubernetes-keda.md)

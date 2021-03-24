---
title: Ligue as funções Azure à Azure Cosmos DB usando o Código do Estúdio Visual
description: Saiba como ligar as Funções Azure a uma conta DB da Azure Cosmos adicionando uma ligação de saída ao seu projeto Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 91d27ce0d6f999ac5d13b079c877e49cdf3fcd61
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962869"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Ligue as funções Azure à Azure Cosmos DB usando o Código do Estúdio Visual

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Este artigo mostra-lhe como usar o Código do Estúdio Visual para ligar [a Azure Cosmos DB](../cosmos-db/introduction.md) à função que criou no artigo de arranque rápido anterior. A vinculação de saída que adiciona a esta função escreve dados do pedido HTTP a um documento JSON armazenado num contentor DB Azure Cosmos. 

::: zone pivot="programming-language-csharp"
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-csharp.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Antes de começar, tem de completar o artigo, [Quickstart: Criar um projeto Azure Functions a partir da linha de comando](create-first-function-cli-node.md). Se já limpou recursos no final desse artigo, passe novamente pelos passos para recriar a app de função e recursos relacionados em Azure.  
::: zone-end   

## <a name="configure-your-environment"></a>Configurar o ambiente

Antes de começar, certifique-se de instalar a [extensão Azure Databases](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) para o Código do Estúdio Visual.

## <a name="create-your-azure-cosmos-db-account"></a>Crie a sua conta DB Azure Cosmos

> [!IMPORTANT]
> [Azure Cosmos DB sem servidor](../cosmos-db/serverless.md) já está disponível em pré-visualização. Este modo baseado no consumo faz da Azure Cosmos DB uma opção forte para cargas de trabalho sem servidor. Para utilizar o Azure Cosmos DB no modo sem servidor, escolha **Serverless** como **o modo Capacidade** ao criar a sua conta.

1. Numa nova janela do browser, inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Clique em **Criar um recurso** > **Bases de dados** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="O painel Bases de dados do portal do Azure" border="true":::

3. Na página **'Conta DB Create Azure Cosmos',** insira as definições da sua nova conta DB Azure Cosmos. 
 
    Definição|Valor|Descrição
    ---|---|---
    Subscrição|*A sua subscrição*|Escolha a subscrição Azure onde criou a sua App de Função no [artigo anterior.](./create-first-function-vs-code-csharp.md)
    Grupo de Recursos|*O seu grupo de recursos*|Escolha o grupo de recursos onde criou a sua App de Função no [artigo anterior.](./create-first-function-vs-code-csharp.md)
    Nome da Conta|*Introduzir um nome exclusivo*|Introduza um nome exclusivo para identificar a sua conta do Azure Cosmos DB.<br><br>O nome da conta só pode usar letras minúsculas, números e hífens (-), e deve ter entre 3 e 31 caracteres de comprimento.
    API|Núcleo (SQL)|Selecione **Core (SQL)** para criar uma base de dados de documentos que possa consultar utilizando uma sintaxe SQL. [Saiba mais sobre a AZure Cosmos DB SQL API](../cosmos-db/introduction.md).|
    Localização|*Selecione a região mais próxima da sua localização*|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima de si ou dos seus utilizadores para obter o acesso mais rápido aos seus dados.
    Modo de capacidade|Produção sem servidor ou provisionada|Selecione **Serverless** para criar uma conta no modo [sem servidor.](../cosmos-db/serverless.md) **Selecione Provisão para** criar uma conta no modo [de produção previsto.](../cosmos-db/set-throughput.md)<br><br>Escolha **Serverless** se estiver a começar com o Azure Cosmos DB.

4. Clique em **Rever + criar**. Pode saltar a secção **'Rede** e **Etiquetas'.** 

5. Reveja as informações de resumo e clique em **Criar**. 

6. Aguarde a criação do seu novo Azure Cosmos DB e, em seguida, selecione **Ir para o recurso**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="A criação da conta DB do Azure Cosmos está completa" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Criar uma base de dados E contentor Azure Cosmos

A partir da sua conta DB Azure Cosmos, selecione **Data Explorer,** em seguida, **Novo Contentor**. Crie uma nova base de dados chamada *minha base de dados,* um novo recipiente chamado *meu contentor* e escolha como chave `/id` de [partição](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Criação de um novo contentor Azure Cosmos DB a partir do portal Azure" border="true":::

## <a name="update-your-function-app-settings"></a>Atualize as definições da aplicação de função

No [artigo de arranque rápido anterior,](./create-first-function-vs-code-csharp.md)criou uma aplicação de função no Azure. Neste artigo, atualiza a sua App de Função para escrever documentos JSON no contentor DB Azure Cosmos que criou acima. Para se ligar à sua conta DB Azure Cosmos, tem de adicionar o seu fio de ligação às definições da sua aplicação. Em seguida, descarregue a nova definição para o seu local.settings.jsno ficheiro para que possa ligar-se à sua conta DB Azure Cosmos quando estiver a funcionar localmente.

1. No Código do Estúdio Visual, localize a conta DB Azure Cosmos que acaba de criar. Clique com o botão direito no seu nome e selecione **Copy Connection String**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Copiar a cadeia de conexão DB Azure Cosmos" border="true":::

1. Prima <kbd>f1</kbd> para abrir a paleta de comando, em seguida, procure e executar o comando `Azure Functions: Add New Setting...` .

1. Escolha a aplicação de função criada no artigo anterior. Forneça as seguintes informações nos pedidos:

    + **Introduza o novo nome de definição de aplicativo**: Escreva `CosmosDbConnectionString` .

    + **Insira valor para "CosmosDbConnectionString"**: Cole a cadeia de ligação da sua conta DB Azure Cosmos, como copiado anteriormente.

1. Prima <kbd>novamente a F1</kbd> para abrir a paleta de comando, em seguida, procure e execute o comando `Azure Functions: Download Remote Settings...` . 

1. Escolha a aplicação de função criada no artigo anterior. Selecione **Sim a todos** para substituir as definições locais existentes. 

## <a name="register-binding-extensions"></a>Registar as extensões de enlace

Como está a utilizar uma ligação de saída DB Azure Cosmos, deve ter a extensão de encadernações correspondente antes de executar o projeto. 

::: zone pivot="programming-language-csharp"

Com exceção dos gatilhos HTTP e timer, as ligações são implementadas como pacotes de extensão. Executar o seguinte [comando de pacote de dotnet](/dotnet/core/tools/dotnet-add-package) na janela Terminal para adicionar o pacote de extensão de Armazenamento ao seu projeto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

O seu projeto foi configurado para utilizar [pacotes de extensão,](functions-bindings-register.md#extension-bundles)que instala automaticamente um conjunto de pacotes de extensão predefinidos. 

A utilização de pacotes de extensão é ativada no host.jsficheiro na raiz do projeto, que aparece da seguinte forma:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Agora, pode adicionar a ligação de saída DB da Azure Cosmos ao seu projeto.

## <a name="add-an-output-binding"></a>Adicionar um enlace de saída

Em Funções, cada tipo de encadernação requer `direction` `type` um, e um único `name` a ser definido no function.jsem arquivo. A forma como define estes atributos depende do idioma da sua aplicação de função.

::: zone pivot="programming-language-csharp"

Num projeto de biblioteca de classes C', as ligações são definidas como atributos de ligação no método de função. A *function.jsno* ficheiro exigido por Funções é então gerada automaticamente com base nestes atributos.

Abra o ficheiro de projeto *httpExample.cs* e adicione o seguinte parâmetro à definição do `Run` método:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

O `documentsOut` parâmetro é um tipo IAsyncCollector, que representa uma coleção de <T> documentos JSON que serão escritos para o seu contentor Azure Cosmos DB quando a função estiver concluída. Atributos específicos especificam o nome do recipiente e o nome da base de dados dos seus pais. A cadeia de ligação para a sua conta DB Azure Cosmos é definida pelo `ConnectionStringSettingAttribute` .

A definição do método Run deve agora parecer-se com o seguinte:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Os atributos de ligação são definidos diretamente no function.jsno ficheiro. Dependendo do tipo de encadernação, podem ser necessárias propriedades adicionais. A [configuração de saída DB Azure Cosmos](./functions-bindings-cosmosdb-v2-output.md#configuration) descreve os campos necessários para uma ligação de saída Azure Cosmos DB. A extensão facilita a adição de encadernações à function.jsem ficheiro. 

Para criar uma ligação, clique à direita (Ctrl+clique no macOS) o `function.json` ficheiro na sua pasta HttpTrigger e escolha Adicionar **ligação...**. Siga as instruções para definir as seguintes propriedades vinculativas para a nova ligação:

| Prompt | Valor | Descrição |
| -------- | ----- | ----------- |
| **Selecione direção de encadernação** | `out` | A ligação é uma ligação de saída. |
| **Selecione a ligação com a direção "out"** | `Azure Cosmos DB` | A ligação é uma ligação Azure Cosmos DB. |
| **O nome usado para identificar esta ligação no seu código** | `outputDocument` | Nome que identifica o parâmetro de ligação referenciado no seu código. |
| **A base de dados cosmos DB onde os dados serão escritos** | `my-database` | O nome da base de dados DB Azure Cosmos que contém o contentor-alvo. |
| **Recolha de bases de dados onde os dados serão escritos** | `my-container` | O nome do contentor Azure Cosmos DB onde serão escritos os documentos do JSON. |
| **Se o valor for verdadeiro, cria a coleção e a base de dados do Cosmos DB** | `false` | A base de dados-alvo e o contentor já existem. |
| **Selecione a definição de "local.setting.json"** | `CosmosDbConnectionString` | O nome de uma definição de aplicação que contém a cadeia de ligação para a conta DB Azure Cosmos. |
| **Chave de partição (opcional)** | *deixar em branco* | Só é necessário quando a ligação de saída criar o recipiente. |
| **Débito da coleção (opcional)** | *deixar em branco* | Só é necessário quando a ligação de saída criar o recipiente. |

Uma ligação é adicionada à `bindings` matriz no seu function.js, que deve parecer o seguinte:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Adicione código que utiliza o enlace de saída

::: zone pivot="programming-language-csharp"  

Adicione código que utiliza o `documentsOut` objeto de ligação de saída para criar um documento JSON. Adicione este código antes do método voltar.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Neste ponto, a sua função deve ser a seguinte:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Adicione código que utiliza o `outputDocument` objeto de ligação de saída `context.bindings` para criar um documento JSON. Adicione este código antes da `context.res` declaração.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Neste ponto, a sua função deve ser a seguinte:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Executar a função localmente

1. Como no artigo anterior, prima <kbd>F5</kbd> para iniciar o projeto de aplicação de função e Ferramentas Core. 

1. Com as Ferramentas Core em funcionamento, vá para a área **Azure: Funções.** Em **Funções**, expandir funções **de projeto**  >  **locais.** Clique com o botão direito (Ctrl-click em Mac) a `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Execute a função agora a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  
 
1. Depois de retornado, prima <kbd>Ctrl + C</kbd> para parar as Ferramentas Core.

### <a name="verify-that-a-json-document-has-been-created"></a>Verifique se foi criado um documento JSON

1. No portal Azure, volte à sua conta DB Azure Cosmos e selecione **Data Explorer**.

1. Expanda a sua base de dados e o seu contentor e selecione **Itens** para listar os documentos criados no seu contentor.

1. Verifique se um novo documento JSON foi criado pela ligação de saída.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Verificação de que um novo documento foi criado no contentor DB Azure Cosmos" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Reimplantar e verificar a aplicação atualizada

1. No Código do Estúdio Visual, prima F1 para abrir a paleta de comando. Na paleta de comando, procure e selecione `Azure Functions: Deploy to function app...` .

1. Escolha a aplicação de função que criou no primeiro artigo. Como está a recolocar o seu projeto na mesma aplicação, selecione **Implementar** para rejeitar o aviso sobre a sobreposição de ficheiros.

1. Após a implementação concluída, pode voltar a utilizar a **função Executar Agora...** para ativar a função em Azure.

1. [Verifique novamente os documentos criados no seu contentor Azure Cosmos DB](#verify-that-a-json-document-has-been-created) para verificar se a ligação de saída gera novamente um novo documento JSON.

## <a name="clean-up-resources"></a>Limpar os recursos

No Azure, *os recursos* referem-se a apps de função, funções, contas de armazenamento, etc. Estão agrupados em *grupos de recursos,* e podes apagar tudo num grupo, eliminando o grupo.

Criou recursos para concluir estes guias de introdução. Poderá ser-lhe cobrado estes recursos, dependendo do seu [estado da conta](https://azure.microsoft.com/account/) e dos [preços dos serviços](https://azure.microsoft.com/pricing/). Se já não precisar dos recursos, pode eliminá-los da seguinte forma:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Passos seguintes

Atualizou a sua função HTTP para escrever documentos JSON num contentor DB Azure Cosmos. Agora pode aprender mais sobre o desenvolvimento de funções utilizando o Código do Estúdio Visual:

+ [Desenvolver funções Azure usando código de estúdio visual](functions-develop-vs-code.md)

+ [Funções Azure aciona e encaderna](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Exemplos de projetos completos de funções em C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referência do programador Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemplos de projetos de função completa no JavaScript.](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript guia de desenvolvedores](functions-reference-node.md)  
::: zone-end  
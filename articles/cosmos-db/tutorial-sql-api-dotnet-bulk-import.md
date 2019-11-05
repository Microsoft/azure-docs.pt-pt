---
title: Otimizar a taxa de transferência ao importar dados em massa para Azure Cosmos DB conta da API do SQL
description: Saiba como criar um aplicativo de console .NET que otimiza a taxa de transferência provisionada (RU/s) necessária para importar dados para Azure Cosmos DB.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 3e8f9f6f839f4effa039335e6326c163b18d3dcc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519346"
---
# <a name="optimize-throughput-when-bulk-importing-data-to-azure-cosmos-db-sql-api-account"></a>Otimizar a taxa de transferência ao importar dados em massa para Azure Cosmos DB conta da API do SQL

Este tutorial mostra como criar um aplicativo de console .NET que otimiza a taxa de transferência provisionada (RU/s) necessária para importar dados para Azure Cosmos DB. Neste artigo, você vai ler dados de uma fonte de dados de exemplo e importá-los para um contêiner Cosmos do Azure.
Este tutorial usa a [versão 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do SDK do .net Azure Cosmos DB, que pode ser direcionado para .NET Framework ou .NET Core.

Este tutorial aborda:

> [!div class="checklist"]
> * Criando uma conta do Azure Cosmos
> * Configurando seu projeto
> * Conectando-se a uma conta do Azure cosmos com suporte em massa habilitado
> * Executar uma importação de dados por meio de operações de criação simultânea

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções neste artigo, verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [SDK do NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Você pode verificar qual versão está disponível em seu ambiente executando `dotnet --version`.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

[Crie um Azure Cosmos DB conta da API do SQL](create-cosmosdb-resources-portal.md) no portal do Azure ou você pode criar a conta usando o [emulador Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Etapa 2: configurar seu projeto do .NET

Abra o prompt de comando do Windows ou uma janela de terminal do computador local. Você executará todos os comandos nas próximas seções do prompt de comando ou do terminal. Execute o seguinte comando dotnet New para criar um novo aplicativo com o nome *Bulk-Import-demo*. O parâmetro `--langVersion` define a propriedade *langversion* no arquivo de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A saída esperada da compilação deve ser semelhante a esta:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Etapa 3: Adicionar o pacote de Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de cliente do Azure Cosmos DB para .NET Core usando o comando dotnet adicionar pacote.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Etapa 4: obter suas credenciais de conta do Azure Cosmos

O aplicativo de exemplo precisa se autenticar na sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais de conta do Azure Cosmos seguindo estas etapas:

1.  Iniciar sessão no [portal do Azure](https://portal.azure.com/).
1.  Navegue até sua conta do Azure Cosmos.
1.  Abra o painel **chaves** e copie o **URI** e a **chave primária** da sua conta.

Se você estiver usando o emulador de Azure Cosmos DB, obtenha as [credenciais do emulador neste artigo](local-emulator.md#authenticating-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Etapa 5: inicializar o objeto CosmosClient com suporte de execução em massa

Abra o arquivo `Program.cs` gerado em um editor de código. Você criará uma nova instância de CosmosClient com a execução em massa habilitada e a usará para realizar operações em relação a Azure Cosmos DB. 

Vamos começar substituindo o método de `Main` padrão e definindo as variáveis globais. Essas variáveis globais incluirão o ponto de extremidade e as chaves de autorização, o nome do banco de dados, o contêiner que você criará e o número de itens que serão inseridos em massa. Certifique-se de substituir os valores de chave de autorização e endpointURL de acordo com seu ambiente. 


   ```csharp
   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Dentro do método `Main`, adicione o seguinte código para inicializar o objeto CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Depois que a execução em massa é habilitada, o CosmosClient agrupa internamente as operações simultâneas em chamadas de serviço único. Dessa forma, ele otimiza a utilização da taxa de transferência distribuindo chamadas de serviço entre partições e, finalmente, atribuindo resultados individuais aos chamadores originais.

Em seguida, você pode criar um contêiner para armazenar todos os nossos itens.  Defina `/pk` como a chave de partição, 50000 RU/s como taxa de transferência provisionada e uma política de indexação personalizada que excluirá todos os campos para otimizar a taxa de transferência de gravação. Adicione o seguinte código após a instrução de inicialização CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Etapa 6: preencher uma lista de tarefas simultâneas

Para aproveitar o suporte à execução em massa, crie uma lista de tarefas assíncronas com base na fonte de dados e nas operações que você deseja executar e use `Task.WhenAll` para executá-las simultaneamente.
Vamos começar usando dados "falsos" para gerar uma lista de itens de nosso modelo de dados. Em um aplicativo do mundo real, os itens seriam provenientes da fonte de dados desejada.

Primeiro, adicione o pacote falso à solução usando o comando dotnet adicionar pacote.

   ```bash
   dotnet add package Bogus
   ```

Defina a definição dos itens que você deseja salvar. Você precisa definir a classe `Item` dentro do arquivo `Program.cs`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Em seguida, crie uma função auxiliar dentro da classe `Program`. Essa função auxiliar obterá o número de itens que você definiu para inserir e gera dados aleatórios:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Leia os itens e Serialize-os em instâncias de fluxo usando a classe `System.Text.Json`. Devido à natureza dos dados gerados automaticamente, você está serializando os dados como fluxos. Você também pode usar a instância de item diretamente, mas convertendo-as em fluxos, você pode aproveitar o desempenho de APIs de fluxo no CosmosClient. Normalmente, você pode usar os dados diretamente desde que conheça a chave de partição. 


Para converter os dados em instâncias de fluxo, dentro do método `Main`, adicione o código a seguir logo após a criação do contêiner:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Em seguida, use os fluxos de dados para criar tarefas simultâneas e preencher a lista de tarefas para inserir os itens no contêiner. Para executar essa operação, adicione o seguinte código à classe `Program`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Todas essas operações de ponto simultâneas serão executadas juntas (isto é, em massa), conforme descrito na seção Introdução.

## <a name="step-7-run-the-sample"></a>Etapa 7: executar o exemplo

Para executar o exemplo, você pode fazer isso simplesmente pelo comando `dotnet`:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Obter o exemplo completo

Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir os exemplos de código, pode obtê-los a partir do [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Depois de clonar o projeto, certifique-se de atualizar as credenciais desejadas dentro de [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

O exemplo pode ser executado alterando para o diretório do repositório e usando `dotnet`:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você fez as seguintes etapas:

> [!div class="checklist"]
> * Criando uma conta do Azure Cosmos
> * Configurando seu projeto
> * Conectando-se a uma conta do Azure cosmos com suporte em massa habilitado
> * Executar uma importação de dados por meio de operações de criação simultânea

Agora você pode prosseguir para o próximo tutorial:

> [!div class="nextstepaction"]
>[Azure Cosmos DB de consulta usando a API do SQL](tutorial-query-sql-api.md)
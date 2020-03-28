---
title: Dados de importação a granel para a conta API Da Azure Cosmos DB SQL utilizando o SDK .Net
description: Saiba importar ou ingerir dados para o Azure Cosmos DB através da construção de uma aplicação de consola .NET que otimize a provisão (RU/s) necessária para importar dados
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587571"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Dados de importação a granel para a conta API Da Azure Cosmos DB SQL utilizando o SDK .NET

Este tutorial mostra como construir uma aplicação de consola .NET que otimiza a entrada (RU/s) necessária para importar dados para o Azure Cosmos DB. Neste artigo, você vai ler dados de uma fonte de dados de amostra e importá-lo em um recipiente Azure Cosmos.
Este tutorial utiliza a [versão 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do Azure Cosmos DB .NET SDK, que pode ser direcionada para .NET Framework ou .NET Core.

Este tutorial aborda:

> [!div class="checklist"]
> * Criar uma conta Azure Cosmos
> * Configurar o seu projeto
> * Ligação a uma conta Azure Cosmos com suporte a granel ativado
> * Realizar uma importação de dados através de operações de criação simultâneas

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Núcleo NET 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Pode verificar qual a versão disponível `dotnet --version`no seu ambiente executando .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

[Crie uma conta API Azure Cosmos DB SQL](create-cosmosdb-resources-portal.md) a partir do portal Azure ou pode criar a conta utilizando o [Emulador DB Azure Cosmos.](local-emulator.md)

## <a name="step-2-set-up-your-net-project"></a>Passo 2: Configurar o seu projeto .NET

Abra o pedido de comando do Windows ou uma janela terminal a partir do seu computador local. Executará todos os comandos nas próximas secções a partir do pedido de comando ou terminal. Executar o novo comando do dotnet seguinte para criar uma nova app com o nome *granel-import-demo*. O `--langVersion` parâmetro define a propriedade *LangVersion* no ficheiro de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A saída esperada da construção deve ser algo assim:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Passo 3: Adicione o pacote Azure Cosmos DB

Ainda no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando de pacote de adição de dotnet.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Passo 4: Obtenha as credenciais da sua conta Azure Cosmos

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deverá passar as credenciais da conta Azure Cosmos para a aplicação. Obtenha as credenciais da sua conta Azure Cosmos seguindo estes passos:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1.  Navegue para a sua conta Azure Cosmos.
1.  Abra o painel **de Teclas** e copie a CHAVE **URI** e **PRIMÁRIA** da sua conta.

Se estiver a utilizar o Emulador Db Azure Cosmos, obtenha as [credenciais do emulador deste artigo.](local-emulator.md#authenticating-requests)

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Passo 5: Inicializar o objeto CosmosClient com suporte à execução a granel

Abra o `Program.cs` ficheiro gerado num editor de código. Criará uma nova instância de CosmosClient com execução a granel habilitada e usá-la-á para fazer operações contra o Azure Cosmos DB. 

Vamos começar por sobrepor `Main` o método padrão e definir as variáveis globais. Estas variáveis globais incluirão as chaves de ponta e autorização, o nome da base de dados, o recipiente que irá criar e o número de itens que irá inserir a granel. Certifique-se de que substitui os valores-chave do ponto final e da autorização de acordo com o seu ambiente. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

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

Dentro `Main` do método, adicione o seguinte código para inicializar o objeto CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Após a execução a granel ser ativada, o CosmosClient agrupa internamente operações simultâneas em chamadas de serviço únicos. Desta forma, otimiza a utilização da entrada distribuindo chamadas de serviço através de divisórias e, finalmente, atribuindo resultados individuais aos chamadores originais.

Em seguida, pode criar um recipiente para armazenar todos os nossos itens.  Defina `/pk` como a chave de partição, 50000 RU/s como entrada provisionada, e uma política de indexação personalizada que excluirá todos os campos para otimizar a entrada de escrita. Adicione o seguinte código após a declaração de inicialização cosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Passo 6: Povoar uma lista de tarefas simultâneas

Para tirar partido do suporte de execução a granel, crie uma lista de tarefas assíncronas `Task.WhenAll` com base na fonte de dados e nas operações que pretende executar, e use para executá-las simultaneamente.
Vamos começar por usar dados "Falsos" para gerar uma lista de itens do nosso modelo de dados. Numa aplicação no mundo real, os itens viriam da sua fonte de dados desejada.

Em primeiro lugar, adicione o pacote Falso à solução utilizando o comando de pacote de adição de dotnet.

   ```bash
   dotnet add package Bogus
   ```

Defina a definição dos itens que pretende guardar. Precisa definir a `Item` classe `Program.cs` dentro do ficheiro:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Em seguida, crie uma `Program` função de ajudante dentro da classe. Esta função de ajudante obterá o número de itens definidos para inserir e gera dados aleatórios:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Leia os itens e serialize-os em instâncias de streaming utilizando a `System.Text.Json` classe. Devido à natureza dos dados autogerados, está a serializar os dados como fluxos. Também pode utilizar a instância do item diretamente, mas ao convertê-los em streams, pode aproveitar o desempenho das APIs stream no CosmosClient. Normalmente, pode utilizar os dados diretamente desde que conheça a chave de partição. 


Para converter os dados em instâncias stream, dentro do `Main` método, adicione o seguinte código logo após a criação do recipiente:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Em seguida, utilize os fluxos de dados para criar tarefas simultâneas e povoar a lista de tarefas para inserir os itens no recipiente. Para efetuar esta operação, `Program` adicione o seguinte código à classe:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Todas estas operações de pontos simultâneos serão executadas em conjunto (isto é, a granel), conforme descrito na secção de introdução.

## <a name="step-7-run-the-sample"></a>Passo 7: Executar a amostra

Para executar a amostra, pode fazê-lo simplesmente pelo `dotnet` comando:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Obtenha a amostra completa

Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir os exemplos de código, pode obtê-los a partir do [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Depois de clonar o projeto, certifique-se de atualizar as credenciais desejadas no interior [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

A amostra pode ser executada alterando-se para `dotnet`o diretório de repositórios e utilizando:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, já fez os seguintes passos:

> [!div class="checklist"]
> * Criar uma conta Azure Cosmos
> * Configurar o seu projeto
> * Ligação a uma conta Azure Cosmos com suporte a granel ativado
> * Realizar uma importação de dados através de operações de criação simultâneas

Agora pode passar para o próximo tutorial:

> [!div class="nextstepaction"]
>[Consulta Azure Cosmos DB usando a API SQL](tutorial-query-sql-api.md)
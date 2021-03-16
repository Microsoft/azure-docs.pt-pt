---
title: Dados de importação a granel para a conta Azure Cosmos DL API através da utilização da conta .NET SDK
description: Saiba como importar ou ingerir dados para a Azure Cosmos DB construindo uma aplicação de consola .NET que otimize o rendimento (RU/s) previsto para a importação de dados
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 03/15/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c178f57a31e02b3dac712a5425db226720200c5
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563628"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Dados de importação a granel para a conta Azure Cosmos DL API através da utilização da conta .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial mostra como construir uma aplicação de consola .NET que otimiza a produção (RU/s) necessária para importar dados para a Azure Cosmos DB. Neste artigo, você vai ler dados de uma fonte de dados de amostra e importá-lo em um recipiente Azure Cosmos.
Este tutorial utiliza a [versão 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do Azure Cosmos DB .NET SDK, que pode ser direcionado para .NET Framework ou .NET Core.

Este tutorial aborda:

> [!div class="checklist"]
> * Criação de uma conta Azure Cosmos
> * Configurar o seu projeto
> * Ligação a uma conta Azure Cosmos com suporte a granel ativado
> * Realizar uma importação de dados através de operações de criação simultâneas

## <a name="prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, certifique-se de que dispõe dos seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Pode verificar qual a versão disponível no seu ambiente executando `dotnet --version` .

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB

[Crie uma conta Azure Cosmos DB SQL API](create-cosmosdb-resources-portal.md) a partir do portal Azure ou pode criar a conta utilizando o [Emulator Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Passo 2: Configurar o seu projeto .NET

Abra o pedido de comando do Windows ou uma janela do terminal a partir do computador local. Executará todos os comandos nas próximas secções a partir do comando ou terminal. Executar o seguinte comando dotnet novo para criar uma nova app com o nome *granão-importação-demonstração*. O `--langVersion` parâmetro define a propriedade *LangVersion* no ficheiro do projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

A produção esperada da construção deve ser algo assim:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Passo 3: Adicione o pacote DB Azure Cosmos

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando do pacote de adicionar dotnet.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Passo 4: Obtenha as suas credenciais de conta Azure Cosmos

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deve passar as credenciais de conta Azure Cosmos para a aplicação. Obtenha as suas credenciais de conta Azure Cosmos seguindo estes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
1.  Navegue para a sua conta Azure Cosmos.
1.  Abra o painel **de chaves** e copie o **URI** e **a CHAVE PRINCIPAL** da sua conta.

Se estiver a utilizar o Emulador Azure Cosmos DB, obtenha [as credenciais de emulador deste artigo](local-emulator.md#authenticate-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Passo 5: Inicializar o objeto CosmosClient com suporte de execução a granel

Abra o ficheiro gerado `Program.cs` num editor de código. Você vai criar um novo exemplo de CosmosClient com execução a granel habilitado e usá-lo para fazer operações contra Azure Cosmos DB. 

Vamos começar por sobrepor o método padrão `Main` e definir as variáveis globais. Estas variáveis globais incluirão as teclas de ponto final e autorização, o nome da base de dados, o contentor que irá criar e o número de itens que irá inserir a granel. Certifique-se de que substitui os valores de chave de ponta e de autorização de acordo com o seu ambiente. 


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
        private const int AmountToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Dentro do `Main` método, adicione o seguinte código para inicializar o objeto CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Após a execução em massa, o CosmosClient agita internamente operações simultâneas em chamadas de serviço únicas. Desta forma, otimiza a utilização da produção distribuindo chamadas de serviço através de divisórias e, finalmente, atribuindo resultados individuais aos chamadores originais.

Em seguida, pode criar um recipiente para armazenar todos os nossos itens.  Defina `/pk` como a chave de partição, 50000 RU/s como produção prevista, e uma política de indexação personalizada que excluirá todos os campos para otimizar a produção de escrita. Adicione o seguinte código após a declaração de inicialização cosmosclient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Passo 6: Povoar uma lista de tarefas simultâneas

Para tirar partido do suporte à execução a granel, crie uma lista de tarefas assíncronos baseadas na fonte de dados e nas operações que pretende executar, e `Task.WhenAll` utilize-as para executá-las simultaneamente.
Comecemos por usar dados "Falsos" para gerar uma lista de itens do nosso modelo de dados. Numa aplicação real, os itens viriam da sua fonte de dados desejada.

Em primeiro lugar, adicione o pacote Bogus à solução utilizando o comando do pacote de adicionar dotnet.

   ```bash
   dotnet add package Bogus
   ```

Defina a definição dos itens que pretende guardar. É necessário definir a `Item` classe dentro do `Program.cs` ficheiro:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Em seguida, crie uma função de ajudante dentro da `Program` classe. Esta função de ajudante obterá o número de itens definidos para inserir e gera dados aleatórios:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Utilize a função de ajudante para inicializar uma lista de documentos para trabalhar com:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Em seguida, utilize a lista de documentos para criar tarefas simultâneas e povoar a lista de tarefas para inserir os itens no recipiente. Para efetuar esta operação, adicione o seguinte código à `Program` classe:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Todas estas operações de ponto simultâneo serão executadas em conjunto (isto é, a granel), conforme descrito na secção de introdução.

## <a name="step-7-run-the-sample"></a>Passo 7: Executar a amostra

Para executar a amostra, pode fazê-lo simplesmente pelo `dotnet` comando:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Obtenha a amostra completa

Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir os exemplos de código, pode obtê-los a partir do [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Após a clonagem do projeto, certifique-se de atualizar as credenciais desejadas dentro [do Programa.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/main/src/Program.cs#L25).

A amostra pode ser executada mudando para o diretório do repositório e `dotnet` utilizando:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez os seguintes passos:

> [!div class="checklist"]
> * Criação de uma conta Azure Cosmos
> * Configurar o seu projeto
> * Ligação a uma conta Azure Cosmos com suporte a granel ativado
> * Realizar uma importação de dados através de operações de criação simultâneas

Pode agora avançar para o próximo tutorial:

> [!div class="nextstepaction"]
>[Consulta Azure Cosmos DB usando o SQL API](tutorial-query-sql-api.md)
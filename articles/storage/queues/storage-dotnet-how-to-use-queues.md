---
title: Introdução ao armazenamento de filas do Azure usando o .NET-armazenamento do Azure
description: As Filas do Azure fornecem um serviço de mensagens fiável e assíncrono entre componentes da aplicação. O serviço de mensagens na nuvem permite que os componentes da sua aplicação sejam dimensionados de forma independente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 5ffee146bdbd666d4175af2f49f6b447743b2bc0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457681"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao Armazenamento de Filas do Azure com o .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Visão geral

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Acerca deste tutorial

Este tutorial demonstra como escrever código .NET para alguns cenários comuns utilizando o Armazenamento de filas do Azure. Os cenários abrangidos incluem criar e eliminar filas e adicionar, ler e eliminar mensagens de filas.

**Tempo estimado para concluir:** 45 minutos

### <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Biblioteca de cliente comum do armazenamento do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Biblioteca de cliente de fila de armazenamento do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Gestor de Configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Uma [Conta do Storage do Azure](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurar o seu ambiente de desenvolvimento

Em seguida, configure o ambiente de desenvolvimento no Visual Studio, para estar pronto para experimentar os exemplos de código deste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicação de consola do Windows

No Visual Studio, crie uma nova aplicação de consola do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Selecione **Ficheiro** > **Novo** > **Projeto**
2. Selecionar **plataforma** > **Windows**
3. Selecione **Aplicação da Consola (.NET Framework)**
4. Selecione **Seguinte**
5. No campo **nome do projeto** , insira um nome para seu aplicativo
6. Selecione **Criar**

Todos os exemplos de código neste tutorial podem ser adicionados ao método **Main ()** do arquivo **Program.cs** do seu aplicativo de console.

Você pode usar as bibliotecas de cliente de armazenamento do Azure em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure ou aplicativo Web, e aplicativos móveis e de desktop. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

Você precisa fazer referência aos três pacotes a seguir em seu projeto para concluir este tutorial:

* [Armazenamento do Microsoft Azure biblioteca de cliente comum para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Este pacote fornece acesso programático a recursos de dados em sua conta de armazenamento.
* [Biblioteca de fila de armazenamento do Microsoft Azure para .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): essa biblioteca de cliente permite trabalhar com o serviço Fila de armazenamento do Microsoft Azure para armazenar mensagens que podem ser acessadas por um cliente.
* [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação estiver a ser executada.

Você pode usar o NuGet para obter esses pacotes. Siga estes passos.

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.
2. Selecione **procurar**
3. Pesquise online "Microsoft. Azure. Storage. Queue" e selecione **instalar** para instalar a biblioteca de cliente de armazenamento e suas dependências. Isso também instalará a biblioteca Microsoft. Azure. Storage. Common, que é uma dependência da biblioteca de filas.
4. Pesquise online por "Microsoft. Azure. ConfigurationManager" e selecione **instalar** para instalar o Configuration Manager do Azure.

> [!NOTE]
> Os pacotes de bibliotecas de cliente de armazenamento também estão incluídos no [SDK do Azure para .net](https://azure.microsoft.com/downloads/). No entanto, recomendamos que você também instale as bibliotecas de cliente de armazenamento do NuGet para garantir que você sempre tenha as versões mais recentes.
>
> As dependências de ODataLib nas bibliotecas de cliente de armazenamento para .NET são resolvidas pelos pacotes ODataLib disponíveis no NuGet, não da WCF Data Services. As bibliotecas ODataLib podem ser transferidas diretamente ou referenciadas pelo seu projeto de código através do NuGet. Os pacotes ODataLib específicos usados pelas bibliotecas de cliente de armazenamento são [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)e [Spatial](https://nuget.org/packages/System.Spatial/). Embora essas bibliotecas sejam usadas pelas classes de armazenamento de tabelas do Azure, elas são dependências necessárias para a programação com as bibliotecas de cliente de armazenamento.

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Tem duas opções de ambiente para executar os exemplos neste guia:

* Pode executar o código numa conta de armazenamento do Azure na nuvem.
* Pode executar o código no emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar o seu código enquanto a aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para obter mais informações, veja [Utilizar o emulador de armazenamento do Azure para programação e teste](../common/storage-use-emulator.md).

Se estiver a filtrar uma conta de armazenamento na cloud, copie a chave de acesso primária para a sua conta de armazenamento a partir do portal do Azure. Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](../common/storage-account-keys-manage.md).

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por filtrar uma conta de armazenamento do Azure na nuvem, os custos para efetuar este tutorial serão negligenciável.

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

As bibliotecas de cliente de armazenamento do Azure para .NET dão suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acessar serviços de armazenamento. A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração.

Para obter mais informações sobre as cadeias de ligação, veja [Configurar uma cadeia de ligação para o Armazenamento do Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.

Para configurar a cadeia de conexão, abra o arquivo **app. config** de Gerenciador de soluções no Visual Studio. Adicione o conteúdo do elemento **\<appsettings\>** mostrado abaixo. Substitua *Account-Name* pelo nome da sua conta de armazenamento e da *chave de conta* pela chave de acesso da conta:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por exemplo, a definição de configuração é semelhante a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Para filtrar o emulador de armazenamento, pode utilizar um atalho que mapeia para o nome de conta e chave bem conhecidas. Nesse caso, a definição da cadeia de ligação é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Adicionar com diretivas

Adicione as seguintes diretivas `using` à parte superior do ficheiro `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

O código de exemplo tem de autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça à aplicação as credenciais da conta de armazenamento sob a forma de uma cadeia de ligação. Para ver as credenciais da conta de armazenamento:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Criar o cliente do serviço Fila

A classe [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) permite-lhe obter filas armazenadas no Armazenamento de filas. Eis uma forma de criar o cliente do serviço:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Agora, está pronto para escrever código que lê dados de e escreve dados para o Armazenamento de filas.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila se ainda não existir:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Introduzir uma mensagem numa fila

Para introduzir uma mensagem numa fila existente, primeiro crie um novo [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Em seguida, chame o método [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet). É possível criar uma **CloudQueueMessage** a partir de uma cadeia (no formato UTF-8) ou uma matriz de **bytes**. Eis o código que cria uma fila (se não existir) e introduz a mensagem "Olá, Mundo":

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Pré-visualização da mensagem seguinte

Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet).

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Remover a mensagem seguinte da fila

O código remove uma mensagem da fila em dois passos. Quando chamar [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet), obterá a seguinte mensagem numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código chama **DeleteMessage** imediatamente após a mensagem ser processada.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utilizar o padrão Async-Await com APIs de Armazenamento de filas comuns

Este exemplo mostra como utilizar o padrão Async-Await com APIs de Armazenamento de filas comuns. O exemplo chama a versão assíncrona de cada um dos métodos em questão, conforme indicado pelo sufixo *Async* de cada método. Quando é utilizado um método assíncrono, o padrão async-await suspende a execução local, até que a chamada seja concluída. Este comportamento permite que o thread atual efetue outro trabalho, o que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Tirar maior partido das opções adicionais para remover as mensagens da fila

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, como tal, 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficarão visíveis novamente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

Pode obter uma estimativa do número de mensagens numa fila. O método [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) devolve o último valor obtido pelo método **FetchAttributes**, sem chamar o serviço Fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens nela contidas, chame o método [Eliminar](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) no objeto de fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu as noções básicas do Armazenamento de filas, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

* Ver a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  * [Referência da Biblioteca de Clientes do Storage para o .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Referência da API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Saiba como simplificar o código de escrita para trabalhar com o Storage do Azure utilizando o [SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
  * [Introdução ao Table Storage do Azure utilizando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) para armazenar dados estruturados.
  * [Introdução ao Blob Storage do Azure utilizando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
  * [Ligar à Base de Dados SQL com o .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2

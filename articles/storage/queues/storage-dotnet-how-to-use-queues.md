---
title: Começa com o armazenamento da Fila Azure usando .NET - Azure Storage
description: As Filas do Azure fornecem um serviço de mensagens fiável e assíncrono entre componentes da aplicação. O serviço de mensagens na nuvem permite que os componentes da sua aplicação sejam dimensionados de forma independente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: c07ad6e631482b47da674549e976953842cf983e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855927"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao Armazenamento de filas do Azure através do .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Descrição geral

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Na conceção de aplicações para escala, os componentes de aplicação são muitas vezes dissociados para que possam escalar de forma independente. O armazenamento de filas fornece mensagens assíncronos entre os componentes da aplicação, quer estejam a funcionar na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Acerca deste tutorial

Este tutorial demonstra como escrever código .NET para alguns cenários comuns utilizando o Armazenamento de filas do Azure. Os cenários abrangidos incluem criar e eliminar filas e adicionar, ler e eliminar mensagens de filas.

**Tempo estimado para concluir:** 45 minutos

### <a name="prerequisites"></a>Pré-requisitos

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Uma [conta de armazenamento Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Em seguida, configure o ambiente de desenvolvimento no Visual Studio, para estar pronto para experimentar os exemplos de código deste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicação de consola do Windows

No Visual Studio, crie uma nova aplicação de consola do Windows. Os passos seguintes mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Selecione **o**novo projeto de  >  **New**  >  **arquivo**
2. Selecione **Janelas de Plataforma**  >  **Windows**
3. Selecione **app de consola (.NET Framework)**
4. Selecione **Seguinte**
5. No campo nome do **Projeto,** insira um nome para a sua aplicação
6. Selecione **Criar**

Todos os exemplos de código neste tutorial podem ser adicionados ao método **Principal do** ficheiro **Program.cs** da sua aplicação de consola.

Pode utilizar as bibliotecas de clientes do Azure Storage em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Você precisa fazer referência aos seguintes quatro pacotes no seu projeto para completar este tutorial:

- [Biblioteca Azure Core para .NET](https://www.nuget.org/packages/Azure.Core/): Este pacote fornece primitivos, abstrações e ajudantes partilhados para bibliotecas de clientes modernos .NET Azure SDK.
- [Biblioteca comum de clientes Azure Storage para .NET](https://www.nuget.org/packages/Azure.Storage.Common/): Este pacote fornece infraestruturas partilhadas pelas outras bibliotecas de clientes do Azure Storage.
- [Biblioteca de fila de armazenamento Azure para .NET](https://www.nuget.org/packages/Azure.Storage.Queues/): Este pacote permite trabalhar com o serviço de Fila de Armazenamento Azure para armazenar mensagens que possam ser acedidas por um cliente.
- [Biblioteca do Gestor de Configuração para .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Este pacote fornece acesso a ficheiros de configuração para aplicações de clientes.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos:

1. Clique com o botão direito no seu projeto no **Solution Explorer**e escolha **Gerir pacotes NuGet**.
1. **Selecione Procurar**
1. Pes faça pesquisa online por "Azure.Storage.Queues", e selecione **Instalar** para instalar a biblioteca do cliente de Armazenamento e as suas dependências. Isto também instalará as bibliotecas Azure.Storage.Common e Azure.Core, que são dependências da biblioteca de fila.
1. Procure online por "System.Configuration.ConfigurationManager", e selecione **Instalar** para instalar o Gestor de Configurações.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Tem de fazer referência aos seguintes três pacotes do seu projeto para completar este tutorial:

- [Microsoft Azure Storage Common Client Library for.NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Este pacote fornece acesso programático aos recursos de dados na sua conta de armazenamento.
- [Microsoft Azure Storage Queue Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Esta biblioteca de clientes permite trabalhar com o serviço microsoft Azure Storage Queue para armazenar mensagens que possam ser acedidas por um cliente.
- [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação estiver a ser executada.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos:

1. Clique com o botão direito no seu projeto no **Solution Explorer**e escolha **Gerir pacotes NuGet**.
1. **Selecione Procurar**
1. Procure online por "Microsoft.Azure.Storage.Queue", e selecione **Instalar** para instalar a biblioteca do cliente de Armazenamento e as suas dependências. Isto também instalará a biblioteca Microsoft.Azure.Storage.Common, que é uma dependência da biblioteca de filas.
1. Pesse online por "Microsoft.Azure.ConfigurationManager", e selecione **Instalar** para instalar o Gestor de Configurações Azure.

---

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Tem duas opções de ambiente para executar os exemplos neste guia:

- Pode executar o código numa conta de armazenamento do Azure na nuvem.
- Pode executar o seu código contra o emulador de armazenamento Azurite. Azurite é um ambiente local que imita uma conta de Armazenamento Azure na nuvem. O Azurite é uma opção gratuita para testar e depurar o seu código enquanto a sua aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para obter mais informações, [consulte o emulador Azurite para o desenvolvimento e testes locais de armazenamento da Azure.](../common/storage-use-azurite.md)

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por filtrar uma conta de armazenamento do Azure na nuvem, os custos para efetuar este tutorial serão negligenciável.

## <a name="get-your-storage-connection-string"></a>Obtenha o seu string de conexão de armazenamento

As bibliotecas de clientes do Azure Storage para suporte .NET utilizando uma cadeia de ligação de armazenamento para configurar pontos finais e credenciais para aceder a serviços de armazenamento. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../common/storage-account-keys-manage.md)

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

O código de exemplo tem de autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça à aplicação as credenciais da conta de armazenamento sob a forma de uma cadeia de ligação. Para ver as credenciais da conta de armazenamento:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Para obter mais informações sobre as cordas de ligação, consulte [configurar uma cadeia de ligação ao Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.

A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração. Para configurar a sua cadeia de ligação, abra o ficheiro *app.config* do Solution Explorer no Visual Studio. Adicione o conteúdo do elemento `\<appSettings\>` mostrado abaixo. Substitua a *cadeia de ligação* pelo valor copiado da sua conta de armazenamento no portal:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Por exemplo, a definição de configuração é semelhante a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Para direcionar o emulador de armazenamento Azurite, pode utilizar um atalho que mapeia para o conhecido nome e chave da conta. Nesse caso, a definição da cadeia de ligação é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Adicionar com diretivas

Adicione as seguintes diretivas `using` à parte superior do ficheiro `Program.cs`:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>Criar o cliente do serviço Fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

A classe [QueueClient](/dotnet/api/azure.storage.queues.queueclient) permite-lhe recuperar as filas armazenadas no armazenamento da fila. Eis uma forma de criar o cliente do serviço:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

A classe [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) permite-lhe obter filas armazenadas no Armazenamento de filas. Eis uma forma de criar o cliente do serviço:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Agora, está pronto para escrever código que lê dados de e escreve dados para o Armazenamento de filas.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Introduzir uma mensagem numa fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Para inserir uma mensagem numa fila existente, ligue para o método [SendMessage.](/dotnet/api/azure.storage.queues.queueclient.sendmessage) Uma mensagem pode ser um `string` (em formato UTF-8) ou um `byte` array. O seguinte código cria uma fila (se não existir) e insere uma mensagem:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para introduzir uma mensagem numa fila existente, primeiro crie um novo [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true). Em seguida, chame o método [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true). Um `CloudQueueMessage` pode ser criado a partir de um `string` (em formato UTF-8) ou de uma `byte` matriz. Aqui está o código que cria uma fila (se não existir) e insere a mensagem "Olá, Mundo":

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Pré-visualização da mensagem seguinte

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Pode espreitar as mensagens na fila sem as retirar da fila, chamando o método [PeekMessages.](/dotnet/api/azure.storage.queues.queueclient.peekmessages) Se não passar um valor para o parâmetro *maxMessages,* o padrão é espreitar uma mensagem.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true).

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

---

## <a name="change-the-contents-of-a-queued-message"></a>Alterar os conteúdos de uma mensagem em fila

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para controlar fluxos de trabalho de vários passos em mensagens de filas, sem ser necessário recomeçar do início se falhar um passo de processamento devido a uma falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

## <a name="de-queue-the-next-message"></a>Remover a mensagem seguinte da fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Des-fila uma mensagem de uma fila em dois passos. Quando ligar para [o ReceiveMessages,](/dotnet/api/azure.storage.queues.queueclient.receivemessages)recebe a próxima mensagem numa fila. Uma mensagem devolvida `ReceiveMessages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage). Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

O código remove uma mensagem da fila em dois passos. Quando chamar [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true), obterá a seguinte mensagem numa fila. Uma mensagem devolvida `GetMessage` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true). Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

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

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utilizar o padrão Async-Await com APIs de Armazenamento de filas comuns

Este exemplo mostra como utilizar o padrão Async-Await com APIs de Armazenamento de filas comuns. O exemplo chama a versão assíncrona de cada um dos métodos em questão, conforme indicado pelo sufixo *Async* de cada método. Quando é utilizado um método assíncrono, o padrão async-await suspende a execução local, até que a chamada seja concluída. Este comportamento permite que o thread atual efetue outro trabalho, o que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Tirar maior partido das opções adicionais para remover as mensagens da fila

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

O seguinte exemplo de código utiliza o método [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) para obter 20 mensagens numa única chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, pelo que, após 5 minutos passados desde a `ReceiveMessages` chamada, quaisquer mensagens que não tenham sido apagadas tornar-se-ão visíveis novamente.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

O seguinte código de exemplo utiliza o método [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, pelo que, após 5 minutos passados desde a `GetMessages` chamada, quaisquer mensagens que não tenham sido apagadas tornar-se-ão visíveis novamente.

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

---

## <a name="get-the-queue-length"></a>Obter o comprimento da fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Pode obter uma estimativa do número de mensagens numa fila. O método [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) pede ao serviço De Fila para recuperar as propriedades da fila, incluindo a contagem de mensagens. A [propriedade AproximadaMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) contém o número aproximado de mensagens na fila. Este número não é inferior ao número real de mensagens na fila, mas pode ser maior.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pode obter uma estimativa do número de mensagens numa fila. O método [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. A [propriedade ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) devolve o último valor recuperado pelo `FetchAttributes` método, sem chamar o serviço de Fila.

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
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Eliminar uma fila

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Para eliminar uma fila e todas as mensagens nela contidas, chame o método [Eliminar](/dotnet/api/azure.storage.queues.queueclient.delete) no objeto de fila.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para eliminar uma fila e todas as mensagens nela contidas, chame o método [Eliminar](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) no objeto de fila.

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

---

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu as noções básicas do Armazenamento de filas, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

- Ver a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  - [Referência da Biblioteca de Clientes do Storage para o .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [Referência da API REST](https://msdn.microsoft.com/library/azure/dd179355)
- Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
  - [Introdução ao Table Storage do Azure utilizando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) para armazenar dados estruturados.
  - [Introdução ao Blob Storage do Azure utilizando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
  - [Ligar à Base de Dados SQL com o .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) para armazenar dados relacionais.
- Aprenda a simplificar o código que escreve para trabalhar com o Azure Storage utilizando o [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

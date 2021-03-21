---
title: Começar com o Azure Queue Storage usando .NET - Azure Storage
description: O Azure Queue Storage fornece mensagens fiáveis e assíncronas entre os componentes da aplicação. O serviço de mensagens na nuvem permite que os componentes da sua aplicação sejam dimensionados de forma independente.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585756"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao Armazenamento de Filas do Azure através do .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Descrição geral

O Azure Queue Storage fornece mensagens em nuvem entre os componentes da aplicação. Na conceção de aplicações para escala, os componentes de aplicação são muitas vezes dissociados para que possam escalar de forma independente. O Armazenamento de Fila fornece mensagens assíncronos entre os componentes da aplicação, quer estejam a funcionar na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de Fila também suporta a gestão de tarefas assíncronos e fluxos de trabalho de processo de construção.

### <a name="about-this-tutorial"></a>Acerca deste tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns usando O Armazenamento da Fila Azure. Os cenários abrangidos incluem criar e eliminar filas e adicionar, ler e eliminar mensagens de filas.

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

1. Selecione **o** novo projeto de  >    >  **arquivo**
2. Selecione **Janelas de Plataforma**  >  
3. Selecione **app de consola (.NET Framework)**
4. Selecione **Seguinte**
5. No campo nome do **Projeto,** insira um nome para a sua aplicação
6. Selecione **Criar**

Todos os exemplos de código deste tutorial podem ser adicionados ao método `Main()` do ficheiro `Program.cs` da aplicação da consola.

Pode utilizar as bibliotecas de clientes do Azure Storage em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Você precisa fazer referência aos seguintes quatro pacotes no seu projeto para completar este tutorial:

- [Biblioteca Azure.Core para .NET](https://www.nuget.org/packages/azure.core/): Este pacote fornece primitivos, abstrações e ajudantes partilhados para bibliotecas de clientes modernas .NET Azure SDK.
- [Azure.Storage.Biblioteca cliente comum para .NET](https://www.nuget.org/packages/azure.storage.common/): Este pacote fornece infraestruturas partilhadas pelas outras bibliotecas de clientes do Azure Storage.
- [Biblioteca cliente Azure.Storage.Queues para .NET](https://www.nuget.org/packages/azure.storage.queues/): Este pacote permite trabalhar com o Azure Queue Storage para armazenar mensagens que possam ser acedidas por um cliente.
- [System.Configuration.Configbiblioteca urationManager para .NET](https://www.nuget.org/packages/system.configuration.configurationmanager/): Este pacote fornece acesso a ficheiros de configuração para aplicações de clientes.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos:

1. Clique com o botão direito no seu projeto no **Solution Explorer** e escolha **Gerir pacotes NuGet**.
1. **Selecione Procurar**
1. Procure online `Azure.Storage.Queues` e selecione **Instalar** para instalar a biblioteca do cliente do Azure Storage e as suas dependências. Isto também instalará as bibliotecas Azure.Storage.Common e Azure.Core, que são dependências da biblioteca de fila.
1. Procure online e `System.Configuration.ConfigurationManager` selecione **Instalar** para instalar o Gestor de Configurações.

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Tem de fazer referência aos seguintes três pacotes do seu projeto para completar este tutorial:

- [Microsoft.Azure.Storage.Biblioteca cliente comum para .NET](https://www.nuget.org/packages/microsoft.azure.storage.common/): Este pacote proporciona acesso programático aos recursos de dados na sua conta de armazenamento.
- [Biblioteca de clientes microsoft Azure Queue Storage para .NET](https://www.nuget.org/packages/microsoft.azure.storage.queue/): Esta biblioteca de clientes permite trabalhar com o Azure Queue Storage para armazenar mensagens que possam ser acedidas por um cliente.
- [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação estiver a ser executada.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos:

1. Clique com o botão direito no seu projeto no **Solution Explorer** e escolha **Gerir pacotes NuGet**.
1. **Selecione Procurar**
1. Procure online `Microsoft.Azure.Storage.Queue` e selecione **Instalar** para instalar a biblioteca do cliente do Azure Storage e as suas dependências. Isto também instalará a biblioteca Microsoft.Azure.Storage.Common, que é uma dependência da biblioteca de filas.
1. Procure online `Microsoft.Azure.ConfigurationManager` e selecione **Instalar** para instalar o Gestor de Configurações Azure.

---

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Tem duas opções de ambiente para executar os exemplos neste guia:

- Pode executar o código numa conta de armazenamento do Azure na nuvem.
- Pode executar o seu código contra o emulador de armazenamento Azurite. Azurite é um ambiente local que imita uma conta de Armazenamento Azure na nuvem. O Azurite é uma opção gratuita para testar e depurar o seu código enquanto a sua aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para obter mais informações, [consulte o emulador Azurite para o desenvolvimento e testes locais de armazenamento da Azure.](../common/storage-use-azurite.md)

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por direcionar uma conta de Armazenamento Azure na nuvem, os custos para a realização deste tutorial serão insignificantes.

## <a name="get-your-storage-connection-string"></a>Obtenha o seu string de conexão de armazenamento

As bibliotecas de clientes do Azure Storage para suporte .NET utilizando uma cadeia de ligação de armazenamento para configurar pontos finais e credenciais para aceder a serviços de armazenamento. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../common/storage-account-keys-manage.md)

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

O código de exemplo tem de autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça à aplicação as credenciais da conta de armazenamento sob a forma de uma cadeia de ligação. Para ver as credenciais da conta de armazenamento:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e clique no botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Para obter mais informações sobre as cordas de ligação, consulte [configurar uma cadeia de ligação ao Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.

A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração. Para configurar a cadeia de ligação, abra o ficheiro `app.config` a partir do Explorador de Soluções no Visual Studio. Adicione o conteúdo do `<appSettings>` elemento mostrado aqui. `connection-string`Substitua-o pelo valor copiado da sua conta de armazenamento no portal:

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Criar o cliente de armazenamento de fila

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

A [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) aula permite-lhe recuperar as filas armazenadas no Armazenamento da Fila. Eis uma forma de criar o cliente do serviço:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

A [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) aula permite-lhe recuperar as filas armazenadas no Armazenamento da Fila. Eis uma forma de criar o cliente do serviço:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Agora está pronto para escrever código que lê dados e escreve dados para o Armazenamento da Fila.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para inserir uma mensagem numa fila existente, ligue para o [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) método. Uma mensagem pode ser uma cadeia (em formato UTF-8) ou uma matriz de byte. O seguinte código cria uma fila (se não existir) e insere uma mensagem:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Para inserir uma mensagem numa fila existente, primeiro crie uma nova [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Em seguida, chame o [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) método. Um `CloudQueueMessage` pode ser criado a partir de uma cadeia (em formato UTF-8) ou de um conjunto de byte. Aqui está o código que cria uma fila (se não existir) e insere a mensagem `Hello, World` : Para inserir uma mensagem numa fila existente, primeiro crie uma nova [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Em seguida, chame o [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) método. Um `CloudQueueMessage` pode ser criado a partir de uma cadeia (em formato UTF-8) ou de um conjunto de byte. Aqui está o código que cria uma fila (se não existir) e insere a `Hello, World` mensagem:

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Pode espreitar as mensagens na fila sem as retirar da fila, chamando o [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) método. Se não passar um valor para o `maxMessages` parâmetro, o padrão é espreitar uma mensagem.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Pode espreitar a mensagem na frente de uma fila sem a retirar da fila, chamando o [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) método.

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

Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O seguinte código atualiza a mensagem de filas com novos conteúdos e expande o tempo limite de visibilidade em 60 segundos. Isto guarda o estado do trabalho associado à mensagem e atribui ao cliente outro minuto para continuar a trabalhar na mensagem. Pode utilizar esta técnica para rastrear fluxos de trabalho em vários passos em mensagens de fila, sem ter de recomeçar desde o início se um passo de processamento falhar devido a falha de hardware ou software. Normalmente, também manteria uma contagem de tentativas e se a mensagem for repetida mais do que *n* vezes, deveria eliminá-la. Esta ação protege contra uma mensagem que aciona um erro da aplicação sempre que é processada.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

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

## <a name="dequeue-the-next-message"></a>Dequeue a próxima mensagem

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Dequeue uma mensagem de uma fila em dois passos. Quando [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) ligar, recebe a próxima mensagem numa fila. Uma mensagem devolvida `ReceiveMessages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve ligar [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

O seu código desmente uma mensagem de uma fila em dois passos. Quando [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) ligar, recebe a próxima mensagem numa fila. Uma mensagem devolvida `GetMessage` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para terminar de remover a mensagem da fila, também deve ligar [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

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

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Use o padrão Async-Await com APIs comuns de armazenamento de fila

Este exemplo mostra como usar o padrão Async-Await com APIs comuns de armazenamento de fila. A amostra chama a versão assíncronea de cada um dos métodos dado, como indicado pelo `Async` sufixo de cada método. Quando um método de assínc é usado, o padrão Async-Await suspende a execução local até que a chamada termine. Este comportamento permite que o thread atual efetue outro trabalho, o que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta global da sua aplicação. Para obter mais detalhes sobre a utilização do padrão Async-Await em .NET, consulte [Async e Await (C# e Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

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

## <a name="use-additional-options-for-dequeuing-messages"></a>Use opções adicionais para desescavar mensagens

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila. Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

O seguinte exemplo de código utiliza o [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) método para obter 20 mensagens numa única chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que os cinco minutos começam para todas as mensagens ao mesmo tempo, pelo que após cinco minutos passados desde a chamada para `ReceiveMessages` , quaisquer mensagens que não tenham sido apagadas tornar-se-ão visíveis novamente.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

O seguinte exemplo de código utiliza o [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) método para obter 20 mensagens numa única chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que os cinco minutos começam para todas as mensagens ao mesmo tempo, pelo que após cinco minutos passados desde a chamada para `GetMessages` , quaisquer mensagens que não tenham sido apagadas tornar-se-ão visíveis novamente.

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Pode obter uma estimativa do número de mensagens numa fila. O [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) método devolve propriedades de fila, incluindo a contagem de mensagens. A [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) propriedade contém o número aproximado de mensagens na fila. Este número não é inferior ao número real de mensagens na fila, mas pode ser maior.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Pode obter uma estimativa do número de mensagens numa fila. O [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) método devolve os atributos da fila, incluindo a contagem de mensagens. A [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) propriedade devolve o último valor recuperado pelo `FetchAttributes` método, sem chamar Armazenamento de Fila.

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

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para eliminar uma fila e todas as mensagens contidas, ligue para o [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) método no objeto da fila.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Para eliminar uma fila e todas as mensagens contidas, ligue para o [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) método no objeto da fila.

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

Agora que aprendeu o básico do Armazenamento de Fila, siga estes links para aprender sobre tarefas de armazenamento mais complexas.

- Consulte a documentação de referência de armazenamento de fila para obter detalhes completos sobre as APIs disponíveis:
  - [Biblioteca de clientes de armazenamento Azure para referência .NET](/dotnet/api/overview/azure/storage)
  - [Referência AZure Storage REST API](/rest/api/storageservices/)
- Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
  - [Começa com o Azure Table Storage utilizando .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) para armazenar dados estruturados.
  - [Começa com o Azure Blob Storage utilizando .NET](../blobs/storage-quickstart-blobs-dotnet.md) para armazenar dados não estruturados.
  - [Ligar à Base de Dados SQL com o .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) para armazenar dados relacionais.
- Aprenda a simplificar o código que escreve para trabalhar com o Azure Storage utilizando o [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

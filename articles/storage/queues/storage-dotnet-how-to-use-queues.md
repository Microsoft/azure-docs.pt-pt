---
title: Começar com o armazenamento de fila Azure usando .NET - Armazenamento Azure
description: As Filas do Azure fornecem um serviço de mensagens fiável e assíncrono entre componentes da aplicação. O serviço de mensagens na nuvem permite que os componentes da sua aplicação sejam dimensionados de forma independente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 6dc94cca66370daa307a21daa6c96a778baed08c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017635"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao Armazenamento de filas do Azure através do .NET

## <a name="overview"></a>Descrição geral

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Ao conceber aplicações para escala, os componentes da aplicação são muitas vezes dissociados para que possam escalar de forma independente. O armazenamento de fila fornece mensagens assíncronas entre componentes de aplicações, quer estejam a funcionar na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Acerca deste tutorial

Este tutorial demonstra como escrever código .NET para alguns cenários comuns utilizando o Armazenamento de filas do Azure. Os cenários abrangidos incluem criar e eliminar filas e adicionar, ler e eliminar mensagens de filas.

**Tempo estimado para concluir:** 45 minutos

### <a name="prerequisites"></a>Pré-requisitos

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Biblioteca comum de clientes azure Storage para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
- [Biblioteca de clientes azure Storage Queue para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
- [Gestor de Configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
- Uma [conta de armazenamento Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Em seguida, configure o ambiente de desenvolvimento no Visual Studio, para estar pronto para experimentar os exemplos de código deste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicação de consola do Windows

No Visual Studio, crie uma nova aplicação de consola do Windows. Os seguintes passos mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Os passos são semelhantes aos de outras versões do Visual Studio.

1. Selecione **File**  >  **Arquivar Novo**  >  **Projeto**
2. Selecione **Platform**  >  **Janelas de** plataforma
3. Selecione **App de Consola (.QUADRO NET)**
4. Selecione **Next**
5. No campo de **nome do Projeto,** insira um nome para a sua aplicação
6. Selecione **Criar**

Todos os exemplos de código neste tutorial podem ser adicionados ao método **principal** do ficheiro **Program.cs** da sua aplicação de consola.

Pode utilizar as bibliotecas de clientes do Azure Storage em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

### <a name="use-nuget-to-install-the-required-packages"></a>Utilizar o NuGet para instalar os pacotes necessários

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Você precisa fazer referência aos seguintes quatro pacotes do seu projeto para completar este tutorial:

- [Biblioteca Azure Core para .NET](https://www.nuget.org/packages/Azure.Core/): Este pacote fornece primitivos, abstrações e ajudantes partilhados para bibliotecas modernas de clientes .NET Azure SDK.
- [Biblioteca De Clientes Comuns de Armazenamento Azure para .NET](https://www.nuget.org/packages/Azure.Storage.Common/): Este pacote fornece infraestruturas partilhadas pelas outras bibliotecas de clientes do Azure Storage.
- Biblioteca de fila de [armazenamento Azure para .NET](https://www.nuget.org/packages/Azure.Storage.Queues/): Este pacote permite trabalhar com o serviço de fila de armazenamento Azure para armazenar mensagens que possam ser acedidas por um cliente.
- Biblioteca de Gestor de [Configuração para .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Este pacote fornece acesso a ficheiros de configuração para aplicações de clientes.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos.

1. Clique no seu projeto no **Solution Explorer**e escolha **Gerir pacotes NuGet**.
1. Selecione **Browse**
1. Procure online "Azure.Storage.Queues", e selecione **Instalar** para instalar a biblioteca do cliente de Armazenamento e as suas dependências. Isto também irá instalar as bibliotecas Azure.Storage.Common e Azure.Core, que são dependências da biblioteca de filas.
1. Procure online "System.Configuration.ConfigurationManager", e selecione **Instalar** para instalar o Gestor de Configuração.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Você precisa fazer referência aos seguintes três pacotes do seu projeto para completar este tutorial:

- [Microsoft Azure Storage Common Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Este pacote fornece acesso programático aos recursos de dados na sua conta de armazenamento.
- [Microsoft Azure Storage Queue Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Esta biblioteca de clientes permite trabalhar com o serviço de fila de armazenamento Microsoft Azure para armazenar mensagens que possam ser acedidas por um cliente.
- [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de ligação num ficheiro de configuração, independentemente de onde a sua aplicação estiver a ser executada.

Pode utilizar o NuGet para obter estes pacotes. Siga estes passos.

1. Clique no seu projeto no **Solution Explorer**e escolha **Gerir pacotes NuGet**.
1. Selecione **Browse**
1. Procure online "Microsoft.Azure.Storage.Queue", e selecione **Instalar** para instalar a biblioteca do cliente storage e as suas dependências. Isto também irá instalar a biblioteca Microsoft.Azure.Storage.Common, que é uma dependência da biblioteca de filas.
1. Procure online "Microsoft.Azure.ConfigurationManager", e selecione **Instalar** para instalar o Gestor de Configuração Do Azure.

---

> [!NOTE]
> Os pacotes de bibliotecas de clientes de armazenamento também estão incluídos no [SDK Azure para .NET](https://azure.microsoft.com/downloads/). No entanto, recomendamos que também instale as bibliotecas de clientes do Armazenamento da NuGet para garantir que tem sempre as versões mais recentes.
>
> As dependências oDataLib nas bibliotecas de clientes de Armazenamento para .NET são resolvidas pelos pacotes ODataLib disponíveis no NuGet, e não nos Serviços de Dados da WCF. As bibliotecas ODataLib podem ser transferidas diretamente ou referenciadas pelo seu projeto de código através do NuGet. Os pacotes ODataLib específicos utilizados pelas bibliotecas de clientes de Armazenamento são [OData,](https://nuget.org/packages/Microsoft.Data.OData/) [Edm](https://nuget.org/packages/Microsoft.Data.Edm/)e [Spatial.](https://nuget.org/packages/System.Spatial/) Embora estas bibliotecas sejam utilizadas pelas classes de armazenamento da Mesa Azure, são necessárias dependências para programação com as bibliotecas de clientes do Armazenamento.

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Tem duas opções de ambiente para executar os exemplos neste guia:

- Pode executar o código numa conta de armazenamento do Azure na nuvem.
- Pode executar o seu código contra o emulador de armazenamento Azurite. Azurite é um ambiente local que imita uma conta de Armazenamento Azure na nuvem. A Azurite é uma opção gratuita para testar e depurar o seu código enquanto a sua aplicação está em desenvolvimento. O emulador utiliza uma conta e chave bem conhecidas. Para mais informações, consulte [Utilize o emulador Azurite para desenvolvimento e teste de armazenamento azure local.](../common/storage-use-azurite.md)

> [!NOTE]
> Pode filtrar o emulador de armazenamento para evitar incorrer em custos associados ao Storage do Azure. No entanto, se optar por filtrar uma conta de armazenamento do Azure na nuvem, os custos para efetuar este tutorial serão negligenciável.

## <a name="get-your-storage-connection-string"></a>Obtenha a sua cadeia de ligação de armazenamento

As bibliotecas de clientes do Azure Storage para suporte .NET usando uma cadeia de conexão de armazenamento para configurar pontos finais e credenciais para aceder a serviços de armazenamento. Para mais informações, consulte Gerir as chaves de [acesso à conta](../common/storage-account-keys-manage.md)de armazenamento .

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

O código de exemplo tem de autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça à aplicação as credenciais da conta de armazenamento sob a forma de uma cadeia de ligação. Para ver as credenciais da conta de armazenamento:

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Para obter mais informações sobre as cordas de ligação, consulte Configurar uma cadeia de [ligação ao Armazenamento Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Tenha sempre o cuidado de proteger a chave da conta de armazenamento. Evite distribui-la a outros utilizadores, pré-programá-la ou guardá-la num ficheiro de texto simples que seja acessível a outras pessoas. Regenere a sua chave através do portal do Azure se considerar que poderá ter sido comprometida.

A melhor forma para manter a cadeia de ligação de armazenamento é num ficheiro de configuração. Para configurar a sua cadeia de ligação, abra o ficheiro *app.config* do Solution Explorer no Visual Studio. Adicione o conteúdo do elemento `\<appSettings\>` mostrado abaixo. Substitua a *linha de ligação* pelo valor copiado da sua conta de armazenamento no portal:

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

Para direcionar o emulador de armazenamento Azurite, pode utilizar um atalho que mapeie para o conhecido nome e chave da conta. Nesse caso, a definição da cadeia de ligação é:

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

A classe [QueueClient](/dotnet/api/azure.storage.queues.queueclient) permite-lhe recuperar filas armazenadas no armazenamento da fila. Eis uma forma de criar o cliente do serviço:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

A classe [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy) permite-lhe obter filas armazenadas no Armazenamento de filas. Eis uma forma de criar o cliente do serviço:

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

Para inserir uma mensagem numa fila existente, ligue para o método [SendMessage.](/dotnet/api/azure.storage.queues.queueclient.sendmessage) Uma mensagem pode ser um `string` (em formato UTF-8) ou uma `byte` matriz. O seguinte código cria uma fila (se não existir) e insere uma mensagem:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para introduzir uma mensagem numa fila existente, primeiro crie um novo [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy). Em seguida, chame o método [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy). A `CloudQueueMessage` pode ser criado a partir de um `string` (em formato UTF-8) ou de uma `byte` matriz. Aqui está o código que cria uma fila (se não existe) e insere a mensagem "Olá, Mundo":

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

Pode espreitar as mensagens na fila sem as retirar da fila, ligando para o método [PeekMessages.](/dotnet/api/azure.storage.queues.queueclient.peekmessages) Se não passar um valor para o parâmetro *maxMessages,* o padrão é espreitar uma mensagem.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy).

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

Desfilar uma mensagem de uma fila em dois passos. Quando ligar para [receber Mensagens,](/dotnet/api/azure.storage.queues.queueclient.receivemessages)recebe a próxima mensagem numa fila. Uma mensagem devolvida `ReceiveMessages` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage). Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

O código remove uma mensagem da fila em dois passos. Quando chamar [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy), obterá a seguinte mensagem numa fila. Uma mensagem devolvida `GetMessage` torna-se invisível a qualquer outra mensagem de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy). Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seu código liga `DeleteMessage` logo após a mensagem ter sido processada.

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

O exemplo de código que se segue utiliza o método [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que os 5 minutos começam para todas as mensagens ao mesmo tempo, pelo que após 5 minutos se passaram desde a chamada para `ReceiveMessages` , quaisquer mensagens que não tenham sido apagadas voltarão a ser visíveis.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

O seguinte código de exemplo utiliza o método [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy) para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem usando um `foreach` loop. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Note que os 5 minutos começam para todas as mensagens ao mesmo tempo, pelo que após 5 minutos se passaram desde a chamada para `GetMessages` , quaisquer mensagens que não tenham sido apagadas voltarão a ser visíveis.

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

Pode obter uma estimativa do número de mensagens numa fila. O método [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) pede ao serviço de fila para recuperar as propriedades da fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) contém o número aproximado de mensagens na fila. Este número não é inferior ao número real de mensagens na fila, mas pode ser maior.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pode obter uma estimativa do número de mensagens numa fila. O método [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy) pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy) devolve o último valor recuperado pelo `FetchAttributes` método, sem ligar para o serviço de fila.

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

Para eliminar uma fila e todas as mensagens nela contidas, chame o método [Eliminar](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy) no objeto de fila.

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

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu as noções básicas do Armazenamento de filas, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

- Ver a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  - [Referência da Biblioteca de Clientes do Storage para o .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [Referência da API REST](https://msdn.microsoft.com/library/azure/dd179355)
- Aprenda a simplificar o código que escreve para trabalhar com o Azure Storage utilizando o [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
- Ver mais guias de funcionalidades para saber mais sobre as opções adicionais para armazenar dados no Azure.
  - [Introdução ao Table Storage do Azure utilizando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) para armazenar dados estruturados.
  - [Introdução ao Blob Storage do Azure utilizando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
  - [Ligar à Base de Dados SQL com o .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2

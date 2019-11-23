---
title: Introdução ao armazenamento de filas usando o Visual Studio (projetos de trabalho Web)
description: Como começar a usar o armazenamento de filas do Azure em um projeto do WebJob depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ffba203bafaf3837cd2d7fc1a6fd962a6926b186
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298745"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (projetos de trabalho Web)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento de filas do Azure em um projeto do Azure WebJob do Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio. Quando você adiciona uma conta de armazenamento a um projeto do WebJob usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio, os pacotes do NuGet do armazenamento do Azure apropriados são instalados, as referências apropriadas do .NET são adicionadas ao projeto e as cadeias de conexão para a conta de armazenamento são atualizadas no arquivo app. config.  

Este artigo fornece C# exemplos de código que mostram como usar o SDK do Azure WebJobs versão 1. x com o serviço de armazenamento de filas do Azure.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. Consulte [introdução ao armazenamento de filas do Azure usando o .net](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações. Para obter mais informações sobre ASP.NET, consulte [ASP.net](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Como disparar uma função quando uma mensagem da fila é recebida
Para gravar uma função que o SDK de trabalhos Web chama quando uma mensagem de fila é recebida, use o atributo **QueueTrigger** . O construtor de atributo usa um parâmetro de cadeia de caracteres que especifica o nome da fila para sondagem. Para ver como definir o nome da fila dinamicamente, confira [como definir as opções de configuração](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens da fila de cadeias de caracteres
No exemplo a seguir, a fila contém uma mensagem de cadeia de caracteres; portanto, **QueueTrigger** é aplicado a um parâmetro de cadeia de caracteres chamado **logMessage** , que contém o conteúdo da mensagem da fila. A função [grava uma mensagem de log no painel](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Além da **cadeia de caracteres**, o parâmetro pode ser uma matriz de bytes, um objeto **CLOUDQUEUEMESSAGE** ou um poco que você define.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Mensagens [de fila poco (objeto antigo CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
No exemplo a seguir, a mensagem da fila contém JSON para um objeto **BlobInformation** que inclui uma propriedade **blobname** . O SDK desserializa automaticamente o objeto.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

O SDK usa o [pacote NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se você criar mensagens de fila em um programa que não usa o SDK de trabalhos Web, poderá escrever um código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funções assíncronas
A função Async a seguir [grava um log no painel](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

As funções assíncronas podem usar um [token de cancelamento](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), conforme mostrado no exemplo a seguir, que copia um blob. (Para obter uma explicação do espaço reservado **queueTrigger** , consulte a seção [BLOBs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) .)

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Tipos com os quais o atributo QueueTrigger funciona
Você pode usar **QueueTrigger** com os seguintes tipos:

* **string**
* Um tipo POCO serializado como JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo de sondagem
O SDK implementa um algoritmo de retirada exponencial aleatória para reduzir o efeito de sondagem de fila ociosa nos custos de transação de armazenamento.  Quando uma mensagem é encontrada, o SDK aguarda dois segundos e, em seguida, verifica se há outra mensagem; quando nenhuma mensagem for encontrada, ela aguardará cerca de quatro segundos antes de tentar novamente. Após as tentativas subsequentes falharem em obter uma mensagem da fila, o tempo de espera continuará aumentando até atingir o tempo de espera máximo, cujo padrão é um minuto. [O tempo de espera máximo é configurável](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Múltiplas instâncias
Se seu aplicativo Web for executado em várias instâncias, um webjobs contínuo será executado em cada computador e cada computador aguardará os gatilhos e tentará executar funções. Em alguns cenários, isso pode levar a algumas funções processando os mesmos dados duas vezes, portanto, as funções devem ser idempotentes (escritas de forma que chamá-las repetidamente com os mesmos dados de entrada não produz resultados duplicados).  

## <a name="parallel-execution"></a>Execução paralela
Se você tiver várias funções escutando em filas diferentes, o SDK as chamará em paralelo quando as mensagens forem recebidas simultaneamente.

O mesmo é verdadeiro quando várias mensagens são recebidas para uma única fila. Por padrão, o SDK Obtém um lote de 16 mensagens de fila por vez e executa a função que as processa em paralelo. [O tamanho do lote é configurável](#how-to-set-configuration-options). Quando o número que está sendo processado chega à metade do tamanho do lote, o SDK Obtém outro lote e começa a processar essas mensagens. Portanto, o número máximo de mensagens simultâneas sendo processadas por função é um e meia o tamanho do lote. Esse limite se aplica separadamente a cada função que tem um atributo **QueueTrigger** . Se você não quiser a execução paralela para mensagens recebidas em uma fila, defina o tamanho do lote como 1.

## <a name="get-queue-or-queue-message-metadata"></a>Obter metadados de mensagem da fila ou fila
Você pode obter as seguintes propriedades de mensagem adicionando parâmetros à assinatura do método:

* Expiração de **DateTimeOffset**
* **DateTimeOffset** inserttime
* NextVisibleTime de **DateTimeOffset**
* **cadeia de caracteres** queueTrigger (contém texto da mensagem)
* ID da **cadeia de caracteres**
* **cadeia de caracteres** popReceipt
* **int** dequeueCount

Se você quiser trabalhar diretamente com a API de armazenamento do Azure, também poderá adicionar um parâmetro **CloudStorageAccount** .

O exemplo a seguir grava todos esses metadados em um log de aplicativo de informações. No exemplo, logMessage e queueTrigger contêm o conteúdo da mensagem da fila.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Aqui está um log de exemplo escrito pelo código de exemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Desligamento normal
Uma função que é executada em um WebJob contínuo pode aceitar um parâmetro **CancellationToken** , que permite ao sistema operacional notificar a função quando o WebJob está prestes a ser encerrado. Você pode usar essa notificação para garantir que a função não seja encerrada inesperadamente de forma a deixar dados em um estado inconsistente.

O exemplo a seguir mostra como verificar o encerramento de trabalho Web iminente em uma função.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Observação:** O painel pode não mostrar corretamente o status e a saída das funções que foram desligadas.

Para obter mais informações, consulte [desligamento normal de trabalhos](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)Web.   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Como criar uma mensagem de fila durante o processamento de uma mensagem da fila
Para gravar uma função que cria uma nova mensagem da fila, use o atributo **Queue** . Como o **QueueTrigger**, você passa o nome da fila como uma cadeia de caracteres ou pode [definir o nome da fila dinamicamente](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens da fila de cadeias de caracteres
O exemplo de código não assíncrono a seguir cria uma nova mensagem de fila na fila chamada "outputqueue" com o mesmo conteúdo da mensagem de fila recebida na fila denominada "inputqueue". (Para funções assíncronas, use **IAsyncCollector\<t >** conforme mostrado posteriormente nesta seção.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Mensagens [de fila poco (objeto antigo CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para criar uma mensagem de fila que contenha um POCO em vez de uma cadeia de caracteres, passe o tipo POCO como um parâmetro de saída para o construtor de atributo **Queue** .

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

O SDK serializa automaticamente o objeto para JSON. Uma mensagem da fila sempre é criada, mesmo se o objeto for nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou em funções assíncronas
Para criar várias mensagens, torne o tipo de parâmetro para a fila de saída **ICollector\<t >** ou **IAsyncCollector\<t >** , conforme mostrado no exemplo a seguir.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Cada mensagem da fila é criada imediatamente quando o método **Add** é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos com os quais o atributo Queue funciona
Você pode usar o atributo **Queue** nos seguintes tipos de parâmetro:

* **cadeia de caracteres de saída** (cria a mensagem da fila se o valor do parâmetro não for nulo quando a função terminar)
* **out byte []** (funciona como uma **cadeia de caracteres**)
* **out CloudQueueMessage** (funciona como uma **cadeia de caracteres**)
* **out poco** (um tipo serializável, cria uma mensagem com um objeto nulo se o parâmetro for nulo quando a função terminar)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para criar mensagens manualmente usando a API de armazenamento do Azure diretamente)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Usar atributos do SDK de trabalhos Web no corpo de uma função
Se você precisar fazer algum trabalho em sua função antes de usar um atributo do SDK de trabalhos Web, como **fila**, **blob**ou **tabela**, você poderá usar a interface **IBinder** .

O exemplo a seguir usa uma mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

A interface **IBinder** também pode ser usada com os atributos **Table** e **blob** .

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Como ler e gravar BLOBs e tabelas durante o processamento de uma mensagem da fila
Os atributos **blob** e **Table** permitem que você leia e grave BLOBs e tabelas. Os exemplos nesta seção se aplicam a BLOBs. Para obter exemplos de código que mostram como disparar processos quando BLOBs são criados ou atualizados, consulte [como usar o armazenamento de BLOBs do Azure com o SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki)Web.
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens da fila de cadeia de caracteres que disparam operações de BLOB
Para uma mensagem de fila que contém uma cadeia de caracteres, **queueTrigger** é um espaço reservado que você pode usar no parâmetro **BlobPath** do atributo **blob** que contém o conteúdo da mensagem.

O exemplo a seguir usa objetos **Stream** para ler e gravar BLOBs. A mensagem da fila é o nome de um blob localizado no contêiner textblobs. Uma cópia do blob com "-New" acrescentado ao nome é criada no mesmo contêiner.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O construtor de atributo de **blob** usa um parâmetro **blobPath** que especifica o contêiner e o nome do blob. Para obter mais informações sobre esse espaço reservado, consulte [como usar o armazenamento de BLOBs do Azure com o SDK de trabalhos](https://github.com/Azure/azure-webjobs-sdk/wiki)Web.

Quando o atributo decora um objeto de **fluxo** , outro parâmetro de construtor Especifica o modo **FileAccess** como leitura, gravação ou leitura/gravação.

O exemplo a seguir usa um objeto **CloudBlockBlob** para excluir um blob. A mensagem da fila é o nome do blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplain_old_clr_object-queue-messages"></a>Mensagens [de fila poco (objeto antigo CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para um POCO armazenado como JSON na mensagem da fila, você pode usar espaços reservados que renomeam as propriedades do objeto no parâmetro **blobPath** do atributo **Queue** . Você também pode usar nomes de propriedade de metadados de fila como espaços reservados. Consulte [obter fila ou metadados de mensagem de fila](#get-queue-or-queue-message-metadata).

O exemplo a seguir copia um blob para um novo BLOB com uma extensão diferente. A mensagem da fila é um objeto **BlobInformation** que inclui as propriedades **blobname** e **BlobNameWithoutExtension** . Os nomes de propriedade são usados como espaços reservados no caminho de BLOB para os atributos de **blob** .

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O SDK usa o [pacote NuGet Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se você criar mensagens de fila em um programa que não usa o SDK de trabalhos Web, poderá escrever um código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Se você precisar fazer algum trabalho em sua função antes de associar um blob a um objeto, poderá usar o atributo no corpo da função, conforme mostrado em [usar atributos do SDK de trabalhos Web no corpo de uma função](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipos com os quais você pode usar o atributo blob
O atributo **blob** pode ser usado com os seguintes tipos:

* **Fluxo** (leitura ou gravação, especificado usando o parâmetro de Construtor FileAccess)
* **TextReader**
* **TextWriter**
* **cadeia de caracteres** (leitura)
* **cadeia de caracteres de saída** (gravação; cria um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função retornar)
* POCO (leitura)
* out POCO (gravação; sempre cria um blob, cria como objeto nulo se o parâmetro POCO é nulo quando a função retorna)
* **CloudBlobStream** (write)
* **ICloudBlob** (leitura ou gravação)
* **CloudBlockBlob** (leitura ou gravação)
* **CloudPageBlob** (leitura ou gravação)

## <a name="how-to-handle-poison-messages"></a>Como lidar com mensagens suspeitas
As mensagens cujo conteúdo faz com que uma função falhe são chamadas de *mensagens suspeitas*. Quando a função falha, a mensagem da fila não é excluída e, eventualmente, é retirada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após um número limitado de iterações ou você pode fazer isso manualmente.

### <a name="automatic-poison-message-handling"></a>Manipulação automática de mensagens suspeitas
O SDK chamará uma função até 5 vezes para processar uma mensagem da fila. Se a quinta tentativa falhar, a mensagem será movida para uma fila suspeita. Você pode ver como configurar o número máximo de repetições em [como definir opções de configuração](#how-to-set-configuration-options).

A fila de suspeitas é denominada *{originalqueuename}* -suspeita. Você pode escrever uma função para processar mensagens da fila de suspeitas, registrando-as ou enviando uma notificação de que a atenção manual é necessária.

No exemplo a seguir, a função **CopyBlob** falhará quando uma mensagem da fila contiver o nome de um blob que não existe. Quando isso acontece, a mensagem é movida da fila copyblobqueue para a fila copyblobqueue-suspeita. Em seguida, o **ProcessPoisonMessage** registra a mensagem suspeita.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

A ilustração a seguir mostra a saída do console dessas funções quando uma mensagem suspeita é processada.

![Saída do console para tratamento de mensagens suspeitas](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manipulação manual de mensagens suspeitas
Você pode obter o número de vezes que uma mensagem foi selecionada para processamento, adicionando um parâmetro **int** chamado **dequeueCount** à sua função. Em seguida, você pode verificar a contagem de remoção da fila no código de função e executar sua própria manipulação de mensagens suspeitas quando o número exceder um limite, conforme mostrado no exemplo a seguir.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Como definir opções de configuração
Você pode usar o tipo **JobHostConfiguration** para definir as seguintes opções de configuração:

* Defina as cadeias de conexão do SDK no código.
* Defina as configurações de **QueueTrigger** como a contagem máxima de remoção da fila.
* Obter nomes de fila da configuração.

### <a name="set-sdk-connection-strings-in-code"></a>Definir cadeias de conexão do SDK no código
Definir as cadeias de conexão do SDK no código permite que você use seus próprios nomes de cadeia de conexão em arquivos de configuração ou variáveis de ambiente, conforme mostrado no exemplo a seguir.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Definir configurações de QueueTrigger
Você pode definir as seguintes configurações que se aplicam ao processamento de mensagens da fila:

* O número máximo de mensagens da fila que são selecionadas simultaneamente para serem executadas em paralelo (o padrão é 16).
* O número máximo de repetições antes que uma mensagem da fila seja enviada a uma fila suspeita (o padrão é 5).
* O tempo de espera máximo antes de sondar novamente quando uma fila está vazia (o padrão é 1 minuto).

O exemplo a seguir mostra como definir essas configurações:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Definir valores para parâmetros do construtor do SDK de trabalhos Web no código
Às vezes, você deseja especificar um nome de fila, um nome de BLOB ou um contêiner ou um nome de tabela no código, em vez de embuti-lo em código. Por exemplo, talvez você queira especificar o nome da fila para **QueueTrigger** em um arquivo de configuração ou variável de ambiente.

Você pode fazer isso passando um objeto **NameResolver** para o tipo **JobHostConfiguration** . Você inclui espaços reservados especiais circundados por porcentagem (%) os sinais dos parâmetros do construtor de atributo do SDK de trabalhos Web e o código **NameResolver** especificam os valores reais a serem usados no lugar desses espaços reservados.

Por exemplo, suponha que você queira usar uma fila chamada logqueuetest no ambiente de teste e outra chamada logqueueprod na produção. Em vez de um nome de fila embutido em código, você deseja especificar o nome de uma entrada na coleção **appSettings** que teria o nome real da fila. Se a chave **appSettings** for logqueue, sua função poderá ser semelhante ao exemplo a seguir.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A classe **NameResolver** poderia então obter o nome da fila de **appSettings** , conforme mostrado no exemplo a seguir:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Você passa a classe **NameResolver** para o objeto **JobHost** , conforme mostrado no exemplo a seguir.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Observação:** Os nomes de fila, tabela e blob são resolvidos sempre que uma função é chamada, mas os nomes de contêiner de blob são resolvidos somente quando o aplicativo é iniciado. Você não pode alterar o nome do contêiner de blob enquanto o trabalho está em execução.

## <a name="how-to-trigger-a-function-manually"></a>Como disparar uma função manualmente
Para disparar uma função manualmente, use o método **Call** ou **CallAsync** no objeto **JobHost** e o atributo **NoAutomaticTrigger** na função, conforme mostrado no exemplo a seguir.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Como gravar logs
O painel mostra os logs em dois locais: a página do WebJob e a página de uma invocação de trabalho Web específica.

![Logs na página WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Logs na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A saída dos métodos de console que você chama em uma função ou no método **Main ()** aparece na página de painel para o WebJob, não na página de uma invocação de método específica. A saída do objeto TextWriter que você obtém de um parâmetro em sua assinatura de método aparece na página painel para uma invocação de método.

A saída do console não pode ser vinculada a uma invocação de método específica porque o console é de thread único, enquanto muitas funções de trabalho podem estar em execução ao mesmo tempo. É por isso que o SDK fornece cada invocação de função com seu próprio objeto de gravador de log exclusivo.

Para gravar [logs de rastreamento de aplicativo](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), use **console. out** (cria logs marcados como informações) e **console. Error** (cria logs marcados como erro). Uma alternativa é usar [trace ou tracename](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que fornece níveis detalhados, de aviso e críticos, além de informações e erros. Os logs de rastreamento de aplicativo aparecem nos arquivos de log do aplicativo Web, tabelas do Azure ou BLOBs do Azure, dependendo de como você configura seu aplicativo Web do Azure. Como é verdadeiro para toda a saída do console, os logs de aplicativo 100 mais recentes também aparecem na página painel do WebJob, não na página de uma invocação de função.

A saída do console aparecerá no painel somente se o programa estiver em execução em um WebJob do Azure, não se o programa estiver em execução localmente ou em algum outro ambiente.

Você pode desabilitar o registro em log definindo a cadeia de conexão do painel como NULL. Para obter mais informações, consulte [como definir opções de configuração](#how-to-set-configuration-options).

O exemplo a seguir mostra várias maneiras de gravar logs:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

No painel do SDK de trabalhos Web, a saída do objeto **TextWriter** é exibida quando você acessa a página para uma invocação de função específica e seleciona **alternar saída**:

![Link de invocação](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Logs na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

No painel do SDK de trabalhos Web, as 100 linhas de saída de console mais recentes são mostradas quando você acessa a página do WebJob (não para a invocação de função) e seleciona **alternar saída**.

![Alternar saída](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Em um WebJob contínuo, os logs de aplicativo aparecem no/data/Jobs/Continuous/ *{nomedotrabalhoweb}* /job_log. txt no sistema de arquivos do aplicativo Web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Em um blob do Azure, os logs de aplicativo têm a seguinte aparência: 2014-09-26T21:01:13, informações, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 17, console. Write-Olá, mundo!, 2014-09-26T21:01:13, erro, contosoadsnew, 491e54, 635473620738373502, 0, 17404, 19, console. Error-Olá, mundo!, 2014-09-26T21:01:13, Information, contosoadsnew, 491e54, 635473620738529920, 0, 17404, 17, console. out-Olá mundo!,

E, em uma tabela do Azure, os logs **console. out** e **console. Error** têm a seguinte aparência:

![Log de informações na tabela](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Log de erros na tabela](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com filas do Azure. Para obter mais informações sobre como usar Azure WebJobs e o SDK de trabalhos Web, consulte [Azure WebJobs recursos de documentação](https://go.microsoft.com/fwlink/?linkid=390226).


---
title: Começar com o armazenamento de fila usando o Visual Studio (projetos WebJob)
description: Como começar a usar o armazenamento da Fila Azure num projeto WebJob depois de ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298745"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Começar com o armazenamento de fila Azure e serviços conectados do Estúdio Visual (WebJob Projects)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento da Fila Azure num projeto Visual Studio Azure WebJob depois de ter criado ou referenciado uma conta de armazenamento Azure utilizando a caixa de diálogo Visual Studio **Add Connected Services.** Quando adiciona uma conta de armazenamento a um projeto WebJob utilizando o diálogo Visual Studio **Add Connected Services,** os pacotes De armazenamento Web Estão instalados, as referências .NET apropriadas são adicionadas ao projeto, e as cordas de ligação para a conta de armazenamento são atualizadas no ficheiro App.config.  

Este artigo fornece amostras de código C# que mostram como usar a versão 1.x Do Azure WebJobs SDK com o serviço de armazenamento de fila Azure.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. Consulte [Começar com o Azure Queue Storage utilizando .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para mais informações. Para mais informações sobre ASP.NET, consulte [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Como desencadear uma função quando uma mensagem de fila é recebida
Para escrever uma função que o WebJobs SDK chama quando uma mensagem de fila é recebida, use o atributo **QueueTrigger.** O construtor de atributos pega num parâmetro de corda que especifica o nome da fila para a sondagem. Para ver como definir o nome da fila de forma dinâmica, confira [como definir opções de configuração](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de fila de cordas
No exemplo seguinte, a fila contém uma mensagem de corda, pelo que o **QueueTrigger** é aplicado a um parâmetro de corda chamado **logMessage** que contém o conteúdo da mensagem de fila. A função escreve uma mensagem de [registo para o Dashboard](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Além da **corda,** o parâmetro pode ser uma matriz byte, um objeto **CloudQueueMessage** ou um POCO que define.

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Objeto CLR Simples)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)mensagens de fila
No exemplo seguinte, a mensagem de fila contém JSON para um objeto **BlobInformation** que inclui uma propriedade **BlobName.** O SDK desserializa automaticamente o objeto.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

O SDK utiliza o [pacote Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se criar mensagens de fila num programa que não utilize o WebJobs SDK, pode escrever código como o seguinte exemplo para criar uma mensagem de fila POCO que o SDK pode analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funções de asincronização
A função assinizadora seguinte [escreve um log para o Dashboard](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

As funções de asincronização podem tomar uma ficha de [cancelamento,](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)como mostra o seguinte exemplo que copia uma bolha. (Para obter uma explicação do espaço reservado para a **filaTrigger,** consulte a secção [Blobs.)](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Tipos com os trabalhos do atributo QueueTrigger
Pode utilizar **o QueueTrigger** com os seguintes tipos:

* **string**
* Um tipo POCO serializado como JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo de sondagem
O SDK implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito das sondagens de fila inativa nos custos de transação de armazenamento.  Quando uma mensagem é encontrada, o SDK espera dois segundos e, em seguida, verifica por outra mensagem; quando nenhuma mensagem é encontrada espera cerca de quatro segundos antes de tentar novamente. Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, o que se desfaz para um minuto. O tempo máximo de [espera é configurável.](#how-to-set-configuration-options)

## <a name="multiple-instances"></a>Múltiplas instâncias
Se a sua aplicação web funcionar em várias instâncias, um WebJobs contínuo funciona em cada máquina, e cada máquina aguardará por gatilhos e tentará executar funções. Em alguns cenários, isto pode levar a que algumas funções processem os mesmos dados duas vezes, pelo que as funções devem ser idempotentes (escritas para que chamá-los repetidamente com os mesmos dados de entrada não produza resultados duplicados).  

## <a name="parallel-execution"></a>Execução paralela
Se tiver múltiplas funções a ouvir em diferentes filas, o SDK irá chamá-las paralelamente quando as mensagens forem recebidas simultaneamente.

O mesmo acontece quando várias mensagens são recebidas para uma única fila. Por padrão, o SDK recebe um lote de 16 mensagens de fila de cada vez e executa a função que as processa em paralelo. [O tamanho do lote é configurável](#how-to-set-configuration-options). Quando o número que está a ser processado reduz para metade do tamanho do lote, o SDK recebe outro lote e começa a processar essas mensagens. Portanto, o número máximo de mensagens simultâneas que estão a ser processadas por função é uma vez e meia o tamanho do lote. Este limite aplica-se separadamente a cada função que tem um atributo **QueueTrigger.** Se não quiser uma execução paralela para mensagens recebidas numa fila, detete o tamanho do lote para 1.

## <a name="get-queue-or-queue-message-metadata"></a>Obtenha metadados de fila ou de mensagem de fila
Pode obter as seguintes propriedades da mensagem adicionando parâmetros à assinatura do método:

* **DataTimeOffset** expiraçãoTempo
* Tempo de inserção **DateTimeOffset**
* **DataTimeOffset** nextVisibleTime
* fila **de cordasTrigger** (contém texto de mensagem)
* **cadeia** id
* **string** popReceipt
* **int** dequeueCount

Se quiser trabalhar diretamente com a API de armazenamento Azure, também pode adicionar um parâmetro **CloudStorageAccount.**

O exemplo que se segue escreve todos estes metadados para um registo de aplicação info. No exemplo, tanto o logMessage como a filaTrigger contêm o conteúdo da mensagem de fila.

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

Aqui está um registo de amostra escrito pelo código da amostra:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Encerramento gracioso
Uma função que funciona num WebJob contínuo pode aceitar um parâmetro **CancelamentoToken** que permite ao sistema operativo notificar a função quando o WebJob está prestes a ser terminado. Pode utilizar esta notificação para se certificar de que a função não termina inesperadamente de uma forma que deixe os dados num estado inconsistente.

O exemplo que se segue mostra como verificar se a rescisão iminente do WebJob numa função.

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

**Nota:** O Dashboard pode não mostrar corretamente o estado e a saída das funções que foram desligadas.

Para mais informações, consulte [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Como criar uma mensagem de fila enquanto processa uma mensagem de fila
Para escrever uma função que cria uma nova mensagem de fila, use o atributo da **fila.** Tal como **o QueueTrigger,** passas o nome da fila como uma corda ou podes [definir o nome da fila de forma dinâmica](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de fila de cordas
A seguinte amostra de código não assinizador cria uma nova mensagem de fila na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem de fila recebida na fila denominada "inputqueue". (Para funções de assinos, utilize **o IAsyncCollector\<T>,** conforme mostrado mais tarde nesta secção.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Objeto CLR Simples)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)mensagens de fila
Para criar uma mensagem de fila que contenha um POCO em vez de uma corda, passe o tipo POCO como parâmetro de saída para o construtor de atributos **de fila.**

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

O SDK serializa automaticamente o objeto para a JSON. Uma mensagem de fila é sempre criada, mesmo que o objeto seja nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou em funções de assinos
Para criar várias mensagens, faça o tipo de parâmetro para a fila de saída **\<ICollector T>** ou **IAsyncCollector\<T>, **como mostra o exemplo seguinte.

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

Cada mensagem de fila é criada imediatamente quando o método **Adicionar** é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos com os que o atributo da Fila funciona
Pode utilizar o atributo da **fila** nos seguintes tipos de parâmetros:

* **fora a corda** (cria mensagem de fila se o valor do parâmetro não for nulo quando a função termina)
* **fora byte[]** (funciona como **corda)**
* **out CloudQueueMessage** (funciona como **corda)**
* **fora POCO** (um tipo serializável, cria uma mensagem com um objeto nulo se o parâmetro for nulo quando a função termina)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para criar mensagens manualmente utilizando a API de armazenamento Azure diretamente)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Use webJobs SDK atributos no corpo de uma função
Se precisar de fazer algum trabalho na sua função antes de utilizar um atributo WebJobs SDK, como **Queue,** **Blob,** ou **Table,** pode utilizar a interface **IBinder.**

O exemplo seguinte pega numa mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo numa fila de saída. O nome da fila de saída é definido por código no corpo da função.

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

A interface **IBinder** também pode ser usada com os atributos **Table** e **Blob.**

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Como ler e escrever bolhas e mesas enquanto processa uma mensagem de fila
Os atributos **Blob** e **Table** permitem-lhe ler e escrever blobs e tabelas. As amostras desta secção aplicam-se a bolhas. Para amostras de código que mostrem como desencadear processos quando as bolhas são criadas ou atualizadas, consulte [como utilizar o armazenamento de blob Azure com o WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens de fila de cordas que desencadeiam operações de blob
Para uma mensagem de fila que contém uma corda, **a filaTrigger** é um espaço reservado que pode utilizar no parâmetro **bloba** atributo **blobPath** que contém o conteúdo da mensagem.

O exemplo que se segue utiliza objetos **Stream** para ler e escrever bolhas. A mensagem de fila é o nome de uma bolha localizada no recipiente textblobs. Uma cópia da bolha com "novo" anexado ao nome é criada no mesmo recipiente.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O **Blob** construtor de atributoblob pega num parâmetro **blobPath** que especifica o recipiente e o nome blob. Para mais informações sobre este espaço reservado, consulte [como utilizar o armazenamento de blob Azure com o WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Quando o atributo decora um objeto **Stream,** outro parâmetro de construção especifica o modo **FileAccess** como ler, escrever ou ler/escrever.

O exemplo que se segue utiliza um objeto **CloudBlockBlob** para apagar uma bolha. A mensagem da fila é o nome da bolha.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-object-queue-messages"></a>POCO [(Objeto CLR Simples)](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)mensagens de fila
Para um POCO armazenado como JSON na mensagem de fila, pode utilizar espaços reservados que nomeiem propriedades do objeto no parâmetro **blobPath** do atributo da **fila.** Também pode usar nomes de propriedade de metadados de fila como espaços reservados. Consulte [Obter metadados](#get-queue-or-queue-message-metadata)de fila ou de mensagem de fila .

O exemplo seguinte copia uma bolha para uma nova bolha com uma extensão diferente. A mensagem de fila é um objeto **BlobInformation** que inclui propriedades **BlobName** e **BlobNameWithoutExtension.** Os nomes de propriedade são usados como espaços reservados no caminho blob para os atributos **Blob.**

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O SDK utiliza o [pacote Newtonsoft.Json NuGet](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se criar mensagens de fila num programa que não utilize o WebJobs SDK, pode escrever código como o seguinte exemplo para criar uma mensagem de fila POCO que o SDK pode analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Se precisar de fazer algum trabalho na sua função antes de ligar uma bolha a um objeto, pode utilizar o atributo no corpo da função, como mostra o [Uso WebJobs SDK atribui no corpo de uma função](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipos com os que pode utilizar o atributo Blob
O atributo **Blob** pode ser utilizado com os seguintes tipos:

* **Fluxo** (ler ou escrever, especificado utilizando o parâmetro do construtor fileAccess)
* **Leitor de Texto**
* **TextWriter**
* **corda** (ler)
* **fora a corda** (escrever; cria uma bolha apenas se o parâmetro de corda não for nulo quando a função retorna)
* POCO (ler)
* fora POCO (escrever; sempre cria uma bolha, cria como objeto nulo se o parâmetro POCO for nulo quando a função retorna)
* **CloudBlobStream** (escrever)
* **ICloudBlob** (ler ou escrever)
* **CloudBlockBlob** (ler ou escrever)
* **CloudPageBlob** (ler ou escrever)

## <a name="how-to-handle-poison-messages"></a>Como lidar com mensagens venenosas
As mensagens cujo conteúdo faz com que uma função falhe são chamadas *mensagens venenosas*. Quando a função falha, a mensagem de fila não é apagada e acaba por ser captada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após um número limitado de iterações, ou pode fazê-lo manualmente.

### <a name="automatic-poison-message-handling"></a>Manipulação automática de mensagens venenosas
O SDK irá chamar uma função até 5 vezes para processar uma mensagem de fila. Se a quinta tentativa falhar, a mensagem é transferida para uma fila de venenos. Pode ver como configurar o número máximo de repetições em [Como definir opções de configuração](#how-to-set-configuration-options).

A fila de veneno *supor-se {originalqueuename}*-veneno. Pode escrever uma função para processar mensagens da fila de veneno, registando-as ou enviando uma notificação de que é necessária atenção manual.

No exemplo seguinte, a função **CopyBlob** falhará quando uma mensagem de fila contiver o nome de uma bolha que não existe. Quando isso acontece, a mensagem é transferida da fila copyblobqueue para a fila de envenenamento por copyblobqueue. A **Mensagem De Veneno de Processo** regista então a mensagem venenosa.

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

A ilustração que se segue mostra a saída da consola a partir destas funções quando uma mensagem venenosa é processada.

![Saída da consola para manipulação de mensagens venenosas](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manipulação manual de mensagens venenosas
Pode obter o número de vezes que uma mensagem foi captada para processamento adicionando um parâmetro **int** chamado **dequeueCount** à sua função. Em seguida, pode verificar a contagem de fila no código de função e efetuar o seu próprio manuseamento de mensagens venenosas quando o número exceder um limiar, como mostra o seguinte exemplo.

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
Pode utilizar o tipo **JobHostConfiguration** para definir as seguintes opções de configuração:

* Descoloque as cordas de ligação SDK em código.
* Configure as definições **de QueueTrigger,** tais como a contagem máxima de defila.
* Obtenha nomes de fila da configuração.

### <a name="set-sdk-connection-strings-in-code"></a>Desmontar as cordas de ligação SDK em código
A definição das cordas de ligação SDK no código permite-lhe utilizar os nomes das cordas de ligação em ficheiros de configuração ou variáveis ambientais, como mostra o seguinte exemplo.

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

### <a name="configure-queuetrigger--settings"></a>Configurar as definições do QueueTrigger
Pode configurar as seguintes definições que se aplicam ao processamento de mensagens de fila:

* O número máximo de mensagens de fila que são captadas simultaneamente para serem executadas em paralelo (o padrão é de 16).
* O número máximo de repetições antes de uma mensagem de fila é enviado para uma fila de veneno (o padrão é 5).
* O tempo máximo de espera antes de voltar a votar quando uma fila está vazia (o padrão é de 1 minuto).

O exemplo que se segue mostra como configurar estas definições:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Definir valores para os parâmetros do construtor SDK WebJobs em código
Por vezes, pretende especificar um nome de fila, um nome ou recipiente blob, ou um nome de mesa em código em vez de código rígido. Por exemplo, é melhor especificar o nome da fila para **QueueTrigger** num ficheiro de configuração ou variável ambiental.

Pode fazê-lo passando um objeto **NameResolver** para o tipo **JobHostConfiguration.** Você inclui espaços reservados especiais cercados por cento (%) sinais em WebJobs SDK atribuem parâmetros de construção, e o seu código **NameResolver** especifica os valores reais a utilizar no lugar desses espaços reservados.

Por exemplo, suponha que queira usar uma fila chamada logqueuetest no ambiente de teste e uma logqueueprod chamada logqueueprod na produção. Em vez de um nome de fila codificado, pretende especificar o nome de uma entrada na coleção **appSettings** que teria o nome real da fila. Se a tecla **appSettings** for logqueue, a sua função pode parecer o seguinte exemplo.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A sua classe **NameResolver** poderia então obter o nome da fila a partir de definições de **aplicações,** como mostra o seguinte exemplo:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Passa a aula **NameResolver** para o objeto **JobHost,** como mostra o seguinte exemplo.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Nota:** Os nomes de fila, mesa e blob são resolvidos cada vez que uma função é chamada, mas os nomes dos contentores blob só são resolvidos quando a aplicação começa. Não pode mudar o nome do recipiente enquanto o trabalho está a decorrer.

## <a name="how-to-trigger-a-function-manually"></a>Como desencadear uma função manualmente
Para acionar uma função manualmente, utilize o método **Call** or **CallAsync** no objeto **JobHost** e o atributo **NoAutomaticTrigger** na função, como mostra o seguinte exemplo.

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

## <a name="how-to-write-logs"></a>Como escrever registos
O Dashboard mostra registos em dois locais: a página para o WebJob e a página para uma determinada invocação webJob.

![Logs na página WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Logs na página de invocação da função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A saída dos métodos de consola que você chama numa função ou no método **principal ()** aparece na página do Dashboard para o WebJob, e não na página para uma determinada invocação do método. A saída do objeto TextWriter que obtém de um parâmetro na sua assinatura de método aparece na página do Dashboard para uma invocação de método.

A saída da consola não pode ser ligada a uma determinada invocação de método porque a Consola é de fio único, enquanto muitas funções de trabalho podem estar em execução ao mesmo tempo. É por isso que o SDK fornece cada invocação de função com o seu próprio objeto de escritor de registo único.

Para escrever registos de rastreio de [aplicações,](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview)utilize **console.out** (cria registos marcados como INFO) e **Console.Error** (cria registos marcados como ERROR). Uma alternativa é utilizar [o Trace ou traceSource,](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)que fornece níveis verbose, de aviso e crítico, além de Informações e Erros. Os registos de rastreio de aplicações aparecem nos ficheiros de registo de aplicações web, tabelas Azure ou blobs Azure dependendo da forma como configura a sua aplicação Web Azure. Como acontece com toda a saída da Consola, os registos de aplicações mais recentes de 100 também aparecem na página do Dashboard para o WebJob, e não na página para uma invocação de função.

A saída da consola só aparece no Dashboard se o programa estiver a funcionar num WebJob Azure, não se o programa estiver a funcionar localmente ou em algum outro ambiente.

Pode desativar a exploração de madeira, definindo a cadeia de ligação do Dashboard a nula. Para mais informações, consulte [Como definir Opções de Configuração](#how-to-set-configuration-options).

O exemplo que se segue mostra várias formas de escrever registos:

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

No Painel WebJobs SDK, a saída do objeto **TextWriter** aparece quando se vai à página para uma determinada invocação de função e selecione **Toggle Output**:

![Ligação de invocação](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Logs na página de invocação da função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

No Painel WebJobs SDK, as mais recentes 100 linhas de saída da Consola aparecem quando se vai à página do WebJob (não para a invocação da função) e selecione **Toggle Output**.

![Saída de alternância](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Num WebJob contínuo, os registos de aplicações aparecem em /data/jobs/continuous/*{webjobname}*/job_log.txt no sistema de ficheiros da aplicação web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Numa bolha Azure os registos da aplicação são assim: 2014-09-26T21:01:13,Informação,Contosoadsnew,491e54,635473620738373502,0,17404,17,Consola.Write - Hello world!, 2014-09-26T21:01:13,Error contosoadsnew,491e54, 635473620738373502,0,17404,19,Consola.Error - Hello world!, 2014-09-26T21:01:13,Informação,Contosoadsnew,491e54,635473620738529920,0,17404,17,Consola.out - Hello world!,

E numa tabela Azure os registos **Console.out** e **Console.Error** soam assim:

![Registo de informações na tabela](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Registo de erros na tabela](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Passos seguintes
Este artigo forneceu amostras de código que mostram como lidar com cenários comuns para trabalhar com as filas Azure. Para obter mais informações sobre como utilizar o Azure WebJobs e o WebJobs SDK, consulte [os recursos de documentação azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).


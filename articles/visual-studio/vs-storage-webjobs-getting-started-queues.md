---
title: Introdução ao armazenamento de filas e o Visual Studio ligados (projetos de trabalho Web) de serviços | Documentos da Microsoft
description: Como começar a utilizar o armazenamento de filas do Azure num projeto do trabalho Web depois de ligar a uma conta de armazenamento com o Visual Studio serviços ligados.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f6f1a3a7f0a406e1dbb40f4bfc6a358da7ac68fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999551"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligados (projetos de trabalho Web) de serviços
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de filas do Azure num projeto de trabalho de Web do Visual Studio do Azure depois de ter criado ou referenciados uma conta de armazenamento do Azure utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo. Quando adiciona uma conta de armazenamento para um projeto do trabalho Web utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo, são instalados os pacotes de NuGet de armazenamento do Azure adequados, as referências apropriadas do .NET são adicionadas ao projeto, e cadeias de ligação para a conta de armazenamento são atualizadas no ficheiro App. config.  

Este artigo fornece c# exemplos de código que mostram como utilizar a versão do SDK de WebJobs do Azure 1.x com o serviço de armazenamento de filas do Azure.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. Ver [introdução ao armazenamento de filas do Azure com o .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações. Para obter mais informações sobre o ASP.NET, consulte [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Como acionar uma função quando é recebida uma mensagem de fila
Para escrever uma função que o SDK do WebJobs chama quando é recebida uma mensagem de fila, utilize o **QueueTrigger** atributo. O construtor de atributo assume um parâmetro de cadeia de caracteres que especifica o nome da fila para consultar. Para ver como definir o nome da fila dinamicamente, confira [como definir opções de configuração](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de fila de cadeia de caracteres
No exemplo seguinte, a fila contém uma mensagem de cadeia de caracteres, então **QueueTrigger** é aplicada a um parâmetro de cadeia de caracteres chamado **logMessage** que contém o conteúdo da mensagem de fila. A função [escreve uma mensagem de registo para o Dashboard](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Além disso **cadeia de caracteres**, o parâmetro pode ser uma matriz de bytes, uma **CloudQueueMessage** objeto ou um POCO que definir.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
No exemplo seguinte, a mensagem de fila contém JSON para um **BlobInformation** objeto que inclui um **BlobName** propriedade. O SDK automaticamente desserializa o objeto.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

O SDK utiliza o [pacote NuGet newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se criar a fila de mensagens num programa que não usa o SDK do WebJobs, pode escrever código semelhante ao seguinte exemplo para criar uma mensagem de fila POCO que o SDK pode analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funções de Async
A seguinte função de async [registra um log para o Dashboard](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

Funções de Async podem demorar um [token de cancelamento](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), conforme mostrado no exemplo a seguir, que copia um blob. (Para obter uma explicação do **queueTrigger** marcador de posição, consulte a [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) seção.)

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

## <a name="types-the-queuetrigger-attribute-works-with"></a>Tipos de que atributo QueueTrigger funciona com
Pode usar **QueueTrigger** com os seguintes tipos:

* **string**
* Um tipo POCO serializado como JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo de consulta
O SDK implementa um aleatório exponencial término algoritmo para minimizar o efeito da fila de inatividade de consulta nos custos de transação de armazenamento.  Quando é encontrada uma mensagem, o SDK tem de aguardar dois segundos e, em seguida, verifica a existência de outra mensagem; Quando não é encontrada nenhuma mensagem que ele espera cerca de quatro segundos antes de tentar novamente. Depois de tentativas falhadas subsequentes para obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, o que está predefinida para um minuto. [O tempo de espera máximo é configurável](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Várias instâncias
Se a sua aplicação web é executado em várias instâncias, um WebJobs contínuos é executado em cada máquina, e cada máquina irá esperar para acionadores e tentar executar as funções. Então, em alguns cenários, que isso pode levar a algumas funções de processar os mesmos dados duas vezes, as funções devem de ser idempotentes (escrito para que a chamá-los repetidamente com os mesmos dados de entrada não produz resultados duplicados).  

## <a name="parallel-execution"></a>Execução paralela
Se tiver várias funções de escutar em filas diferentes, o SDK irá chamá-los em paralelo quando as mensagens são recebidas em simultâneo.

O mesmo se aplica quando várias mensagens são recebidas para uma única fila. Por predefinição, o SDK obtém um lote de 16 mensagens em fila por vez e executa a função que processa-as em paralelo. [O tamanho do lote é configurável](#how-to-set-configuration-options). Quando o número a ser processado chega à metade do tamanho do batch, o SDK é outro lote e começa a processar essas mensagens. Por conseguinte, o número máximo de mensagens em simultâneo a ser processado por função é um vezes e meia o tamanho de lote. Este limite aplica-se em separado para cada função que tem um **QueueTrigger** atributo. Se não pretender que a execução paralela para mensagens recebidas numa fila, defina o tamanho de lote para 1.

## <a name="get-queue-or-queue-message-metadata"></a>Obter a fila ou metadados de mensagem de fila
Pode obter as seguintes propriedades de mensagem adicionando parâmetros para a assinatura do método:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **cadeia de caracteres** queueTrigger (contém o texto da mensagem)
* **cadeia de caracteres** id
* **cadeia de caracteres** popReceipt
* **int** dequeueCount

Se quiser trabalhar diretamente com a API de armazenamento do Azure, também pode adicionar um **CloudStorageAccount** parâmetro.

O exemplo seguinte grava todos esses metadados para um log de aplicativo de informações. No exemplo, logMessage e queueTrigger contêm o conteúdo da mensagem de fila.

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

Este é um registo de exemplo escrito por código de exemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Encerramento correto
Uma função que é executado num trabalho Web contínuo pode aceitar uma **CancellationToken** parâmetro que permite que o sistema operativo notificar a função quando o WebJob está prestes a ser terminada. Pode utilizar esta notificação para se certificar de que a função não terminar inesperadamente de uma forma que mantém os dados num estado inconsistente.

O exemplo seguinte mostra como verificar a existência de terminação de WebJob iminente numa função.

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

**Nota:** O Dashboard não poderá mostrar corretamente o estado e a saída das funções que foram encerradas.

Para obter mais informações, consulte [encerramento correto de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Como criar uma mensagem de fila ao processar uma mensagem de fila
Para escrever uma função que cria uma nova mensagem de fila, utilize o **fila** atributo. Como **QueueTrigger**, passa o nome da fila como uma cadeia de caracteres ou pode [definir o nome da fila dinamicamente](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens de fila de cadeia de caracteres
O exemplo de código não-async seguinte cria uma nova mensagem de fila na fila com o nome "outputqueue" com o mesmo conteúdo, como a mensagem de fila recebida na fila com o nome "inputqueue". (Para async funções usam **IAsyncCollector<T>**  conforme mostrado posteriormente nesta seção.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
Para criar uma mensagem de fila que contém um POCO, em vez de uma cadeia de caracteres, transmita o tipo POCO como um parâmetro de saída para o **fila** construtor de atributo.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

O SDK automaticamente serializa o objeto em JSON. Uma mensagem de fila é sempre criada, mesmo que o objeto é nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou nas funções de async
Para criar várias mensagens, verifique o tipo de parâmetro para a fila de saída **ICollector<T>**  ou **IAsyncCollector<T>**, conforme mostrado no exemplo a seguir.

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

Cada mensagem de fila é criada imediatamente quando o **adicionar** método é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos de que o atributo de fila funciona com
Pode utilizar o **fila** atributo nos seguintes tipos de parâmetro:

* **cadeia de caracteres de** (cria a mensagem de fila se o valor do parâmetro não nulo quando termina a função)
* **o byte []** (como funciona **cadeia**)
* **out CloudQueueMessage** (como funciona **cadeia**)
* **out POCO** (um tipo serializável, cria uma mensagem com um objeto nulo se o parâmetro for nulo quando termina a função)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para criar mensagens manualmente usando a API de armazenamento do Azure diretamente)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Utilizar atributos do SDK do WebJobs no corpo de uma função
Se precisar de fazer algum trabalho na sua função antes de utilizar, tais como um atributo de SDK do WebJobs **fila**, **Blob**, ou **tabela**, pode utilizar o **IBinder**interface.

O exemplo a seguir usa uma mensagem de fila de entrada e cria uma nova mensagem com o mesmo conteúdo numa fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

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

O **IBinder** interface também pode ser utilizada com o **tabela** e **Blob** atributos.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Como ler e escrever os blobs e tabelas ao processar uma mensagem de fila
O **Blob** e **tabela** atributos permitem-lhe ler e escrever os blobs e tabelas. Os exemplos nesta secção aplicam-se aos blobs. Para exemplos de código que mostram como acionar processos quando blobs são criados ou atualizados, consulte [como utilizar o armazenamento de Blobs do Azure com o SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens de fila de cadeia de caracteres acionar operações de BLOBs
Para uma mensagem de fila que contém uma cadeia de caracteres **queueTrigger** é um espaço reservado que pode utilizar na **Blob** do atributo **blobPath** parâmetro que inclui o conteúdo do mensagem.

O exemplo seguinte utiliza **Stream** objetos para leitura e escrita de blobs. A mensagem de fila é o nome de um blob localizado no contentor de textblobs. Uma cópia do blob com "-novo" acrescentado para o nome é criado no mesmo contentor.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O **Blob** demora de construtor de atributo um **blobPath** parâmetro que especifica o nome de contentor e blob. Para obter mais informações sobre este marcador de posição, consulte [como utilizar o armazenamento de Blobs do Azure com o SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Quando o atributo decora uma **Stream** objeto, o outro parâmetro do construtor Especifica a **FileAccess** modo como leitura, escrita ou leitura/escrita.

O exemplo seguinte utiliza uma **CloudBlockBlob** objeto para eliminar um blob. A mensagem de fila é o nome do blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO [(objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) mensagens da fila
Para um POCO armazenado como JSON na mensagem de fila, pode usar os marcadores de posição esse nome propriedades do objeto no **fila** do atributo **blobPath** parâmetro. Também pode utilizar nomes de propriedade de metadados de fila como espaços reservados. Ver [obter a fila ou metadados de mensagem de fila](#get-queue-or-queue-message-metadata).

O exemplo seguinte copia um blob para um blob novo com uma extensão diferente. A mensagem de fila é uma **BlobInformation** objeto que inclui **BlobName** e **BlobNameWithoutExtension** propriedades. Os nomes de propriedade são utilizados como marcadores de posição no caminho do blob para o **BLOBs** atributos.

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O SDK utiliza o [pacote NuGet newtonsoft](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar mensagens. Se criar a fila de mensagens num programa que não usa o SDK do WebJobs, pode escrever código semelhante ao seguinte exemplo para criar uma mensagem de fila POCO que o SDK pode analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Se precisar de fazer algum trabalho na sua função antes de um blob de enlace a um objeto, pode usar o atributo no corpo da função, conforme mostrado na [atributos de utilizar o SDK do WebJobs no corpo de uma função](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipos que pode utilizar o atributo de Blob com o
O **BLOBs** atributo pode ser utilizado com os seguintes tipos:

* **Stream** (leitura ou escrita, especificado utilizando o parâmetro de construtor FileAccess)
* **TextReader**
* **TextWriter**
* **cadeia de caracteres** (ler)
* **cadeia de caracteres de** (escrever; cria um blob apenas se o parâmetro de cadeia de caracteres não-nulo quando a função devolve)
* POCO (leitura)
* out POCO (escrever; sempre cria um blob, cria-se como um objeto nulo se POCO parâmetro for nulo quando a função devolve)
* **CloudBlobStream** (write)
* **ICloudBlob** (leitura ou escrita)
* **CloudBlockBlob** (leitura ou escrita)
* **CloudPageBlob** (leitura ou escrita)

## <a name="how-to-handle-poison-messages"></a>Como lidar com mensagens suspeitas
As mensagens cujo conteúdo faz com que uma função falha são chamadas *mensagens não processáveis*. Quando a função falhar, a mensagem de fila não é eliminada e, eventualmente, é escolhida novamente, fazendo com que o ciclo de ser repetido. O SDK automaticamente pode interromper o ciclo de após um número limitado de iterações, ou pode fazê-lo manualmente.

### <a name="automatic-poison-message-handling"></a>Processamento de mensagens não processáveis automática
O SDK chamará uma função até 5 vezes para processar uma mensagem de fila. Se o tentar quinto falhar, a mensagem é movida para uma fila não processáveis. Pode ver como configurar o número máximo de repetições em [como definir opções de configuração](#how-to-set-configuration-options).

Com o nome da fila não processáveis *{originalqueuename}*-não processáveis. Pode escrever uma função para processar mensagens da fila não processáveis por registar ou enviar uma notificação que atenção manual é necessária.

No exemplo a seguir a **CopyBlob** função irão falhar quando uma mensagem de fila contém o nome de um blob que não existe. Quando isso acontece, a mensagem é movida da fila copyblobqueue para a fila de veneno copyblobqueue. O **ProcessPoisonMessage** , em seguida, regista as mensagens não processáveis.

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

A ilustração seguinte mostra a saída da consola por estas funções quando uma mensagem não processáveis é processada.

![Resultado da consola para a manipulação de mensagens não processáveis](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Processamento de mensagens não processáveis manual
Pode obter o número de vezes que uma mensagem foi recolhida para processamento, adicionando um **int** com o nome do parâmetro **dequeueCount** à sua função. Em seguida, pode verificar a contagem de dequeue no código de função e realizar suas próprias mensagens não processáveis manipuladores quando o número excede um limiar, conforme mostrado no exemplo a seguir.

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
Pode utilizar o **JobHostConfiguration** tipo para definir as opções de configuração seguinte:

* Defina as cadeias de ligação do SDK no código.
* Configurar **QueueTrigger** definições, tais como o máximo da fila contagem.
* Obter nomes de filas a partir da configuração.

### <a name="set-sdk-connection-strings-in-code"></a>Definir as cadeias de ligação do SDK no código
Definir as cadeias de ligação do SDK no código permite-lhe utilizar os seus próprios nomes de cadeia de ligação em arquivos de configuração ou variáveis de ambiente, conforme mostrado no exemplo a seguir.

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

### <a name="configure-queuetrigger--settings"></a>Configurar as definições de QueueTrigger
Pode configurar as seguintes definições que se aplicam ao processamento de mensagens de fila:

* O número máximo de mensagens em fila que são aplicadas ao mesmo tempo a ser executado em paralelo (a predefinição é 16).
* O número máximo de tentativas antes do envio de uma mensagem de fila para uma fila não processáveis (a predefinição é 5).
* O número máximo de tempo antes da consulta novamente quando a fila está vazia de espera (a predefinição é 1 minuto).

O exemplo seguinte mostra como configurar estas definições:

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

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Definir valores para o SDK do WebJobs parâmetros do construtor no código
Por vezes, pretende especificar um nome de fila, um nome de blob ou contentor ou nome de uma tabela no código, em vez de codificá-lo. Por exemplo, pode pretender especificar o nome da fila **QueueTrigger** numa variável de ambiente ou ficheiro de configuração.

Pode fazê-lo ao transmitir uma **NameResolver** objeto para o **JobHostConfiguration** tipo. Inclui marcadores de posição especiais rodeados por sinal de percentagem) iniciar sessão nos parâmetros do construtor de atributo de SDK do WebJobs e a sua **NameResolver** código especifica os valores reais para ser utilizado em vez dos marcadores de posição.

Por exemplo, suponha que pretende utilizar uma fila com o nome logqueuetest no ambiente de teste e um logqueueprod nomeado em produção. Em vez de um nome de fila codificada, pretender especificar o nome de uma entrada no **appSettings** coleção que tem o nome de fila real. Se o **appSettings** chave é logqueue, sua função pode ter um aspeto semelhante ao seguinte exemplo.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Sua **NameResolver** classe, em seguida, foi possível obter o nome da fila de **appSettings** conforme mostrado no exemplo a seguir:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Passa o **NameResolver** classe para o **JobHost** objeto conforme mostrado no exemplo a seguir.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Nota:** Nomes de BLOBs, tabela e fila são resolvidos sempre que uma função é chamada, mas os nomes dos contentores de BLOBs são resolvidos apenas quando o aplicativo é iniciado. Não é possível alterar o nome do contentor de BLOBs, enquanto a tarefa está em execução.

## <a name="how-to-trigger-a-function-manually"></a>Como acionar uma função manualmente
Para acionar manualmente uma função, utilize o **chamar** ou **CallAsync** método no **JobHost** objeto e a **NoAutomaticTrigger** atributo na função, conforme mostrado no exemplo a seguir.

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
O Dashboard mostra os registos em dois locais: a página para o trabalho Web e a página de uma invocação de trabalho Web específica.

![Registos na página de WebJob](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Registos na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Saída dos métodos de consola que chamar uma função ou no **main ()** método é apresentado na página do Dashboard para o trabalho Web e não na página para uma invocação de método em particular. Saída do objeto TextWriter que obtém a partir de um parâmetro na sua assinatura do método é apresentado na página do Dashboard para uma invocação de método.

Resultado da consola não pode ser associado a uma invocação de método em particular, porque a consola é um único thread, embora muitas funções de trabalho podem ser executadas ao mesmo tempo. É por isso que o SDK fornece cada invocação de função com seu próprio objeto de escritor de log exclusivos.

Escrever [dos logs de rastreamento do aplicativo](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), utilize **out** (cria registos marcados como informação) e **Error** (cria registos marcados como erro). Uma alternativa é usar [rastreio ou TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que fornece verboso, aviso, e os níveis de crítico para além das informações e erro. Registos de rastreio de aplicações são apresentadas nos ficheiros de registo de aplicação web, as tabelas do Azure, ou blobs do Azure, dependendo de como configurar a aplicação web do Azure. Como é verdadeiro para todos os saída da consola, os registos de 100 aplicativos mais recentes também aparecem na página do Dashboard para o trabalho Web, não a página de uma invocação de função.

Resultado da consola é apresentado no Dashboard apenas se o programa está em execução num trabalho Web do Azure, não se o programa está em execução localmente ou em algum outro ambiente.

Pode desativar o registo ao definir a cadeia de ligação do Dashboard como nulo. Para obter mais informações, consulte [como definir opções de configuração](#how-to-set-configuration-options).

O exemplo seguinte mostra várias formas de escrever os registos:

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

No Dashboard de SDK do WebJobs, o resultado do **TextWriter** objeto é exibida quando passa para a página para um determinado invocação de função e selecione **Ativar/desativar saída**:

![Ligação de invocação](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Registos na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

No Dashboard do SDK do WebJobs, as mais recentes 100 linhas de Console de saída show cópia de segurança quando aceder à página para o trabalho Web (não para a invocação de função) e selecione **saída de alternância**.

![Ativar/desativar de saída](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Num trabalho Web contínuo, registos de aplicações apresentada na/dados/tarefas/contínua/*{webjobname}*/job_log.txt no sistema de ficheiros de aplicação web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

No Azure blob a aparência de registos de aplicações como esta: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Olá, mundo!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Olá, mundo!, 2014-09-26T21 : 01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Olá, mundo!,

E uma tabela do Azure a **out** e **Error** registos ter este aspeto:

![Registo de informações na tabela](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Registo de erros na tabela](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Passos Seguintes
Este artigo fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com as filas do Azure. Para obter mais informações sobre como utilizar o WebJobs do Azure e o SDK de WebJobs, consulte [recursos de documentação de WebJobs do Azure](https://go.microsoft.com/fwlink/?linkid=390226).


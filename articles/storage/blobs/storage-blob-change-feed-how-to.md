---
title: Processar feed de alterações no armazenamento de BLOBs do Azure (visualização) | Microsoft Docs
description: Saiba como processar logs do feed de alterações em um aplicativo cliente .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 90b7d79cd2a0e215af17856796bcdda2fbabb43f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693634"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Processar feed de alterações no armazenamento de BLOBs do Azure (visualização)

O feed de alterações fornece logs de transações de todas as alterações que ocorrem nos BLOBs e nos metadados de BLOB em sua conta de armazenamento. Este artigo mostra como ler registros do feed de alterações usando a biblioteca do processador do feed de alterações de BLOB.

Para saber mais sobre o feed de alterações, consulte [alterar feed no armazenamento de BLOBs do Azure (versão prévia)](storage-blob-change-feed.md).

> [!NOTE]
> O feed de alterações está em visualização pública e está disponível nas regiões **westcentralus** e **westus2** . Para saber mais sobre esse recurso juntamente com limitações e problemas conhecidos, consulte [suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md). A biblioteca do processador do feed de alterações está sujeita a alterações entre agora e quando essa biblioteca se torna disponível de forma geral.

## <a name="get-the-blob-change-feed-processor-library"></a>Obter a biblioteca do processador do feed de alterações de BLOB

1. No Visual Studio, adicione a URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` às suas fontes de pacote NuGet. 

   Para saber como, consulte [origens do pacote](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. No Gerenciador de pacotes NuGet, localize o pacote **Microsoft. Azure. Storage. changefeed** e instale-o em seu projeto. 

   Para saber como, consulte [Localizar e instalar um pacote](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Conectar-se à conta de armazenamento

Analise a cadeia de conexão chamando o método [CloudStorageAccount. TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) . 

Em seguida, crie um objeto que represente o armazenamento de BLOBs em sua conta de armazenamento chamando o método [CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet) .

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Inicializar o feed de alterações

Adicione as seguintes instruções using à parte superior do seu arquivo de código. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Em seguida, crie uma instância da classe **ChangeFeed** chamando o método **GetContainerReference** . Passe o nome do contêiner do feed de alterações.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Lendo registros

> [!NOTE]
> O feed de alterações é uma entidade imutável e somente leitura em sua conta de armazenamento. Qualquer número de aplicativos pode ler e processar o feed de alterações simultaneamente e de forma independente a sua própria conveniência. Os registros não são removidos do feed de alterações quando um aplicativo os lê. O estado de leitura ou de iteração de cada leitor de consumo é independente e mantido somente pelo seu aplicativo.

A maneira mais simples de ler registros é criar uma instância da classe **ChangeFeedReader** . 

Este exemplo itera por todos os registros no feed de alterações e, em seguida, imprime no console alguns valores de cada registro. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Retomando registros de leitura de uma posição salva

Você pode optar por salvar sua posição de leitura em seu feed de alterações e retomar a iteração dos registros em um momento futuro. Você pode salvar o estado da iteração do feed de alterações a qualquer momento usando o método **ChangeFeedReader. serializestate ()** . O estado é uma **cadeia de caracteres** e seu aplicativo pode salvar esse estado com base no design do seu aplicativo (por exemplo: para um banco de dados ou um arquivo).

```csharp
    string currentReadState = processor.SerializeState();
```

Você pode continuar Iterando por meio de registros do último estado criando o **ChangeFeedReader** usando o método **CreateChangeFeedReaderFromPointerAsync** .

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Fluxo de processamento de registros

Você pode optar por processar registros do feed de alterações à medida que eles chegam. Consulte [especificações](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Lendo registros dentro de um intervalo de tempo

O feed de alterações é organizado em segmentos por hora com base na hora do evento de alteração. Consulte [especificações](storage-blob-change-feed.md#specifications). Você pode ler registros de segmentos do feed de alterações que se enquadram em um intervalo de tempo específico.

Este exemplo obtém os horários de início de todos os segmentos. Em seguida, ele faz a iteração por meio dessa lista até que a hora de início esteja além da hora do último segmento de consumo ou além da hora de término do intervalo desejado. 

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para um intervalo de tempo

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Lendo registros em um segmento

Você pode ler registros de segmentos individuais ou intervalos de segmentos.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Ler registros a partir de uma hora

Você pode ler os registros do feed de alterações de um segmento inicial até o final. Semelhante à leitura de registros dentro de um intervalo de tempo, você pode listar os segmentos e escolher um segmento para começar a iterar.

Este exemplo obtém o [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) do primeiro segmento a ser processado.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Este exemplo processa registros de feed de alteração começando do [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) de um segmento inicial.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Um segmento do pode ter logs do feed de alterações em um ou mais *chunkFilePath*. No caso de vários *chunkFilePath* , o sistema particionou internamente os registros em vários fragmentos para gerenciar a taxa de transferência de publicação. É garantido que cada partição do segmento conterá alterações para BLOBs mutuamente exclusivos e pode ser processada de forma independente sem violar a ordem. Você pode usar a classe **ChangeFeedSegmentShardReader** para iterar por meio de registros no nível do fragmento, se isso for mais eficiente para seu cenário.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os logs do feed de alterações. Consulte o [feed de alterações no armazenamento de BLOBs do Azure (visualização)](storage-blob-change-feed.md)

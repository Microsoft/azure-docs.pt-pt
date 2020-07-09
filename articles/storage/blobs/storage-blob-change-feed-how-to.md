---
title: Feed de mudança de processo no Azure Blob Storage (Preview) / Microsoft Docs
description: Saiba como processar alterar registos de alimentação numa aplicação de cliente .NET
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74111867"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Alterar o feed de mudança de processo no Armazenamento Azure Blob (Pré-visualização)

O feed de alteração fornece registos de transações de todas as alterações que ocorrem às bolhas e aos metadados blob na sua conta de armazenamento. Este artigo mostra-lhe como ler alterar registos de feed utilizando a biblioteca do processador de feed de alteração de bolhas.

Para saber mais sobre o feed de mudança, consulte [change feed in Azure Blob Storage (Preview)](storage-blob-change-feed.md).

> [!NOTE]
> O feed de mudança está em pré-visualização pública, e está disponível nas **regiões oeste** e **oeste22.** Para saber mais sobre esta funcionalidade juntamente com questões e limitações conhecidas, consulte [o suporte para feed change no Azure Blob Storage](storage-blob-change-feed.md). A biblioteca do processador de feed de alteração está sujeita a alterações entre agora e quando esta biblioteca se tornar geralmente disponível.

## <a name="get-the-blob-change-feed-processor-library"></a>Obtenha a biblioteca do processador de feed de mudança de blob

1. No Visual Studio, adicione o URL às fontes de `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` pacotes NuGet. 

   Para saber como, consulte [as fontes de pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)

2. No Gestor de Pacotes NuGet, encontre o pacote **Microsoft.Azure.Storage.Changefeed** e instale-o no seu projeto. 

   Para saber como, consulte [Localizar e instalar um pacote.](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)

## <a name="connect-to-the-storage-account"></a>Ligue-se à conta de armazenamento

Analise a cadeia de ligação chamando o método [CloudStorageAccount.TryParse.](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) 

Em seguida, crie um objeto que represente o Blob Storage na sua conta de armazenamento, chamando o método [CloudStorageAccount.CreateCloudBlobClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)

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

## <a name="initialize-the-change-feed"></a>Inicializar o feed de alteração

Adicione as seguintes declarações usando a parte superior do seu ficheiro de código. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Em seguida, crie uma instância da classe **ChangeFeed** chamando o método **GetContainerReference.** Passe em nome do recipiente de alimentação de mudança.

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

## <a name="reading-records"></a>Registos de leitura

> [!NOTE]
> O feed de alteração é uma entidade imutável e só de leitura na sua conta de armazenamento. Qualquer número de aplicações pode ler e processar o feed de alteração simultaneamente e independentemente à sua própria conveniência. Os registos não são removidos do feed de alteração quando uma aplicação os lê. O estado de leitura ou iteração de cada leitor consumista é independente e mantido apenas pela sua aplicação.

A forma mais simples de ler registos é criar uma instância da classe **ChangeFeedReader.** 

Este exemplo iteração através de todos os registos no feed de alteração e, em seguida, imprime para a consola alguns valores de cada registo. 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>Retomando os registos de leitura de uma posição salva

Pode optar por guardar a sua posição de leitura no feed de alteração e retomar a imersão dos registos num futuro. Pode guardar o estado da sua iteração do feed de alteração a qualquer momento utilizando o método **ChangeFeedReader.SerializeState().** O estado é uma **corda** e a sua aplicação pode salvar esse estado com base no design da sua aplicação (por exemplo: para uma base de dados ou um ficheiro).

```csharp
    string currentReadState = processor.SerializeState();
```

Pode continuar a iterar através de registos do último estado criando o **ChangeFeedReader** utilizando o método **CreateChangeFeedReaderFromPointerAsync.**

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

## <a name="stream-processing-of-records"></a>Processamento de fluxo de registos

Pode optar por processar a alteração dos registos de feed à medida que chegam. Ver [Especificações](storage-blob-change-feed.md#specifications).

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

## <a name="reading-records-within-a-time-range"></a>Ler registos dentro de um intervalo de tempo

O feed de mudança é organizado em segmentos de hora em hora com base no tempo de evento de mudança. Ver [Especificações](storage-blob-change-feed.md#specifications). Pode ler registos de segmentos de feed de alteração que se enquadram num intervalo de tempo específico.

Este exemplo obtém os tempos de início de todos os segmentos. Em seguida, itera através dessa lista até que o tempo de início seja para além do tempo do último segmento consumível ou para além do tempo final da gama desejada. 

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para uma gama de tempo

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

### <a name="reading-records-in-a-segment"></a>Ler registos num segmento

Pode ler registos de segmentos individuais ou gamas de segmentos.

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

## <a name="read-records-starting-from-a-time"></a>Ler registos a partir de um tempo

Pode ler os registos do feed de alteração de um segmento inicial até ao fim. Semelhante a ler registos dentro de um intervalo de tempo, você pode listar os segmentos e escolher um segmento para começar a iterar a partir de.

Este exemplo obtém o [DataTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) do primeiro segmento para processar.

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

Este exemplo processa alterar registos de feed a partir do [DataTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) de um segmento inicial.

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
> Um segmento do pode ter registos de feed de alteração em um ou mais *chunkFilePath*. No caso de vários *chunkFilePath,* o sistema dividiu internamente os registos em vários fragmentos para gerir a produção de publicações. É garantido que cada divisão do segmento conterá alterações para bolhas mutuamente exclusivas e pode ser processada de forma independente sem violar a encomenda. Pode utilizar a classe **ChangeFeedSegmentShardReader** para iterar através de registos ao nível de fragmentos, se isso for mais eficiente para o seu cenário.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre alterar registos de feed. Ver [Alterar o feed no armazenamento de blob Azure (pré-visualização)](storage-blob-change-feed.md)

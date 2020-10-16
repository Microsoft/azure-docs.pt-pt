---
title: Mudança de processo feed no Azure Blob Storage / Microsoft Docs
description: Saiba como processar alterar registos de alimentação numa aplicação de cliente .NET
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568256"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Alterar o feed de mudança de processo no armazenamento de blob Azure

O feed de alteração fornece registos de transações de todas as alterações que ocorrem às bolhas e aos metadados blob na sua conta de armazenamento. Este artigo mostra-lhe como ler alterar registos de feed utilizando a biblioteca do processador de feed de alteração de bolhas.

Para saber mais sobre o feed de mudança, consulte [change feed in Azure Blob Storage](storage-blob-change-feed.md).

## <a name="get-the-blob-change-feed-processor-library"></a>Obtenha a biblioteca do processador de feed de mudança de blob

1. Abra uma janela de comando (Por exemplo: Windows PowerShell).
2. A partir do seu diretório de projeto, instale o pacote [ **Azure.Storage.Blobs.Changefeed** NuGet](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/).

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>Ler registos

> [!NOTE]
> O feed de alteração é uma entidade imutável e só de leitura na sua conta de armazenamento. Qualquer número de aplicações pode ler e processar o feed de alteração simultaneamente e independentemente à sua própria conveniência. Os registos não são removidos do feed de alteração quando uma aplicação os lê. O estado de leitura ou iteração de cada leitor consumista é independente e mantido apenas pela sua aplicação.

Este exemplo itera através de todos os registos do feed de alteração, adiciona-os a uma lista e, em seguida, devolve essa lista ao chamador.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

Este exemplo imprime à consola alguns valores de cada registo da lista. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>Retomar os registos de leitura de uma posição salva

Pode optar por guardar a sua posição de leitura no feed de alteração e, em seguida, retomar a itering através dos registos num futuro. Pode guardar a posição de leitura obtenda pelo cursor de alimentação de mudança. O cursor é uma **corda** e a sua aplicação pode guardar essa cadeia de qualquer forma que faça sentido para o design da sua aplicação (por exemplo: para um ficheiro ou base de dados).

Este exemplo itera através de todos os registos do feed de alteração, adiciona-os a uma lista e guarda o cursor. A lista e o cursor são devolvidos ao chamador. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>Processamento de fluxo de registos

Pode optar por processar a alteração dos registos de alimentação, uma vez que estão comprometidos com o feed de alteração. Ver [Especificações](storage-blob-change-feed.md#specifications). Os eventos de alteração são publicados no feed de mudança a um período de 60 segundos, em média. Recomendamos que faça sondagens para novas alterações com este período em mente ao especificar o intervalo de votação.

Este exemplo sonda periodicamente para mudanças.  Se existirem registos de alteração, este código processa esses registos e poupa o cursor de alimentação de alteração. Desta forma, se o processo for interrompido e depois reinicie, a aplicação pode utilizar o cursor para retomar os registos de processamento onde parou pela última vez. Este exemplo guarda o cursor para um ficheiro de configuração de aplicação local, mas a sua aplicação pode guardá-lo de qualquer forma que faça mais sentido para o seu cenário. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>Ler registos dentro de um intervalo de tempo

Pode ler registos que se enquadram num intervalo de tempo específico. Este exemplo imita através de todos os registos do feed de alteração que caem entre as 15:00 de 2 de março de 2020 e as 2:00 da manhã de 7 de agosto de 2020, adiciona-os a uma lista, e depois devolve essa lista ao chamador.

### <a name="selecting-segments-for-a-time-range"></a>Selecionando segmentos para uma gama de tempo

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

A hora de início que fornece é arredondada para a hora mais próxima e o tempo final é arredondado até a hora mais próxima. É possível que os utilizadores possam ver eventos que ocorreram antes da hora de início e depois do fim do tempo. Também é possível que alguns eventos que ocorrem entre o início e o fim do tempo não apareçam. Isto porque os eventos podem ser registados durante a hora anterior à hora de início ou durante a hora após o fim do tempo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre alterar registos de feed. Ver [Alterar feed no armazenamento de blob Azure](storage-blob-change-feed.md)

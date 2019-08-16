---
title: Introdução ao armazenamento de filas e aos serviços conectados do Visual Studio (serviços de nuvem) | Microsoft Docs
description: Como começar a usar o armazenamento de filas do Azure em um projeto de serviço de nuvem no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5ea0af23ef8cf41b20de033d38e4d8652f9f8310
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510688"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de filas do Azure e aos serviços conectados do Visual Studio (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio.

Mostraremos como criar uma fila no código. Também mostraremos como executar operações básicas de fila, como adicionar, modificar, ler e remover mensagens da fila. Os exemplos são escritos em C# código e usam a [biblioteca de cliente armazenamento do Microsoft Azure para .net](https://msdn.microsoft.com/library/azure/dn261237.aspx).

A operação **Adicionar serviços conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure em seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto.

* Consulte Introdução [ao armazenamento de filas do Azure usando o .net](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre como manipular filas no código.
* Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
* Consulte a [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem do Azure.
* Consulte [ASP.net](https://www.asp.net) para obter mais informações sobre a programação de aplicativos ASP.net.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Acessar filas no código
Para acessar filas em projetos de serviços de nuvem do Visual Studio, você precisa incluir os seguintes itens em C# qualquer arquivo de origem que acesse o armazenamento de filas do Azure.

1. Verifique se as declarações de namespace na parte superior do C# arquivo incluem as instruções **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Obtenha um objeto **CloudQueueClient** para fazer referência aos objetos de fila em sua conta de armazenamento.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Obtenha um objeto **CloudQueue** para fazer referência a uma fila específica.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**NOTA:** Use todo o código acima na frente do código nos exemplos a seguir.

## <a name="create-a-queue-in-code"></a>Criar uma fila no código
Para criar a fila no código, basta adicionar uma chamada para **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila
Para inserir uma mensagem em uma fila existente, crie um novo objeto **CloudQueueMessage** e, em seguida, chame o Método AddMessage.

Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

Aqui está um exemplo que insere a mensagem ' Olá, mundo '.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem em uma fila
Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem em uma fila
Seu código pode remover (retirar da fila) uma mensagem de uma fila em duas etapas.

1. Chame **GetMessage** para obter a próxima mensagem em uma fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
2. Para concluir a remoção da mensagem da fila, chame **DeleteMessage**.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O código a seguir chama **DeleteMessage** logo após a mensagem ter sido processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Usar opções adicionais para processar e remover mensagens da fila
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

* Você pode obter um lote de mensagens (até 32).
* Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo que seu código tenha mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método **GetMessages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, como tal, 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficarão visíveis novamente.

Segue-se um exemplo:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O método **FetchAttributes** pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método **fetchattributes** , sem chamar o serviço fila.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Usar o padrão Async-Await com APIs comuns de fila do Azure
Este exemplo mostra como usar o padrão Async-Await com APIs comuns de fila do Azure. O exemplo chama a versão assíncrona de cada um dos métodos fornecidos, isso pode ser visto pela postagem assíncrona de cada método. Quando um método assíncrono é usado, o padrão Async-Await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho, o que ajuda a evitar gargalos de desempenho e melhora a capacidade de resposta geral do seu aplicativo. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o método **Eliminar** no objeto de fila.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]


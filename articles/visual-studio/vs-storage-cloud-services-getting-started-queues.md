---
title: Introdução ao armazenamento de filas e o Visual Studio (serviços cloud) de serviços ligados | Documentos da Microsoft
description: Serviços ligados de como começar a utilizar o armazenamento de filas do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 28a7de1b43d793641237197aea841022996b07e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994039"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de filas do Azure e o Visual Studio ligados (projetos de serviços cloud) de serviços
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de filas do Azure no Visual Studio depois de ter criado ou referenciados uma conta de armazenamento do Azure num projeto de serviços em nuvem utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo.

Vamos mostrar-lhe como criar uma fila no código. Também mostraremos como realizar operações de fila básicas, como adicionar, modificar, ler e remover mensagens na fila. Os exemplos são escritos em código c# e utilizar o [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

O **adicionar serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto.

* Ver [introdução ao armazenamento de filas do Azure com o .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações sobre a manipulação de filas no código.
* Ver [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
* Ver [documentação de serviços Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços cloud do Azure.
* Ver [ASP.NET](https://www.asp.net) para obter mais informações sobre a programação de aplicativos do ASP.NET.

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento.

## <a name="access-queues-in-code"></a>Filas de acesso no código
Para aceder a filas em projetos de serviços de nuvem do Visual Studio, precisa incluir os seguintes itens para qualquer ficheiro de origem do c#, que acede ao armazenamento de filas do Azure.

1. Certifique-se de que as declarações de namespace na parte superior do arquivo c# incluem-las **usando** instruções.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Obter um **CloudQueueClient** objeto para referenciar os objetos de fila na sua conta de armazenamento.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Obter um **CloudQueue** objeto para fazer referência a uma fila específica.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**NOTA:** Utilize todo o código acima na frente o código nos exemplos a seguir.

## <a name="create-a-queue-in-code"></a>Criar uma fila no código
Para criar a fila no código, basta adicionar uma chamada para **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem numa fila
Para introduzir uma mensagem numa fila existente, crie uma nova **CloudQueueMessage** objeto, em seguida, chamar os **AddMessage** método.

R **CloudQueueMessage** objeto pode ser criado a partir de uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes.

Eis um exemplo que irá inserir a mensagem "Olá, mundo".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Ler uma mensagem numa fila
Pode pré-visualizar a mensagem no início de um fila sem a remover da fila ao chamar o método **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Ler e remover uma mensagem numa fila
Pode remover o seu código (remover da fila) uma mensagem da fila em dois passos.

1. Chamar **GetMessage** para obter a seguinte mensagem numa fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos.
2. Para concluir a remover a mensagem da fila, chame **DeleteMessage**.

Este processo de dois passos da remoção de uma mensagem garante que se o código não conseguir processar uma mensagem devido a uma falha de hardware ou software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente. O seguinte código chamadas **DeleteMessage** imediatamente após a mensagem foi processada.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Utilize opções adicionais para processar e remover mensagens na fila
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

* Pode obter um lote de mensagens (até 32).
* Pode definir um tempo de limite de invisibilidade superiores ou inferiores, permitindo que seu código mais ou menos tempo para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o método **GetMessages** para obter 20 mensagens numa chamada. Em seguida, processa cada mensagem através de um ciclo **foreach**. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem. Tenha em atenção que os 5 minutos começam para todas as mensagens ao mesmo tempo, como tal, 5 minutos após a chamada para **GetMessages**, as mensagens que não tenham sido eliminadas ficarão visíveis novamente.

Segue-se um exemplo:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O método **FetchAttributes** pede ao serviço Fila que devolva os atributos de fila, incluindo a contagem de mensagens. O **ApproximateMethodCount** propriedade retorna o último valor obtido pela **FetchAttributes** método, sem chamar o serviço de fila.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Utilizar o padrão Async-Await com APIs de fila comuns do Azure
Este exemplo mostra como utilizar o padrão Async-Await com APIs de fila comuns do Azure. O exemplo chama a versão assíncrona de cada um dos métodos em questão, isso pode ser visto pela **Async** pós-correção de cada método. Quando um método async é utilizado o async-await padrão suspende a execução local até que a chamada é concluída. Este comportamento permite que o thread atual efetue outro trabalho que ajuda a evitar congestionamentos de desempenho e melhora a capacidade de resposta do seu aplicativo. Para obter mais detalhes sobre como utilizar o padrão Async-Await no .NET, consulte [Async-Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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


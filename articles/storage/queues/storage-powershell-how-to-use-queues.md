---
title: Execute ações de armazenamento da fila Azure em PowerShell
description: Escote operações no armazenamento da fila Azure com o PowerShell. Com o armazenamento da Fila Azure, pode armazenar um grande número de mensagens acessíveis por HTTP/HTTPS.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f45a4de2e13f936556f8dd99aa107110edc6e91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077930"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Realizar operações no armazenamento de Filas do Azure com o Azure PowerShell

O armazenamento da Fila Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer parte do mundo através de HTTP ou HTTPS. Para obter informações [detalhadas, consulte Introdução às Filas Azure](storage-queues-introduction.md). Este artigo abrange operações comuns de armazenamento de fila. Saiba como:

> [!div class="checklist"]
>
> * Criar uma fila
> * Recupere uma fila
> * Adicionar uma mensagem
> * Leia uma mensagem
> * Eliminar uma mensagem
> * Eliminar uma fila

Este como-fazer requer a versão Azure PowerShell Az ou mais tarde. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

Não existem cmdlets PowerShell para o plano de dados para filas. Para realizar operações de plano de dados como adicionar uma mensagem, ler uma mensagem e apagar uma mensagem, tem de utilizar a biblioteca de clientes de armazenamento .NET tal como está exposta no PowerShell. Cria-se um objeto de mensagem e depois pode utilizar comandos como AddMessage para realizar operações nessa mensagem. Este artigo mostra-lhe como fazer isso.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar lista de locais

Se não souber qual a localização que quer utilizar, pode listar as localizações disponíveis. Depois de a lista ser apresentada, localize a que quer utilizar. Este exercício utilizará **eastus.** Guarde-o na **localização** variável para utilização futura.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Guarde o nome do grupo de recursos numa variável para utilização futura. Neste exemplo, um grupo de recursos chamado *howtoqueuesrg* é criado na região *leste.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

Crie uma conta de armazenamento de uso geral padrão com armazenamento localmente redundante (LRS) utilizando [o New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Obtenha o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao efetuar ações em contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo a seguir estabelece primeiro uma ligação ao Azure Storage utilizando o contexto da conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, chama [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet para criar uma fila chamada "howtoqueue".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Para obter informações sobre as convenções de nomeação do Serviço de Fila Azure, consulte [Filas de Nomeação e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recupere uma fila

Pode consultar e recuperar uma fila específica ou uma lista de todas as filas numa conta de Armazenamento. Os exemplos que se seguem demonstram como recuperar todas as filas na conta de armazenamento e uma fila específica; ambos os comandos usam o [cmdlet Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem a uma fila

As operações que impactam as mensagens reais na fila utilizam a biblioteca do cliente de armazenamento .NET, tal como exposta no PowerShell. Para adicionar uma mensagem a uma fila, crie uma nova instância do objeto de mensagem, [microsoft.Azure.storage.queue.CloudQueueMessage.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) Em seguida, chame o método [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage). É possível criar uma CloudQueueMessage a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

O exemplo a seguir demonstra como adicionar uma mensagem à sua fila.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Se utilizar o [Azure Storage Explorer,](https://storageexplorer.com)pode ligar-se à sua conta Azure e ver as filas na conta de armazenamento e perfurar numa fila para visualizar as mensagens na fila.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Leia uma mensagem da fila e, em seguida, elimine-a

As mensagens são lidas na melhor primeira tentativa de primeira ordem. Isto não é garantido. Quando se lê a mensagem da fila, torna-se invisível para todos os outros processos que olham para a fila. Isto garante que se o seu código não processar a mensagem devido a hardware ou falha de software, outra instância do seu código pode obter a mesma mensagem e tentar novamente.  

Este **tempo limite de invisibilidade** define quanto tempo a mensagem permanece invisível antes de estar novamente disponível para o processamento. A predefinição é 30 segundos.

O seu código lê uma mensagem da fila em dois passos. Quando ligar para o método [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage,](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) obtém a próxima mensagem na fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Para terminar a remoção da mensagem da fila, ligue para o método [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

No exemplo seguinte, lê-se as três mensagens de fila e, em seguida, aguarde 10 segundos (o tempo limite de invisibilidade). Em seguida, leia novamente as três mensagens, apagando as mensagens depois de as ler, chamando **DeleteMessage**. Se tentar ler a fila após a apagar as mensagens, $queueMessage será devolvida como NU.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Eliminar uma fila

Para eliminar uma fila e todas as mensagens contidas, ligue para o Remove-AzStorageQueue cmdlet. O exemplo a seguir mostra como eliminar a fila específica utilizada neste exercício utilizando o cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os ativos que criou neste exercício, remova o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu sobre a gestão básica de armazenamento de filas com a PowerShell, incluindo como:

> [!div class="checklist"]
>
> * Criar uma fila
> * Recupere uma fila
> * Adicionar uma mensagem
> * Leia a próxima mensagem
> * Eliminar uma mensagem
> * Eliminar uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de armazenamento powerShell da Microsoft Azure

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

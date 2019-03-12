---
title: Executar operações no armazenamento de filas do Azure com o PowerShell | Documentos da Microsoft
description: Como realizar operações no armazenamento de filas do Azure com o PowerShell
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 64e8f9d1fb29718b0a3d4655c739abc93ecb088c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763845"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Efetuar operações de armazenamento de filas do Azure com o Azure PowerShell

Armazenamento de filas do Azure é um serviço para armazenar grandes quantidades de mensagens que podem ser acedidas em qualquer local no mundo através de HTTP ou HTTPS. Para obter informações detalhadas, consulte [introdução às filas do Azure](storage-queues-introduction.md). Este artigo que mostra como abrange operações de armazenamento de filas comuns. Saiba como:

> [!div class="checklist"]
> * Criar uma fila
> * Obter uma fila
> * Adicione uma mensagem
> * Ler uma mensagem
> * Eliminar uma mensagem 
> * Eliminar uma fila

Nesta explicação de procedimento requer o módulo Azure PowerShell Az versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

Não há nenhum cmdlets do PowerShell para o plano de dados para filas. Para executar os dados de operações do plano de como adicionar uma mensagem, leia uma mensagem e eliminar uma mensagem, precisa usar a biblioteca de cliente de armazenamento do .NET, como ele é exposto no PowerShell. Cria um objeto de mensagem e, em seguida, pode utilizar comandos, como AddMessage para realizar operações em que a mensagem. Este artigo mostra-lhe como fazer isso.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Obter a lista de localizações

Se não souber qual a localização que quer utilizar, pode listar as localizações disponíveis. Depois de a lista ser apresentada, localize a que quer utilizar. Irá utilizar este exercício **eastus**. Store isso na variável **localização** para utilização futura.

```powershell
Get-AzLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos com o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando. 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Store o nome do grupo de recursos numa variável para utilização futura. Neste exemplo, um grupo de recursos chamado *howtoqueuesrg* é criado na *eastus* região.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

Criar uma conta de armazenamento para fins gerais com armazenamento localmente redundante (LRS) usando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Obtenha o contexto de conta de armazenamento que define a conta de armazenamento a ser utilizado. Ao efetuar ações em contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo seguinte primeiro estabelece uma ligação ao armazenamento do Azure com o contexto da conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, ele chama [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet para criar uma fila com o nome 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Para informações sobre as convenções de nomenclatura para Azure Queue Service, consulte [nomenclatura de filas e metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Obter uma fila

Pode consultar e obter uma fila específica ou uma lista de todas as filas numa conta de armazenamento. Os exemplos seguintes demonstram como as pode recuperar todas as filas na conta de armazenamento e uma fila específica; os dois comandos utilizam o [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Adicione uma mensagem numa fila

Operações que afetam as mensagens reais na fila de utilizam a biblioteca de cliente de armazenamento de .NET como expostos no PowerShell. Para adicionar uma mensagem numa fila, crie uma nova instância do objeto message, [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](https://msdn.microsoft.com/library/azure/jj732474.aspx) classe. Em seguida, chame o método [AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Um CloudQueueMessage pode ser criado a partir de uma cadeia de caracteres (em formato UTF-8) ou uma matriz de bytes.

O exemplo seguinte demonstra como adicionar uma mensagem à sua fila.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Se utilizar o [Explorador de armazenamento do Azure](http://storageexplorer.com), pode ligar à sua conta do Azure e ver as filas na conta de armazenamento e fazer uma busca detalhada numa fila para ver as mensagens na fila. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Leia uma mensagem da fila, em seguida, eliminá-lo

As mensagens são de leitura por ordem melhor: Experimente first-in-first-out. Não é garantido. Quando ler a mensagem da fila, torna-se invisível para todos os outros processos olhar para a fila. Isto garante que, se seu código não conseguir processar a mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente.  

Isso **tempo limite de invisibilidade** define o tempo que a mensagem permanece invisível antes de ser novamente disponível para processamento. A predefinição é 30 segundos. 

Seu código ler uma mensagem da fila em dois passos. Quando chama a [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) método, obterá a seguinte mensagem na fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Para concluir a remover a mensagem da fila, chama o [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) método. 

No exemplo a seguir, leia as mensagens de fila de três depois, aguarde 10 segundos (o tempo limite de invisibilidade). Em seguida, leia as mensagens de três novamente, a eliminar as mensagens depois de lê-los ao chamar **DeleteMessage**. Se tentar ler a fila depois das mensagens são eliminadas, $queueMessage será devolvido como NULL.

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

Para eliminar uma fila e todas as mensagens contidas no mesmo, chame o cmdlet Remove-AzStorageQueue. O exemplo seguinte mostra como eliminar a fila específica utilizada neste exercício, utilizando o cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue 
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os ativos que criou neste exercício, remova o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Neste caso, remove a conta de armazenamento que criou e o grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de procedimentos, aprendeu sobre a gestão de armazenamento de fila básica com o PowerShell, incluindo como:

> [!div class="checklist"]
> * Criar uma fila
> * Obter uma fila
> * Adicione uma mensagem
> * Ler a mensagem seguinte
> * Eliminar uma mensagem 
> * Eliminar uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de armazenamento do Microsoft Azure PowerShell

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

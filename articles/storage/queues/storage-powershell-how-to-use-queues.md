---
title: Executar ações de armazenamento de filas do Azure no PowerShell
description: Como executar operações no armazenamento de filas do Azure com o PowerShell
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: bd2f372bdcb949b64f748d186a9b060bb9cbec4a
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087066"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Realizar operações no armazenamento de Filas do Azure com o Azure PowerShell

O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de HTTP ou HTTPS. Para obter informações detalhadas, consulte [introdução às filas Azure](storage-queues-introduction.md). Este artigo de instruções aborda as operações comuns de armazenamento de filas. Saiba como:

> [!div class="checklist"]
>
> * Criar uma fila
> * Recuperar uma fila
> * Adicione uma mensagem
> * Ler uma mensagem
> * Apagar uma mensagem
> * Eliminar uma fila

Este "como" requer o módulo Azure PowerShell AZ versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps).

Não há cmdlets do PowerShell para o plano de dados para filas. Para executar operações de plano de dados, como adicionar uma mensagem, ler uma mensagem e excluir uma mensagem, você precisa usar a biblioteca de cliente de armazenamento do .NET, pois ela é exposta no PowerShell. Você cria um objeto de mensagem e, em seguida, pode usar comandos como AddMessage para executar operações nessa mensagem. Este artigo mostra como fazer isso.

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

Crie um grupo de recursos com o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Armazene o nome do grupo de recursos em uma variável para uso futuro. Neste exemplo, um grupo de recursos chamado *howtoqueuesrg* é criado na região *oriental.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

Crie uma conta de armazenamento padrão para fins gerais com armazenamento redundante localmente (LRS) utilizando [new-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Obtenha o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao efetuar ações em contas de armazenamento, referencia o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo a seguir primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto da conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso. Em seguida, chama [o cmdlet New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) para criar uma fila chamada "howtoqueue".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Para obter informações sobre nomeação de convenções para o Serviço de Fila Azure, consulte [Filas de Nomeação e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recuperar uma fila

Você pode consultar e recuperar uma fila específica ou uma lista de todas as filas em uma conta de armazenamento. Os seguintes exemplos demonstram como recuperar todas as filas na conta de armazenamento, e uma fila específica; ambos os comandos usam o cmdlet [Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

As operações que afetam as mensagens reais na fila usam a biblioteca de cliente de armazenamento do .NET, como exposto no PowerShell. Para adicionar uma mensagem a uma fila, crie uma nova instância do objeto de mensagem, [Microsoft.Azure.Storage.Queue.CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) class. Em seguida, chame o método [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage). Um CloudQueueMessage pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

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

Se utilizar o [Azure Storage Explorer,](https://storageexplorer.com)pode ligar-se à sua conta Azure e ver as filas na conta de armazenamento e perfurar numa fila para ver as mensagens na fila.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Ler uma mensagem da fila e, em seguida, excluí-la

As mensagens são lidas em ordem de primeiro a entrar, primeiro a sair. Isso não é garantido. Quando você lê a mensagem da fila, ela se torna invisível para todos os outros processos que procuram na fila. Isso garante que, se o código não processar a mensagem devido a uma falha de hardware ou de software, outra instância do seu código poderá obter a mesma mensagem e tentar novamente.  

Este **tempo de invisibilidade** define quanto tempo a mensagem permanece invisível antes de estar novamente disponível para processamento. O padrão é de 30 segundos.

Seu código lê uma mensagem da fila em duas etapas. Quando ligar para o método [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage,](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) obtém a próxima mensagem na fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Para terminar de remover a mensagem da fila, ligue para o método [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

No exemplo a seguir, você lê as três mensagens da fila e aguarda 10 segundos (o tempo limite de invisibilidade). Depois, leia novamente as três mensagens, eliminando as mensagens depois de as ler, ligando para **o DeleteMessage**. Se você tentar ler a fila depois que as mensagens forem excluídas, $queueMessage serão retornadas como nulas.

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

Para excluir uma fila e todas as mensagens contidas nela, chame o cmdlet Remove-AzStorageQueue. O exemplo a seguir mostra como excluir a fila específica usada neste exercício usando o cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os ativos que você criou neste exercício, remova o grupo de recursos. Isto também elimina todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você aprendeu sobre o gerenciamento de armazenamento de filas básico com o PowerShell, incluindo como:

> [!div class="checklist"]
>
> * Criar uma fila
> * Recuperar uma fila
> * Adicione uma mensagem
> * Ler a próxima mensagem
> * Apagar uma mensagem
> * Eliminar uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShellndo cmdlets de armazenamento

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.

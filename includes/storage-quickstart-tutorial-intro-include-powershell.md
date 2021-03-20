---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67184483"
---
## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Se não souber qual a localização que quer utilizar, pode listar as localizações disponíveis. Apresente a lista de localizações com o seguinte exemplo de código e encontre o que pretende utilizar. Este exemplo utiliza **eastus**. Armazene a localização numa variável e utilize-a de forma a poder alterá-la num único local.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento padrão, de uso geral, com replicação LRS utilizando [o New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Em seguida, obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar. Ao realizar ações em contas de armazenamento, referencia o contexto em vez de transmitir repetidamente as credenciais. Utilize o exemplo a seguir para criar uma conta de armazenamento denominada *mystorageaccount* com armazenamento localmente redundante (LRS) e encriptação de blobs (ativada por predefinição).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```

---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170640"
---
## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

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

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Criar uma conta de armazenamento para fins gerais padrão com replicação LRS através de [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Em seguida, obtenha o contexto da conta de armazenamento que define a conta de armazenamento que pretende utilizar. Ao realizar ações em contas de armazenamento, referencia o contexto em vez de transmitir repetidamente as credenciais. Utilize o exemplo a seguir para criar uma conta de armazenamento denominada *mystorageaccount* com armazenamento localmente redundante (LRS) e encriptação de blobs (ativada por predefinição).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```

---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073638"
---
Este quickstart usa um cofre de chave Azure pré-criado. Pode criar um cofre-chave seguindo os passos no [quickstart Azure CLI,](../articles/key-vault/general/quick-create-cli.md) [Azure PowerShell quickstart](../articles/key-vault/general/quick-create-powershell.md), ou [Azure portal quickstart](../articles/key-vault/general/quick-create-portal.md). 

Em alternativa, pode simplesmente executar os comandos Azure CLI ou Azure PowerShell abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---
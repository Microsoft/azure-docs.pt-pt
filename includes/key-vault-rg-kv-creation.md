---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512817"
---
Este quickstart usa um cofre de chave Azure pré-criado. Pode criar um cofre-chave seguindo os passos no [quickstart Azure CLI,](/azure/key-vault/general/quick-create-cli) [Azure PowerShell quickstart](/azure/key-vault/general/quick-create-powershell), ou [Azure portal quickstart](/azure/key-vault/general/quick-create-portal). 

Em alternativa, pode simplesmente executar os comandos Azure CLI ou Azure PowerShell abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
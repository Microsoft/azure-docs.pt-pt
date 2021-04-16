---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502266"
---
Utilize o cmdlet Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) para criar um Cofre-Chave no grupo de recursos a partir do passo anterior. Terá de fornecer algumas informações:

- Nome do cofre chave: Uma sequência de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)

  > [!Important]
  > Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.

- Nome do grupo de recursos: **myResourceGroup**.
- A localização: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

O resultado deste cmdlet mostra as propriedades do cofre de chaves recém-criado. Tome nota das duas propriedades listadas abaixo:

- **Nome do cofre**: O nome que forneceu ao parâmetro de nome acima.
- **Cofre URI**: No exemplo, este é https:// &lt; o seu nome único de keyvault &gt; .vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

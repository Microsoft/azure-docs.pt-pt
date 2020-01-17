---
title: Erros de nome de conta de armazenamento
description: Descreve os erros que você pode encontrar ao especificar um nome de conta de armazenamento.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153977"
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolver erros de nomes de conta de armazenamento

Este artigo descreve os erros de nomenclatura que você pode encontrar ao implantar uma conta de armazenamento.

## <a name="symptom"></a>Sintoma

Se o nome da conta de armazenamento incluir caracteres proibidos, você receberá um erro como:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Para contas de armazenamento, você deve fornecer um nome para o recurso que seja exclusivo no Azure. Se não indicar um nome exclusivo, receberá um erro como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se você implantar uma conta de armazenamento com o mesmo nome de uma conta de armazenamento existente em sua assinatura, mas fornecer um local diferente, você receberá um erro indicando que a conta de armazenamento já existe em um local diferente. Exclua a conta de armazenamento existente ou forneça o mesmo local da conta de armazenamento existente.

## <a name="cause"></a>Causa

Os nomes de conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e usar apenas números e letras minúsculas. O nome deve ser exclusivo.

## <a name="solution"></a>Solução

Verifique se o nome da conta de armazenamento é exclusivo. Você pode criar um nome exclusivo concatenando sua Convenção de nomenclatura com o resultado da função [uniquestring](template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Verifique se o nome da conta de armazenamento não excede 24 caracteres. A função [uniquestring](template-functions-string.md#uniquestring) retorna 13 caracteres. Se você concatenar um prefixo ou sufixo ao resultado de **uniquestring** , forneça um valor que tenha 11 caracteres ou menos.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Verifique se o nome da conta de armazenamento não inclui letras maiúsculas ou caracteres especiais.
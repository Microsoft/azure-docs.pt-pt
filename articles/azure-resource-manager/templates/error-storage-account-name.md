---
title: Erros de nome da conta de armazenamento
description: Descreve os erros que pode encontrar ao especificar um nome de conta de armazenamento.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76153977"
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolver erros para nomes de conta de armazenamento

Este artigo descreve erros de nomeação que pode encontrar ao implementar uma conta de armazenamento.

## <a name="symptom"></a>Sintoma

Se o nome da sua conta de armazenamento incluir caracteres proibidos, recebe um erro como:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Para contas de armazenamento, você deve fornecer um nome para o recurso que é único em Azure. Se não indicar um nome exclusivo, receberá um erro como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se implementar uma conta de armazenamento com o mesmo nome que uma conta de armazenamento existente na sua subscrição, mas fornecer uma localização diferente, recebe um erro indicando que a conta de armazenamento já existe num local diferente. Ou elimina a conta de armazenamento existente ou fornece a mesma localização que a conta de armazenamento existente.

## <a name="cause"></a>Causa

Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas. O nome tem de ser exclusivo.

## <a name="solution"></a>Solução

Certifique-se de que o nome da conta de armazenamento é único. Você pode criar um nome único concatenando sua convenção de nomeação com o resultado da função [unicamenteString.](template-functions-string.md#uniquestring)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Certifique-se de que o nome da sua conta de armazenamento não excede 24 caracteres. A função [un uniqueString](template-functions-string.md#uniquestring) retorna a 13 caracteres. Se concatenar um prefixo ou postfixo para o resultado **exclusivo Destring,** forneça um valor que seja de 11 caracteres ou menos.

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

Certifique-se de que o nome da sua conta de armazenamento não inclui nenhuma carta maiúscula ou caracteres especiais.
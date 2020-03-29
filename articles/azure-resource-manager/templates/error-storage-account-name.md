---
title: Erros de nome da conta de armazenamento
description: Descreve os erros que pode encontrar ao especificar um nome de conta de armazenamento.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153977"
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolver erros para nomes de conta de armazenamento

Este artigo descreve erros de nomeação que pode encontrar ao implementar uma conta de armazenamento.

## <a name="symptom"></a>Sintoma

Se o nome da sua conta de armazenamento incluir caracteres proibidos, receberá um erro como:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Para contas de armazenamento, você deve fornecer um nome para o recurso que é único em azure. Se não indicar um nome exclusivo, receberá um erro como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se implementar uma conta de armazenamento com o mesmo nome que uma conta de armazenamento existente na sua subscrição, mas fornecer uma localização diferente, recebe um erro indicando que a conta de armazenamento já existe num local diferente. Ou apaga a conta de armazenamento existente ou fornece a mesma localização que a conta de armazenamento existente.

## <a name="cause"></a>Causa

Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar números e letras minúsculas apenas. O nome deve ser único.

## <a name="solution"></a>Solução

Certifique-se de que o nome da conta de armazenamento é único. Pode criar um nome único concatenando a sua convenção de nomeação com o resultado da função [String única.](template-functions-string.md#uniquestring)

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Certifique-se de que o nome da sua conta de armazenamento não excede 24 caracteres. A função [String única](template-functions-string.md#uniquestring) devolve 13 caracteres. Se concatenar um prefixo ou postfixo ao resultado único do **String,** forneça um valor que seja de 11 caracteres ou menos.

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

Certifique-se de que o nome da sua conta de armazenamento não inclui letras maiúsculas ou caracteres especiais.
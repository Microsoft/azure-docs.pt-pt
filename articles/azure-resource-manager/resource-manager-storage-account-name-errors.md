---
title: Erros de nomes de conta de armazenamento do Azure | Documentos da Microsoft
description: Descreve os erros que poderá encontrar ao especificar um nome de conta de armazenamento.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c3d4d764b1076c8705cfa64d6c0b38e3b8c1a801
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716398"
---
# <a name="resolve-errors-for-storage-account-names"></a>Resolver erros de nomes de conta de armazenamento

Este artigo descreve os erros de nomenclatura que poderá encontrar ao implementar uma conta de armazenamento.

## <a name="symptom"></a>Sintoma

Se o nome da sua conta de armazenamento inclui carateres proibidos, receberá um erro, como:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Para contas de armazenamento, tem de fornecer um nome para o recurso que seja exclusivo em todo o Azure. Se não fornecer um nome exclusivo, receberá um erro, como:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Se implementar uma conta de armazenamento com o mesmo nome como uma conta de armazenamento existente na sua subscrição, mas fornecem uma localização diferente, receberá um erro que indica que a conta de armazenamento já existe numa localização diferente. Ou eliminar a conta de armazenamento existente ou fornecer a mesma localização que a conta de armazenamento existente.

## <a name="cause"></a>Causa

Nomes de conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas. O nome tem de ser exclusivo.

## <a name="solution"></a>Solução

Certifique-se de que o nome de conta de armazenamento é exclusivo. Pode criar um nome exclusivo, concatenando a Convenção de nomenclatura com o resultado do [uniqueString](resource-group-template-functions-string.md#uniquestring) função.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Certifique-se de que o nome da sua conta de armazenamento não exceder os 24 carateres. O [uniqueString](resource-group-template-functions-string.md#uniquestring) função devolve 13 carateres. Se concatenar um prefixo ou sufixo para a **uniqueString** resultar, forneça um valor que é de 11 carateres ou menos.

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

Certifique-se de que o nome da sua conta de armazenamento não inclui quaisquer letras maiúsculas ou carateres especiais.
---
title: Elemento UI do uI do dispositivo de armazenamentoBlobSelector
description: Descreve o elemento UI microsoft.storageBlobSelector para o portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754530"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft.Storage.StorageBlobSelector UI elemento

Um controlo para selecionar uma bolha a partir de uma conta de armazenamento Azure.

## <a name="ui-sample"></a>Amostra de UI

O utilizador é apresentado com a opção de procurar por bolhas de armazenamento disponíveis.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft.StorageBlobSelector - navegue":::

Depois de selecionar **Browse,** o utilizador pode selecionar uma conta de armazenamento.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft.StorageBlobSelector - selecione armazenamento":::

O utilizador vê os recipientes na conta de armazenamento e pode selecionar um.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft.StorageBlobSelector - selecione recipiente":::

A partir do recipiente, o utilizador pode selecionar um ficheiro.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft.Storage.StorageBlobSelector - ficheiro":::

O controlo é atualizado para exibir o nome do ficheiro selecionado.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft.StorageBlobSelector - mostrar ficheiro selecionado":::

## <a name="schema"></a>Esquema

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Observações

Os **constrangimentos.permitiu que a propriedade DefileExtensions** especifica os tipos de ficheiros permitidos.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

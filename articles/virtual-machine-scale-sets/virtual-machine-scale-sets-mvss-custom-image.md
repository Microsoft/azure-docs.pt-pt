---
title: Refira uma imagem personalizada num modelo de conjunto de escala Azure
description: Saiba como adicionar uma imagem personalizada a um modelo de conjunto de escala de máquina virtual Azure existente
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/26/2018
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5ed9ee79dde73e738417031b928a675ea913179c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83124912"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de escala básica](virtual-machine-scale-sets-mvss-start.md) para implementar a partir da imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição de modelo
Num [artigo anterior](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo de conjunto de escala básica. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa uma escala definida a partir de uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criar uma imagem de disco gerida

Se já tiver uma imagem de disco gerida sob medida (um recurso do `Microsoft.Compute/images` tipo), então pode saltar esta secção.

Em primeiro lugar, adicione um `sourceImageVhdUri` parâmetro, que é o URI à bolha generalizada no Azure Storage que contém a imagem personalizada a ser implantada.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Em seguida, adicione um recurso do tipo , que é a imagem de `Microsoft.Compute/images` disco gerido com base na bolha generalizada localizada na URI `sourceImageVhdUri` . Esta imagem deve estar na mesma região que o conjunto de escala que a utiliza. Nas propriedades da imagem, especifique o tipo de SO, a localização do blob (a partir do `sourceImageVhdUri` parâmetro) e o tipo de conta de armazenamento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

No recurso conjunto de escala, adicione uma `dependsOn` cláusula referente à imagem personalizada para garantir que a imagem é criada antes que o conjunto de escala tente implementar a partir dessa imagem:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterar as propriedades do conjunto de escala para usar a imagem do disco gerido

No `imageReference` conjunto de `storageProfile` escala, em vez de especificar o editor, oferta, sku e versão de uma imagem de plataforma, especificar `id` o `Microsoft.Compute/images` recurso:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, utilize a `resourceId` função para obter o ID de recursos da imagem criada no mesmo modelo. Se criou previamente a imagem do disco gerido, deve fornecer o ID dessa imagem. Esta identificação deve ser do formulário: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` .


## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

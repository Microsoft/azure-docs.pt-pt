---
title: Referência a uma imagem personalizada em um modelo de conjunto de escala Azure
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
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124912"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto](virtual-machine-scale-sets-mvss-start.md) de escala básica para implantar a partir de imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo
Num [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo básico de conjunto de escala. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa um conjunto de escala a partir de uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criar uma imagem de disco gerida

Se já tem uma imagem de disco gerida sob medida (um recurso de `Microsoft.Compute/images` tipo), então pode saltar esta secção.

Em primeiro lugar, adicione um `sourceImageVhdUri` parâmetro, que é o URI à bolha generalizada no Armazenamento Azure que contém a imagem personalizada para implantar.


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

Em seguida, adicione um recurso de tipo `Microsoft.Compute/images` , que é a imagem de disco gerida com base na bolha generalizada localizada na URI `sourceImageVhdUri` . Esta imagem deve estar na mesma região que o conjunto de escala que a utiliza. Nas propriedades da imagem, especifique o tipo DE Os, a localização da bolha (a partir do parâmetro) e o tipo de conta de `sourceImageVhdUri` armazenamento:

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

No recurso de conjunto de escala, adicione uma cláusula que se refere à imagem personalizada para se certificar de que a imagem é criada antes que o conjunto de `dependsOn` escala tente ser implantado a partir dessa imagem:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterar propriedades definidas de escala para usar a imagem de disco gerida

No `imageReference` conjunto de escala `storageProfile` , em vez de especificar a editora, oferecer, sku e versão de uma imagem de plataforma, especificar o do `id` `Microsoft.Compute/images` recurso:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, utilize a `resourceId` função para obter o ID de recurso da imagem criada no mesmo modelo. Se criou previamente a imagem gerida do disco, deve fornecer a identificação dessa imagem. Esta identificação deve ser do formulário: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>` .


## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

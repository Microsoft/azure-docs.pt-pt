---
title: Referência a uma imagem personalizada em um modelo de conjunto de escala Azure
description: Saiba como adicionar uma imagem personalizada a um modelo de conjunto de escala de máquina virtual Azure existente
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimckitt
ms.openlocfilehash: 3965090239949b5e1116ceebe427728e49ffafe4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273703"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto](virtual-machine-scale-sets-mvss-start.md) de escala básica para implantar a partir de imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo
Num [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo básico de conjunto de escala. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa um conjunto de escala a partir de uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criar uma imagem de disco gerida

Se já tem uma imagem de disco gerida `Microsoft.Compute/images`sob medida (um recurso de tipo), então pode saltar esta secção.

Em primeiro `sourceImageVhdUri` lugar, adicione um parâmetro, que é o URI à bolha generalizada no Armazenamento Azure que contém a imagem personalizada para implantar.


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

Em seguida, adicione `Microsoft.Compute/images`um recurso de tipo , que é a imagem `sourceImageVhdUri`de disco gerida com base na bolha generalizada localizada na URI . Esta imagem deve estar na mesma região que o conjunto de escala que a utiliza. Nas propriedades da imagem, especifique o tipo DE Os, a localização da bolha (a partir do `sourceImageVhdUri` parâmetro) e o tipo de conta de armazenamento:

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

No recurso de conjunto `dependsOn` de escala, adicione uma cláusula que se refere à imagem personalizada para se certificar de que a imagem é criada antes que o conjunto de escala tente ser implantado a partir dessa imagem:

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

No `imageReference` conjunto `storageProfile`de escala , em vez de especificar a editora, oferecer, sku `id` e `Microsoft.Compute/images` versão de uma imagem de plataforma, especificar o do recurso:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, utilize `resourceId` a função para obter o ID de recurso da imagem criada no mesmo modelo. Se criou previamente a imagem gerida do disco, deve fornecer a identificação dessa imagem. Esta identificação deve ser `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`do formulário: .


## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

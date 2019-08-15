---
title: Fazer referência a uma imagem personalizada em um modelo do conjunto de dimensionamento do Azure | Microsoft Docs
description: Saiba como adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento de máquinas virtuais do Azure existente
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: 2ed75a72360253996471034b001e12e8190cf733
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935270"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo do conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implantar a partir de uma imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo
Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md) , criamos um modelo de conjunto de dimensionamento básico. Agora vamos usar esse modelo anterior e modificá-lo para criar um modelo que implanta um conjunto de dimensionamento de uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criando uma imagem de disco gerenciado

Se você já tiver uma imagem de disco gerenciado personalizada (um recurso do `Microsoft.Compute/images`tipo), poderá ignorar esta seção.

Primeiro, adicione um `sourceImageVhdUri` parâmetro, que é o URI para o blob generalizado no armazenamento do Azure que contém a imagem personalizada da qual implantar.


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

Em seguida, adicione um recurso do `Microsoft.Compute/images`tipo, que é a imagem do disco gerenciado com base no blob generalizado localizado `sourceImageVhdUri`no URI. Essa imagem deve estar na mesma região que o conjunto de dimensionamento que a utiliza. Nas propriedades da imagem, especifique o tipo de sistema operacional, o local do blob (do `sourceImageVhdUri` parâmetro) e o tipo de conta de armazenamento:

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

No recurso do conjunto de dimensionamento, `dependsOn` adicione uma cláusula referindo-se à imagem personalizada para garantir que a imagem seja criada antes que o conjunto de dimensionamento tente implantar a partir dessa imagem:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterando as propriedades do conjunto de dimensionamento para usar a imagem do disco gerenciado

No do conjunto `storageProfile`de dimensionamento, em vez de especificar o editor, a oferta, a SKU e a versão de uma imagem de `id` plataforma `Microsoft.Compute/images` , especifique o do recurso: `imageReference`

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, use a `resourceId` função para obter a ID de recurso da imagem criada no mesmo modelo. Se você tiver criado a imagem de disco gerenciado com antecedência, deverá fornecer a ID dessa imagem em vez disso. Essa ID deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximos Passos

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

---
title: Fazer referência a uma imagem personalizada em um modelo do conjunto de dimensionamento do Azure
description: Saiba como adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento de máquinas virtuais do Azure existente
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275597"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo do conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implantar a partir de uma imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo
Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md) , criamos um modelo de conjunto de dimensionamento básico. Agora vamos usar esse modelo anterior e modificá-lo para criar um modelo que implanta um conjunto de dimensionamento de uma imagem personalizada.  

### <a name="creating-a-managed-disk-image"></a>Criando uma imagem de disco gerenciado

Se você já tiver uma imagem de disco gerenciado personalizada (um recurso do tipo `Microsoft.Compute/images`), poderá ignorar esta seção.

Primeiro, adicione um parâmetro `sourceImageVhdUri`, que é o URI para o blob generalizado no armazenamento do Azure que contém a imagem personalizada da qual implantar.


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

Em seguida, adicione um recurso do tipo `Microsoft.Compute/images`, que é a imagem do disco gerenciado com base no blob generalizado localizado no URI `sourceImageVhdUri`. Essa imagem deve estar na mesma região que o conjunto de dimensionamento que a utiliza. Nas propriedades da imagem, especifique o tipo de sistema operacional, o local do blob (do parâmetro `sourceImageVhdUri`) e o tipo de conta de armazenamento:

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

No recurso do conjunto de dimensionamento, adicione uma cláusula `dependsOn` referindo-se à imagem personalizada para garantir que a imagem seja criada antes que o conjunto de dimensionamento tente implantar a partir dessa imagem:

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

Na `imageReference` do conjunto de dimensionamento `storageProfile`, em vez de especificar o editor, a oferta, a SKU e a versão de uma imagem de plataforma, especifique a `id` do recurso de `Microsoft.Compute/images`:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, use a função `resourceId` para obter a ID de recurso da imagem criada no mesmo modelo. Se você tiver criado a imagem de disco gerenciado com antecedência, deverá fornecer a ID dessa imagem em vez disso. Essa ID deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximos Passos

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

---
title: Fazer referência a uma imagem personalizada num modelo de conjunto de dimensionamento do Azure | Documentos da Microsoft
description: Saiba como adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento de Máquina Virtual do Azure existente
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
ms.openlocfilehash: 2415d0dc2b9a2c4229d9910b42eb8ec9309ac7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869100"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implementar a partir da imagem personalizada.

## <a name="change-the-template-definition"></a>Altere a definição do modelo
Num [artigo anterior](virtual-machine-scale-sets-mvss-start.md) que criamos um modelo de conjunto de dimensionamento básico. Iremos agora utilizar esse modelo anterior e modificá-lo para criar um modelo que implementa um conjunto a partir de uma imagem personalizada de dimensionamento.  

### <a name="creating-a-managed-disk-image"></a>Criar uma imagem de disco gerido

Se já tiver uma imagem de disco gerido personalizado (um recurso do tipo `Microsoft.Compute/images`), em seguida, pode ignorar esta secção.

Primeiro, adicione um `sourceImageVhdUri` parâmetro, que é o URI para o blob generalizado no armazenamento do Azure que contém a imagem personalizada para implementar a partir de.


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

Em seguida, adicione um recurso do tipo `Microsoft.Compute/images`, que é a imagem de disco gerido com base no blob generalizado localizado no URI `sourceImageVhdUri`. Esta imagem tem de ser na mesma região que o conjunto de dimensionamento que o utiliza. Nas propriedades da imagem, especifique o tipo de SO, a localização do blob (da `sourceImageVhdUri` parâmetro) e o tipo de conta de armazenamento:

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

O dimensionamento do conjunto de recursos, adicione um `dependsOn` cláusula referir-se para a imagem personalizada para se certificar de que a imagem é criada antes do conjunto de dimensionamento tenta implementar a partir dessa imagem:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterar o dimensionamento a definir as propriedades para utilizar a imagem de disco gerido

Na `imageReference` da escala definida `storageProfile`, em vez de especificar o publicador, oferta, sku, e especificar a versão de uma imagem de plataforma, o `id` da `Microsoft.Compute/images` recursos:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, utilize o `resourceId` função para obter o ID de recurso da imagem criada no mesmo modelo. Se tiver criado a imagem de disco gerido com antecedência, deve fornecer o ID da imagem em vez disso. Este ID deve ter o formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximos Passos

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

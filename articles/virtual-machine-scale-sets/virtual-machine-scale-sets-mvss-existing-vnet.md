---
title: Fazer referência a uma rede virtual existente no modelo de conjunto de dimensionamento do Azure | Documentos da Microsoft
description: Saiba como adicionar uma rede virtual a um modelo de conjunto de dimensionamento de Máquina Virtual do Azure existente
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 06/27/2017
ms.date: 11/30/2018
ms.author: v-junlch
ms.openlocfilehash: 1dcb97a94bd5790edc2e40acf890bb47baec7a4b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108032"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente num modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento mínimo viável](./virtual-machine-scale-sets-mvss-start.md) para implementar numa rede virtual existente, em vez de criar um novo.

## <a name="change-the-template-definition"></a>Altere a definição do modelo

Pode ser visto o modelo de conjunto de dimensionamento mínimo viável [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e o modelo para implementar o conjunto de dimensionamento numa rede virtual existente pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Vamos examinar o diff utilizado para criar este modelo (`git diff minimum-viable-scale-set existing-vnet`) parte por parte:

Primeiro, adicione um `subnetId` parâmetro. Esta cadeia é passada para a configuração de conjunto de dimensionamento, permitindo que o conjunto de dimensionamento para identificar a sub-rede previamente criada para implementar máquinas virtuais em. Esta cadeia tem de ser do formulário: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Por exemplo, para implementar o dimensionamento conjunto numa rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg`e subscrição `00000000-0000-0000-0000-000000000000`, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Em seguida, elimine o recurso de rede virtual do `resources` matriz, à medida que utilize uma rede virtual existente e não precisa de implementar um novo.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A rede virtual já existe antes do modelo é implementado, pelo que é necessário especificar uma cláusula de dependsOn do conjunto de dimensionamento para a rede virtual. Elimine as seguintes linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Finalmente, passar o `subnetId` parâmetro definido pelo utilizador (em vez de usar `resourceId` para obter o ID de uma vnet na mesma implementação, que é o que o dimensionamento mínimo viável definir modelo faz).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

<!-- Update_Description: update metedata properties -->
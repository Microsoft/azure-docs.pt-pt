---
title: Referência a uma rede virtual existente em um modelo de conjunto de escala Azure
description: Saiba como adicionar uma rede virtual a um modelo de conjunto de escala de máquina virtual Azure existente
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273669"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicione referência a uma rede virtual existente num modelo de conjunto de escala Azure

Este artigo mostra como modificar o [modelo de conjunto](virtual-machine-scale-sets-mvss-start.md) de escala básica para implantar numa rede virtual existente em vez de criar uma nova.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Num [artigo anterior,](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo básico de conjunto de escala. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implemente uma escala definida numa rede virtual existente. 

Primeiro, adicione `subnetId` um parâmetro. Esta cadeia é passada para a configuração do conjunto de escala, permitindo que o conjunto de escala identifique a sub-rede pré-criada para implantar máquinas virtuais. Esta corda deve ser da forma:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo, para implantar a escala definida numa `myvnet`rede `mysubnet`virtual existente `myrg`com `00000000-0000-0000-0000-000000000000`nome, subnet, grupo `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`de recursos, e subscrição, o subnetId seria: .

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

Em seguida, elimine o `resources` recurso de rede virtual da matriz, uma vez que utiliza uma rede virtual existente e não precisa de implementar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
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

A rede virtual já existe antes de o modelo ser implementado, pelo que não há necessidade de especificar uma cláusula dependsOn da escala definida para a rede virtual. Eliminar as seguintes linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por fim, `subnetId` passe no parâmetro definido pelo `resourceId` utilizador (em vez de utilizar para obter o ID de uma vnet na mesma implementação, que é o que o modelo de conjunto de escala viável básico faz).

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




## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

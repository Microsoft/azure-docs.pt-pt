---
title: Referência de uma rede virtual existente em um modelo de conjunto de escala Azure
description: Saiba como adicionar uma rede virtual a um modelo de conjunto de escala de máquina virtual Azure existente
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 04/26/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: fab6e6742fa43e1e38ee661b67896ae4aa11b3ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83124827"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicione referência a uma rede virtual existente num modelo de conjunto de escala Azure

Este artigo mostra como modificar o [modelo de conjunto de escala básica](virtual-machine-scale-sets-mvss-start.md) para implementar numa rede virtual existente em vez de criar um novo.

## <a name="change-the-template-definition"></a>Alterar a definição de modelo

Num [artigo anterior](virtual-machine-scale-sets-mvss-start.md) tínhamos criado um modelo de conjunto de escala básica. Vamos agora usar esse modelo anterior e modificá-lo para criar um modelo que implementa uma escala definida numa rede virtual existente. 

Primeiro, adicione um `subnetId` parâmetro. Esta cadeia é transmitida para a configuração do conjunto de escala, permitindo que a escala definida identifique a sub-rede pré-criada para implantar máquinas virtuais. Esta corda deve ser da forma: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo, para implantar a escala definida numa rede virtual existente com `myvnet` nome, sub-rede, `mysubnet` grupo de recursos e `myrg` `00000000-0000-0000-0000-000000000000` subscrição, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet` .

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

Em seguida, elimine o recurso de rede virtual da `resources` matriz, uma vez que utiliza uma rede virtual existente e não precisa de implementar uma nova.

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

A rede virtual já existe antes do modelo ser implementado, pelo que não há necessidade de especificar uma cláusula dependOn da escala definida para a rede virtual. Eliminar as seguintes linhas:

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

Por fim, passe o `subnetId` parâmetro definido pelo utilizador (em vez de utilizar para obter o `resourceId` ID de um vnet na mesma implementação, que é o que o modelo de conjunto de escala viável básico faz).

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

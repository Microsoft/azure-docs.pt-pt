---
title: Fazer referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure
description: Saiba como adicionar uma rede virtual a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275540"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente em um modelo de conjunto de dimensionamento do Azure

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implantar em uma rede virtual existente em vez de criar um novo.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md) , criamos um modelo de conjunto de dimensionamento básico. Agora, usaremos esse modelo anterior e o modificaremos para criar um modelo que implanta um conjunto de dimensionamento em uma rede virtual existente. 

Primeiro, adicione um parâmetro `subnetId`. Essa cadeia de caracteres é passada para a configuração do conjunto de dimensionamento, permitindo que o conjunto de dimensionamento identifique a sub-rede criada previamente na qual implantar máquinas virtuais. Essa cadeia de caracteres deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo, para implantar o conjunto de dimensionamento em uma rede virtual existente com o nome `myvnet`, `mysubnet`de sub-rede, `myrg`de grupo de recursos e `00000000-0000-0000-0000-000000000000`de assinatura, a subnetid seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Em seguida, exclua o recurso de rede virtual da matriz `resources`, pois você usa uma rede virtual existente e não precisa implantar uma nova.

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

A rede virtual já existe antes de o modelo ser implantado, portanto, não é necessário especificar uma cláusula depending do conjunto de dimensionamento para a rede virtual. Exclua as seguintes linhas:

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

Por fim, passe o parâmetro `subnetId` definido pelo usuário (em vez de usar `resourceId` para obter a ID de uma vnet na mesma implantação, que é o que o modelo básico do conjunto de dimensionamento viável).

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

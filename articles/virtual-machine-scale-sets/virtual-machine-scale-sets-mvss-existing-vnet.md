---
title: Referência de uma rede virtual existente em um modelo de conjunto de escala Azure
description: Saiba como adicionar uma rede virtual a um modelo de conjunto de escala de máquina virtual Azure existente
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076671"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Referência de uma rede virtual existente em um modelo de conjunto de escala Azure

Este artigo mostra como modificar o [modelo de conjunto de escala básica](virtual-machine-scale-sets-mvss-start.md) para implementar numa rede virtual existente em vez de criar um novo.

## <a name="prerequisites"></a>Pré-requisitos

Num artigo anterior tínhamos criado um [modelo de conjunto de escala básica.](virtual-machine-scale-sets-mvss-start.md) Você precisará desse modelo anterior para que você possa modificá-lo para criar um modelo que implementa uma escala definida em uma rede virtual existente.

## <a name="identify-subnet"></a>Identificar sub-rede

Primeiro, adicione um `subnetId` parâmetro. Esta cadeia é transmitida para a configuração do conjunto de escala, permitindo que a escala definida identifique a sub-rede pré-criada para implantar máquinas virtuais. Esta corda deve ser da forma:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Por exemplo, para implantar a escala definida numa rede virtual existente com `myvnet` nome, sub-rede, `mysubnet` grupo de recursos e `myrg` `00000000-0000-0000-0000-000000000000` subscrição, o subnetId seria: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

## <a name="delete-extra-virtual-network-resource"></a>Eliminar recurso de rede virtual extra

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
## <a name="remove-dependency-clause"></a>Remover cláusula de dependência

A rede virtual já existe antes do modelo ser implementado, pelo que não há necessidade de especificar uma `dependsOn` cláusula da escala definida para a rede virtual. Eliminar as seguintes linhas:

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

## <a name="pass-subnet-parameter"></a>Parâmetro de sub-rede de passagem

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

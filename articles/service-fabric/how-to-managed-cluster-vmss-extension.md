---
title: Adicione uma extensão de conjunto de escala de máquina virtual a um tipo de nó de cluster gerido por Tecido de Serviço (pré-visualização)
description: Aqui está como adicionar uma extensão de conjunto de escala de máquina virtual um tipo de nó de cluster gerido de tecido de serviço
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: 64df4b82795f382e176d66dc61470296447b9e29
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788083"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Adicione uma extensão de conjunto de escala de máquina virtual a um tipo de nó de cluster gerido por Tecido de Serviço (pré-visualização)

Cada tipo de nó num cluster gerido por Tecido de Serviço é apoiado por um conjunto de escala de máquina virtual. Isto permite-lhe adicionar [extensões de conjunto de escala de máquina virtual](../virtual-machines/extensions/overview.md) aos tipos de nós de cluster geridos por Tecido de Serviço.

Pode adicionar uma extensão de conjunto de balança de máquina virtual a um tipo de nó utilizando o comando [Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell.

Alternadamente, pode uma extensão de conjunto de escala de máquina virtual num tipo de nó de cluster gerido por Tecido de Serviço no seu modelo de Gestor de Recursos Azure, por exemplo:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Para obter mais informações sobre a configuração dos tipos de nós de cluster geridos do Tecido de Serviço, consulte o [tipo de nó de cluster gerido](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre clusters geridos pela Service Fabric, consulte:

> [!div class="nextstepaction"]
> [Clusters geridos de tecido de serviço frequentemente fazem perguntas](./faq-managed-cluster.md)

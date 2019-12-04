---
title: Usar um modelo para implantar VMs de ponto do Azure (versão prévia)
description: Saiba como usar um modelo para implantar VMs pontuais para economizar custos.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: cynthn
ms.openlocfilehash: 2e94c48188d0eed22b338d0d7238c0d27a5d1862
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782206"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Implantar VMs Spot usando um modelo do Resource Manager

O uso de [VMs pontuais](spot-vms.md) permite que você tire proveito de nossa capacidade não utilizada a uma economia de custo significativa. A qualquer momento, quando o Azure precisar da capacidade de volta, a infraestrutura do Azure removerá as VMs pontuais. Portanto, as VMs pontuais são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

Os preços para VMs pontuais são variáveis, com base na região e SKU. Para obter mais informações, consulte preços de VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Você tem a opção de definir um preço máximo que está disposto a pagar, por hora, para a VM. O preço máximo de uma VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0.98765 USD por hora. Se você definir o preço máximo como `-1`, a VM não será removida com base no preço. O preço da VM será o preço atual para o ponto ou o preço de uma VM padrão, o que nunca é menor, desde que haja capacidade e cota disponível. Para obter mais informações sobre como definir o preço máximo, consulte [VMs pontuais – preços](spot-vms.md#pricing).

> [!IMPORTANT]
> Atualmente, as instâncias especiais estão em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Para a parte inicial da visualização pública, as instâncias Spot terão um preço fixo, portanto, não haverá nenhuma remoção baseada em preço.


## <a name="use-a-template"></a>Utilizar um modelo 

Para implantações de modelo Spot, use`"apiVersion": "2019-03-01"` ou posterior. Adicione as propriedades `priority`, `evictionPolicy` e `billingProfile` ao seu modelo: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```


> [!IMPORTANT]
> Para a parte inicial da visualização pública, você pode definir um preço máximo, mas ele será ignorado. As VMs pontuais terão um preço fixo, portanto, não haverá nenhuma remoção baseada em preço.


Aqui está um modelo de exemplo com as propriedades adicionadas para uma VM Spot. Substitua os nomes de recursos pelos seus próprios e `<password>` com uma senha para a conta de administrador local na VM.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }               
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Você também pode criar uma VM Spot usando [Azure PowerShell](../windows/spot-powershell.md) ou a [CLI do Azure](spot-cli.md).

Se você encontrar um erro, consulte [códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
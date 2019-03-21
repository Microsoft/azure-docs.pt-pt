---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261449"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Utilizar Managed Disks no modelos Azure Resource Manager

Este documento explica as diferenças entre discos geridos e, ao utilizar modelos Azure Resource Manager para aprovisionar máquinas virtuais. Os exemplos ajudá-lo a atualizar os modelos existentes que estão a utilizar discos não geridos para discos geridos. Para referência, estamos a utilizar o [101-vm-simples-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) modelo como um guia. Pode ver o modelo usando ambos [discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior utilizando [discos não geridos](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) se quiser diretamente compará-los.

## <a name="unmanaged-disks-template-formatting"></a>Modelo de discos não gerido formatação

Para começar, vamos dar uma olhada em discos como não geridos implementadas. Durante a criação de discos não geridos, terá de uma conta de armazenamento para armazenar os ficheiros VHD. Pode criar uma nova conta de armazenamento ou utilizar um já existente. Este artigo mostra-lhe como criar uma nova conta de armazenamento. Crie um recurso de conta de armazenamento no bloco de recursos, conforme mostrado abaixo.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Dentro do objeto de máquina virtual, adicione uma dependência na conta de armazenamento para se certificar de que foi criado antes da máquina virtual. Dentro do `storageProfile` secção, especifique o URI completo da localização do VHD, o que faz referência a conta de armazenamento e é necessário para o disco do SO e de quaisquer discos de dados.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Managed disks formatação de modelo

Com o Managed Disks do Azure, o disco torna-se um recurso de nível superior e já não necessita de uma conta de armazenamento a ser criada pelo utilizador. Discos geridos foram expostos pela primeira vez no `2016-04-30-preview` versão de API, elas estão disponíveis em todas as versões de API posteriores e agora são o tipo de disco predefinido. As secções seguintes percorrer as configurações padrão e como personalizar ainda mais os seus discos em pormenor.

> [!NOTE]
> É recomendado utilizar uma versão de API posterior `2016-04-30-preview` pois ocorreram alterações significativas entre `2016-04-30-preview` e `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Predefinições de disco gerido

Para criar uma VM com discos geridos, já não terá de criar o armazenamento de conta de recurso e pode atualizar o recurso de máquina virtual da seguinte forma. Especificamente tenha em atenção que o `apiVersion` reflete `2017-03-30` e o `osDisk` e `dataDisks` Consulte já não é um URI específico para o VHD. Quando implementar sem especificar propriedades adicionais, o disco utiliza um tipo de armazenamento com base no tamanho da VM. Por exemplo, se estiver a utilizar um tamanho de VM com capacidade Premium (tamanhos com "s" no respetivo nome, como Standard_D2s_v3) sistema utilizarão o armazenamento de Premium_LRS. Utilize a definição de sku do disco para especificar um tipo de armazenamento. Se não for especificado nenhum nome, ele assume o formato `<VMName>_OsDisk_1_<randomstring>` para o disco do SO e `<VMName>_disk<#>_<randomstring>` para cada disco de dados. Por predefinição, a encriptação de disco do Azure está desativada; colocação em cache é de leitura/escrita para o disco do SO e não discos de dados. Pode observar no exemplo a seguir que há ainda uma dependência de conta de armazenamento, embora isso seja apenas pelo armazenamento de diagnóstico e não é necessária para o armazenamento de disco.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Através de um recurso de nível superior de disco gerido

Como alternativa a especificar a configuração do disco no objeto de máquina virtual, pode criar um recurso de disco de nível superior e anexá-lo como parte da criação de máquina virtual. Por exemplo, pode criar um recurso de disco da seguinte forma para utilizar como um disco de dados.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dentro do objeto VM, referenciar o objeto de disco a ser ligado. Especificar o ID de recurso de disco gerido, criado no `managedDisk` propriedade permite o anexo do disco, enquanto a VM é criada. O `apiVersion` para a VM recurso está definido como `2017-03-30`. Uma dependência no recurso do disco é adicionada para garantir que é criado com êxito antes da criação da VM. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade gerido de VMS com discos geridos

Para criar gerida conjuntos de disponibilidade com VMs a utilizar discos geridos, adicione a `sku` objeto para o conjunto de disponibilidade recursos e defina a `name` propriedade `Aligned`. Esta propriedade garante que os discos para cada VM são suficientemente isolados uns dos outros para evitar pontos únicos de falha. Observe também que o `apiVersion` para a disponibilidade do conjunto de recursos está definida como `2017-03-30`.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Discos SSD Standard

Seguem-se os parâmetros necessários no modelo do Resource Manager para criar os discos de SSD Standard:

* *apiVersion* para a Microsoft. Compute tem de ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk.storageAccountType* como `StandardSSD_LRS`

A exemplo a seguir mostra a *properties.storageProfile.osDisk* secção para uma VM que utilize discos de SSD Standard:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Para obter um exemplo de modelo completo de como criar um disco Standard SSD com um modelo, veja [criar uma VM a partir de uma imagem do Windows com discos de dados padrão do SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Cenários adicionais e personalizações

Para obter informações completas sobre as especificações de REST API, consulte a [criar um disco gerido documentação da REST API](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará cenários adicionais, bem como padrão e os valores aceitáveis que podem ser submetidos para a API através de implementações de modelo. 

## <a name="next-steps"></a>Passos Seguintes

* Para modelos completos que utilizam discos geridos, visite as seguintes ligações de repositório de início rápido do Azure.
    * [VM do Windows com disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [VM do Linux com o disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Lista completa dos modelos de disco gerido](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Visite o [descrição geral de discos geridos do Azure](../articles/virtual-machines/windows/managed-disks-overview.md) documento para obter mais informações sobre os discos geridos.
* Reveja a documentação de referência de modelo para os recursos de máquina virtual ao visitar a [referência de modelo Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) documento.
* Reveja a documentação de referência de modelo para os recursos de disco, visitando a [referência de modelo Microsoft.Compute/disks](/azure/templates/microsoft.compute/disks) documento.
* Para obter informações sobre como utilizar discos geridos em conjuntos de dimensionamento de máquina virtual do Azure, visite o [utilizar discos de dados com conjuntos de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) documento.

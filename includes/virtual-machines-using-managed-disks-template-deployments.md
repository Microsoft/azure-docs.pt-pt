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
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037847"
---
Este documento atravessa as diferenças entre discos geridos e não geridos ao utilizar modelos do Gestor de Recursos Azure para fornecer máquinas virtuais. Os exemplos ajudam-no a atualizar os modelos existentes que estão a usar discos não geridos para gerir discos. Para referência, estamos a usar o modelo de [101 vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como guia. Pode ver o modelo utilizando ambos [os Discos geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior utilizando [discos não geridos](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) se quiser compará-los diretamente.

## <a name="unmanaged-disks-template-formatting"></a>Formatação de modelo de disco não gerido

Para começar, vamos ver como os discos não geridos são implantados. Ao criar discos não geridos, precisa de uma conta de armazenamento para guardar os ficheiros VHD. Pode criar uma nova conta de armazenamento ou usar uma que já existe. Este artigo mostra-lhe como criar uma nova conta de armazenamento. Crie um recurso de conta de armazenamento no bloco de recursos, como mostrado abaixo.

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

Dentro do objeto de máquina virtual, adicione uma dependência da conta de armazenamento para garantir que é criado antes da máquina virtual. Dentro `storageProfile` da secção, especifique o URI completo da localização VHD, que refere a conta de armazenamento e é necessário para o disco OS e quaisquer discos de dados.

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

## <a name="managed-disks-template-formatting"></a>Formatação de modelo de discos geridos

Com discos geridos pelo Azure, o disco torna-se um recurso de alto nível e já não necessita de uma conta de armazenamento para ser criada pelo utilizador. Os discos geridos foram `2016-04-30-preview` expostos pela primeira vez na versão API, estão disponíveis em todas as versões API subsequentes e são agora o tipo de disco padrão. As seguintes secções passam pelas definições predefinidas e detalham como personalizar ainda mais os seus discos.

> [!NOTE]
> Recomenda-se utilizar uma versão API mais tarde do que `2016-04-30-preview` a que se verificou a rutura de alterações entre `2016-04-30-preview` e `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Definições de disco geridas por padrão

Para criar um VM com discos geridos, já não precisa de criar o recurso da conta de armazenamento. Referenciando o exemplo do modelo abaixo, existem algumas diferenças em relação aos exemplos de disco não-manhosos anteriores para notar:

- Esta `apiVersion` é uma versão que suporta discos geridos.
- `osDisk`e `dataDisks` já não se refere a um URI específico para o VHD.
- Ao implantar sem especificar propriedades adicionais, o disco utilizará um tipo de armazenamento baseado no tamanho do VM. Por exemplo, se estiver a utilizar um tamanho VM que suporta armazenamento premium (tamanhos com "s" em seu nome, como Standard_D2s_v3), então os discos premium serão configurados por padrão. Pode alterá-lo utilizando a definição de sku do disco para especificar um tipo de armazenamento.
- Se não for especificado nenhum nome para `<VMName>_OsDisk_1_<randomstring>` o disco, `<VMName>_disk<#>_<randomstring>` toma o formato para o disco OS e para cada disco de dados.
  - Se um VM está a ser criado a partir de uma imagem personalizada, então as definições padrão para o tipo de conta de armazenamento e o nome do disco são recuperados das propriedades do disco definidas no recurso de imagem personalizado. Estes podem ser ultrapassados especificando valores para estes no modelo.
- Por defeito, a encriptação do disco Azure é desativada.
- Por predefinição, o cache de disco é Read/Write para o disco OS e nenhum para discos de dados.
- No exemplo abaixo ainda existe uma dependência da conta de armazenamento, embora esta seja apenas para armazenamento de diagnósticos e não seja necessária para armazenamento de discos.

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

### <a name="using-a-top-level-managed-disk-resource"></a>Usando um recurso de disco gerido de alto nível

Como alternativa à especificação da configuração do disco no objeto de máquina virtual, pode criar um recurso de disco de alto nível e fixá-lo como parte da criação da máquina virtual. Por exemplo, pode criar um recurso de disco da seguinte forma a utilizar como um disco de dados.

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

Dentro do objeto VM, consulte o objeto do disco a ser fixado. Especificar o ID de recurso do disco `managedDisk` gerido criado na propriedade permite a fixação do disco à medida que o VM é criado. O `apiVersion` recurso VM está `2017-03-30`definido para . Uma dependência do recurso do disco é adicionada para garantir que é criada com sucesso antes da criação de VM. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade geridos com VMs usando discos geridos

Para criar conjuntos de disponibilidade geridos com VMs `sku` usando discos geridos, adicione `name` o `Aligned`objeto ao recurso conjunto de disponibilidade e coloque a propriedade para . Esta propriedade garante que os discos de cada VM estão suficientemente isolados uns dos outros para evitar pontos de falha únicos. Note também `apiVersion` que o recurso definido para `2018-10-01`a disponibilidade está definido para .

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

### <a name="standard-ssd-disks"></a>Discos SSD padrão

Abaixo estão os parâmetros necessários no modelo de Gestor de Recursos para criar discos SSD standard:

* *apiVersão* para Microsoft.Compute deve `2018-04-01` ser definido como (ou mais tarde)
* Especificar *managedDisk.storageAccountType* como`StandardSSD_LRS`

O exemplo seguinte mostra as *propriedades.storageProfile.osDisk* para um VM que utiliza discos SSD padrão:

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

Para obter um exemplo completo de como criar um disco SSD padrão com um modelo, consulte [Criar um VM a partir de uma imagem do Windows com discos de dados SSD padrão](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Cenários e personalizações adicionais

Para encontrar informações completas sobre as especificações da API REST, por favor reveja a [criação de uma documentação aPI de risada gerida](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará cenários adicionais, bem como valores padrão e aceitáveis que podem ser submetidos à API através de implementações de modelos. 

## <a name="next-steps"></a>Passos seguintes

* Para modelos completos que utilizam discos geridos, visite os seguintes links Azure Quickstart Repo.
    * [Windows VM com disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM com disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Visite o documento de visão geral dos [Discos Geridos azure](../articles/virtual-machines/windows/managed-disks-overview.md) para saber mais sobre discos geridos.
* Reveja a documentação de referência do modelo para recursos de máquinas virtuais visitando o documento de referência do [modelo Microsoft.Compute/virtualMachines.](/azure/templates/microsoft.compute/virtualmachines)
* Reveja a documentação de referência do modelo para os recursos do disco visitando o documento de referência do [modelo Microsoft.Compute/disks.](/azure/templates/microsoft.compute/disks)
* Para obter informações sobre como utilizar discos geridos em conjuntos de escala de máquinas virtuais Azure, visite os discos de [dados Use com documento de conjuntos](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) de escala.

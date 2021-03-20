---
title: Implementação de discos com modelos de Gestor de Recursos Azure
description: Detalhes como usar discos geridos e não geridos em modelos Azure Resource Manager para VMs Azure.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 7c66a8b8483673a9d8fbdc9922b9cc377781bab3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91976673"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>Utilização de discos em modelos de gestor de recursos Azure

Este documento percorre as diferenças entre discos geridos e não geridos ao utilizar modelos do Azure Resource Manager para a provisionar máquinas virtuais. Os exemplos ajudam-no a atualizar os modelos existentes que estão a usar Discos não geridos para discos geridos. Para referência, estamos a usar o modelo [de janelas simples de 101 vm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como guia. Pode ver o modelo usando discos [geridos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior usando [discos não geridos](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) se quiser compará-los diretamente.

## <a name="unmanaged-disks-template-formatting"></a>Formatação do modelo de discos não geridos

Para começar, vamos ver como os discos não geridos são implantados. Ao criar discos não geridos, precisa de uma conta de armazenamento para reter os ficheiros VHD. Pode criar uma nova conta de armazenamento ou usar uma que já existe. Este artigo mostra como criar uma nova conta de armazenamento. Crie um recurso de conta de armazenamento no bloco de recursos, como mostrado abaixo.

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

Dentro do objeto da máquina virtual, adicione uma dependência na conta de armazenamento para garantir que é criada antes da máquina virtual. Dentro da `storageProfile` secção, especifique o URI completo da localização VHD, que faz referência à conta de armazenamento e é necessário para o disco de so e quaisquer discos de dados.

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

Com discos geridos Azure, o disco torna-se um recurso de topo e já não requer que uma conta de armazenamento seja criada pelo utilizador. Os discos geridos foram expostos pela primeira vez na `2016-04-30-preview` versão API, estão disponíveis em todas as versões API subsequentes e são agora o tipo de disco predefinido. As secções seguintes percorrem as definições predefinidos e detalham como personalizar ainda mais os discos.

> [!NOTE]
> Recomenda-se a utilização de uma versão API mais tarde do que `2016-04-30-preview` as alterações entre `2016-04-30-preview` e . `2017-03-30` .
>
>

### <a name="default-managed-disk-settings"></a>Definições de disco geridas por padrão

Para criar um VM com discos geridos, já não é necessário criar o recurso de conta de armazenamento. Referindo-se ao exemplo do modelo abaixo, existem algumas diferenças em comparação com os exemplos anteriores de disco não geridos para notar:

- É `apiVersion` uma versão que suporta discos geridos.
- `osDisk` e `dataDisks` já não se referir a um URI específico para o VHD.
- Ao implementar sem especificar propriedades adicionais, o disco utilizará um tipo de armazenamento baseado no tamanho do VM. Por exemplo, se estiver a utilizar um tamanho VM que suporta armazenamento premium (tamanhos com "s" em seu nome, como Standard_D2s_v3), então os discos premium serão configurados por padrão. Pode alterá-lo utilizando a definição de sku do disco para especificar um tipo de armazenamento.
- Se não for especificado nenhum nome para o disco, toma o formato do `<VMName>_OsDisk_1_<randomstring>` disco DE e para cada disco de `<VMName>_disk<#>_<randomstring>` dados.
  - Se um VM estiver a ser criado a partir de uma imagem personalizada, as definições padrão para o tipo de conta de armazenamento e o nome do disco são recuperadas a partir das propriedades do disco definidas no recurso de imagem personalizado. Estes podem ser ultrapassados especificando valores para estes no modelo.
- Por padrão, a encriptação do disco Azure é desativada.
- Por predefinição, o cache do disco é Ler/Escrever para o disco OS e nenhum para discos de dados.
- No exemplo abaixo ainda existe uma dependência da conta de armazenamento, embora esta seja apenas para armazenamento de diagnósticos e não é necessária para armazenamento de discos.

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

Como alternativa a especificar a configuração do disco no objeto da máquina virtual, pode criar um recurso de disco de alto nível e anexá-lo como parte da criação de máquinas virtuais. Por exemplo, pode criar um recurso de disco da seguinte forma a ser utilizado como um disco de dados.

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

Dentro do objeto VM, consulte o objeto do disco a ser fixado. Especificar o ID de recurso do disco gerido criado na `managedDisk` propriedade permite a fixação do disco à medida que o VM é criado. O `apiVersion` recurso para o VM está definido para `2017-03-30` . Uma dependência do recurso de disco é adicionada para garantir que é criada com sucesso antes da criação de VM. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade geridos com VMs utilizando discos geridos

Para criar conjuntos de disponibilidade geridos com VMs utilizando discos geridos, adicione o `sku` objeto ao recurso definido de disponibilidade e defina a propriedade para `name` `Aligned` . Esta propriedade garante que os discos de cada VM estão suficientemente isolados uns dos outros para evitar pontos únicos de falha. Note também que o `apiVersion` recurso para o conjunto de disponibilidade está definido para `2018-10-01` .

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

### <a name="standard-ssd-disks"></a>Discos SSD standard

Abaixo estão os parâmetros necessários no modelo de Gestor de Recursos para criar discos SSD Standard:

* *apiVersionação* para Microsoft.Compute deve ser definida como `2018-04-01` (ou mais tarde)
* Especificar *managedDisk.storageAccountType* como `StandardSSD_LRS`

O exemplo a seguir mostra a secção *properties.storageProfile.osDisk* para um VM que utiliza discos SSD Standard:

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

Para obter um exemplo completo do modelo de como criar um disco SSD standard com um modelo, consulte [Criar um VM a partir de uma imagem do Windows com discos de dados SSD padrão](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Cenários e personalizações adicionais

Para obter informações completas sobre as especificações da API REST, reveja a [documentação da API do disco gerido](/rest/api/manageddisks/disks/disks-create-or-update). Encontrará cenários adicionais, bem como valores padrão e aceitáveis que podem ser submetidos à API através de implementações de modelos. 

## <a name="next-steps"></a>Passos seguintes

* Para modelos completos que usam discos geridos, visite as seguintes ligações Azure Quickstart Repo.
    * [Windows VM com disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM com disco gerido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Visite o documento de visão geral dos [discos geridos azure](managed-disks-overview.md) para saber mais sobre discos geridos.
* Reveja a documentação de referência do modelo para recursos de máquinas virtuais visitando o documento [de referência do modelo Microsoft.Compute/virtualMachines.](/azure/templates/microsoft.compute/virtualmachines)
* Reveja a documentação de referência do modelo para os recursos do disco visitando o documento [de referência do modelo Microsoft.Compute/disks.](/azure/templates/microsoft.compute/disks)
* Para obter informações sobre como utilizar discos geridos em conjuntos de escala de máquina virtual Azure, visite os discos de dados utilizar com documento [de conjuntos de escala.](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md)
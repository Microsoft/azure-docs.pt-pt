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
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037847"
---
Este documento percorre as diferenças entre discos gerenciados e não gerenciados ao usar modelos de Azure Resource Manager para provisionar máquinas virtuais. Os exemplos ajudam você a atualizar os modelos existentes que estão usando discos não gerenciados para discos gerenciados. Para referência, estamos usando o modelo [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como um guia. Você pode ver o modelo usando [discos gerenciados](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) e uma versão anterior usando [discos não gerenciados](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) se quiser compará-los diretamente.

## <a name="unmanaged-disks-template-formatting"></a>Formatação de modelo de discos não gerenciados

Para começar, vamos dar uma olhada em como os discos não gerenciados são implantados. Ao criar discos não gerenciados, você precisa de uma conta de armazenamento para manter os arquivos VHD. Você pode criar uma nova conta de armazenamento ou usar uma já existente. Este artigo mostra como criar uma nova conta de armazenamento. Crie um recurso de conta de armazenamento no bloco de recursos, conforme mostrado abaixo.

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

Dentro do objeto de máquina virtual, adicione uma dependência na conta de armazenamento para garantir que ela seja criada antes da máquina virtual. Na seção `storageProfile`, especifique o URI completo do local do VHD, que faz referência à conta de armazenamento e é necessário para o disco do sistema operacional e quaisquer discos de dados.

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

## <a name="managed-disks-template-formatting"></a>Formatação de modelo de Managed disks

Com o Azure Managed Disks, o disco torna-se um recurso de nível superior e não requer mais uma conta de armazenamento para ser criada pelo usuário. Os discos gerenciados foram expostos primeiro na versão `2016-04-30-preview` API, eles estão disponíveis em todas as versões de API subsequentes e agora são o tipo de disco padrão. As seções a seguir percorrem as configurações padrão e detalham como personalizar ainda mais seus discos.

> [!NOTE]
> É recomendável usar uma versão de API posterior à `2016-04-30-preview`, pois houve alterações significativas entre `2016-04-30-preview` e `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Configurações padrão de disco gerenciado

Para criar uma VM com discos gerenciados, você não precisa mais criar o recurso de conta de armazenamento. Fazendo referência ao exemplo de modelo abaixo, há algumas diferenças dos exemplos de discos não-gerenciadas anteriores a serem observados:

- O `apiVersion` é uma versão que dá suporte a discos gerenciados.
- `osDisk` e `dataDisks` não se referem mais a um URI específico para o VHD.
- Ao implantar sem especificar propriedades adicionais, o disco usará um tipo de armazenamento com base no tamanho da VM. Por exemplo, se você estiver usando um tamanho de VM que dá suporte ao armazenamento Premium (tamanhos com "s" em seu nome, como Standard_D2s_v3), os discos Premium serão configurados por padrão. Você pode alterar isso usando a configuração de SKU do disco para especificar um tipo de armazenamento.
- Se nenhum nome para o disco for especificado, ele usará o formato de `<VMName>_OsDisk_1_<randomstring>` para o disco do sistema operacional e `<VMName>_disk<#>_<randomstring>` para cada disco de dados.
  - Se uma VM estiver sendo criada a partir de uma imagem personalizada, as configurações padrão para o tipo de conta de armazenamento e o nome do disco serão recuperadas das propriedades do disco definidas no recurso de imagem personalizada. Eles podem ser substituídos especificando-se valores para eles no modelo.
- Por padrão, a criptografia de disco do Azure está desabilitada.
- Por padrão, o cache de disco é de leitura/gravação para o disco do sistema operacional e nenhum para discos de dados.
- No exemplo abaixo, ainda há uma dependência de conta de armazenamento, embora isso seja apenas para o armazenamento de diagnósticos e não seja necessário para o armazenamento em disco.

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

### <a name="using-a-top-level-managed-disk-resource"></a>Usando um recurso de disco gerenciado de nível superior

Como alternativa para especificar a configuração de disco no objeto de máquina virtual, você pode criar um recurso de disco de nível superior e anexá-lo como parte da criação da máquina virtual. Por exemplo, você pode criar um recurso de disco da seguinte maneira para usar como um disco de dados.

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

No objeto da VM, faça referência ao objeto de disco a ser anexado. A especificação da ID de recurso do disco gerenciado criado na propriedade `managedDisk` permite o anexo do disco à medida que a VM é criada. O `apiVersion` para o recurso de VM é definido como `2017-03-30`. Uma dependência no recurso de disco é adicionada para garantir que ele seja criado com êxito antes da criação da VM. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Criar conjuntos de disponibilidade gerenciados com VMs usando Managed disks

Para criar conjuntos de disponibilidade gerenciados com VMs usando discos gerenciados, adicione o objeto `sku` ao recurso de conjunto de disponibilidade e defina a propriedade `name` como `Aligned`. Essa propriedade garante que os discos de cada VM estejam suficientemente isolados uns dos outros para evitar pontos únicos de falha. Observe também que o `apiVersion` para o recurso de conjunto de disponibilidade está definido como `2018-10-01`.

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

Abaixo estão os parâmetros necessários no modelo do Resource Manager para criar SSD Standard discos:

* *apiVersion* para Microsoft. Compute deve ser definido como `2018-04-01` (ou posterior)
* Especifique *managedDisk. storageAccountType* como `StandardSSD_LRS`

O exemplo a seguir mostra a seção *Properties. storageProfile. osDisk* para uma VM que usa SSD Standard discos:

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

Para obter um exemplo de modelo completo de como criar um disco de SSD Standard com um modelo, consulte [criar uma VM de uma imagem do Windows com discos de dados SSD Standard](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Cenários e personalizações adicionais

Para encontrar informações completas sobre as especificações da API REST, consulte a [documentação criar uma API REST de disco gerenciado](/rest/api/manageddisks/disks/disks-create-or-update). Você encontrará cenários adicionais, bem como valores padrão e aceitáveis que podem ser enviados para a API por meio de implantações de modelo. 

## <a name="next-steps"></a>Passos seguintes

* Para obter modelos completos que usam discos gerenciados, visite os seguintes links do repositório de início rápido do Azure.
    * [VM do Windows com disco gerenciado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [VM do Linux com disco gerenciado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Visite o documento [visão geral do Azure Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md) para saber mais sobre o Managed disks.
* Examine a documentação de referência do modelo para recursos de máquina virtual visitando o documento de [referência do modelo Microsoft. Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines) .
* Examine a documentação de referência de modelo para recursos de disco visitando o documento de [referência de modelo Microsoft. Compute/discos](/azure/templates/microsoft.compute/disks) .
* Para obter informações sobre como usar discos gerenciados em conjuntos de dimensionamento de máquinas virtuais do Azure, visite o documento [usar discos de dados com conjuntos de dimensionamento](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) .

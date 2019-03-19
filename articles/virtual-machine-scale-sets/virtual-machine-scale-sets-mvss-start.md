---
title: Saiba mais sobre modelos de conjunto de dimensionamento de máquinas virtuais | Documentos da Microsoft
description: Saiba como criar um modelo de conjunto de dimensionamento mínimo viável para conjuntos de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: d4a3dd6ae390fd48a8085cca33063a6bb74bd96c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008411"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba mais sobre modelos de conjunto de dimensionamento de máquina virtual
Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma ótima maneira de implementar grupos de recursos relacionados. Nesta série de tutoriais mostra como criar um modelo de conjunto de dimensionamento mínimo viável e como modificar este modelo de acordo com vários cenários. Todos os exemplos vêm de isso [repositório do GitHub](https://github.com/gatneil/mvss). 

Este modelo destina-se para ser simples. Para obter exemplos mais completos de dimensionamento do conjunto de modelos, consulte a [repositório do GitHub de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) e procure a pastas que contêm a cadeia de caracteres `vmss`.

Se já estiver familiarizado com a criação de modelos, pode avançar para a secção "Passos seguintes" para ver como modificar este modelo.

## <a name="review-the-template"></a>Reveja o modelo

Utilizar o GitHub para rever o modelo de conjunto de dimensionamento mínimo viável [azuredeploy. JSON](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

Neste tutorial, vamos examinar o diff (`git diff master minimum-viable-scale-set`) criar o dimensionamento mínimo viável modelo de conjunto de parte por parte.

## <a name="define-schema-and-contentversion"></a>Definir $schema e contentVersion
Em primeiro lugar, defina `$schema` e `contentVersion` no modelo. O `$schema` elemento define a versão da linguagem do modelo e é utilizado para o realce de sintaxe do Visual Studio e os recursos de validação semelhantes. O `contentVersion` elemento não é utilizado pelo Azure. Em vez disso, ajuda-o a manter o controle de versão de modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definir os parâmetros
Em seguida, defina dois parâmetros, `adminUsername` e `adminPassword`. Os parâmetros são valores que especificou no momento da implantação. O `adminUsername` parâmetro é simplesmente um `string` tipo, mas porque `adminPassword` é um proporcionar secreta, que ele escreva `securestring`. Mais tarde, esses parâmetros são transmitidos para a configuração de conjunto de dimensionamento.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definir variáveis
Modelos do Resource Manager também permitem-lhe definir as variáveis a ser utilizado mais tarde no modelo. O exemplo não utiliza quaisquer variáveis, para que o objeto JSON está vazio.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definir os recursos
Em seguida, é a seção de recursos do modelo. Aqui, é possível definir o que realmente deseja implantar. Ao contrário `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos exigem `type`, `name`, `apiVersion`, e `location` propriedades. Recurso de primeira neste exemplo tem o tipo [Network/virtualnetwork](/azure/templates/microsoft.network/virtualnetworks)e o nome `myVnet`e apiVersion `2016-03-30`. (Para obter a versão mais recente do API para um tipo de recurso, consulte a [referência de modelo do Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Especifique a localização
Para especificar a localização para a rede virtual, utilize um [função de modelo do Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Esta função deve estar entre aspas e parênteses Retos como este: `"[<template-function>]"`. Neste caso, utilize o `resourceGroup` função. Ele aceita nenhum argumento e retorna um objeto JSON com metadados sobre esta implementação está a ser implementada para o grupo de recursos. O grupo de recursos é definido pelo utilizador no momento da implantação. Este valor, em seguida, é indexado para esse objeto JSON com `.location` para obter a localização do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificar as propriedades de rede virtual
Cada recurso do Resource Manager tem o seu próprio `properties` secção para configurações específicas para o recurso. Neste caso, especifique que a rede virtual deve ter uma sub-rede com o intervalo de endereços IP privados `10.0.0.0/16`. Um conjunto de dimensionamento sempre está contido dentro de uma sub-rede. Ele não pode abranger sub-redes.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Adicionar a lista de dependsOn
Além de necessários `type`, `name`, `apiVersion`, e `location` propriedades, cada recurso podem ter um opcional `dependsOn` lista de cadeias de caracteres. Esta lista especifica que outros recursos a partir desta implementação deverá ser concluída antes de implementar este recurso.

Neste caso, há apenas um elemento na lista, a rede virtual do exemplo anterior. Especifique esta dependência porque o conjunto de dimensionamento às suas necessidades da rede de existir antes de criar todas as VMs. Dessa forma, o conjunto de dimensionamento pode permitir que estes endereços IP privados de VMs do intervalo de endereços IP anteriormente especificado nas propriedades de rede. O formato de cada cadeia na lista dependsOn é `<type>/<name>`. Utilizar o mesmo `type` e `name` utilizado anteriormente na definição do recurso de rede virtual.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Especificar as propriedades do conjunto de dimensionamento
Conjuntos de dimensionamento tem muitas propriedades para personalizar as VMs no conjunto de dimensionamento. Para obter uma lista completa dessas propriedades, consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachinescalesets). Para este tutorial, apenas algumas propriedades usadas são definidas.
### <a name="supply-vm-size-and-capacity"></a>Tamanho da VM e a capacidade de fornecer
O conjunto de dimensionamento tem de saber qual será o tamanho da VM para criar ("nome de sku") e o número de tal VMs para criar ("capacidade de sku"). Para ver os tamanhos de VM estão disponíveis, consulte a [documentação de tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Escolha o tipo de atualizações
A escala definida também precisa saber como lidar com atualizações no conjunto de dimensionamento. Atualmente, existem duas opções, `Manual` e `Automatic`. Para obter mais informações sobre as diferenças entre os dois, consulte a documentação sobre [como atualizar um conjunto de dimensionamento](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Escolha o sistema operacional VM
O conjunto de dimensionamento tem de saber qual o sistema operacional para colocar nas VMs. Aqui, crie as VMs com uma imagem de 16.04 LTS do Ubuntu totalmente corrigida.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Especifique o computerNamePrefix
O conjunto de dimensionamento implementa várias VMs. Em vez de especificar cada nome da VM, especifique `computerNamePrefix`. O conjunto de dimensionamento acrescenta um índice para o prefixo para cada VM, para que os nomes VM têm a forma `<computerNamePrefix>_<auto-generated-index>`.

No seguinte fragmento, utilize os parâmetros de antes para definir o nome de utilizador de administrador e a palavra-passe para todas as VMs no conjunto de dimensionamento. Este processo utiliza a `parameters` função de modelo. Esta função aceita uma cadeia de caracteres que especifica qual parâmetro para fazer referência e produz o valor para esse parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificar a configuração de rede VM
Por fim, especifique a configuração de rede para as VMs no conjunto de dimensionamento. Neste caso, só precisa de especificar o ID da sub-rede que criou anteriormente. Isso informa ao conjunto de dimensionamento para colocar as interfaces de rede nesta sub-rede.

Pode obter o ID da rede virtual que contém a sub-rede utilizando o `resourceId` função de modelo. Esta função aceita o tipo e o nome de um recurso e devolve o identificador completamente qualificado desse recurso. Este ID tem o formato: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

No entanto, o identificador da rede virtual não é suficiente. Fornece a sub-rede específica que o conjunto de dimensionamento de VMs deve estar no. Para tal, concatenar `/subnets/mySubnet` para o ID da rede virtual. O resultado é o identificador completamente qualificado da sub-rede. Fazer este concatenação com o `concat` função, que recebe uma série de cadeias de caracteres e retorna a concatenação.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

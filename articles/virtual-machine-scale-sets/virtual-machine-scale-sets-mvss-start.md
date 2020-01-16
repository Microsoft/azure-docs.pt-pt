---
title: Saiba mais sobre modelos do conjunto de dimensionamento de máquinas virtuais
description: Saiba como criar um modelo de conjunto de dimensionamento básico para conjuntos de dimensionamento de máquinas virtuais do Azure por meio de várias etapas simples.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 52fb7c770e9f9e2570cad92a8c0dd9bc4374e708
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980169"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba mais sobre modelos do conjunto de dimensionamento de máquinas virtuais
Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) são uma ótima maneira de implementar grupos de recursos relacionados. Esta série de tutoriais mostra como criar um modelo de conjunto de dimensionamento básico e como modificar esse modelo para atender a vários cenários. Todos os exemplos vêm deste [repositório GitHub](https://github.com/gatneil/mvss).

Esse modelo deve ser simples. Para obter exemplos mais completos de modelos de conjunto de dimensionamento, consulte o [repositório GitHub de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) e pesquise pastas que contenham a cadeia de caracteres `vmss`.

Se você já estiver familiarizado com a criação de modelos, poderá pular para a seção "próximas etapas" para ver como modificar esse modelo.

## <a name="define-schema-and-contentversion"></a>Definir $schema e contentVersion
Primeiro, defina `$schema` e `contentVersion` no modelo. O elemento `$schema` define a versão do idioma do modelo e é usado para realce de sintaxe do Visual Studio e recursos de validação semelhantes. O elemento `contentVersion` não é usado pelo Azure. Em vez disso, ele ajuda a manter o controle da versão do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definir parâmetros
Em seguida, defina dois parâmetros, `adminUsername` e `adminPassword`. Parâmetros são valores que você especifica no momento da implantação. O parâmetro `adminUsername` é simplesmente um tipo `string`, mas como `adminPassword` é um segredo, dê a ele o tipo `securestring`. Posteriormente, esses parâmetros são passados para a configuração do conjunto de dimensionamento.

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
Os modelos do Resource Manager também permitem que você defina variáveis a serem usadas posteriormente no modelo. O exemplo não usa nenhuma variável, portanto, o objeto JSON está vazio.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definir recursos
A seguir está a seção de recursos do modelo. Aqui, você define o que realmente deseja implantar. Ao contrário de `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos exigem as propriedades `type`, `name`, `apiVersion`e `location`. O primeiro recurso deste exemplo tem [o tipo Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), Name `myVnet`e apiVersion `2018-11-01`. (Para encontrar a versão mais recente da API para um tipo de recurso, consulte a [referência do modelo Azure Resource Manager](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Especificar local
Para especificar o local da rede virtual, use uma [função de modelo do Resource Manager](../azure-resource-manager/templates/template-functions.md). Essa função deve ser colocada entre aspas e colchetes como este: `"[<template-function>]"`. Nesse caso, use a função `resourceGroup`. Ele não usa nenhum argumento e retorna um objeto JSON com metadados sobre o grupo de recursos no qual essa implantação está sendo implantada. O grupo de recursos é definido pelo usuário no momento da implantação. Esse valor é então indexado nesse objeto JSON com `.location` para obter o local do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificar propriedades de rede virtual
Cada recurso do Resource Manager tem sua própria seção `properties` para configurações específicas para o recurso. Nesse caso, especifique que a rede virtual deve ter uma sub-rede usando o intervalo de endereços IP privado `10.0.0.0/16`. Um conjunto de dimensionamento sempre está contido em uma sub-rede. Ele não pode abranger sub-redes.

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

## <a name="add-dependson-list"></a>Adicionar lista de dependentes
Além das propriedades requeridas `type`, `name`, `apiVersion`e `location`, cada recurso pode ter uma lista de cadeias de `dependsOn` opcional. Esta lista especifica quais outros recursos dessa implantação devem ser concluídos antes da implantação desse recurso.

Nesse caso, há apenas um elemento na lista, a rede virtual do exemplo anterior. Você especifica essa dependência porque o conjunto de dimensionamento precisa que a rede exista antes de criar qualquer VM. Dessa forma, o conjunto de dimensionamento pode fornecer a essas VMs endereços IP privados do intervalo de endereços IP especificado anteriormente nas propriedades de rede. O formato de cada cadeia de caracteres na lista de dependentes é `<type>/<name>`. Use o mesmo `type` e `name` usados anteriormente na definição de recurso de rede virtual.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Especificar propriedades do conjunto de dimensionamento
Os conjuntos de dimensionamento têm muitas propriedades para personalizar as VMs no conjunto de dimensionamento. Para obter uma lista completa dessas propriedades, consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachinescalesets). Para este tutorial, apenas algumas propriedades usadas com frequência são definidas.
### <a name="supply-vm-size-and-capacity"></a>Fornecer capacidade e tamanho da VM
O conjunto de dimensionamento precisa saber o tamanho da VM a ser criado ("nome do SKU") e quantas dessas VMs devem ser criadas ("capacidade do SKU"). Para ver quais tamanhos de VM estão disponíveis, consulte a [documentação tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Escolher tipo de atualizações
O conjunto de dimensionamento também precisa saber como lidar com atualizações no conjunto de dimensionamento. Atualmente, há três opções, `Manual`, `Rolling` e `Automatic`. Para obter mais informações sobre as diferenças entre os dois, consulte a documentação sobre [como atualizar um conjunto de dimensionamento](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Escolher sistema operacional da VM
O conjunto de dimensionamento precisa saber qual sistema operacional deve ser colocado nas VMs. Aqui, crie as VMs com uma imagem completa com patch do Ubuntu 16, 4-LTS.

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

### <a name="specify-computernameprefix"></a>Especificar computerNamePrefix
O conjunto de dimensionamento implanta várias VMs. Em vez de especificar cada nome de VM, especifique `computerNamePrefix`. O conjunto de dimensionamento acrescenta um índice ao prefixo para cada VM, para que os nomes de VM tenham o formato `<computerNamePrefix>_<auto-generated-index>`.

No trecho a seguir, use os parâmetros de antes para definir o nome de usuário e a senha do administrador para todas as VMs no conjunto de dimensionamento. Esse processo usa a função de modelo `parameters`. Essa função usa uma cadeia de caracteres que especifica o parâmetro para o qual se refere e gera o valor para esse parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificar configuração de rede VM
Por fim, especifique a configuração de rede para as VMs no conjunto de dimensionamento. Nesse caso, você só precisa especificar a ID da sub-rede criada anteriormente. Isso informa ao conjunto de dimensionamento para colocar os adaptadores de rede nesta sub-rede.

Você pode obter a ID da rede virtual que contém a sub-rede usando a função de modelo `resourceId`. Essa função usa o tipo e o nome de um recurso e retorna o identificador totalmente qualificado desse recurso. Essa ID tem o formato: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

No entanto, o identificador da rede virtual não é suficiente. Forneça a sub-rede específica em que as VMs do conjunto de dimensionamento devem estar. Para fazer isso, concatene `/subnets/mySubnet` para a ID da rede virtual. O resultado é a ID totalmente qualificada da sub-rede. Faça essa concatenação com a função `concat`, que usa uma série de cadeias de caracteres e retorna sua concatenação.

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

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

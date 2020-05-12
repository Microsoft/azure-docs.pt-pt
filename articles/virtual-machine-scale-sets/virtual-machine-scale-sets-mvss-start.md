---
title: Saiba sobre modelos de conjunto de escala de máquina virtual
description: Aprenda a criar um modelo de conjunto de escala básica para conjuntos de escala de máquinas virtuais Azure através de vários passos simples.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: af2f000b9f9a7bf64898c46b3126cf180802b445
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198129"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba sobre modelos de conjunto de escala de máquina virtual
Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) são uma ótima maneira de implementar grupos de recursos relacionados. Esta série tutorial mostra como criar um modelo de conjunto de escala básica e como modificar este modelo para se adequar a vários cenários. Todos os exemplos vêm deste [repositório GitHub.](https://github.com/gatneil/mvss)

Este modelo destina-se a ser simples. Para obter exemplos mais completos de modelos de conjunto de escala, consulte o [repositório Azure Quickstart Templates GitHub](https://github.com/Azure/azure-quickstart-templates) e procure por pastas que contenham a cadeia `vmss` .

Se já está familiarizado com a criação de modelos, pode saltar para a secção "Próximos passos" para ver como modificar este modelo.

## <a name="define-schema-and-contentversion"></a>Definir $schema e conteúdoVersão
Primeiro, defina `$schema` e `contentVersion` no modelo. O elemento define a versão da linguagem do modelo e é usado para realçar a `$schema` sintaxe do Estúdio Visual e funcionalidades de validação semelhantes. O `contentVersion` elemento não é utilizado pelo Azure. Em vez disso, ajuda-o a acompanhar a versão do modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Definir parâmetros
Em seguida, defina dois parâmetros, `adminUsername` e `adminPassword` . Os parâmetros são valores que especifica no momento da implantação. O `adminUsername` parâmetro é simplesmente um `string` tipo, mas por ser um `adminPassword` segredo, dá-lhe `securestring` tipo. Mais tarde, estes parâmetros são passados para a configuração do conjunto de escala.

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
Os modelos do Gestor de Recursos também permitem definir variáveis a serem usadas mais tarde no modelo. O exemplo não usa variáveis, por isso o objeto JSON está vazio.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definir recursos
Em seguida, a secção de recursos do modelo. Aqui, define o que realmente quer implementar. Ao contrário `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos `type` `name` requerem, e `apiVersion` `location` propriedades. O primeiro recurso deste exemplo tem o tipo [Microsoft.Network/virtualNetwork,](/azure/templates/microsoft.network/virtualnetworks)nome `myVnet` e apiVersion `2018-11-01` . (Para encontrar a mais recente versão API para um tipo de recurso, consulte a referência do modelo do Gestor de [Recursos Azure](/azure/templates/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Especificar a localização
Para especificar a localização da rede virtual, utilize uma [função](../azure-resource-manager/templates/template-functions.md)de modelo de Gestor de Recursos . Esta função deve ser fechada em aspas e suportes quadrados como este: `"[<template-function>]"` . Neste caso, utilize a `resourceGroup` função. Não tem argumentos e devolve um objeto JSON com metadados sobre o grupo de recursos para o que esta implementação está a ser implementada. O grupo de recursos é definido pelo utilizador no momento da implantação. Este valor é então indexado neste objeto JSON com `.location` para obter a localização do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificar propriedades de rede virtual
Cada recurso do Gestor de Recursos tem a sua própria `properties` secção para configurações específicas do recurso. Neste caso, especifique que a rede virtual deve ter uma sub-rede utilizando a gama de endereços IP privado `10.0.0.0/16` . Um conjunto de escala está sempre contido numa sub-rede. Não pode abranger subredes.

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

## <a name="add-dependson-list"></a>Adicionar lista depende
Além das propriedades `type` necessárias, `name` e `apiVersion` `location` propriedades, cada recurso pode ter uma lista opcional `dependsOn` de cordas. Esta lista especifica quais os outros recursos desta implantação devem terminar antes de implantar este recurso.

Neste caso, há apenas um elemento na lista, a rede virtual do exemplo anterior. Especifica esta dependência porque o conjunto de escala precisa que a rede exista antes de criar quaisquer VMs. Desta forma, o conjunto de escala pode dar a estes VMs endereços IP privados da gama de endereços IP previamente especificadas nas propriedades da rede. O formato de cada corda na lista depende é `<type>/<name>` . Utilize o mesmo `type` e usado anteriormente na definição de recursos de `name` rede virtual.

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
## <a name="specify-scale-set-properties"></a>Especificar propriedades definidas por escala
Os conjuntos de escala têm muitas propriedades para personalizar os VMs no conjunto de escala. Para obter uma lista completa destas propriedades, consulte a referência do [modelo](/azure/templates/microsoft.compute/virtualmachinescalesets). Para este tutorial, apenas algumas propriedades comumente usadas são definidas.
### <a name="supply-vm-size-and-capacity"></a>Tamanho e capacidade vm de fornecimento
O conjunto de escala precisa de saber qual o tamanho do VM para criar ("nome sku") e quantos VMs para criar ("capacidade de sku"). Para ver quais tamanhos vm estão disponíveis, consulte a [documentação vm sizes](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Escolha o tipo de atualizações
O conjunto de escala também precisa de saber como lidar com as atualizações no conjunto de escala. Atualmente, existem três `Manual` opções, `Rolling` `Automatic` e. Para obter mais informações sobre as diferenças entre os dois, consulte a documentação sobre como atualizar um conjunto de [escalas](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Escolha o sistema operativo VM
O conjunto de escala precisa de saber que sistema operativo colocar nos VMs. Aqui, crie os VMs com uma imagem Ubuntu 16.04-LTS totalmente remendada.

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
O conjunto de escala implanta vários VMs. Em vez de especificar cada nome VM, especifique `computerNamePrefix` . O conjunto de escala afixa um índice ao prefixo para cada VM, pelo que os nomes vm têm o formulário `<computerNamePrefix>_<auto-generated-index>` .

No seguinte corte, utilize os parâmetros de antes para definir o nome de utilizador e a palavra-passe do administrador para todos os VMs no conjunto de escala. Este processo utiliza a função do `parameters` modelo. Esta função leva numa cadeia que especifica a que parâmetro se referir e produz o valor para esse parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificar a configuração da rede VM
Por fim, especifique a configuração da rede para os VMs no conjunto de escala. Neste caso, basta especificar a identificação da subnet criada anteriormente. Isto indica a escala definida para colocar as interfaces de rede nesta sub-rede.

Pode obter a identificação da rede virtual que contém a sub-rede utilizando a `resourceId` função do modelo. Esta função tem o tipo e o nome de um recurso e devolve o identificador totalmente qualificado desse recurso. Este ID tem o formulário:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

No entanto, o identificador da rede virtual não é suficiente. Forneça a sub-rede específica em que os VMs definidos em escala devem estar. Para isso, concatena `/subnets/mySubnet` à identificação da rede virtual. O resultado é a identificação totalmente qualificada da sub-rede. Faça esta concatenação com a `concat` função, que leva uma série de cordas e devolve a sua concatenação.

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

---
title: Converter um modelo de conjunto de dimensionamento do Azure Resource Manager para utilizar o disco gerido | Documentos da Microsoft
description: Converta um modelo de conjunto de dimensionamento para um modelo de conjunto de dimensionamento de disco gerido.
keywords: conjuntos de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: b2d1738b85799079b3af7ab39c5cb1799a38d382
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731742"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Converter um modelo de conjunto de dimensionamento de um modelo de conjunto de dimensionamento de disco gerido

Os clientes com um modelo do Resource Manager para a criação de um conjunto de dimensionamento não utilizar o disco gerido, podem querer modificá-lo para utilizar o disco gerido. Este artigo mostra como utilizar discos geridos, utilizando como exemplo, um pedido pull a partir da [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates), um repositório dirigidas pela Comunidade para modelos do Resource Manager de exemplo. O pedido pull completa pode ser visto aqui: [ https://github.com/Azure/azure-quickstart-templates/pull/2998 ](https://github.com/Azure/azure-quickstart-templates/pull/2998), e as partes relevantes de diferenças são abaixo, juntamente com explicações:

## <a name="making-the-os-disks-managed"></a>Tornando os discos de SO geridos

Nas seguintes diferenças, várias variáveis relacionadas às propriedades de disco e de conta de armazenamento são removidas. Tipo de conta de armazenamento já não é necessário (Standard_LRS é o padrão), mas pode especificar se assim o desejar. Apenas Standard_LRS e Premium_LRS são suportadas com disco gerido. Novo sufixo de conta de armazenamento, a matriz de cadeia de caracteres exclusivo e a contagem de sa eram utilizadas no modelo antigo para gerar nomes de conta de armazenamento. Estas variáveis, já não são necessárias no novo modelo porque o disco gerido cria automaticamente as contas de armazenamento em nome do cliente. Da mesma forma, nome do contentor de vhd e nome do disco já não são necessárias porque o disco gerido automaticamente nomes a contentores de BLOBs de armazenamento e discos subjacentes.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Nas seguintes diferenças, computa a que API é atualizada para a versão 2016-04-30-pré-visualização, que é a versão mais antiga necessária para o suporte de disco gerido com conjuntos de dimensionamento. Poderia usar discos não geridos na nova versão de API com a sintaxe antiga, se assim o desejar. Se atualizar a versão de API de computação apenas e não altere mais nada, o modelo deve continuar a funcionar como antes.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Nas seguintes diferenças, o recurso de conta de armazenamento é removido da matriz de recursos completamente. O recurso já não é necessário porque o disco gerido cria-los automaticamente.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Nas seguintes diferenças, podemos ver que estamos a remover o depende da cláusula de referência do conjunto de dimensionamento para o loop que estava criando contas de armazenamento. No modelo antigo, isso era garantir que as contas de armazenamento foram criadas antes do conjunto de dimensionamento começou a criação, mas esta cláusula já não é necessária com disco gerido. A propriedade de contentores de vhd é também removida, juntamente com a propriedade de nome de disco do SO como essas propriedades são manipuladas automaticamente em segundo plano por disco gerido. Poderia adicionar `"managedDisk": { "storageAccountType": "Premium_LRS" }` na configuração de "osDisk" se quisesse os discos de SO de premium. Apenas as VMs com uma letra maiúscula ou do minúscula "na VM sku pode utilizar os discos premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Não existe nenhuma propriedade explícita na configuração de conjunto de dimensionamento para se deve utilizar o disco gerido ou não gerido. O conjunto de dimensionamento sabe quais pode ser utilizado com base nas propriedades que estão presentes no perfil de armazenamento. Portanto, é importante ao modificar o modelo para se certificar de que as propriedades corretas no perfil de armazenamento do conjunto de dimensionamento.


## <a name="data-disks"></a>Discos de dados

Com as alterações acima, os dimensionamento conjunto utiliza discos geridos para o sistema operativo disco, mas e quanto a discos de dados? Para adicionar discos de dados, adicione a propriedade de "dataDisks" em "storageProfile" no mesmo nível, como "osDisk". O valor da propriedade é uma lista JSON de objetos, cada um deles tem propriedades "lun" (que tem de ser exclusivo por disco de dados numa VM), "createOption" ("vazia" está atualmente a única opção suportada) e "diskSizeGB" (o tamanho do disco em gigabytes; tem de ser maior que 0 e inferior a 1024) como no exemplo seguinte:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se especificar `n` discos nessa matriz, cada VM à escala definida obtém `n` discos de dados. Observe, no entanto, que estes discos de dados são dispositivos sem formato. Não são formatados. É responsabilidade do cliente para anexar, partição e formatar os discos antes de os utilizar. Opcionalmente, também pode especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que deve ser um disco de dados premium. Apenas as VMs com uma letra maiúscula ou do minúscula "na VM sku pode utilizar os discos premium.

Para saber mais sobre como utilizar discos de dados com conjuntos de dimensionamento, veja [este artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passos Seguintes
Por exemplo modelos do Resource Manager, utilizando conjuntos de dimensionamento, procure "vmss" na [repositório do GitHub de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, veja a [página de aterrissagem principal para conjuntos de dimensionamento](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


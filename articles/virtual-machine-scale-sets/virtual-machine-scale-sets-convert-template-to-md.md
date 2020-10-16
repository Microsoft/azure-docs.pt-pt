---
title: Converter um modelo de conjunto de dimensionamento para utilizar um disco gerido
description: Converta um modelo de conjunto de escala de máquina virtual Azure Resource Manager para um modelo de conjunto de escala de disco gerido.
keywords: conjuntos de dimensionamento de máquinas virtuais
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 03cbe4eb56f3b3b99f87048b699f76b30b7937c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85373969"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Converter um modelo de conjunto de escala para um modelo de conjunto de escala de disco gerido

Os clientes com um modelo de Gestor de Recursos para criar um conjunto de escala que não utilize o disco gerido podem desejar modificá-lo para utilizar o disco gerido. Este artigo mostra como usar discos geridos, usando como exemplo um pedido de puxar dos [Modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates), um repo orientado pela comunidade para modelos de gestor de recursos de amostra. O pedido completo de puxar pode ser visto aqui: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) , e as partes relevantes do diff estão abaixo, juntamente com explicações:

## <a name="making-the-os-disks-managed"></a>Fazer os discos de SO geridos

No diff seguinte, várias variáveis relacionadas com a conta de armazenamento e propriedades do disco são removidas. O tipo de conta de armazenamento já não é necessário (Standard_LRS é o padrão), mas pode especibilizá-lo se desejar. Apenas Standard_LRS e Premium_LRS são suportados com disco gerido. Novos sufixos de conta de armazenamento, matriz de cordas únicas e sa count foram usados no modelo antigo para gerar nomes de conta de armazenamento. Estas variáveis já não são necessárias no novo modelo porque o disco gerido cria automaticamente contas de armazenamento em nome do cliente. Da mesma forma, o nome do recipiente vhd e o nome do disco oss já não são necessários porque o disco gerido designa automaticamente os recipientes e discos de armazenamento subjacentes.

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


No seguinte diff, o seu cálculo API é atualizado para a versão 2016-04-30-preview, que é a versão mais antiga necessária para suporte a discos geridos com conjuntos de escala. Pode utilizar discos não geridos na nova versão API com a sintaxe antiga, se desejar. Se atualizar apenas a versão API do computação e não alterar mais nada, o modelo deve continuar a funcionar como antes.

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

Na sequência seguinte, o recurso da conta de armazenamento é completamente removido do conjunto de recursos. O recurso já não é necessário, uma vez que o disco gerido os cria automaticamente.

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

No seguinte, podemos ver que estamos a remover a cláusula que se refere da escala definida para o loop que estava a criar contas de armazenamento. No modelo antigo, isto era garantir que as contas de armazenamento foram criadas antes do conjunto de escala começar a ser criado, mas esta cláusula já não é necessária com o disco gerido. A propriedade dos contentores vhd também é removida, juntamente com a propriedade do nome do disco OS, uma vez que estas propriedades são automaticamente manuseadas sob o capot por disco gerido. Pode adicionar `"managedDisk": { "storageAccountType": "Premium_LRS" }` a configuração "osDisk" se quiser discos DESemescados premium. Apenas os VM com um 's' maiúscula ou inferior no sku VM podem utilizar discos premium.

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

Não existe uma propriedade explícita na configuração definida na escala para se utilizar disco gerido ou não gerido. O conjunto de escala sabe qual usar com base nas propriedades que estão presentes no perfil de armazenamento. Assim, é importante ao modificar o modelo para garantir que as propriedades certas estão no perfil de armazenamento do conjunto de escala.


## <a name="data-disks"></a>Discos de dados

Com as alterações acima, o conjunto de escala utiliza discos geridos para o disco DE, mas e os discos de dados? Para adicionar discos de dados, adicione a propriedade "dataDisks" em "storageProfile" ao mesmo nível que "osDisk". O valor do imóvel é uma lista de objetos JSON, cada um dos quais tem propriedades "lun" (que deve ser única por disco de dados em VM), "createOption" ("empty" ("empty" é atualmente a única opção suportada) e "diskSizeGB" (o tamanho do disco em gigabytes; deve ser superior a 0 e menos de 1024) como no seguinte exemplo:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se especificar `n` discos nesta matriz, cada VM no conjunto de escala obtém `n` discos de dados. Note-se, no entanto, que estes discos de dados são dispositivos brutos. Não estão formatados. Cabe ao cliente anexar, dividir e formatar os discos antes de os utilizar. Opcionalmente, também pode especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que deve ser um disco de dados premium. Apenas os VM com um 's' maiúscula ou inferior no sku VM podem utilizar discos premium.

Para saber mais sobre a utilização de discos de dados com conjuntos de escala, consulte [este artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passos seguintes
Por exemplo, os modelos do Gestor de Recursos utilizando conjuntos de escala, procure por "vmss" nos [modelos Azure Quickstart GitHub repo](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, consulte a página principal de [aterragem para conjuntos de escalas](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


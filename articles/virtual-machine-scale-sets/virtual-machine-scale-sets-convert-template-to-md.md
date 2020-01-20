---
title: Converter um modelo de conjunto de dimensionamento para utilizar um disco gerido
description: Converta um modelo de conjunto de dimensionamento de máquinas virtuais Azure Resource Manager em um modelo de conjunto de dimensionamento de disco gerenciado.
keywords: conjuntos de dimensionamento de máquinas virtuais
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278116"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Converter um modelo de conjunto de dimensionamento em um modelo de conjunto de dimensionamento de disco gerenciado

Os clientes com um modelo do Resource Manager para criar um conjunto de dimensionamento não usando disco gerenciado podem querer modificá-lo para usar o disco gerenciado. Este artigo mostra como usar discos gerenciados, usando como um exemplo de uma solicitação de pull dos [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates), um repositório controlado pela Comunidade para modelos do Resource Manager de exemplo. A solicitação pull completa pode ser vista aqui: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), e as partes relevantes da comparação estão abaixo, juntamente com explicações:

## <a name="making-the-os-disks-managed"></a>Tornando os discos do sistema operacional gerenciados

Na comparação a seguir, várias variáveis relacionadas à conta de armazenamento e às propriedades do disco são removidas. O tipo de conta de armazenamento não é mais necessário (Standard_LRS é o padrão), mas você pode especificá-lo se desejado. Somente Standard_LRS e Premium_LRS têm suporte com o disco gerenciado. O novo sufixo de conta de armazenamento, a matriz de cadeia de caracteres exclusiva e a contagem SA foram usados no modelo antigo para gerar nomes de conta de armazenamento. Essas variáveis não são mais necessárias no novo modelo porque o disco gerenciado cria automaticamente contas de armazenamento em nome do cliente. Da mesma forma, o nome do contêiner do VHD e o nome do disco do so não são mais necessários porque o disco gerenciado nomeia automaticamente os contêineres e discos do blob de armazenamento subjacente.

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


Na comparação a seguir, a API de computação é atualizada para a versão 2016-04-30-Preview, que é a versão mais antiga necessária para suporte de disco gerenciado com conjuntos de dimensionamento. Você pode usar discos não gerenciados na nova versão de API com a sintaxe antiga, se desejado. Se você atualizar apenas a versão da API de computação e não alterar mais nada, o modelo deverá continuar a funcionar como antes.

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

Na comparação a seguir, o recurso de conta de armazenamento é removido da matriz de recursos completamente. O recurso não é mais necessário, pois o disco gerenciado os cria automaticamente.

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

Na diferença a seguir, podemos ver que estamos removendo a cláusula depends on do conjunto de dimensionamento para o loop que estava criando contas de armazenamento. No modelo antigo, isso estava garantindo que as contas de armazenamento foram criadas antes da criação do conjunto de dimensionamento começar, mas essa cláusula não é mais necessária com o disco gerenciado. A propriedade contêineres VHD também é removida, juntamente com a propriedade nome do disco do sistema operacional, pois essas propriedades são manipuladas automaticamente nos bastidores pelo disco gerenciado. Você pode adicionar `"managedDisk": { "storageAccountType": "Premium_LRS" }` na configuração "osDisk" se quisesse discos do sistema operacional Premium. Somente as VMs com letras maiúsculas ou minúsculas no SKU da VM podem usar discos Premium.

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

Não há nenhuma propriedade explícita na configuração do conjunto de dimensionamento para se usar disco gerenciado ou não gerenciado. O conjunto de dimensionamento sabe qual usar com base nas propriedades presentes no perfil de armazenamento. Portanto, é importante ao modificar o modelo para garantir que as propriedades certas estejam no perfil de armazenamento do conjunto de dimensionamento.


## <a name="data-disks"></a>Discos de dados

Com as alterações acima, o conjunto de dimensionamento usa discos gerenciados para o disco do sistema operacional, mas e quanto aos discos de dados? Para adicionar discos de dados, adicione a propriedade "datadisks" em "storageProfile" no mesmo nível que "osDisk". O valor da propriedade é uma lista JSON de objetos, cada um com propriedades "LUN" (que deve ser exclusiva por disco de dados em uma VM), "createoption" ("Empty" atualmente a única opção com suporte) e "diskSizeGB" (o tamanho do disco em gigabytes; deve ser maior que 0 e menor que 1024) como no exemplo a seguir:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se você especificar `n` discos nessa matriz, cada VM no conjunto de dimensionamento obterá `n` discos de dados. No entanto, observe que esses discos de dados são dispositivos brutos. Eles não estão formatados. Cabe ao cliente anexar, particionar e formatar os discos antes de usá-los. Opcionalmente, você também pode especificar `"managedDisk": { "storageAccountType": "Premium_LRS" }` em cada objeto de disco de dados para especificar que ele deve ser um disco de dados Premium. Somente as VMs com letras maiúsculas ou minúsculas no SKU da VM podem usar discos Premium.

Para saber mais sobre como usar discos de dados com conjuntos de dimensionamento, confira [Este artigo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passos seguintes
Para obter exemplos de modelos do Resource Manager usando conjuntos de dimensionamento, pesquise por "vmss" no [repositório GitHub de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter informações gerais, confira a [página de aterrissagem principal para conjuntos de dimensionamento](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


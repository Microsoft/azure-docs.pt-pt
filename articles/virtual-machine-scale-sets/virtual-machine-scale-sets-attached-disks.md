---
title: Discos de Dados Anexados a Conjuntos de Dimensionamento de Máquinas Virtuais do Azure | Microsoft Docs
description: Saiba como utilizar discos de dados anexados com conjuntos de dimensionamento de máquinas virtuais
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
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: 5482e082e3e37d279c4374a8642f2cb6db588dd5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781668"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Conjunto de dimensionamento de máquinas virtuais do Azure e discos de dados anexados
Para expandir o armazenamento disponível, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure suportam instâncias de VM com discos de dados anexados. Pode anexar os discos de dados quando o conjunto de dimensionamento é criado ou a um conjunto de dimensionamento existente.

> [!NOTE]
> Quando cria um conjunto de dimensionamento com discos de dados anexados, tem de montar e formatar os discos a partir de uma VM para poder utilizá-los (tal como acontece com VMs do Azure autónomas). Uma forma conveniente de executar este processo é utilizar uma Extensão de Script Personalizado que chama um script para particionar e formatar todos os discos de dados numa VM. Para obter exemplos deste processo, veja [CLI do Azure](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Criar e gerir discos num conjunto de dimensionamento
Para obter informações detalhadas sobre como criar um conjunto de dimensionamento com discos de dados anexados, preparar e formatar ou adicionar e remover discos de dados, veja um dos seguintes tutoriais:

- [CLI do Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

O resto deste artigo descreve casos de utilização específicos, tais como clusters do Service Fabric que necessitam de discos de dados ou anexar discos de dados existentes com conteúdo a um conjunto de dimensionamento.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Criar um cluster do Service Fabric com discos de dados anexados
Cada [tipo de nó](../service-fabric/service-fabric-cluster-nodetypes.md) num cluster do [Service Fabric](/azure/service-fabric) em execução no Azure é suportado por um conjunto de dimensionamento de máquinas virtuais. Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric. Pode utilizar um [modelo existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como um ponto de partida. No modelo, inclua uma secção _dataDisks_ no _storageProfile_ do(s) recursos _Microsoft.Compute/virtualMachineScaleSets_ e implemente o modelo. O exemplo seguinte anexa um disco de dados de 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Pode automaticamente criar partições, formatar e montar os discos de dados quando o cluster é implementado. Adicione uma extensão de script personalizado ao _extensionProfile_ do _virtualMachineProfile_ do(s) conjunto(s) de dimensionamento.

Para preparar automaticamente o(s) disco(s) de dados num cluster do Windows, adicione o seguinte:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Para preparar automaticamente o(s) disco(s) de dados num cluster do Linux, adicione o seguinte:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Adicionar discos de dados pré-preenchidos a um conjunto de dimensionamento existente
Os discos de dados especificados no modelo de conjunto de dimensionamento estão sempre vazios. No entanto, pode anexar um disco de dados existente a uma VM específica num conjunto de dimensionamento. Esta funcionalidade está em pré-visualização, com exemplos ativada [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Se quiser propagar dados em todas as VMs no conjunto de dimensionamento, pode duplicar o disco de dados e anexá-lo a cada VM no conjunto de dimensionamento, pode criar uma imagem personalizada que contenha os dados e fornecer o conjunto de dimensionamento a partir desta imagem personalizada, ou pode utilizar Ficheiros do Azure ou uma oferta de armazenamento de dados semelhante.


## <a name="additional-notes"></a>Notas adicionais
O suporte para o Azure Managed Disks e para discos de dados anexados a conjuntos de dimensionamento está disponível na versão da API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) ou posterior da API Microsoft.Compute.

O suporte do portal do Azure para discos de dados anexados em conjuntos de dimensionamento é limitado inicialmente. Dependendo das suas necessidades, pode utilizar modelos do Azure, a CLI, o PowerShell, SDKs e a API REST para gerir discos anexados.



---
title: Azure virtual máquina conjuntos de discos de dados anexados
description: Aprenda a usar discos de dados anexados com conjuntos de escala de máquina virtual através de contornos de casos de utilização específicos.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86527443"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Conjunto de dimensionamento de máquinas virtuais do Azure e discos de dados anexados
Para expandir o armazenamento disponível, os [conjuntos de dimensionamento de máquinas virtuais](./index.yml) do Azure suportam instâncias de VM com discos de dados anexados. Pode anexar os discos de dados quando o conjunto de dimensionamento é criado ou a um conjunto de dimensionamento existente.

> [!NOTE]
> Quando cria um conjunto de dimensionamento com discos de dados anexados, tem de montar e formatar os discos a partir de uma VM para poder utilizá-los (tal como acontece com VMs do Azure autónomas). Uma forma conveniente de executar este processo é utilizar uma Extensão de Script Personalizado que chama um script para particionar e formatar todos os discos de dados numa VM. Para obter exemplos deste processo, veja [CLI do Azure](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Criar e gerir discos num conjunto de dimensionamento
Para obter informações detalhadas sobre como criar um conjunto de dimensionamento com discos de dados anexados, preparar e formatar ou adicionar e remover discos de dados, veja um dos seguintes tutoriais:

- [CLI do Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

O resto deste artigo descreve casos de utilização específicos, tais como clusters do Service Fabric que necessitam de discos de dados ou anexar discos de dados existentes com conteúdo a um conjunto de dimensionamento.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Criar um cluster do Service Fabric com discos de dados anexados
Cada [tipo de nó](../service-fabric/service-fabric-cluster-nodetypes.md) num cluster do [Service Fabric](../service-fabric/index.yml) em execução no Azure é suportado por um conjunto de dimensionamento de máquinas virtuais. Ao utilizar um modelo do Azure Resource Manager, pode anexar discos de dados aos conjuntos de dimensionamento que compõem o cluster do Service Fabric. Pode utilizar um [modelo existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como um ponto de partida. No modelo, inclua uma secção _dataDisks_ no _storageProfile_ do(s) recursos _Microsoft.Compute/virtualMachineScaleSets_ e implemente o modelo. O exemplo seguinte anexa um disco de dados de 128 GB:

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
Os discos de dados especificados no modelo de conjunto de dimensionamento estão sempre vazios. No entanto, pode anexar um disco de dados existente a uma VM específica num conjunto de dimensionamento. Se pretender propagar dados em todos os VMs do conjunto de escala, poderá duplicar o seu disco de dados e anexá-lo a cada VM no conjunto de escala, ou criar uma imagem personalizada que contenha os dados e forre a escala definida a partir desta imagem personalizada, ou poderá utilizar ficheiros Azure ou uma oferta de armazenamento de dados semelhante.


## <a name="additional-notes"></a>Notas adicionais
O suporte para o Azure Managed Disks e para discos de dados anexados a conjuntos de dimensionamento está disponível na versão da API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) ou posterior da API Microsoft.Compute.

O suporte do portal Azure para discos de dados anexados em conjuntos de escala é limitado. Dependendo das suas necessidades, pode utilizar modelos do Azure, a CLI, o PowerShell, SDKs e a API REST para gerir discos anexados.

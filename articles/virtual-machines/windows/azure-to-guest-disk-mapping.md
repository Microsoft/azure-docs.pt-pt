---
title: Como mapear discos Azure para discos de hóspedes Windows VM
description: Como determinar os Discos Azure que estão na base dos discos de hóspedes do Windows VM.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 7a4fad066af37217eb42060d5fc5a7ef716770c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560995"
---
# <a name="how-to-map-azure-disks-to-windows-vm-guest-disks"></a>Como mapear discos Azure para discos de hóspedes Windows VM

Poderá ser necessário determinar os Discos Azure que apoiam os discos de hóspedes de um VM. Em alguns cenários, pode comparar o tamanho do disco ou volume com o tamanho dos Discos Azure anexados. Em cenários em que existem vários Discos Azure do mesmo tamanho ligados ao VM é necessário utilizar o Número de Unidade Lógica (LUN) dos discos de dados. 

## <a name="what-is-a-lun"></a>O que é um LUN?

Um Número de Unidade Lógica (LUN) é um número que é usado para identificar um dispositivo de armazenamento específico. A cada dispositivo de armazenamento é atribuído um identificador numérico único, a partir de zero. O caminho completo para um dispositivo é representado pelo número do autocarro, número de ID do alvo e número de unidade lógica (LUN). 

Por exemplo: ***Número de autocarro 0, Target ID 0, LUN 3***

Para o nosso exercício, só precisa usar o LUN.

## <a name="finding-the-lun"></a>Encontrar o LUN

Existem dois métodos para encontrar o LUN, que você escolhe vai depender se você estiver usando [Espaços de Armazenamento](/windows-server/storage/storage-spaces/overview) ou não.

### <a name="disk-management"></a>Gestão de Disco

Se não estiver a utilizar piscinas de armazenamento, pode utilizar a [Gestão do Disco](/windows-server/storage/disk-management/overview-of-disk-management) para encontrar o LUN.

1. Ligue-se ao VM e abra a Gestão do Disco a. Clique com o botão 'Direito' no botão Iniciar e escolha "Gestão do Disco" a. Também pode digitar `diskmgmt.msc` na caixa 'Iniciar'
1. No painel inferior, clique com o botão direito em qualquer um dos Discos e escolha "Propriedades"
1. O LUN será listado na propriedade "Localização" no separador "Geral"

### <a name="storage-pools"></a>Piscinas de Armazenamento

1. Ligue-se ao VM e abra o Gestor do Servidor
1. Selecione "Serviços de Arquivo e Armazenamento", "Volumes", "Piscinas de Armazenamento"
1. No canto inferior direito do Server Manager, haverá uma secção "Discos Físicos". Os discos que compõem o Pool de Armazenamento estão listados aqui, bem como o LUN para cada disco.

## <a name="finding-the-lun-for-the-azure-disks"></a>Encontrar o LUN para os Discos Azure

Pode localizar o LUN para um Disco Azure utilizando o portal Azure CLI ou Azure PowerShell

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Encontrar um LUN de Disco Azure no portal Azure

1. No portal Azure, selecione "Máquinas Virtuais" para apresentar uma lista das suas Máquinas Virtuais
1. Selecione a Máquina Virtual
1. Selecione "Discos"
1. Selecione um disco de dados da lista de discos anexos.
1. O LUN do disco será apresentado no painel de detalhes do disco. O LUN apresentado aqui correlaciona-se com os LUNs que foram olhados para cima no Guest usando o Gestor de Dispositivos ou o Gestor do Servidor.

### <a name="finding-an-azure-disks-lun-using-azure-cli-or-azure-powershell"></a>Encontrar um LUN de disco Azure usando Azure CLI ou Azure PowerShell

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
$vm.StorageProfile.DataDisks | ft
```
---
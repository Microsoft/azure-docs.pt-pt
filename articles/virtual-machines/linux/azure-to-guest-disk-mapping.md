---
title: Como mapear discos Azure para discos de hóspedes Linux VM
description: Como determinar os Discos Azure que estão na base dos discos de hóspedes do Linux VM.
author: timbasham
ms.service: virtual-machines-linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 4f0e48bf1c14728c54d4e89f30700017b0420d7d
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523704"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Como mapear discos Azure para discos de hóspedes Linux VM

Poderá ser necessário determinar os Discos Azure que apoiam os discos de hóspedes de um VM. Em alguns cenários, pode comparar o tamanho do disco ou volume com o tamanho dos Discos Azure anexados. Em cenários em que existem vários Discos Azure do mesmo tamanho ligados ao VM é necessário utilizar o Número de Unidade Lógica (LUN) dos discos de dados. 

## <a name="what-is-a-lun"></a>O que é um LUN?

Um Número de Unidade Lógica (LUN) é um número que é usado para identificar um dispositivo de armazenamento específico. A cada dispositivo de armazenamento é atribuído um identificador numérico único, a partir de zero. O caminho completo para um dispositivo é representado pelo número do autocarro, número de ID do alvo e número de unidade lógica (LUN). 

Por exemplo: ***Número de autocarro 0, Target ID 0, LUN 3** _

Para o nosso exercício, só precisa usar o LUN.

## <a name="finding-the-lun"></a>Encontrar o LUN

Abaixo listamos dois métodos para encontrar o LUN de um disco em Linux.

### <a name="lsscsi"></a>Rio Issscsi

1. Ligar à VM
1. `sudo lsscsi`

A primeira coluna listada conterá o LUN, o formato é [Host:Channel:Target:_*LUN**].

### <a name="listing-block-devices"></a>Dispositivos de bloqueio de listagem

1. Ligar à VM
1. `sudo ls -l /sys/block/*/device`

A última coluna listada conterá o LUN, o formato é [Host:Channel:Target:**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>Encontrar o LUN para os Discos Azure

Pode localizar o LUN para um Disco Azure utilizando o portal Azure CLI.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Encontrar um LUN de Disco Azure no portal Azure

1. No portal Azure, selecione "Máquinas Virtuais" para apresentar uma lista das suas Máquinas Virtuais
1. Selecione a Máquina Virtual
1. Selecione "Discos"
1. Selecione um disco de dados da lista de discos anexos.
1. O LUN do disco será apresentado no painel de detalhes do disco. O LUN apresentado aqui correlaciona-se com os LUNs que procurou no Guest usando o Isssi, ou listando os dispositivos de bloqueio.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Encontrar um LUN de Disco Azure usando O Azure CLI

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

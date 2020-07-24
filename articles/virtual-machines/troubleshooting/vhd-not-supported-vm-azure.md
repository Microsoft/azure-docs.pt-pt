---
title: O VHD não é suportado quando se cria uma máquina virtual no Azure Microsoft Docs
description: Este artigo ajuda a corrigir erros de VHD ao executar uma máquina virtual no Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: a843a42de6fc1e6cd8ef788552ab4a8ac17b4e25
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999059"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>VHD não é suportado quando cria uma máquina virtual em Azure

Este artigo ajuda a corrigir erros de VHD ao executar a Máquina Virtual no Windows ou Linux.

## <a name="symptoms"></a>Sintomas

Quando cria uma máquina virtual no Microsoft Azure utilizando um VHD carregado, a implementação falha e devolve a seguinte mensagem de erro: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Causa

Este problema ocorre por uma das seguintes razões:

- O VHD não está em conformidade com o alinhamento de 1MB (deslocamento). O tamanho de disco suportado deverá ser 1 MB * N. Por exemplo, o disco deve ser de 102,401 MB.
- O VHD está corrompido ou não apoiado. 

## <a name="resolution"></a>Resolução

> [!NOTE]
> Para executar a seguinte correção, o cliente terá de executar estes passos antes de carregar o VHD para o Azure.

Para resolver este problema, redimensione o disco para cumprir o alinhamento de 1 MB:

- Para resolver o problema no Windows, utilize o [cmdlet Resize-VHD PowerShell](/powershell/module/hyper-v/resize-vhd). Note que **o Resize-VHD** não é um cmdlet Azure PowerShell.

  1. [Instale a função Hyper-V no Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Converter o disco virtual num VHD de tamanho fixo](../windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Para resolver o problema em Linux, utilize o [comando qemu-img](../linux/create-upload-generic.md).

Para obter mais informações sobre como criar e carregar um VHD para criar Azure VM, consulte os seguintes artigos:

- [Faça upload e crie um Linux VM a partir da imagem de disco personalizado utilizando o Azure CLI 1.0](../linux/upload-vhd.md)
- [Criar e carregar um VHD do Windows Server para o Azure](../windows/upload-generalized-managed.md)

Problemas contínuos podem indicar um VHD corrompido. Nesta situação, recomendamos que reconstrua o VHD do zero.

Para obter mais informações, veja os seguintes artigos:

- [Sobre o Windows VHD](../windows/managed-disks-overview.md)
- [Sobre Linux VHD](../linux/managed-disks-overview.md)

---
title: Suporte do Azure para VMs de geração 2 (pré-visualização) | Documentos da Microsoft
description: Descrição geral do suporte do Azure para VMs de geração 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 653d4baa89e28255f11df1c5e2d813d37535793a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667548"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Suporte para a geração 2 VMs (pré-visualização) no Azure

> [!IMPORTANT]
> Suporte do Azure para VMs de geração 2 está atualmente em pré-visualização. Esta versão de pré-visualização é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Suporte para máquinas de virtuais de geração 2 (VMs) está agora disponível em pré-visualização no Azure. Não é possível alterar a geração de uma máquina virtual depois de criá-lo, por isso, reveja as considerações nesta página antes de escolher uma geração. 

VMs de geração 2 suportam as principais funcionalidades que não são suportadas nas VMs de geração 1. Estas funcionalidades incluem a maior memória, extensões de proteção de Software do Intel (Intel SGX) e virtualizada de memória persistente (vPMEM). VMs de geração 2 também tem algumas funcionalidades que não são suportadas no Azure ainda. Para obter mais informações, consulte a [funcionalidades e capacidades](#features-and-capabilities) secção.

VMs de geração 2 utilizam a nova arquitetura de arranque baseadas em UEFI, em vez da arquitetura baseados em BIOS usada pela geração 1 VMs. Em comparação com VMs de geração 1, VMs de geração 2 podem melhoraram tempos de arranque e instalação. Para obter uma descrição geral das VMs de geração 2 e algumas das diferenças entre a geração 1 e geração 2, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tamanhos de VM de geração 2

VMs de geração 1 são suportadas por todos os tamanhos VM no Azure. O Azure oferece agora suporte de geração 2 de pré-visualização para a seguinte série VM selecionada:

* [Série Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) e [série Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Série ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) e [Lsv2 série](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Imagens VM de geração 2 no Azure Marketplace

VMs de geração 2 suportam as seguintes imagens do Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>No local vs. VMs de geração 2 do Azure

O Azure não suporta atualmente algumas das funcionalidades que no local suporta de Hyper-V para VMs de geração 2.

| Recurso de geração 2                | Hyper-V no local | Azure |
|-------------------------------------|---------------------|-------|
| Arranque seguro                         | :heavy_check_mark:  | :x:   |
| VM blindada                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Segurança baseada em Virtualização (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

### <a name="generation-1-vs-generation-2-features"></a>Geração 1 vs. recursos de geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|---------|--------------|--------------|
| Arranque             | PCAT                      | UEFI                               |
| Controladores do disco | IDE                       | SCSI                               |
| Tamanhos de VM         | Todos os tamanhos VM | Apenas as VMs que suportam o premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Geração 1 vs. recursos de geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|------------|--------------|--------------|
| > 2 TB de disco do SO                    | :x:                        | :heavy_check_mark: |
| Personalizados/imagem/troca de disco SO         | :heavy_check_mark:         | :heavy_check_mark: |
| Suporte de conjunto de dimensionamento de máquina virtual | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| Galeria de imagem partilhada              | :heavy_check_mark:         | :x:                |
| Encriptação de disco do Azure             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Criação de uma geração 2 VM

### <a name="marketplace-image"></a>Imagem do Marketplace

No portal do Azure ou da CLI do Azure, pode criar geração 2 VMs de uma imagem do Marketplace que suporte o arranque UEFI.

O `windowsserver-gen2preview` oferta contém apenas imagens de geração 2 em Windows. Este empacotamento evita confusão entre a geração 1 e imagens de geração 2. Para criar uma geração 2 VM, selecione **imagens** nesta oferta e siga o processo padrão para criar a VM.

Atualmente, o Marketplace oferece a geração seguinte do Windows 2 imagens:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Consulte a [funcionalidades e capacidades](#features-and-capabilities) secção para obter uma lista atual de imagens do Marketplace suportadas.

### <a name="managed-image-or-managed-disk"></a>Imagem gerida ou disco gerido

Pode criar uma geração 2 VM a partir de uma imagem gerida ou um disco gerido da mesma forma, criaria uma geração 1 VM.

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Também pode criar geração 2 VMs ao utilizar conjuntos de dimensionamento de máquina virtual. Na CLI do Azure, utilize o dimensionamento do Azure define para criar a geração 2 VMs.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **São geração 2 VMs disponíveis em todas as regiões do Azure?**  
    Sim. Mas nem todos os [tamanhos de VM de geração 2](#generation-2-vm-sizes) estão disponíveis em cada região. A disponibilidade da geração que 2 VM depende da disponibilidade do tamanho da VM.

* **Existe uma diferença de preços entre a geração 1 e geração 2 VMs?**  
    Não.

* **Como posso aumentar o tamanho do disco de SO?**  
  Mais de 2 TB de discos de SO estiver familiarizados com a geração 2 VMs. Por predefinição, os discos de SO são menores do que 2 TB para VMs de geração 2. Pode aumentar o tamanho do disco até um máximo recomendado de 4 TB. Utilize a CLI do Azure ou o portal do Azure para aumentar o tamanho do disco de SO. Para obter informações sobre como expandir os discos por meio de programação, consulte [redimensionar um disco](expand-disks.md).

  Para aumentar o tamanho do disco de SO do portal do Azure:

  1. No portal do Azure, vá para a página de propriedades VM.
  1. Para encerrar e desalocar a VM, selecione o **parar** botão.
  1. Na **discos** secção, selecione o disco de SO que deseja aumentar.
  1. Na **discos** secção, selecione **configuração**e atualizar o **tamanho** para o valor desejado.
  1. Volte à página de propriedades da VM e **iniciar** a VM.

  Poderá ver um aviso para discos de SO maiores que 2 TB. O aviso não se aplica a VMs de geração 2. No entanto, tamanhos de disco de SO superiores a 4 TB são *não recomendado.*

* **Fazer a geração 2 VMs suporte accelerated networking?**  
    Sim. Para obter mais informações, consulte [criar uma VM com o funcionamento em rede acelerado](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **VHDX é suportado na geração 2?**  
    Não, as VMs de geração 2 suportam apenas o VHD.

* **VMs de geração 2 suportam Ultra armazenamento em disco do Azure?**  
    Sim.

* **Posso migrar uma VM de geração 1 para geração 2?**  
    Não, não é possível alterar a geração de uma VM depois de o criar. Se precisa de alternar entre gerações de VM, crie uma nova VM da geração de diferente.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [máquinas de virtuais de geração 2 no Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

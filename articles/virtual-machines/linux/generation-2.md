---
title: VMs de geração 2 (pré-visualização) no Azure | Documentos da Microsoft
description: Descrição geral das VMs de geração 2 do Azure
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: lahugh
ms.openlocfilehash: e6bb947503371e379e4d4972ddfc3614e129174b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835200"
---
# <a name="generation-2-vms-preview-on-azure"></a>VMs de geração 2 (pré-visualização) no Azure

> [!IMPORTANT]
> VMs de geração 2 estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Suporte para máquinas de virtuais de geração 2 (VMs) está agora disponível em pré-visualização pública no Azure. Não é possível alterar a geração de uma máquina virtual depois de criá-lo. Por isso, recomendamos que reveja as considerações [aqui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) , bem como as informações nesta página antes de escolher uma geração.

Funcionalidades principais do suporte de VMs de geração 2, como: aumentou memória, a Intel® Software Guard extensões (SGX) e a memória persistente virtual (vPMEM), que não são suportadas em VMs de geração 1. As VMs de geração 2 têm algumas funcionalidades que não são suportadas no Azure ainda. Para obter mais informações, consulte a [funcionalidades e capacidades](#features-and-capabilities) secção. 

VMs de geração 2 utilizam os novo vs de arquitetura de arranque baseadas em UEFI, a arquitetura baseados em BIOS utilizada por VMs de geração 1. Em comparação com VMs de geração 1, VMs de geração 2 podem melhoraram tempos de arranque e instalação. Para obter uma descrição geral das VMs de geração 2 e algumas das principais diferenças entre a geração 1 e geração 2, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tamanhos de VM de geração 2

VMs de geração 1 são suportadas por todos os tamanhos VM no Azure. O Azure oferece agora suporte de geração 2 para a seguinte série VM selecionada em pré-visualização pública:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) e [série Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2-series](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Série ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) e [Lsv2 série](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Imagens VM de geração 2 no Azure Marketplace

VMs de geração 2 suportam as seguintes imagens do Azure Marketplace:

* Servidor de Windows 2019 Datacenter
* Servidor do Windows 2016 Datacenter
* Servidor do Windows 2012 R2 Datacenter
* Servidor do Windows 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>No local vs VMs de geração 2 do Azure

Azure não suporta atualmente algumas das funcionalidades que no local suporta de Hyper-V para VMs de geração 2.

| Recurso de geração 2                | Hyper-V no local | Azure |
|-------------------------------------|---------------------|-------|
| Arranque seguro                         | :heavy_check_mark:  | :x:   |
| VM blindada                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Segurança baseada em Virtualização (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

### <a name="generation-1-vs-generation-2-features"></a>Recursos de geração 2 vs de geração 1

| Funcionalidade | Geração 1 | Geração 2 |
|---------|--------------|--------------|
| Arranque             | PCAT                      | UEFI                               |
| Controladores do disco | IDE                       | SCSI                               |
| Tamanhos de VMs         | Disponível em todos os tamanhos VM | O armazenamento Premium suportado VMs apenas |

### <a name="generation-1-vs-generation-2-capabilities"></a>Recursos de geração 2 vs de geração 1

| Capacidade | Geração 1 | Geração 2 |
|------------|--------------|--------------|
| > 2 TB de disco do SO                    | :x:                        | :heavy_check_mark: |
| SO de disco/imagem/troca personalizado         | :heavy_check_mark:         | :heavy_check_mark: |
| Suporte de conjunto de dimensionamento de máquina virtual | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/cópia de segurança                        | :heavy_check_mark:         | :x:                |
| Galeria de Imagens Partilhadas              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Criação de uma geração 2 VM

### <a name="marketplace-image"></a>Imagem do Marketplace

VMs de geração 2 podem ser criadas a partir de uma imagem do marketplace (que suporta o arranque UEFI) através do portal do Azure ou a CLI do Azure.

O `windowsserver-gen2preview` oferta contém apenas imagens de geração 2 em Windows. Isso evita a confusão com respeito a imagens de geração 2 vs de geração 1. Para criar a geração 2 VMs, selecione **imagens** nesta oferta e siga o processo de criação de VM standard.

Atualmente, a geração seguinte do Windows 2 imagens são publicadas no Azure Marketplace:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Consulte a secção de recursos para obter uma lista de imagens do marketplace suportados como continuaremos a adicionar imagens adicionais que suportam a geração 2.

### <a name="managed-image-or-managed-disk"></a>Imagem gerida ou disco gerido

Geração 2 VMs podem ser criadas a partir de imagem gerida ou um disco gerido da mesma forma, criaria uma geração 1 VM.

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Geração que 2 VMs também podem ser criadas com conjuntos de dimensionamento de máquina virtual. Pode criar geração 2 VMs com conjuntos de dimensionamento de máquina virtual do Azure através da CLI do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **VMs de geração 2 suportam redes aceleradas?**  
    Sim, o suporte de VMs de geração 2 [redes aceleradas](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **. Vhdx é suportado na geração 2?**  
    Não, apenas. vhd é suportada em VMs de geração 2.

* **VMs de geração 2 suportam unidades de estado sólido Ultra (SSD)?**  
    Sim, as VMs de geração 2 suportam Ultra SSD.

* **Posso migrar de geração 1 para VMs de geração 2?**  
    Não, não é possível alterar a geração de uma VM depois de criá-lo. Se precisa de alternar entre gerações de VM, terá de criar uma nova VM da geração de diferente.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [máquinas de virtuais de geração 2 no Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
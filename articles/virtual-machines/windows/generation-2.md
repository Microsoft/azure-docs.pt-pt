---
title: Suporte do Azure para VMs de geração 2 (visualização) | Microsoft Docs
description: Visão geral do suporte do Azure para VMs de geração 2
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/10/2019
ms.author: lahugh
ms.openlocfilehash: e4572ac6041caffc6c77d74dcbb2cf52f9f0aed0
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173807"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Suporte para VMs de geração 2 (versão prévia) no Azure

> [!IMPORTANT]
> O suporte do Azure para VMs de geração 2 está atualmente em visualização.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O suporte para VMs (máquinas virtuais) de geração 2 agora está disponível em versão prévia no Azure. Você não pode alterar a geração de uma máquina virtual depois de criá-la, portanto, examine as considerações nesta página antes de escolher uma geração. 

As VMs de geração 2 oferecem suporte a recursos principais que não têm suporte em VMs de geração 1. Esses recursos incluem memória aumentada, extensões Intel SGX (Intel® software Guard) e vPMEM (memória persistente virtualizada). As VMs de geração 2 também têm alguns recursos que ainda não têm suporte no Azure. Para obter mais informações, consulte a seção [recursos e funcionalidades](#features-and-capabilities) .

As VMs de geração 2 usam a nova arquitetura de inicialização baseada em UEFI em vez da arquitetura baseada em BIOS usada pelas VMs de geração 1. Em comparação com as VMs de geração 1, as VMs de geração 2 podem ter tempos de inicialização e de instalação aprimorados. Para obter uma visão geral das VMs de geração 2 e algumas das diferenças entre a geração 1 e a geração 2, consulte [devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Tamanhos de VM de geração 2

As VMs de geração 1 têm suporte de todos os tamanhos de VM no Azure. O Azure agora oferece suporte à versão de visualização 2 para a seguinte série de VMs selecionada:

* [Série B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Série DC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* Série [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) e [série Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Série Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Série Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Série HB](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [Série HC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* Série [ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) e [série Lsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Série Mv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* Série [NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) e [série NCv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Série ND](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [Série NVv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Imagens de VM de geração 2 no Azure Marketplace

As VMs de geração 2 dão suporte às seguintes imagens do Marketplace:

* Windows Server 2019 datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>Local vs. VMs do Azure geração 2

Atualmente, o Azure não dá suporte a alguns dos recursos que o Hyper-V local dá suporte para VMs de geração 2.

| Recurso de geração 2                | Hyper-V local | Azure |
|-------------------------------------|---------------------|-------|
| Inicialização segura                         | :heavy_check_mark:  | w.x.y.   |
| VM blindada                         | :heavy_check_mark:  | w.x.y.   |
| vTPM                                | :heavy_check_mark:  | w.x.y.   |
| Segurança baseada em virtualização (VBS) | :heavy_check_mark:  | w.x.y.   |
| Formato VHDX                         | :heavy_check_mark:  | w.x.y.   |

## <a name="features-and-capabilities"></a>Recursos e funcionalidades

### <a name="generation-1-vs-generation-2-features"></a>Recursos da geração 1 versus geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|---------|--------------|--------------|
| Arranque             | PCAT                      | UEFI                               |
| Controladores de disco | IDE                       | SCSI                               |
| Tamanhos de VM         | Todos os tamanhos de VM | Somente VMs que dão suporte ao armazenamento Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Recursos de geração 1 versus geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|------------|--------------|--------------|
| Disco do sistema operacional > 2 TB                    | w.x.y.                | :heavy_check_mark: |
| Disco personalizado/imagem/sistema operacional de permuta         | :heavy_check_mark: | :heavy_check_mark: |
| Suporte ao conjunto de dimensionamento de máquinas virtuais | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | w.x.y.                |
| Backup/restauração                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria de imagens partilhadas              | :heavy_check_mark: | w.x.y.                |
| Criptografia de disco do Azure             | :heavy_check_mark: | w.x.y.                |

## <a name="creating-a-generation-2-vm"></a>Criando uma VM de geração 2

### <a name="marketplace-image"></a>Imagem do Marketplace

No portal do Azure ou CLI do Azure, você pode criar VMs de geração 2 de uma imagem do Marketplace que dá suporte à inicialização de UEFI.

#### <a name="azure-portal"></a>Portal do Azure

As imagens de geração 2 para Windows e SLES estão incluídas na mesma oferta de servidor que as imagens Gen1. O que isso significa de uma perspectiva de fluxo é que você seleciona a oferta e a SKU do portal para sua VM. Se a SKU der suporte a imagens de geração 1 e geração 2, você poderá optar por criar uma VM de geração 2 na guia *avançado* no fluxo de criação de VM.

Atualmente, as SKUs a seguir oferecem suporte a imagens de geração 1 e geração 2:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server de 2019

Quando você seleciona uma SKU do Windows Server como a oferta, na guia **avançado** , há uma opção para criar uma VM **Gen 1** (BIOS) ou **Gen 2** (UEFI). Se você selecionar **Gen 2**, verifique se o tamanho da VM selecionado na guia **noções básicas** tem [suporte para VMs de geração 2](#generation-2-vm-sizes).

![Selecione a VM Gen 1 ou Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Você também pode usar o PowerShell para criar uma VM referenciando diretamente a SKU de geração 1 ou geração 2.

Por exemplo, use o seguinte cmdlet do PowerShell para obter uma lista das SKUs na `WindowsServer` oferta.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Se você estiver criando uma VM com o Windows Server 2012 como o sistema operacional, você selecionará a SKU de VM de geração 1 (BIOS) ou de geração 2 (UEFI), que tem esta aparência:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Consulte a seção [recursos e funcionalidades](#features-and-capabilities) para obter uma lista atual de imagens do Marketplace com suporte.

### <a name="managed-image-or-managed-disk"></a>Imagem gerenciada ou disco gerenciado

Você pode criar uma VM de geração 2 de uma imagem gerenciada ou de um disco gerenciado da mesma maneira que criaria uma VM de geração 1.

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Você também pode criar VMs de geração 2 usando conjuntos de dimensionamento de máquinas virtuais. No CLI do Azure, use conjuntos de dimensionamento do Azure para criar VMs de geração 2.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **As VMs de geração 2 estão disponíveis em todas as regiões do Azure?**  
    Sim. Mas nem todos os [tamanhos de VM de geração 2](#generation-2-vm-sizes) estão disponíveis em todas as regiões. A disponibilidade da VM de geração 2 depende da disponibilidade do tamanho da VM.

* **Há uma diferença de preço entre as VMs de geração 1 e de geração 2?**  
   Não.

* **Tenho um arquivo. vhd da minha VM de geração 2 local. Posso usar esse arquivo. VHD para criar uma VM de geração 2 no Azure?**
  Sim, você pode colocar seu arquivo. VHD de geração 2 no Azure e usá-lo para criar uma VM de geração 2. Use as seguintes etapas para fazer isso:
    1. Carregue o. VHD em uma conta de armazenamento na mesma região em que você gostaria de criar sua VM.
    1. Crie um disco gerenciado com base no arquivo. vhd. Defina a propriedade de geração do Hyper-V como v2. Os comandos do PowerShell a seguir definem a propriedade de geração do Hyper-V ao criar um disco gerenciado.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Quando o disco estiver disponível, crie uma VM anexando esse disco. A VM criada será uma VM de geração 2.
    Quando a VM de geração 2 é criada, você pode, opcionalmente, generalizar a imagem dessa VM. Ao generalizar a imagem, você pode usá-la para criar várias VMs.

* **Como fazer aumentar o tamanho do disco do sistema operacional?**  
  OS discos do sistema operacional com mais de 2 TB são novos para VMs de geração 2. Por padrão, OS discos do sistema operacional são menores que 2 TB para VMs de geração 2. Você pode aumentar o tamanho do disco até um máximo recomendado de 4 TB. Use o CLI do Azure ou o portal do Azure para aumentar o tamanho do disco do sistema operacional. Para obter informações sobre como expandir discos programaticamente, consulte [redimensionar um disco](expand-os-disk.md).

  Para aumentar o tamanho do disco do sistema operacional do portal do Azure:

  1. Na portal do Azure, vá para a página de propriedades da VM.
  1. Para desligar e desalocar a VM, selecione o botão **parar** .
  1. Na seção **discos** , selecione o disco do sistema operacional que você deseja aumentar.
  1. Na seção **discos** , selecione **configuração**e atualize o **tamanho** para o valor desejado.
  1. Volte para a página de propriedades da VM e **inicie** a VM.
  
  Você pode ver um aviso para discos do sistema operacional com mais de 2 TB. O aviso não se aplica às VMs de geração 2. No entanto, OS tamanhos de disco do sistema operacional maiores que 4 TB *não são recomendados.*

* **As VMs de geração 2 dão suporte à rede acelerada?**  
    Sim. Para obter mais informações, consulte [criar uma VM com rede acelerada](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Há suporte para VHDX na geração 2?**  
    Não, as VMs de geração 2 dão suporte apenas a VHD.

* **As VMs de geração 2 dão suporte ao Azure ultra Armazenamento em Disco?**  
    Sim.

* **Posso migrar uma VM da geração 1 para a geração 2?**  
    Não, você não pode alterar a geração de uma VM depois de criá-la. Se você precisar alternar entre gerações de VM, crie uma nova VM de uma geração diferente.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as [máquinas virtuais de geração 2 no Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Saiba como [preparar um VHD](prepare-for-upload-vhd-image.md) para carregar de sistemas locais para o Azure.

---
title: Suporte azure para geração 2 VMs
description: Visão geral do suporte do Azure para as VMs da geração 2
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 3336869a5f91613849cdccb67f9d804205148608
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652527"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Apoio à geração 2 VMs em Azure

O suporte para máquinas virtuais de geração 2 (VMs) já está disponível no Azure. Não podes mudar a geração de uma máquina virtual depois de a criares, por isso reveja as considerações nesta página antes de escolheres uma geração.

Os VMs da Geração 2 suportam características-chave que não são suportadas na geração 1 VMs. Estas funcionalidades incluem memória aumentada, Extensões de Guarda de Software Intel (Intel SGX) e memória persistente virtualizada (vPMEM). Os VMs da Geração 2 que correm no local, têm algumas funcionalidades que ainda não são suportadas em Azure. Para mais informações, consulte a secção [Funcionalidades e Capacidades.](#features-and-capabilities)

Os VMs da geração 2 utilizam a nova arquitetura de botas baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs. Em comparação com os VMs da geração 1, a geração 2 VMs pode ter melhorado os tempos de arranque e instalação. Para uma visão geral da geração 2 VMs e algumas das diferenças entre a geração 1 e a geração 2, ver devo criar uma máquina virtual de [geração 1 ou 2 em Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Tamanhos VM da geração 2

Os VMs da geração 1 são suportados por todos os tamanhos vm em Azure (exceto para VMs série Mv2). O Azure oferece agora suporte à geração 2 para as seguintes séries VM selecionadas:

* [Série B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Série DCsv2](../dcv2-series.md)
* [Série DSv2](../dv2-dsv2-series.md) e [série Dsv3](../dv3-dsv3-series.md)
* [Série Esv3](../ev3-esv3-series.md)
* [Série Fsv2](../fsv2-series.md)
* [Série GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Série HB](../hb-series.md)
* [Série HC](../hc-series.md)
* [Série Ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) e [lsv2](../lsv2-series.md)
* [Série M](../m-series.md)
* [Série Mv2](../mv2-series.md)<sup>1</sup>
* [Série NCv2](../ncv2-series.md) e [série NCv3](../ncv3-series.md)
* [Série ND](../nd-series.md)
* [Série NVv3](../nvv3-series.md)

<sup>1</sup> A série Mv2 não suporta imagens VM da Geração 1 e apenas suporta um subconjunto de imagens da Geração 2. Por favor, consulte a [documentação da série Mv2](https://docs.microsoft.com/azure/virtual-machines/mv2-series) para obter mais detalhes.

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Imagens VM da Geração 2 no Mercado Azure

Os VMs da Geração 2 suportam as seguintes imagens do Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>No local vs. Azure geração 2 VMs

A Tualmente, o Azure não suporta algumas das funcionalidades que o Hyper-V suporta no local para os VMs da geração 2.

| Característica da Geração 2                | Hyper-V no local | Azure |
|-------------------------------------|---------------------|-------|
| Bota segura                         | :heavy_check_mark:  | :x:   |
| VM Blindada                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Segurança baseada em virtualização (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

### <a name="generation-1-vs-generation-2-features"></a>Geração 1 vs. geração 2 características

| Funcionalidade | Geração 1 | Geração 2 |
|---------|--------------|--------------|
| Arranque             | PCAT         | UEFI |
| Controladores de disco | IDE          | SCSI |
| Tamanhos de VM         | Todos os tamanhos VM | Apenas VMs que suportam armazenamento premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Geração 1 vs. geração 2 capacidades

| Capacidade | Geração 1 | Geração 2 |
|------------|--------------|--------------|
| Disco osso > 2 TB                    | :x:                | :heavy_check_mark: |
| Sistema de disco/imagem/swap personalizado         | :heavy_check_mark: | :heavy_check_mark: |
| Suporte de conjunto de escala de máquina virtual | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Cópia/restauro                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria de imagens partilhadas              | :heavy_check_mark: | :heavy_check_mark: |
| Encriptação de disco azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Criação de uma geração 2 VM

### <a name="marketplace-image"></a>Imagem de mercado

No portal Azure ou Azure CLI, pode criar VMs de geração 2 a partir de uma imagem do Marketplace que suporta o arranque UEFI.

#### <a name="azure-portal"></a>Portal do Azure

Abaixo estão os passos para criar uma geração 2 (Gen2) VM no portal Azure.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Selecione **Criar um recurso**.
1. Clique em **Ver tudo** a partir do Mercado Azure à esquerda.
1. Selecione uma imagem que suporte o Gen2.
1. Clique em **Criar**.
1. No separador **Avançado,** sob a secção **de geração VM,** selecione a opção **Gen 2.**
1. No separador **Basics,** Em **detalhes de Sob Instância,** vá ao **Tamanho** e abra a lâmina de **tamanho VM.**
1. Selecione uma [geração 2 VM suportada](#generation-2-vm-sizes).
1. Percorra o fluxo de criação do [portal Azure](quick-create-portal.md) para terminar a criação do VM.

![Selecione Gen 1 ou Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Também pode usar o PowerShell para criar um VM, referindo-se diretamente à geração 1 ou geração 2 SKU.

Por exemplo, utilize o seguinte cmdlet PowerShell para obter uma lista das SKUs na `WindowsServer` oferta.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Se estiver a criar um VM com o Windows Server 2012 como O, então irá selecionar a geração 1 (BIOS) ou a geração 2 (UEFI) VM SKU, que se parecem com isto:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Consulte a secção [Funcionalidades e capacidades](#features-and-capabilities) para obter uma lista atual de imagens do Marketplace suportadas.

#### <a name="azure-cli"></a>CLI do Azure

Em alternativa, pode utilizar o Azure CLI para ver quaisquer imagens de geração 2 disponíveis, listadas pela **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Imagem gerida ou disco gerido

Pode criar uma geração 2 VM a partir de uma imagem gerida ou disco gerido da mesma forma que criaria uma Geração 1 VM.

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Também pode criar VMs de geração 2 utilizando conjuntos de escala de máquinas virtuais. No Azure CLI, utilize conjuntos de escala Azure para criar vMs de geração 2.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

* **Os VMs de geração 2 estão disponíveis em todas as regiões do Azure?**  
    Yes. Mas nem todas as [dimensões vm da geração 2](#generation-2-vm-sizes) estão disponíveis em todas as regiões. A disponibilidade da geração 2 VM depende da disponibilidade do tamanho VM.

* **Existe uma diferença de preço entre a geração 1 e a geração 2 VMs?**  
    Não.

* **Tenho um ficheiro .vhd da minha geração 2 VM. Posso usar aquele ficheiro .vhd para criar uma geração 2 VM em Azure?**
  Sim, pode trazer o seu ficheiro de geração 2.vhd para o Azure e usá-lo para criar uma Geração 2 VM. Utilize os seguintes passos para o fazer:
    1. Faça o upload do .vhd para uma conta de armazenamento na mesma região onde gostaria de criar o seu VM.
    1. Crie um disco gerido a partir do ficheiro .vhd. Desloque a propriedade hyper-V Generation para V2. Os comandos PowerShell seguintes definiram a propriedade Hyper-V Generation ao criar em disco gerido.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Uma vez que o disco esteja disponível, crie um VM fixando este disco. O VM criado será uma geração 2 VM.
    Quando a geração 2 VM é criada, você pode opcionalmente generalizar a imagem deste VM. Ao generalizar a imagem pode usá-la para criar vários VMs.

* **Como aumentar o tamanho do disco osso?**  
  Os discos os maiores que 2 TB são novos na geração 2 VMs. Por padrão, os discos OS são inferiores a 2 TB para os VMs da geração 2. Pode aumentar o tamanho do disco até um máximo recomendado de 4 TB. Utilize o Azure CLI ou o portal Azure para aumentar o tamanho do disco OS. Para obter informações sobre como expandir os discos programáticamente, consulte [Redimensionar um disco](expand-disks.md).

  Para aumentar o tamanho do disco OS do portal Azure:

  1. No portal Azure, vá à página de propriedades VM.
  1. Para desligar e desalocar o VM, selecione o botão **Stop.**
  1. Na secção **Discos,** selecione o disco OS que pretende aumentar.
  1. Na secção **Discos,** selecione **Configuração**, e atualize o **Tamanho** ao valor que deseja.
  1. Volte para a página de propriedades VM e **inicie** o VM.

  Pode ver um aviso para discos de SO maiores do que 2 TB. O aviso não se aplica aos VMs da geração 2. No entanto, não são recomendados tamanhos de disco osso superiores a 4 *TB.*

* **Os VMs da geração 2 suportam o networking acelerado?**  
    Yes. Para mais informações, consulte [Criar um VM com networking acelerado](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **O VHDX é suportado na geração 2?**  
    Não, geração 2 VMs suportam apenas VHD.

* **Os VMs da geração 2 suportam o Armazenamento de Discos Ultra Azure?**  
    Yes.

* **Posso migrar um VM da geração 1 para a geração 2?**  
    Não, não podes mudar a geração de um VM depois de o criares. Se precisar de alternar entre gerações VM, crie um novo VM de uma geração diferente.

* **Porque é que o meu tamanho vm não está ativado no seletor de tamanhos quando tento criar um VM Gen2?**

    Isto pode ser resolvido fazendo o seguinte:

    1. Verifique se a propriedade de **geração VM** está definida para **a Gen 2** no separador **Advanced.**
    1. Verifique se está à procura de um [tamanho VM que suporta VMs Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Passos seguintes

* Conheça as máquinas virtuais da [geração 2 em Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

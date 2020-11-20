---
title: Suporte azul para geração 2 VMs
description: Visão geral do suporte do Azure para a geração 2 VMs
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/28/2020
ms.author: jushiman
ms.openlocfilehash: 0aedcc3be4cb319dc24990507d85756bd77777e4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963832"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Suporte para VMs de geração 2 no Azure

O suporte para máquinas virtuais de geração 2 (VMs) já está disponível no Azure. Não podes mudar a geração de uma máquina virtual depois de a criares, por isso revê as considerações desta página antes de escolheres uma geração.

A geração 2 VMs suporta características-chave que não são suportadas na geração 1 VMs. Estas funcionalidades incluem memória aumentada, extensões de guarda de software Intel (Intel SGX) e memória persistente virtualizada (vPMEM). Os VMs de geração 2 que estão no local, têm algumas funcionalidades que ainda não foram suportadas no Azure. Para mais informações, consulte a secção [Funcionalidades e Capacidades.](#features-and-capabilities)

A geração 2 VMs usa a nova arquitetura de arranque baseada na UEFI em vez da arquitetura baseada em BIOS usada pela geração 1 VMs. Em comparação com a geração 1 VMs, os VMs de geração 2 podem ter melhorado os tempos de arranque e instalação. Para uma visão geral da geração 2 VMs e algumas das diferenças entre a geração 1 e a geração 2, ver [Devo criar uma máquina virtual de geração 1 ou 2 em Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Tamanhos de geração 2 VM

Os VMs de geração 1 são suportados por todos os tamanhos VM em Azure (exceto para VMs da série Mv2). A Azure oferece agora suporte de geração 2 para as seguintes séries VM selecionadas:


* [Série B](sizes-b-series-burstable.md)
* [Série DCsv2](dcv2-series.md)
* [Série DSv2](dv2-dsv2-series.md)
* [Série Dsv3](dv3-dsv3-series.md)
* [Série Dsv4](dv4-dsv4-series.md)
* [Série Dasv4](dav4-dasv4-series.md)
* [Série Ddsv4](ddv4-ddsv4-series.md)
* [Série Esv3](ev3-esv3-series.md)
* [Série Easv4](eav4-easv4-series.md)
* [Série Fsv2](fsv2-series.md)
* [Série GS](sizes-previous-gen.md#gs-series)
* [Série HB](hb-series.md)
* [Série HC](hc-series.md)
* [Série Ls](sizes-previous-gen.md#ls-series) 
* [Série Lsv2](lsv2-series.md)
* [Série M](m-series.md)
* [Série Mv2](mv2-series.md)<sup>1</sup>
* [Série NCv2](ncv2-series.md) 
* [Série NCv3](ncv3-series.md)
* [Série ND](nd-series.md)
* [Série NVv3](nvv3-series.md)

<sup>1</sup> Mv2 série não suporta imagens De Geração 1 VM e suporta apenas um subconjunto de imagens da Geração 2. Consulte a [documentação da série Mv2](mv2-series.md) para mais detalhes.


## <a name="generation-2-vm-images-in-azure-marketplace"></a>Geração 2 Imagens VM no Azure Marketplace

A Geração 2 VMs suporta as seguintes imagens do Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Empresa Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

> [!NOTE]
> Tamanhos específicos da máquina virtual como Mv2-Series só podem suportar um subconjunto destas imagens - por favor, olhe para a documentação do tamanho da máquina virtual relevante para obter detalhes completos.

## <a name="on-premises-vs-azure-generation-2-vms"></a>No local vs. Azure geração 2 VMs

A Azure não suporta atualmente algumas das funcionalidades que o Hyper-V suporta no local para a geração 2 VMs.

| Característica da Geração 2                | Hyper-V no local | Azure |
|-------------------------------------|---------------------|-------|
| Bota segura                         | :heavy_check_mark:  | :x:   |
| VM Blindada                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Segurança baseada na virtualização (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funcionalidades e capacidades

### <a name="generation-1-vs-generation-2-features"></a>Características da geração 1 vs. geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|---------|--------------|--------------|
| Arranque             | PCAT                      | UEFI                               |
| Controladores de disco | IDE                       | SCSI                               |
| Tamanhos de VM         | Todos os tamanhos VM | [Ver tamanhos disponíveis](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Capacidades de geração 1 vs geração 2

| Funcionalidade | Geração 1 | Geração 2 |
|------------|--------------|--------------|
| Disco de oss > 2 TB                    | :x:                | :heavy_check_mark: |
| Disco/imagem/swap OS personalizados         | :heavy_check_mark: | :heavy_check_mark: |
| Suporte de conjunto de escala de máquina virtual | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Cópia de segurança/restauro                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria de imagens partilhada              | :heavy_check_mark: | :heavy_check_mark: |
| [Encriptação do disco Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :x:                |
| [Encriptação do lado do servidor](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Criar uma geração 2 VM

### <a name="marketplace-image"></a>Imagem do Marketplace

No portal Azure ou no Azure CLI, pode criar geração 2 VMs a partir de uma imagem marketplace que suporta a bota UEFI.

#### <a name="azure-portal"></a>Portal do Azure

Abaixo estão os passos para criar uma VM de geração 2 (Gen2) no portal Azure.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. Selecione **Criar um recurso**.
1. Clique **em Ver tudo** a partir do Azure Marketplace à esquerda.
1. Selecione uma imagem que suporte a Gen2.
1. Clique em **Criar**.
1. No separador **Avançado,** na secção **de geração VM,** selecione a opção **Gen 2.**
1. No **separador Basics,** detalhes de Under **Instance,** vá ao **Tamanho** e abra a lâmina **de tamanho VM** Select.
1. Selecione uma [geração suportada 2 VM](#generation-2-vm-sizes).
1. Consulte o resto das páginas para terminar a criação do VM.

![Selecione Gen 1 ou Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Também pode utilizar o PowerShell para criar um VM, referindo-se diretamente à geração 1 ou geração 2 SKU.

Por exemplo, use o seguinte cmdlet PowerShell para obter uma lista dos SKUs na `WindowsServer` oferta.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Se estiver a criar um VM com o Windows Server 2012 como o OS, então irá selecionar a geração 1 (BIOS) ou a geração 2 (UEFI) VM SKU, que se parece com isto:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Consulte a secção [funcionalidades e capacidades](#features-and-capabilities) para obter uma lista atual de imagens do Marketplace suportadas.

#### <a name="azure-cli"></a>CLI do Azure

Em alternativa, pode utilizar o CLI Azure para ver qualquer geração disponível de 2 imagens, listadas pela **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Imagem gerida ou disco gerido

Pode criar uma geração de 2 VM a partir de uma imagem gerida ou de um disco gerido da mesma forma que criaria uma geração de 1 VM.

### <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Também pode criar geração de 2 VMs utilizando conjuntos de escala de máquina virtual. No CLI Azure, utilize conjuntos de escala Azure para criar geração 2 VMs.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

* **A geração 2 VMs está disponível em todas as regiões de Azure?**  
    Sim. Mas nem todos os [tamanhos de geração 2 VM](#generation-2-vm-sizes) estão disponíveis em todas as regiões. A disponibilidade da geração 2 VM depende da disponibilidade do tamanho VM.

* **Existe uma diferença de preço entre a geração 1 e a geração 2 VMs?**  
   Não.

* **Tenho um ficheiro .vhd da minha geração 2 VM no local. Posso usar esse ficheiro .vhd para criar uma geração 2 VM em Azure?**
  Sim, podes trazer o teu ficheiro geração 2 .vhd para o Azure e usá-lo para criar uma geração de 2 VM. Utilize os seguintes passos para o fazer:
    1. Faça o upload do .vhd para uma conta de armazenamento na mesma região onde gostaria de criar o seu VM.
    1. Crie um disco gerido a partir do ficheiro .vhd. Coloque a propriedade Hyper-V Generation em V2. Os seguintes comandos PowerShell definem a propriedade Hyper-V Generation ao criar o disco gerido.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Uma vez que o disco esteja disponível, crie um VM fixando este disco. O VM criado será uma geração 2 VM.
    Quando a geração 2 VM é criada, pode generalizar opcionalmente a imagem deste VM. Ao generalizar a imagem, pode usá-la para criar vários VMs.

* **Como posso aumentar o tamanho do disco de SO?**  

  Discos de OS maiores do que 2 TiB são novos para a geração 2 VMs. Por padrão, os discos DE SÃO são menores que 2 TiB para a geração 2 VMs. Pode aumentar o tamanho do disco até um máximo recomendado de 4 TiB. Utilize o CLI Azure ou o portal Azure para aumentar o tamanho do disco OS. Para obter informações sobre como expandir os discos programáticamente, consulte **Redimensionar um disco** para [Windows](./windows/expand-os-disk.md) ou [Linux.](./linux/resize-os-disk-gpt-partition.md)

  Para aumentar o tamanho do disco OS a partir do portal Azure:

  1. No portal Azure, aceda à página de propriedades VM.
  1. Para desligar e negociar o VM, selecione o botão **Stop.**
  1. Na secção **Discos,** selecione o disco SO que pretende aumentar.
  1. Na secção **Discos,** selecione **Configuração** e atualize o **Tamanho** para o valor que pretende.
  1. Volte para a página de propriedades VM e **inicie** o VM.
  
  Pode ver um aviso para discos de SO maiores que 2 TiB. O aviso não se aplica à geração 2 VMs. No entanto, os tamanhos do disco DEL superior a 4 TiB não são suportados.

* **A geração 2 VMs suporta rede acelerada?**  
    Sim. Para obter mais informações, consulte [Criar um VM com rede acelerada.](../virtual-network/create-vm-accelerated-networking-cli.md)

* **A geração 2 VMs suporta Secure Boot ou vTPM em Azure?**
    Tanto a geração 1 como a geração 2 VMs em Azure não suportam o Secure Boot ou o vTPM. 
    
* **A VHDX é suportada na geração 2?**  
    Não, a geração 2 VMs suporta apenas VHD.

* **A geração 2 VMs suporta o armazenamento do disco Azure Ultra?**  
    Sim.

* **Posso migrar um VM da geração 1 para a geração 2?**  
    Não, não podes mudar a geração de um VM depois de o criares. Se precisar de alternar entre gerações VM, crie um novo VM de uma geração diferente.

* **Porque é que o meu tamanho VM não está ativado no seletor de tamanhos quando tento criar um Gen2 VM?**

    Isto pode ser resolvido fazendo o seguinte:

    1. Verifique se a propriedade de **geração VM** está definida para **a Gen 2** no separador **Avançado.**
    1. Verifique se está à procura de um [tamanho VM que suporte VMs Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [máquinas virtuais de geração 2 em Hyper-V.](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
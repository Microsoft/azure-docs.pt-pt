---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e59a28ce1fb3c562bf76420a5e62e347230e964
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669788"
---
Os discos do sistema operacional efêmero são criados no armazenamento da VM (máquina virtual) local e não são salvos no armazenamento remoto do Azure. Os discos do sistema operacional efêmero funcionam bem para cargas de trabalho sem estado, em que os aplicativos são tolerantes a falhas de VM individuais, mas são mais afetados pelo tempo de implantação da VM ou refazendo a imagem das instâncias de VM individuais. Com o disco do sistema operacional efêmero, você obtém latência de leitura/gravação mais baixa no disco do sistema operacional e uma reimagem de VM mais rápida. 
 
Os principais recursos dos discos efêmeras são: 
- Ideal para aplicativos sem estado.
- Eles podem ser usados com imagens do Marketplace e personalizadas.
- Capacidade de redefinir rapidamente ou refazer a imagem de VMs e instâncias do conjunto de dimensionamento para o estado de inicialização original.  
- Menor latência, semelhante a um disco temporário. 
- Discos do sistema operacional efêmero são gratuitos, você não incorre em nenhum custo de armazenamento para o disco do sistema operacional.
- Eles estão disponíveis em todas as regiões do Azure. 
- O disco do so efêmero tem suporte pela [Galeria de imagens](/azure/virtual-machines/linux/shared-image-galleries)compartilhadas. 
 

 
Principais diferenças entre discos do sistema operacional persistentes e efêmeras:

|                             | Disco do so persistente                          | Disco de SO Efémero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho para o disco do sistema operacional      | 2 TiB                                                                                        | Tamanho do cache para o tamanho da VM ou 2TiB, o que for menor. Para o **tamanho do cache em GIB**, consulte [DS](../articles/virtual-machines/linux/sizes-general.md), [es](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md)e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Tamanhos de VM suportados          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte a tipo de disco           | Disco do sistema operacional gerenciado e não gerenciado                                                                | Somente disco do sistema operacional gerenciado                                                               |
| Suporte de região              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Os dados do disco do sistema operacional gravados no disco do sistema operacional são armazenados no armazenamento do Azure                                  | Os dados gravados no disco do sistema operacional são armazenados no armazenamento da VM local e não são persistidos no armazenamento do Azure. |
| Estado de parada desalocada      | As VMs e as instâncias do conjunto de dimensionamento podem ser interrompidas e reiniciadas a partir do estado de parada/desalocada | VMs e instâncias do conjunto de dimensionamento não podem ser interrompidas-desalocadas                                  |
| Suporte a disco do so especializado | Sim                                                                                          | Não                                                                                 |
| Redimensionamento de disco do so              | Com suporte durante a criação da VM e depois que a VM é parada-desalocada                                | Com suporte somente durante a criação da VM                                                  |
| Redimensionando para um novo tamanho de VM   | OS dados do disco do sistema operacional são preservados                                                                    | Os dados no disco do sistema operacional são excluídos, o sistema operacional é reprovisionado                                      |

## <a name="size-requirements"></a>Requisitos de tamanho

Você pode implantar as imagens da VM e da instância até o tamanho do cache da VM. Por exemplo, as imagens padrão do Windows Server do Marketplace são cerca de 127 GiB, o que significa que você precisa de um tamanho de VM que tenha um cache maior do que 127 GiB. Nesse caso, o [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) tem um tamanho de cache de 86 Gib, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Nesse caso, o Standard_DS3_v2 é o menor tamanho na série DSv2 que você pode usar com essa imagem. As imagens básicas do Linux no Marketplace e as imagens do Windows Server que são indicadas `[smallsize]` tendem a ser cerca de 30 GiB e podem usar a maioria dos tamanhos de VM disponíveis.

Os discos efêmeros também exigem que o tamanho da VM dê suporte ao armazenamento Premium. Os tamanhos geralmente (mas nem sempre) têm um `s` no nome, como DSv2 e EsV3. Para obter mais informações, consulte [tamanhos de VM do Azure](../articles/virtual-machines/linux/sizes.md) para obter detalhes sobre quais tamanhos dão suporte ao armazenamento Premium.

## <a name="powershell"></a>PowerShell

Para usar um disco efêmero para uma implantação de VM do PowerShell, use [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) em sua configuração de VM. `-DiffDiskSetting` Defina como `Local` e como`-Caching` . `ReadOnly`     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implantações de conjunto de dimensionamento, use o cmdlet [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) em sua configuração. `-DiffDiskSetting` Defina como `Local` e como`-Caching` . `ReadOnly`


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para usar um disco efêmero para uma implantação de VM da CLI, defina `--ephemeral-os-disk` o parâmetro em [AZ VM Create](/cli/azure/vm#az-vm-create) como `true` e `--os-disk-caching` o parâmetro `ReadOnly`para.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Para conjuntos de dimensionamento, use o `--ephemeral-os-disk true` mesmo parâmetro para [AZ-vmss-Create](/cli/azure/vmss#az-vmss-create) e defina `--os-disk-caching` o parâmetro `ReadOnly`como.

## <a name="portal"></a>Portal   

No portal do Azure, você pode optar por usar discos efêmeros ao implantar uma VM abrindo a seção **avançada** da guia **discos** . Para **usar disco do sistema operacional efêmero** , selecione **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do sistema operacional efêmero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção para usar um disco efêmero estiver esmaecida, você poderá ter selecionado um tamanho de VM que não tenha um tamanho de cache maior do que a imagem do sistema operacional ou que não dê suporte ao armazenamento Premium. Volte para a página **noções básicas** e tente escolher outro tamanho de VM.

Você também pode criar conjuntos de dimensionamento com discos do sistema operacional efêmero usando o Portal. Apenas certifique-se de selecionar um tamanho de VM com tamanho de cache grande o suficiente e, em seguida, em **usar disco do so efêmero** , selecione **Sim**.

![Captura de tela mostrando o botão de opção para escolher usar um disco do so efêmero para seu conjunto de dimensionamento](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implantação do modelo do conjunto de dimensionamento  
O processo para criar um conjunto de dimensionamento que usa um disco do sistema operacional efêmero é `diffDiskSettings` adicionar a propriedade `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` ao tipo de recurso no modelo. Além disso, a política de cache deve ser `ReadOnly` definida como para o disco do sistema operacional efêmero. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Implantação de modelo de VM 
Você pode implantar uma VM com um disco do sistema operacional efêmero usando um modelo. O processo para criar uma VM que usa discos do sistema operacional efêmero é adicionar a `diffDiskSettings` Propriedade ao tipo de recurso Microsoft. Compute/virtualMachines no modelo. Além disso, a política de cache deve ser `ReadOnly` definida como para o disco do sistema operacional efêmero. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Refazer a imagem de uma VM usando REST
Atualmente, o único método para refazer a imagem de uma instância de máquina virtual com o disco do sistema operacional efêmero é por meio do uso da API REST. Para conjuntos de dimensionamento, a recriação de imagens já está disponível por meio do PowerShell, da CLI e do Portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Qual é o tamanho dos discos do sistema operacional local?**

R: Damos suporte a plataformas e imagens personalizadas, até o tamanho do cache da VM, onde todas as leituras/gravações no disco do sistema operacional serão locais no mesmo nó que a máquina virtual. 

**P: O disco do sistema operacional efêmero pode ser redimensionado?**

R: Não, depois que o disco do sistema operacional efêmero é provisionado, o disco do sistema operacional não pode ser redimensionado. 

**P: Posso anexar um Managed Disks a uma VM efêmera?**

R: Sim, você pode anexar um disco de dados gerenciado a uma VM que usa um disco do sistema operacional efêmero. 

**P: Todos os tamanhos de VM terão suporte para discos do sistema operacional efêmero?**

R: Não, todos os tamanhos de VM de armazenamento Premium têm suporte (DS, ES, FS, GS e M), exceto os tamanhos da série B, da série N e da série H.  
 
**P: O disco do sistema operacional efêmero pode ser aplicado a VMs e conjuntos de dimensionamento existentes?**

R: Não, o disco do sistema operacional efêmero só pode ser usado durante a criação da VM e do conjunto de dimensionamento. 

**P: Você pode misturar discos do sistema operacional efêmero e normal em um conjunto de dimensionamento?**

R: Não, você não pode ter uma mistura de instâncias de disco do sistema operacional efêmeras e persistentes dentro do mesmo conjunto de dimensionamento. 

**P: O disco do sistema operacional efêmero pode ser criado usando o PowerShell ou a CLI?**

R: Sim, você pode criar VMs com o disco do sistema operacional efêmero usando REST, modelos, PowerShell e CLI.

**P: Quais recursos não têm suporte com o disco do sistema operacional efêmero?**

R: Os discos efêmeros não dão suporte a:
- Capturando imagens de VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Permuta de disco do so 

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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805881"
---
Discos de SO efémeros são criados no armazenamento local de máquina virtual (VM) e não guardados para o armazenamento remoto do Azure. Discos de SO efémeros funcionam bem para cargas de trabalho sem monitorização de estado, onde as aplicações são tolerância a falhas de falhas VM individuais, mas são mais afetados pelo tempo de implementação de VM ou recriar imagem das instâncias VM individuais. Com disco de SO efémero, tem de menor latência de leitura/escrita para o disco do SO e recriação de imagem de VM mais rápida. 
 
Os principais recursos de discos efémeros são: 
- Ideal para aplicações sem estado.
- Eles podem ser usados com o Marketplace e imagens personalizadas.
- Capacidade de reposição rápida ou VMs de recriação de imagem e o dimensionamento de instâncias de conjunto para o estado original do arranque.  
- Latência mais baixa, semelhante a um disco temporário. 
- Discos de SO efémeros são gratuitos, pode incorrer sem custos de armazenamento do disco do SO.
- Eles estão disponíveis em todas as regiões do Azure. 
- Disco de SO efémero é suportado pelo [Galeria de imagens de partilhado](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Principais diferenças entre discos de SO persistentes e efêmeras:

|                             | Disco de SO persistente                          | Disco de SO Efémero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho do disco do SO      | 2 TiB                                                                                        | Cache de tamanho para o tamanho da VM ou 2TiB, que for menor. Para o **colocar em cache tamanho em GiB**, consulte [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Tamanhos de VM suportados          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte de tipo de disco           | Discos de SO geridos e                                                                | Disco de SO gerido apenas                                                               |
| Suporte de região              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Dados do disco de SO escritos para o disco do SO são armazenados no armazenamento do Azure                                  | Dados escritos no disco do SO são armazenados no armazenamento de VM local e não são mantidos no armazenamento do Azure. |
| Estado parada (desalocada)      | VMs e instâncias do conjunto de dimensionamento podem ser parada (desalocada) e reiniciadas a partir do Estado parada (desalocada) | VMs e instâncias do conjunto de dimensionamento não podem ser parada (desalocada)                                  |
| Suporte de disco do SO especializado | Sim                                                                                          | Não                                                                                 |
| Redimensionamento do disco de SO              | Suportado durante a criação da VM e depois da VM está parada (desalocada)                                | Suportado durante a criação de VM apenas                                                  |
| Redimensionar para um novo tamanho VM   | Dados de disco do SO são preservados                                                                    | Dados no disco do SO são eliminados, sistema operacional é reaprovisionada                                      |

## <a name="size-requirements"></a>Requisitos de tamanho

Pode implantar imagens VM e a instância até o tamanho da cache VM. Por exemplo, imagens padrão do Windows Server do marketplace são GiB cerca 127, o que significa que precisa de um tamanho de VM que tenha uma cache superior a 127 GiB. Neste caso, o [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) tem um tamanho de cache de 86 GiB, que não é suficientemente grande. O Standard_DS2_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Neste caso, o Standard_DS3_v2 é o tamanho mais pequeno da série DSv2, que pode utilizar com esta imagem. Imagens de Linux Basic nas imagens do Marketplace e no Windows Server que estão assinaladas com o `[smallsize]` tendem a ser GiB cerca de 30 e pode utilizar a maior parte dos tamanhos de VM disponíveis.

Discos efémeros também exigem que o tamanho da VM suporta o armazenamento Premium. Os tamanhos normalmente (mas não sempre) tem um `s` o nome, como DSv2 e EsV3. Para obter mais informações, consulte [tamanhos de VM do Azure](../articles/virtual-machines/linux/sizes.md) para obter detalhes de volta do qual tamanhos suportam o Premium storage.

## <a name="powershell"></a>PowerShell

Para utilizar um disco efémero para uma implementação de VM do PowerShell, utilize [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) na sua configuração de VM. Definir o `-DiffDiskSetting` para `Local` e `-Caching` para `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implementações de conjunto de dimensionamento, utilize o [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet na sua configuração. Definir o `-DiffDiskSetting` para `Local` e `-Caching` para `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para utilizar um disco efémero para uma implementação de VM da CLI, defina o `--ephemeral-os-disk` parâmetro na [az vm crie](/cli/azure/vm#az-vm-create) para `true` e o `--os-disk-caching` parâmetro `ReadOnly`.

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

Para conjuntos de dimensionamento, utilize o mesmo `--ephemeral-os-disk true` parâmetro [az vmss create](/cli/azure/vmss#az-vmss-create) e defina a `--os-disk-caching` parâmetro `ReadOnly`.

## <a name="portal"></a>Portal   

No portal do Azure, pode optar por utilizar discos efémeros quando implementar uma VM ao abrir o **avançadas** secção a **discos** separador. Para **disco de SO efémero uso** selecionar **Sim**.

![Captura de ecrã que mostra o botão de opção para selecionar a opção para utilizar um disco de SO efémero](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção para utilizar um disco efémero está esbatida, poderá ter selecionado um tamanho de VM que não tem um tamanho de cache, maior do que a imagem do SO ou que não suporta o armazenamento Premium. Volte para o **Noções básicas** página e tente escolher outro tamanho da VM.

Também pode criar conjuntos de dimensionamento com discos de SO efémeros com o portal. Apenas Certifique-se de selecionar um tamanho de VM com um tamanho de cache grande o suficiente e, em seguida, no **disco de SO efémero uso** selecionar **Sim**.

![Captura de ecrã que mostra o botão de opção para escolher a utilizar um disco de SO efémero para seu conjunto de dimensionamento](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementação do modelo de conjunto de dimensionamento  
O processo de criação de um conjunto de dimensionamento que utilize um disco de SO efémero é adicionar o `diffDiskSettings` propriedade o `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` tipo de recurso no modelo. Além disso, a política de colocação em cache tem de ser definida `ReadOnly` para o disco de SO efémero. 


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

## <a name="vm-template-deployment"></a>Implementação do modelo VM 
Pode implementar uma VM com discos de SO efémeros através de um modelo. O processo para criar uma VM que utilize discos de SO efémeros é adicionar o `diffDiskSettings` propriedade para o tipo de recurso Microsoft.Compute/virtualMachines no modelo. Além disso, a política de colocação em cache tem de ser definida `ReadOnly` para o disco de SO efémero. 

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


## <a name="reimage-a-vm-using-rest"></a>Recriar a imagem de uma VM com o REST
Atualmente, o único método para recriar a imagem de uma instância de Máquina Virtual com discos de SO efémeros é através da utilização da REST API. Para conjuntos de dimensionamento, a recriar a imagem já está disponível através do Powershell, CLI e o portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: O que é o tamanho dos discos de SO local?**

R: Damos suporte a plataforma e imagens personalizadas, até o tamanho de cache da VM, onde todos os leitura/escrita para o disco do SO será o local no mesmo nó que a Máquina Virtual. 

**P: O disco de SO efémero pode ser redimensionado?**

R: Não, uma vez que o disco de SO efémero é provisionado, o disco do SO não pode ser redimensionado. 

**P: Pode anexar um Managed Disks para uma VM efémeras?**

R: Sim, pode anexar um disco de dados geridos para uma VM que utilize um disco de SO efémero. 

**P: Todos os tamanhos VM também serão suportados discos de SO efémeros?**

R: Não, todos os tamanhos de VM de armazenamento Premium são suportados (DS, ES, FS, GS e M), exceto a série B, série N e os tamanhos de série H.  
 
**P: O disco de SO efémero pode ser aplicado às VMs existentes e conjuntos de dimensionamento?**

R: Não, efêmera disco de SO só pode ser utilizado durante a VM e criação de conjunto de dimensionamento. 

**P: Pode misturar discos de SO efémeros e normais num conjunto de dimensionamento?**

R: Não, não pode ter uma mistura de efémeras e persistentes a instâncias do disco de sistema operacional dentro do mesmo conjunto de dimensionamento. 

**P: Pode o disco de SO efémero ser criado com o Powershell ou CLI?**

R: Sim, pode criar VMs com discos de SO Efémeros com REST, modelos, PowerShell e CLI.

**P: Quais recursos não são suportados com o disco de SO efémero?**

R: Não suportam discos efêmeros:
- Captura de imagens VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Troca de disco do SO 

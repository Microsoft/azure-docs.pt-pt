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
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155480"
---
Os discos Efémeros SÃO criados no armazenamento da máquina virtual local (VM) e não são guardados para o armazenamento remoto azure. Os discos efémeros os discos operativos funcionam bem para cargas de trabalho apátridas, onde as aplicações são tolerantes a falhas individuais de VM, mas são mais afetadas pelo tempo de implantação de VM ou reimaging as instâncias vm individuais. Com o disco Ephemeral OS, obtém latência de leitura/escrita mais baixa para o disco OS e reimagem vM mais rápida. 
 
As principais características dos discos efémeros são: 
- Ideal para aplicações apátridas.
- Podem ser usados tanto com o Marketplace como com imagens personalizadas.
- Capacidade de repor ou reimagem rápida e configurar instâncias para o estado de arranque original.  
- Latência mais baixa, semelhante a um disco temporário. 
- Os discos efémeros são gratuitos, não incorre em nenhum custo de armazenamento para o disco OS.
- Estão disponíveis em todas as regiões de Azure. 
- O Disco Efémero OS é suportado pela [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Principais diferenças entre discos operativos osso persistentes e efémeros:

|                             | Disco operativo persistente                          | Disco de SO Efémero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho para o disco OS      | 2 TiB                                                                                        | Tamanho de cache para o tamanho VM ou 2TiB, o que for menor. Para o tamanho da **cache em GiB,** ver [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)e [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Tamanhos vm suportados          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte do tipo disco           | Disco de Os gerido e não gerido                                                                | Apenas o disco de Os gerido                                                               |
| Apoio da região              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Os dados do disco os escritos para o disco OS são armazenados no Armazenamento Azure                                  | Os dados escritos para o disco OS são armazenados no armazenamento vm local e não são persistidos no Armazenamento Azure. |
| Estado de stop-deallocalizado      | VMs e instâncias de conjunto de escala podem ser stop-deal localizados e reiniciados do estado de stop-deallocalizado | VMs e instâncias de conjunto de escala não podem ser stop-deal                                  |
| Suporte especializado do disco osso | Sim                                                                                          | Não                                                                                 |
| Redimensionamento do disco OS              | Suportado durante a criação vm e depois de VM ser stop-deallocalizado                                | Apoiado apenas durante a criação vm                                                  |
| Redimensionamento para um novo tamanho VM   | Os dados do disco osso são preservados                                                                    | Os dados do disco OS são eliminados, o OS é reprovisionado                                      |

## <a name="size-requirements"></a>Requisitos de tamanho

Pode implantar imagens VM e, por exemplo, até ao tamanho da cache VM. Por exemplo, as imagens standard do Windows Server do mercado são de cerca de 127 GiB, o que significa que você precisa de um tamanho VM que tem uma cache maior que 127 GiB. Neste caso, o [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho cache de 172 GiB, que é grande o suficiente. Neste caso, o Standard_DS3_v2 é o menor tamanho da série DSv2 que pode usar com esta imagem. Imagens Básicas do Linux no Marketplace e no Windows Server imagens que são denotadas por `[smallsize]` tendem a rondar os 30 GiB e podem usar a maioria dos tamanhos VM disponíveis.

Os discos efémeros também requerem que o tamanho VM suporte o armazenamento Premium. Os tamanhos geralmente (mas nem sempre) têm um `s` no nome, como DSv2 e EsV3. Para mais informações, consulte os [tamanhos de VM Azure](../articles/virtual-machines/linux/sizes.md) para obter detalhes em torno dos tamanhos suportam o armazenamento Premium.

## <a name="powershell"></a>PowerShell

Para utilizar um disco efémero para uma implementação de PowerShell VM, utilize o [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) na sua configuração VM. Detete a `-DiffDiskSetting` para `Local` e `-Caching` para `ReadOnly`.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para as implementações de conjuntos de escala, utilize o [cmdlet Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) na sua configuração. Detete a `-DiffDiskSetting` para `Local` e `-Caching` para `ReadOnly`.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para utilizar um disco efémero para uma implantação CLI VM, coloque o parâmetro `--ephemeral-os-disk` em [az vm criar](/cli/azure/vm#az-vm-create) para `true` e o parâmetro `--os-disk-caching` para `ReadOnly`.

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

Para conjuntos de escala, utilize o mesmo parâmetro de `--ephemeral-os-disk true` para [criar az-vmss-e](/cli/azure/vmss#az-vmss-create) definir o parâmetro `--os-disk-caching` para `ReadOnly`.

## <a name="portal"></a>Portal   

No portal Azure, pode optar por utilizar discos efémeros ao implementar um VM abrindo a secção **Avançada** do separador **Discos.** Para utilizar o **disco efémero osseeis** selecione **Sim**.

![Screenshot mostrando o botão de rádio para escolher usar um disco efémero OS](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção de utilizar um disco efémero estiver cinzenta, pode ter selecionado um tamanho VM que não tenha um tamanho de cache maior do que a imagem de OS ou que não suporte o armazenamento Premium. Volte para a página **Basics** e tente escolher outro tamanho VM.

Também pode criar conjuntos de escala com discos efémeros operativos operativos operativos através do portal. Certifique-se apenas de que seleciona um tamanho VM com um tamanho de cache suficientemente grande e, em seguida, em Use o **disco efémero osse** **Sim**.

![Screenshot mostrando o botão de rádio para escolher usar um disco efémero oso para o seu conjunto de escala](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implantação do modelo de conjunto de escala  
O processo para criar um conjunto de escala que usa um disco efémero de OS é adicionar a propriedade `diffDiskSettings` ao tipo de recurso `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` no modelo. Além disso, a política de cache deve ser definida para `ReadOnly` para o disco efémero osso. 


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

## <a name="vm-template-deployment"></a>Implantação do modelo VM 
Pode implantar um VM com um disco efémero operativo utilizando um modelo. O processo para criar um VM que utiliza discos efémeros de OS é adicionar a propriedade `diffDiskSettings` ao tipo de recursos Microsoft.Compute/virtualMachines no modelo. Além disso, a política de cache deve ser definida para `ReadOnly` para o disco efémero osso. 

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


## <a name="reimage-a-vm-using-rest"></a>Reimagem de um VM usando REST
Pode reimagem de uma instância de Máquina Virtual com disco efémero operativo operativo, utilizando a API REST, conforme descrito abaixo e via Portal Azure, indo para o painel de visão geral do VM. Para conjuntos de escala, a reimagem já está disponível através da Powershell, CLI e do portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Qual é o tamanho dos Discos OS locais?**

R: Apoiamos a plataforma e imagens personalizadas, até ao tamanho da cache VM, onde todas as leituras/escritos para o disco OS serão locais no mesmo nó que a Máquina Virtual. 

**P: O disco efémero osso pode ser redimensionado?**

R: Não, uma vez aprovisionado o disco efémero osso, o disco osso não pode ser redimensionado. 

**P: Posso anexar um Disco Gerido a um VM efémero?**

R: Sim, pode anexar um disco de dados gerido a um VM que utiliza um disco efémero. 

**P: Todos os tamanhos vm serão suportados para discos efémeros osso?**

R: Não, todos os tamanhos de VM de armazenamento premium são suportados (DS, ES, FS, GS e M) exceto os tamanhos da série B, série N e H.  
 
**P: O disco efémero osso pode ser aplicado aos VMs existentes e aos conjuntos de escala?**

R: Não, o disco osso efémero só pode ser utilizado durante a VM e a criação de conjuntos de escala. 

**P: Pode misturar discos efémeros e normais num conjunto de escala?**

R: Não, não pode ter uma mistura de instâncias efémeras e persistentes de discos operativos dentro da mesma escala. 

**P: O disco efémero OS pode ser criado utilizando powershell ou CLI?**

R: Sim, pode criar VMs com disco efémero operativo usando REST, Templates, PowerShell e CLI.

**P: Quais as funcionalidades que não são suportadas com o disco efémero osso?**

R: Os discos efémeros não suportam:
- Capturar imagens VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Troca de Discos OS 

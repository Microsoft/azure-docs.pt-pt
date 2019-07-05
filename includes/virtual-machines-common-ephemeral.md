---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24c2bfa4aae94642d3ed66f2cfa6e31ba1e6b19a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457488"
---
Discos de SO efémeros são criados no armazenamento de Máquina Virtual (VM) local e não persistente para o armazenamento remoto do Azure. Discos de SO efémeros funcionam bem para cargas de trabalho sem monitorização de estado, em que aplicativos são tolerantes a de falhas VM individuais, mas estiver mais preocupado com o tempo necessário para implementações em grande escala ou tempo a recriar imagem das instâncias VM individuais. Também é adequado para aplicativos, implantados usando o modelo de implementação clássica, para mover para o modelo de implementação do Resource Manager. Com o Disco de SO Efémero, observaria uma latência de leitura/escrita inferior para o Disco de SO e uma recriação mais rápida da imagem da VM. Além disso, o disco de SO efémero é gratuito, pode incorrer sem custos de armazenamento do disco do SO. 
 
Os principais recursos de discos efémeros são: 
- Eles podem ser usados com imagens do Marketplace e imagens personalizadas.
- Pode implantar imagens de VM até ao tamanho da Cache de VM.
- Capacidade de rapidamente repor ou recriar imagem das suas VMs para o estado original do arranque.  
- Tempo de execução mais baixas semelhante a um disco temporário. 
- Sem custos para o disco do SO. 
 
 
Principais diferenças entre discos de SO persistentes e efêmeras:

|                             | Disco de SO persistente                          | Disco de SO Efémero                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Limite de tamanho do disco do SO      | 2 TiB                                                                                        | Cache de tamanho para o tamanho da VM ou 2TiB, que for menor - [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md), e [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Tamanhos de VM suportados          | Todos                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Suporte de tipo de disco           | Discos de SO geridos e                                                                | Disco de SO gerido apenas                                                               |
| Suporte de região              | Todas as regiões                                                                                  | Todas as regiões                              |
| Persistência de dados            | Dados do disco de SO escritos para o disco do SO são armazenados no armazenamento do Azure                                  | Dados escritos no disco do SO são armazenados no armazenamento de VM local e não são mantidos no armazenamento do Azure. |
| Estado parada (desalocada)      | VMs e instâncias do conjunto de dimensionamento podem ser parada (desalocada) e reiniciadas a partir do Estado parada (desalocada) | VMs e instâncias do conjunto de dimensionamento não podem ser parada (desalocada)                                  |
| Suporte de disco do SO especializado | Sim                                                                                          | Não                                                                                 |
| Redimensionamento do disco de SO              | Suportado durante a criação da VM e depois da VM está parada (desalocada)                                | Suportado durante a criação de VM apenas                                                  |
| Redimensionar para um novo tamanho VM   | Dados de disco do SO são preservados                                                                    | Dados no disco do SO são eliminados, sistema operacional é reaprovisionada                                      |

## <a name="scale-set-deployment"></a>Conjunto de dimensionamento da implementação  
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

## <a name="vm-deployment"></a>Implementação da VM 
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

R: Para a pré-visualização, iremos suportar plataforma e/ou imagens até o tamanho de cache da VM, onde todos os leitura/escrita para o disco do SO será o local no mesmo nó que a Máquina Virtual. 

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

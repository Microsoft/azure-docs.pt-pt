---
title: Discos de SO Efémeros
description: Saiba mais sobre discos de OS efémeros para VMs Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 814824fb6708abaf549bb3de19b4aced4774a244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102485763"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Discos EFÉMEROS para VMs Azure

Os discos EFÉMER OS são criados no armazenamento da máquina virtual local (VM) e não guardados para o remoto Azure Storage. Os discos efémeros de OS funcionam bem para cargas de trabalho apátridas, onde as aplicações são tolerantes a falhas individuais de VM, mas são mais afetadas pelo tempo de implantação de VM ou pela reimaging das instâncias VM individuais. Com o disco Efemeral OS, obtém-se uma menor leitura/escrita latência para o disco de OS e uma reimagem VM mais rápida. 
 
As principais características dos discos efémeros são: 
- Ideal para aplicações apátridas.
- Podem ser usados tanto com o Marketplace como com imagens personalizadas.
- Capacidade de repor ou reimagem VMs rápidos e definir instâncias para o estado de arranque original.  
- Latência mais baixa, semelhante a um disco temporário. 
- Os discos de SO efémeros são gratuitos, não incorre em custos de armazenamento para o disco de SO.
- Estão disponíveis em todas as regiões de Azure. 
- O disco EFÉMER OS É suportado pela [Shared Image Gallery](./shared-image-galleries.md). 
 

 
Principais diferenças entre discos de OS persistentes e efémeros:

|                             | Disco de SO persistente                          | Disco de SO Efémero                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Limite de tamanho para disco de SO**      | 2 TiB                                                                                        | Tamanho da cache para o tamanho VM ou 2TiB, o que for menor. Para o tamanho da **cache em GiB**, ver [DS,](sizes-general.md) [ES,](sizes-memory.md) [M,](sizes-memory.md) [FS](sizes-compute.md)e [GS](sizes-previous-gen.md#gs-series)              |
| **Tamanhos VM suportados**          | Todos                                                                                          | Tamanhos VM que suportam armazenamento Premium como DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Suporte do tipo de disco**           | Disco de OS gerido e não gerido                                                                | Apenas disco de SO gerido                                                               |
| **Suporte de região**              | Todas as regiões                                                                                  | Todas as regiões                              |
| **Persistência de dados**            | Os dados do disco de SO escritos no disco OS são armazenados no Azure Storage                                  | Os dados escritos no disco OS são armazenados no armazenamento local de VM e não são persistidos ao Azure Storage. |
| **Estado stop-deallocated**      | VMs e instâncias definidas em escala podem ser stop-dealloced e reiniciado a partir do estado stop-deallocated | VMs e instâncias definidas em escala não podem ser stop-deallocated                                  |
| **Suporte especializado em disco de SO** | Yes                                                                                          | No                                                                                 |
| **Redimensionar o disco de OS**              | Suportado durante a criação de VM e depois de VM é stop-deallocated                                | Suportado apenas durante a criação de VM                                                  |
| **Redimensionamento para um novo tamanho VM**   | Os dados do disco de SO são preservados                                                                    | Os dados do disco de so são eliminados, o SO é re-provisionado       
| **Colocação de ficheiro de página**   | Para o Windows, o ficheiro de página é armazenado no disco de recursos                                              | Para o Windows, o ficheiro de página é armazenado no disco OS   |

## <a name="size-requirements"></a>Requisitos de tamanho

Pode implantar imagens de VM e de instância até ao tamanho da cache VM. Por exemplo, as imagens Standard Windows Server do mercado são de cerca de 127 GiB, o que significa que você precisa de um tamanho VM que tem um cache maior que 127 GiB. Neste caso, o [Standard_DS2_v2](dv2-dsv2-series.md) tem um tamanho de cache de 86 GiB, que não é grande o suficiente. O Standard_DS3_v2 tem um tamanho de cache de 172 GiB, que é grande o suficiente. Neste caso, o Standard_DS3_v2 é o menor tamanho da série DSv2 que pode utilizar com esta imagem. Imagens Básicas do Linux nas imagens do Marketplace e do Windows Server que são denotadas `[smallsize]` tendem a rondar os 30 GiB e podem utilizar a maioria dos tamanhos VM disponíveis.

Os discos efémeros também exigem que o tamanho VM suporte o armazenamento Premium. Os tamanhos geralmente (mas nem sempre) têm um `s` no nome, como DSv2 e EsV3. Para obter mais informações, consulte [os tamanhos Azure VM](sizes.md) para obter mais detalhes sobre quais tamanhos suportam armazenamento Premium.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Pré-visualização - Os discos EFÉMERes já podem ser armazenados em discos temporários
Os discos EFÉMEROS podem agora ser armazenados em disco de vm temp/recursos, além da cache VM. Assim, agora pode usar discos EFÉMEROS com VM que não têm cache ou têm cache insuficiente, mas tem um disco temporário/recursos para armazenar o disco EFÉMER OS como Dav3, Dav4, Eav4 e Eav3. Se um VM tiver cache e espaço temporário suficiente, agora também poderá especificar onde pretende armazenar o disco EFÉMER OS Utilizando uma nova propriedade chamada [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement). Com esta funcionalidade, quando um VM do Windows é a provisionado, configuramos o ficheiro de página a ser localizado no disco OS. Esta funcionalidade encontra-se em pré-visualização. Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para começar, [solicite acesso.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)

## <a name="powershell"></a>PowerShell

Para utilizar um disco efémero para uma implementação de VM PowerShell, utilize [o Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) na sua configuração VM. Desema estalem o `-DiffDiskSetting` `Local` de e para `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Para implementações definidas em escala, utilize o [cmdeto Desativação de Set-AzVmssStorage](/powershell/module/az.compute/set-azvmssstorageprofile) na sua configuração. Desema estalem o `-DiffDiskSetting` `Local` de e para `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Para utilizar um disco efémero para uma implantação de VM CLI, defina o `--ephemeral-os-disk` parâmetro em [az vm criar](/cli/azure/vm#az-vm-create) e o parâmetro para `true` `--os-disk-caching` `ReadOnly` .

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

Para conjuntos de escala, utilize o mesmo `--ephemeral-os-disk true` parâmetro para [criar az-vmss](/cli/azure/vmss#az-vmss-create) e definir o `--os-disk-caching` parâmetro para `ReadOnly` .

## <a name="portal"></a>Portal

No portal Azure, pode optar por utilizar discos efémeros ao implementar um VM abrindo a secção **Avançada** do separador **Discos.** Para **utilizar o disco EFÉMER OS** selecione **Sim**.

![Screenshot mostrando o botão de rádio para escolher usar um disco efémero DE](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Se a opção de utilização de um disco efémero estiver acinzentada, pode ter selecionado um tamanho VM que não tenha um tamanho de cache maior do que a imagem de SO ou que não suporte o armazenamento Premium. Volte para a página **Basics** e tente escolher outro tamanho VM.

Também pode criar conjuntos de escala com discos efémeros de OS utilizando o portal. Apenas certifique-se de selecionar um tamanho VM com um tamanho de cache grande o suficiente e, em seguida, em **Utilizar o disco EFÉMER SISTEMA** Selecione **Sim**.

![Screenshot mostrando o botão de rádio para escolher usar um disco de SO efémero para o seu conjunto de escala](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementação do modelo de conjunto de escala  
O processo para criar um conjunto de escala que usa um disco efémero de SO é adicionar a `diffDiskSettings` propriedade ao tipo de recurso no `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` modelo. Além disso, a política de cache deve ser definida `ReadOnly` para o disco efémero de SO. 


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
Pode implantar um VM com um disco de OS efémero utilizando um modelo. O processo para criar um VM que utiliza discos efémeros de OS é adicionar a `diffDiskSettings` propriedade ao tipo de recurso Microsoft.Compute/virtualMachines no modelo. Além disso, a política de cache deve ser definida `ReadOnly` para o disco efémero de SO. 

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


## <a name="reimage-a-vm-using-rest"></a>Reimage a VM usando REST
Pode reimagemar uma instância da Máquina Virtual com disco DEE efémero utilizando a API REST, conforme descrito abaixo e através do Portal Azure, indo para o painel de visão geral do VM. Para conjuntos de escala, a reimaging já está disponível através de Powershell, CLI e do portal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Qual é o tamanho dos discos oss locais?**

R: Suportamos imagens de plataforma e personalizadas, até ao tamanho da cache VM, onde todas as leituras/escritas para o disco OS serão locais no mesmo nó que a Máquina Virtual. 

**P: O disco efémero de SO pode ser redimensionado?**

R: Não, uma vez que o disco efémero de SO é provisionado, o disco SO não pode ser redimensionado. 

**P: Posso anexar um Discos Geridos a um VM efémero?**

R: Sim, pode anexar um disco de dados gerido a um VM que utiliza um disco de OS efémero. 

**P: Todos os tamanhos de VM serão suportados para discos de OS efémeros?**

R: Não, a maioria dos tamanhos de VM de armazenamento premium são suportados (DS, ES, FS, GS, M, etc.). Para saber se um determinado tamanho VM suporta discos efémeros de OS, você pode:

Chame o `Get-AzComputeResourceSku` cmdlet PowerShell
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**P: O disco efémero de SO pode ser aplicado em VMs e conjuntos de escala existentes?**

R: Não, o disco de OS efémero só pode ser utilizado durante a criação de VM e conjunto de escala. 

**P: Pode misturar discos de sossia efémeros e normais num conjunto de escala?**

R: Não, não pode ter uma mistura de instâncias efémeras e persistentes do disco de SO dentro da mesma escala definida. 

**P: O disco EFÉMER PODE ser criado com recurso a Powershell ou CLI?**

R: Sim, pode criar VMs com disco EFémero DES usando REST, Modelos, PowerShell e CLI.

**P: Quais as características que não são suportadas com disco EFÉMER?**

R: Os discos efémeros não suportam:
- Captação de imagens VM
- Instantâneos de disco 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Troca de discos de OS 

> [!NOTE]
> 
> O disco efémero não será acessível através do portal. Receberá o erro de "Recurso não Encontrado" ou "404" ao aceder ao disco efémero que é esperado.
> 
 
## <a name="next-steps"></a>Passos seguintes
Pode criar um VM com um disco efémero de OS utilizando o [Azure CLI](/cli/azure/vm#az-vm-create).

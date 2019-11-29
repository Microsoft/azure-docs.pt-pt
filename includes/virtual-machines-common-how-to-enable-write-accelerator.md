---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566336"
---
Acelerador de Gravação é um recurso de disco para VMs (máquinas virtuais) da série M no armazenamento Premium com o Azure Managed Disks exclusivamente. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de gravações no armazenamento Premium do Azure. Acelerador de Gravação é ideal para onde as atualizações de arquivo de log são necessárias para persistir no disco de maneira altamente eficaz para bancos de dados modernos.

Acelerador de Gravação está geralmente disponível para VMs da série M na nuvem pública.

## <a name="planning-for-using-write-accelerator"></a>Planejando o uso de Acelerador de Gravação

Acelerador de Gravação deve ser usado para os volumes que contêm o log de transações ou logs de restauração de um DBMS. Não é recomendável usar Acelerador de Gravação para os volumes de dados de um DBMS, pois o recurso foi otimizado para ser usado em relação aos discos de log.

Acelerador de Gravação só funciona em conjunto com o [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Habilitar Acelerador de Gravação para o disco do sistema operacional da VM reiniciará a VM.
>
> Para habilitar Acelerador de Gravação a um disco do Azure existente que não faz parte de uma compilação de volume de vários discos com gerenciadores de disco ou volume do Windows, espaços de armazenamento do Windows, servidor de arquivos de escalabilidade horizontal do Windows (SOFS), LVM do Linux ou MDADM, a carga de trabalho acessando o disco do Azure precisa ser desligado. Os aplicativos de banco de dados que usam o disco do Azure devem ser desligados.
>
> Se você quiser habilitar ou desabilitar Acelerador de Gravação para um volume existente criado de vários discos de armazenamento Premium do Azure e distribuídos usando gerenciadores de disco ou volume do Windows, espaços de armazenamento do Windows, servidor de arquivos de escalabilidade horizontal do Windows (SOFS), LVM do Linux ou MDADM, todos os discos que criam o volume devem ser habilitados ou desabilitados para Acelerador de Gravação em etapas separadas. **Antes de habilitar ou desabilitar acelerador de gravação nessa configuração, desligue a VM do Azure**.

A habilitação de Acelerador de Gravação para discos do sistema operacional não deve ser necessária para configurações de VM relacionadas ao SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrições ao usar Acelerador de Gravação

Ao usar Acelerador de Gravação para um disco/VHD do Azure, essas restrições se aplicam:

- O cache de disco Premium deve ser definido como ' nenhum ' ou ' somente leitura '. Não há suporte para todos os outros modos de cache.
- No momento, não há suporte para instantâneos em discos habilitados para Acelerador de Gravação. Durante o backup, o serviço de backup do Azure exclui automaticamente os discos habilitados para Acelerador de Gravação conectados à VM.
- Somente os tamanhos de e/s menores (< = 512 KiB) estão pegando o caminho acelerado. Em situações de carga de trabalho em que os dados estão sendo carregados em massa ou onde os buffers de log de transações do DBMS diferente são preenchidos para um grau maior antes de serem persistidos no armazenamento, é provável que a e/s gravada em disco não esteja tomando o caminho acelerado.

Há limites de VHDs de armazenamento Premium do Azure por VM que podem ser suportados pelo Acelerador de Gravação. Os limites atuais são:

| SKU DA VM | Número de discos Acelerador de Gravação | Acelerador de Gravação IOPS de disco por VM |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Os limites de IOPS são por VM e *não* por disco. Todos os discos de Acelerador de Gravação compartilham o mesmo limite de IOPS por VM.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Habilitando Acelerador de Gravação em um disco específico

As próximas seções descreverão como Acelerador de Gravação podem ser habilitadas nos VHDs do armazenamento Premium do Azure.

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos se aplicam ao uso de Acelerador de Gravação neste momento:

- Os discos aos quais você deseja aplicar o Azure Acelerador de Gravação precisam ser [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) no armazenamento Premium.
- Você deve estar usando uma VM da série M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Habilitando o Azure Acelerador de Gravação usando Azure PowerShell

O módulo Azure Power Shell da versão 5.5.0 inclui as alterações nos cmdlets relevantes para habilitar ou desabilitar Acelerador de Gravação para discos específicos do armazenamento Premium do Azure.
Para habilitar ou implantar discos com suporte pelo Acelerador de Gravação, os seguintes comandos do PowerShell foram alterados e estendidos para aceitar um parâmetro para Acelerador de Gravação.

Um novo parâmetro de opção, **-WriteAccelerator** , foi adicionado aos seguintes cmdlets:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Não dar ao parâmetro define a propriedade como false e implantará discos que não têm suporte pelo Acelerador de Gravação.

Um novo parâmetro de opção, **-OsDiskWriteAccelerator** , foi adicionado aos seguintes cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Não especificar o parâmetro define a propriedade como false por padrão, retornando discos que não aproveitam Acelerador de Gravação.

Um novo parâmetro booliano (não anulável) opcional, **-OsDiskWriteAccelerator** , foi adicionado aos seguintes cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Especifique $true ou $false para controlar o suporte do Azure Acelerador de Gravação com os discos.

Exemplos de comandos podem ser semelhantes a:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dois cenários principais podem ser inseridos no script, conforme mostrado nas seções a seguir.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Adicionando um novo disco com suporte pelo Acelerador de Gravação usando o PowerShell

Você pode usar esse script para adicionar um novo disco à sua VM. O disco criado com esse script usa Acelerador de Gravação.

Substitua `myVM`, `myWAVMs`, `log001`, tamanho do disco e LunID do disco com os valores apropriados para sua implantação específica.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Habilitando Acelerador de Gravação em um disco do Azure existente usando o PowerShell

Você pode usar esse script para habilitar Acelerador de Gravação em um disco existente. Substitua `myVM`, `myWAVMs`e `test-log001` pelos valores apropriados para sua implantação específica. O script adiciona Acelerador de Gravação a um disco existente onde o valor de **$newstatus** é definido como ' $true '. Usar o valor ' $false ' desabilitará Acelerador de Gravação em um determinado disco.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> A execução do script acima desanexará o disco especificado, habilitará Acelerador de Gravação no disco e, em seguida, anexará o disco novamente

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Habilitando Acelerador de Gravação usando o portal do Azure

Você pode habilitar Acelerador de Gravação por meio do portal em que você especifica as configurações de cache de disco:

![Acelerador de Gravação na portal do Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Habilitando Acelerador de Gravação usando o CLI do Azure

Você pode usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para habilitar acelerador de gravação.

Para habilitar Acelerador de Gravação em um disco existente, use [AZ VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), você poderá usar os seguintes exemplos se substituir o diskname, VMName e resourcegroup pelos seus próprios valores: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Para anexar um disco com Acelerador de Gravação habilitado, use [AZ VM Disk Attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), você poderá usar o exemplo a seguir se substituir seus próprios valores: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Para desabilitar Acelerador de Gravação, use [AZ VM Update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), definindo as propriedades como false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Habilitando Acelerador de Gravação usando APIs REST

Para implantar por meio da API REST do Azure, você precisa instalar o armclient do Azure.

### <a name="install-armclient"></a>Instalar o armclient

Para executar o armclient, você precisa instalá-lo por meio de Chocolatey. Você pode instalá-lo por meio do cmd. exe ou do PowerShell. Use direitos elevados para esses comandos ("executar como administrador").

Usando o cmd. exe, execute o seguinte comando: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Usando o Power Shell, execute o seguinte comando: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Agora você pode instalar o armclient usando o comando a seguir no cmd. exe ou no PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Obtendo sua configuração atual de VM

Para alterar os atributos da configuração do disco, primeiro você precisa obter a configuração atual em um arquivo JSON. Você pode obter a configuração atual executando o seguinte comando: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Substitua os termos em ' < < > > ' pelos seus dados, incluindo o nome do arquivo que o arquivo JSON deve ter.

A saída poderia ser semelhante a:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Em seguida, atualize o arquivo JSON e habilite Acelerador de Gravação no disco chamado ' LOG1 '. Isso pode ser feito adicionando esse atributo ao arquivo JSON após a entrada de cache do disco.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Em seguida, atualize a implantação existente com este comando: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A saída deve ser semelhante à seguinte. Você pode ver que Acelerador de Gravação habilitado para um disco.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Depois de fazer essa alteração, a unidade deve ser suportada pelo Acelerador de Gravação.

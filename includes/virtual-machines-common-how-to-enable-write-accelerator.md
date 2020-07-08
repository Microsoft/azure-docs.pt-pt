---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 11/27/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 456d550659c04b2272c048fcd64fe73b1a11522a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74566336"
---
Write Accelerator é uma capacidade de disco para máquinas virtuais da série M (VMs) em Armazenamento Premium com Discos Geridos Azure exclusivamente. Como o nome afirma, o objetivo da funcionalidade é melhorar a latência de I/O de escritas contra o Azure Premium Storage. O Write Accelerator é idealmente adequado para que as atualizações de ficheiros de registo sejam necessárias para persistir em disco de uma forma altamente performante para bases de dados modernas.

O Write Accelerator está geralmente disponível para VMs da série M na Nuvem Pública.

## <a name="planning-for-using-write-accelerator"></a>Planeamento para a utilização do Acelerador de Escrita

O Acelerador de Escrita deve ser utilizado para os volumes que contenham o registo de transações ou redo registos de um DBMS. Não é aconselhável utilizar o Write Accelerator para os volumes de dados de um DBMS, uma vez que a funcionalidade foi otimizada para ser utilizada contra discos de log.

O Write Accelerator só funciona em conjunto com [discos geridos pela Azure.](https://azure.microsoft.com/services/managed-disks/)

> [!IMPORTANT]
> Ativar o Acelerador de Escrita para o disco do sistema operativo do VM reiniciará o VM.
>
> Para ativar o Write Accelerator a um disco Azure existente que não faça parte de um volume construído a partir de vários discos com discos windows ou gestores de volume, Windows Storage Spaces, Windows Scale-out file server (SOFS), Linux LVM ou MDADM, a carga de trabalho que acede ao disco Azure precisa de ser desligada. As aplicações de base de dados que utilizam o disco Azure DEVEM ser desligadas.
>
> Se pretender ativar ou desativar o Write Accelerator para um volume existente que seja construído a partir de vários discos de armazenamento Azure Premium e listrados utilizando gestores de discos ou volumes windows, Windows Storage Spaces, Windows Scale-out file server (SOFS), Linux LVM ou MDADM, todos os discos que couram o volume devem ser ativados ou desativados para o Write Accelerator em etapas separadas. **Antes de ativar ou desativar o Acelerador de Escrita numa configuração desta forma, desligue o Azure VM**.

Permitir o acelerador de escrita para discos DESo não deve ser necessário para configurações VM relacionadas com o SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrições de utilização do Acelerador de Escrita

Ao utilizar o Acelerador de Escrita para um disco Azure/VHD, estas restrições aplicam-se:

- O cache do disco Premium deve ser definido como 'Nenhum' ou 'Read Only'. Todos os outros modos de cache não são suportados.
- O Snapshot não é atualmente suportado para discos ativados pelo Write Accelerator. Durante a cópia de segurança, o serviço de Backup Azure exclui automaticamente discos ativados pelo Acelerador de Escrita ligados ao VM.
- Apenas tamanhos de E/S mais pequenos (<=512 KiB) estão a tomar o caminho acelerado. Em situações de carga de trabalho em que os dados estão a ser carregados a granel ou em que os amortecedores de registo de transações dos diferentes DBMS são preenchidos em maior grau antes de serem persistidos no armazenamento, é provável que o E/O escrito para o disco não esteja a tomar o caminho acelerado.

Existem limites de VHDs de armazenamento Azure Premium por VM que podem ser suportados pelo Write Accelerator. Os limites atuais são:

| SKU da VM | Número de discos de acelerador de escrita | Escreva IOPS de disco de acelerador por VM |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20 000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20 000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Os limites do IOPS são por VM e *não* por disco. Todos os discos do Write Accelerator partilham o mesmo limite de IOPS por VM.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Ativar o Acelerador de Escrita num disco específico

As próximas secções descreverão como o Acelerador de Escrita pode ser ativado em VHDs de armazenamento Azure Premium.

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos aplicam-se à utilização do Acelerador de Escrita neste momento:

- Os discos que pretende aplicar O Acelerador de Escrita Azure contra a necessidade de serem [discos geridos pelo Azure](https://azure.microsoft.com/services/managed-disks/) no Armazenamento Premium.
- Deve estar a usar um VM da série M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Ativar o Acelerador de Escrita do Azure com o Azure PowerShell

O módulo Azure Power Shell da versão 5.5.0 inclui as alterações aos cmdlets relevantes para ativar ou desativar o Acelerador de Escrita para discos de armazenamento Azure Premium específicos.
Para ativar ou implantar discos suportados pelo Write Accelerator, os seguintes comandos power shell foram alterados e estendidos para aceitar um parâmetro para o Acelerador de Escrita.

Um novo parâmetro do interruptor, **-WriteAccelerator** foi adicionado aos seguintes cmdlets:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Não dar o parâmetro define a propriedade para falso e irá implantar discos que não têm suporte por Write Accelerator.

Um novo parâmetro de comutação, **-OsDiskWriteAccelerator** foi adicionado aos seguintes cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Não especificando o parâmetro define a propriedade em falso por padrão, devolvendo discos que não alavancam o Write Accelerator.

Um novo parâmetro opcional booleano (não anulado), **-OsDiskWriteAccelerator** foi adicionado aos seguintes cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Atualização-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Especifique $true ou $false para controlar o suporte do Acelerador de Escrita Azure com os discos.

Exemplos de comandos podem parecer:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dois cenários principais podem ser descritos como mostrado nas seguintes secções.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Adicionar um novo disco suportado pelo Write Accelerator usando PowerShell

Pode utilizar este script para adicionar um novo disco ao seu VM. O disco criado com este script utiliza o Write Accelerator.

`myVM`Substitua, , , tamanho do `myWAVMs` `log001` disco, e LunID do disco por valores apropriados para a sua implantação específica.

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

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Ativar o Acelerador de Escrita num disco Azure existente utilizando o PowerShell

Pode utilizar este script para ativar o Write Accelerator num disco existente. Substitua `myVM` `myWAVMs` , e por `test-log001` valores apropriados para a sua implantação específica. O script adiciona o Write Accelerator a um disco existente onde o valor para **$newstatus** está definido para '$true'. A utilização do valor '$false' desativa o Acelerador de Escrita num dado disco.

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
> A execução do script acima irá separar o disco especificado, ativar o Acelerador de Escrita contra o disco e, em seguida, voltar a ligar o disco

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Ativar o Acelerador de Escrita com o portal do Azure

Pode ativar o Write Accelerator através do portal onde especifica as definições de cache do disco:

![Escreva Acelerador no portal Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Ativar o Acelerador de Escrita com a CLI do Azure

Pode utilizar o [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para ativar o Acelerador de Escrita.

Para ativar o Acelerador de Escrita num disco existente, utilize [a atualização az vm,](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)pode utilizar os seguintes exemplos se substituir o discoName, VMName e ResourceGroup pelos seus próprios valores:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Para anexar um disco com o Write Accelerator ativado, utilize [o az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), pode utilizar o seguinte exemplo se substituir nos seus próprios valores:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Para desativar o Acelerador de Escrita, utilize [a atualização az vm,](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)definindo as propriedades em falso:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Ativar o acelerador de escrita usando APIs de repouso

Para implementar através da Azure Rest API, é necessário instalar o braço Azure.

### <a name="install-armclient"></a>Instalar o braço

Para funcionar o braço, precisa instalá-lo através do Chocolatey. Pode instalá-lo através de cmd.exe ou de powershell. Utilize direitos elevados para estes comandos ("Executar como Administrador").

Utilizando cmd.exe, executar o seguinte comando:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Utilizando a Power Shell, executar o seguinte comando:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Agora pode instalar o braço utilizando o seguinte comando em cmd.exe ou PowerShell`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Obtenção da sua configuração VM atual

Para alterar os atributos da configuração do disco, primeiro precisa de obter a configuração atual num ficheiro JSON. Pode obter a configuração atual executando o seguinte comando:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Substitua os termos dentro de '<<   >>' pelos seus dados, incluindo o nome do ficheiro que o ficheiro JSON deve ter.

A saída pode parecer:

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

Em seguida, atualize o ficheiro JSON e ative o Acelerador de Escrita no disco chamado 'log1'. Isto pode ser conseguido adicionando este atributo no ficheiro JSON após a entrada em cache do disco.

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

Em seguida, atualize a implementação existente com este comando:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

A saída deve parecer a de baixo. Pode ver que o Acelerador de Escrita está ativado para um disco.

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

Uma vez feita esta alteração, a unidade deve ser suportada pelo Write Accelerator.

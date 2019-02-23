---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 02/22/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 44533bc6ed0656be387fa76c0e975046ad7f79ab
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740815"
---
# <a name="enable-write-accelerator"></a>Ativar o acelerador de escrita

Escreva que Accelerator é um recurso de disco à série M máquinas virtuais (VMs) no armazenamento Premium com Azure Managed Disks exclusivamente. Como o nome indica, a finalidade da funcionalidade é melhorar a latência de e/s de escrita no armazenamento Premium do Azure. Escreva que Accelerator é ideal em que as atualizações de arquivo de log são necessárias para manter as para o disco num modo de alto desempenho altamente para bases de dados modernos.

Escreva que Accelerator está em disponibilidade geral para VMs de série M na Cloud pública.

## <a name="planning-for-using-write-accelerator"></a>Planeamento de acelerador de escrita de utilização

Escreva que acelerador deve ser utilizado para os volumes que contêm o registo de transações ou registos de um DBMS de Refazer. Não é recomendado utilizar acelerador de escrita para os volumes de dados de um DBMS, que o recurso foi otimizado para ser utilizada no âmbito de discos de registo.

Escrever Accelerator só funciona em conjunto com [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Ativar o acelerador de escrita para o disco de sistema operativo da VM irá reiniciar a VM.
>
> Para ativar o acelerador de escrita para um disco do Azure existente que não faz parte de uma compilação de volume de vários discos com os gestores de disco ou volume do Windows, espaços de armazenamento Windows, o servidor de ficheiros de escalamento com o Windows horizontal (SOFS), LVM do Linux ou MDADM, a carga de trabalho acessar o disco do Azure tem de ser encerradas. TEM de encerrar a aplicativos de banco de dados a utilizar o disco do Azure.
>
> Se pretender ativar ou desativar o acelerador de escrita de mensagens em fila para um volume existente que baseia-se fora do vários discos de armazenamento Premium do Azure e distribuídos com os gestores de disco ou volume do Windows, de espaços de armazenamento do Windows, Windows escaláveis ficheiros servidor (SOFS), LVM de Linux ou MDADM, todos os discos que criar o volume tem de ser ativados ou desativados para acelerador de escrita nos passos separados. **Antes de ativar ou desativar o acelerador de escrita em tal configuração, encerre a VM do Azure**.

Ativar o acelerador de escrita para discos de SO não é necessário para configurações de VM relacionados com o SAP.

### <a name="restrictions-when-using-write-accelerator"></a>Restrições de utilização de acelerador de escrita

Ao usar o acelerador de escrita para um VHD/disco do Azure, estas restrições aplicam-se:

- O cache de disco Premium deve ser definido como 'None' ou "Só de leitura". Todos os outros modos de colocação em cache não são suportados.
- Instantâneo não são atualmente suportadas para discos com acelerador de escrita ativado. Durante a cópia de segurança, o serviço Azure Backup exclui automaticamente os discos com acelerador de escrita ativado, ligados à VM.
- Apenas os tamanhos de e/s menores (< = 512 KiB) estão a tirar o melhor caminho. Na carga de trabalho situações em que está a ficar em massa dados carregados ou em que os buffers de log de transação de diferentes DBMS são preenchidos para um maior grau antes da introdução persistida para o armazenamento, é provável que a e/s escrito para disco não está a demorar o caminho acelerado.

Existem limites de VHDs de armazenamento Premium do Azure por VM, que pode ser suportado pelo acelerador de escrita. Os limites atuais são:

| SKU DE VM | Número de discos de acelerador de escrita | Escrever Accelerator disco IOPS por VM |
| --- | --- | --- |
| M128ms, 128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Os limites IOPS são por VM e *não* por disco. Todos os discos de acelerador de escrita partilham o mesmo limite de IOPS por VM.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Ativar o acelerador de escrita num disco específico

As secções seguintes descrevem como o acelerador de escrita pode ser ativada em VHDs de armazenamento Premium do Azure.

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos aplicam-se à utilização do acelerador de escrita no momento:

- Os discos que pretende aplicar o acelerador de escrita do Azure em relação tem de ser [discos geridos do Azure](https://azure.microsoft.com/services/managed-disks/) no armazenamento Premium.
- Tem de utilizar uma VM de série M

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Ativar o acelerador de escrita de Azure com o Azure PowerShell

O módulo do Azure Power Shell da versão 5.5.0 incluem as alterações para os cmdlets relevantes para ativar ou desativar o acelerador de escrita para discos de armazenamento Premium do Azure específicos.
Para ativar ou implementar discos suportados pela capacidade acelerador de escrita, os seguintes comandos de Power Shell foi alterados e estendidos para aceita um parâmetro do acelerador de escrita.

Um novo parâmetro, **- WriteAccelerator** foi adicionado para os seguintes cmdlets:

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Não fornecer o parâmetro define a propriedade como false e irá implementar discos com nenhum suporte por acelerador de escrita.

Um novo parâmetro, **- OsDiskWriteAccelerator** foi adicionado para os seguintes cmdlets:

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Não especificar o parâmetro define a propriedade como false por padrão, retornando discos que não tiram partido do acelerador de escrita.

Um novo booleano (nulo) parâmetro opcional, **- OsDiskWriteAccelerator** foi adicionado para os seguintes cmdlets:

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Especifique $true ou $false para controlar o suporte do acelerador de escrita do Azure com os discos.

Exemplos de comandos podem ter o seguinte aspeto:

```PowerShell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Dois cenários principais podem ser colocado em script conforme mostrado nas seções a seguir.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Adicionar um novo disco suportado pelo acelerador de escrita de mensagens em fila com o PowerShell

Pode utilizar este script para adicionar um novo disco à VM. O disco criado com este script utiliza o acelerador de escrita.

Substitua `myVM`, `myWAVMs`, `log001`, tamanho do disco e LunID do disco com valores adequados para a sua implementação específica.

```PowerShell
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

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Ativar o acelerador de escrita num disco do Azure existente com o PowerShell

Pode utilizar este script para ativar o acelerador de escrita num disco existente. Substitua `myVM`, `myWAVMs`, e `test-log001` com valores adequados para a sua implementação específica. O script adiciona o acelerador de escrita para um disco existente onde o valor para **$newstatus** está definida como '$true'. Usando o valor '$false' irá desativar o acelerador de escrita num determinado disco.

```PowerShell
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
> Executar o script acima serão desanexar o disco especificado, ativar o acelerador de escrita no disco e, em seguida, anexar o disco novamente

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Ativar o acelerador de escrita de mensagens em fila com o portal do Azure

Pode ativar o acelerador de escrita através do portal em que especificar seu disco, as definições de colocação em cache:

![Acelerador de escrita no portal do Azure](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Ativar o acelerador de escrita com a CLI do Azure

Pode utilizar o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para ativar o acelerador de escrita.

Para ativar o acelerador de escrita num disco existente, utilize [atualização do az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), pode usar os exemplos seguintes, se substituir o diskName VMName e ResourceGroup pelos seus próprios valores: `az vm update -g group1 -n vm1 -write-accelerator 1=true`

Anexar um disco com acelerador de escrita ativado uso [anexar o disco da vm az](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach), pode usar o exemplo seguinte, se substituir em seus próprios valores: `az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Para desabilitar o acelerador de escrita, usar [atualização do az vm](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update), definindo as propriedades como false: `az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Ativar o acelerador de escrita de mensagens em fila utilizando Rest APIs

Para implementar através da API Rest do Azure, terá de instalar o armclient do Azure.

### <a name="install-armclient"></a>Instalar armclient

Para executar armclient, terá de instalá-lo usando o Chocolatey. Pode instalá-lo por meio de cmd.exe ou powershell. Utilize direitos elevados para estes comandos ("Executar como administrador").

Utilizar cmd.exe, execute o seguinte comando: `@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Utilizar o Power Shell, execute o seguinte comando: `Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Agora pode instalar o armclient utilizando o seguinte comando no cmd.exe ou o PowerShell `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Obter a configuração da VM atual

Para alterar os atributos da sua configuração de disco, tem primeiro de obter a configuração atual num ficheiro JSON. Pode obter a configuração atual, executando o seguinte comando: `armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Substitua os termos nos <> <>com os seus dados, incluindo o nome do ficheiro que deve ter o ficheiro JSON.

A saída pode ser parecida com:

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

Em seguida, atualize o ficheiro JSON e para ativar o acelerador de escrita no disco chamado 'log1'. Pode fazê-lo ao adicionar este atributo para o ficheiro JSON após a entrada de cache do disco.

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

Em seguida, atualize a implementação existente com este comando: `armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

O resultado deverá ser semelhante ao mostrado abaixo. Pode ver que o acelerador de escrita ativado para um disco.

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

Depois de fazer esta alteração, a unidade deve ser suportada pela acelerador de escrita.

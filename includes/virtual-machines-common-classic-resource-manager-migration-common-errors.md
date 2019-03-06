---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: aa58890f74c7c667189fa10c009769de043227c4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457827"
---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Erros comuns durante a migração da implementação Clássica para a implementação Azure Resource Manager
Este artigo cataloga os erros e mitigações mais comuns durante a migração de recursos de IaaS do modelo de implementação clássica do Azure para a pilha do Azure Resource Manager.

## <a name="list-of-errors"></a>Lista de erros
| Cadeia do erro | Mitigação |
| --- | --- |
| Erro de servidor interno |Em alguns casos, este é um erro transitório que desaparece com uma nova tentativa. Se persistir, [contacte o suporte do Azure](../articles/azure-supportability/how-to-create-azure-support-request.md), pois requer que sejam investigados os registos da plataforma. <br><br> **NOTA:** Assim que o incidente é controlado pela equipe de suporte, não tente qualquer atenuação automática pois pode originar consequências indesejadas no seu ambiente. |
| A migração não é suporta na Implementação {deployment-name} em HostedService {hosted-service-name}, porque é uma implementação de PaaS (função da Web/Trabalho). |Isto acontece quando uma implementação contém uma função da Web/trabalho. Uma vez que a migração só é suportada para Máquinas Virtuais, remova a função da Web/Trabalho da implementação e repita a migração. |
| Falha na implementação de modelo {template-name}. CorrelationId={guid} |No back-end do serviço de migração, utilizamos modelos do Azure Resource Manager para criar recursos na pilha do Azure Resource Manager. Uma vez que os modelos são idempotent, pode, normalmente, repetir a operação de migração em segurança para ultrapassar este erro. Se o mesmo persistir, [contacte o suporte do Azure](../articles/azure-supportability/how-to-create-azure-support-request.md) e dê o CorrelationId ao técnico. <br><br> **NOTA:** Assim que o incidente é controlado pela equipe de suporte, não tente qualquer atenuação automática pois pode originar consequências indesejadas no seu ambiente. |
| A rede virtual {virtual-network-name} não existe. |Isto pode acontecer se tiver criado a Rede Virtual no novo portal do Azure. O nome da Rede Virtual real segue o padrão "Grupo * <VNET name>". |
| A VM {vm-name} em HostedService {hosted-service-name} contém a Extensão {extension-name}, que o Azure Resource Manager não suporta. Recomenda-se desinstalá-la da VM antes de continuar a migração. |O Azure Resource Manager suporta extensões XML, como BGInfo 1.*. Por conseguinte, estas extensões não podem ser migradas. Se permanecerem instaladas na máquina virtual, são desinstaladas automaticamente antes de a migração ser concluída. |
| A VM {vm-name} em HostedService {hosted-service-name} contém a Extensão VMSnapshot/VMSnapshotLinux, que não é suportada, atualmente, para Migração. Desinstale-a da VM e adicione-a novamente com o Azure Resource Manager após a Conclusão da Migração. |Este é o cenário em que a máquina virtual está configurada para o Azure Backup. Uma vez que esta é atualmente um cenário não suportado, siga a solução disponível em https://aka.ms/vmbackupmigration |
| A VM {vm-name} em HostedService {hosted-service-name} contém a Extensão {extension-name}, cujo Estado não está a ser reportado a partir da VM. Por este motivo, esta VM não pode ser migrada. Verifique se o estado da Extensão é reportado ou desinstale-a da VM e repita a migração. <br><br> A VM {vm-name} em HostedService {hosted-service-name} contém a Extensão {extension-name}, que reporta o Estado do Processador: {handler-status}. Por este motivo, a VM não pode ser migrada. Verifique se o estado do processador da Extensão reportado é {handler-status} ou desinstale-a da VM e repita a migração. <br><br> O Agente da VM para a VM {vm-name} em HostedService {hosted-service-name} está a reportar o estado do agente geral como Não Pronto. Por conseguinte, a VM pode não ser migrada, se tiver uma extensão que pode ser migrada. Confirme que o estado do agente geral que o Agente da VM está a reportar é Pronto. Consulte https://aka.ms/classiciaasmigrationfaqs. |O agente convidado do Azure e as Extensões da VM precisam de acesso de saída à Internet para a conta de armazenamento do Azure para preencher o respetivo estado. As causas comuns das falhas de estado incluem <li> Um Grupo de Segurança de Rede que bloqueia o acesso de saída à Internet <li> Se a VNET tiver no local, servidores DNS e a conectividade DNS se perder <br><br> Se continuar a ver um estado não suportado, pode desinstalar as extensões para ignorar esta verificação e avançar com a migração. |
| A migração não é suporta na Implementação {deployment-name} em HostedService {hosted-service-name}, porque tem vários Conjuntos de Disponibilidade. |Atualmente, só podem ser migrados serviços alojados que tenham um ou menos Conjuntos de Disponibilidade. Para contornar este problema, mova os Conjuntos de Disponibilidade adicionais e as Máquinas Virtuais nos mesmos para outro serviço alojado. |
| A migração não é suportada na Implementação {deployment-name} em HostedService {hosted-service-name, porque tem VMs que não fazem parte do Conjunto de Disponibilidade, apesar de haver um no HostedService. |A solução para este cenário é mover todas as máquinas virtuais para um único Conjunto de Disponibilidade ou removê-las todas do Conjunto de Disponibilidade no serviço alojado. |
| O Serviço Alojado/a Conta de armazenamento/a Rede Virtual {virtual-network-name} está no processo de migração, pelo que não pode ser alterado. |Este erro acontece quando a operação de migração “Preparar” é concluída no recurso e uma operação que faria uma alteração ao mesmo é acionada. Devido ao bloqueio no plano de gestão após a operação “Preparar”, todas as alterações ao recurso são bloqueadas. Para desbloquear o plano de gestão, pode executar a operação de migração “Consolidar”, para concluir a migração, ou “Abortar” a mesma, para reverter a operação “Preparar”. |
| Migração não é permitida em HostedService {hosted-service-name}, porque a VM {vm-name} no Estado: RoleStateUnknown. A migração só é permitida se a VM estiver num dos estados seguintes - Em Execução, Parada, Parada (Desalocada). |A VM poderá estar a ser submetida a uma transição de estado, o que, geralmente, acontece durante uma operação de atualização no HostedService, como um reinício, a instalação de uma extensão, etc. Recomenda-se que a operação de atualização no HostedService seja concluída antes de repetir a migração. |
| A implementação {deployment-name} em HostedService {hosted-service-name} contém uma VM {vm-name} com um Disco de Dados {data-disk-name}, cujos bytes do tamanho físico do blob {size-of-the-vhd-blob-backing-the-data-disk} não correspondem aos bytes tamanho lógico do Disco de Dados da VM {size-of-the-data-disk-specified-in-the-vm-api}. A migração prossegue sem especificar um tamanho para o disco de dados da VM do Azure Resource Manager. | Este erro acontece se tiver redimensionado o blob do VHD sem atualizar o tamanho no modelo da API da VM. Os passos de mitigação detalhados estão descritos [abaixo](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Ocorreu uma exceção de armazenamento ao validar o disco de dados {data disk name} com a ligação de multimédia {data disk Uri} para a VM {VM name} no Serviço Cloud {Cloud Service name}. Confirme que a ligação de multimédia do VHD está acessível para esta máquina virtual. | Este erro pode acontecer se os discos da VM tiverem sido eliminados ou já não estiverem acessíveis. Certifique-se de que os discos da VM existem.|
| A VM {vm-name} em HostedService {cloud-service-name} contém o Disco com a Ligação de Multimédia {vhd-uri}, que tem o nome de blob {vhd-blob-name}, o qual não é suportado no Azure Resource Manager. | Este erro ocorre quando o nome do blob tem uma "/" no mesmo, o que não é atualmente suportado no Fornecedor de Recursos de Computação. |
| A migração não é suportada na Implementação {deployment-name} em HostedService {cloud-service-name}, pois não está no âmbito regional. Consulte http://aka.ms/regionalscope para mover esta implementação para o âmbito regional. | Em 2014, o Azure anunciou que os recursos de rede vão passar de um âmbito de nível de cluster para o âmbito regional. Veja [http://aka.ms/regionalscope] para obter mais detalhes (http://aka.ms/regionalscope). Este erro ocorre se a implementação que está a ser migrada não tiver tido uma operação de atualização, o que a move automaticamente para um âmbito regional. A melhor solução é adicionar um ponto final a uma VM ou um disco de dados à VM e repetir a migração. <br> Veja [How to set up endpoints on a classic Windows virtual machine in Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) (Como configurar pontos finais em máquinas virtuais do Windows clássicas no Azure) ou [Attach a data disk to a Windows virtual machine created with the classic deployment model](../articles/virtual-machines/windows/classic/attach-disk.md) (Ligar um disco de dados a uma máquina virtual do Windows criada com o modelo de implementação clássica).|
| Migração não é suportada para a rede Virtual {vnet-name}, porque tem implementações de PaaS sem gateways. | Este erro ocorre quando tem implementações de PaaS sem gateways como os serviços de Gateway de aplicação ou gestão de API que estão ligadas à rede Virtual.|


## <a name="detailed-mitigations"></a>Mitigações detalhadas

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>VM com Disco de Dados cujos bytes de tamanho físico do blob não correspondem aos bytes do tamanho lógico do Disco de Dados da VM.

Isto acontece se o tamanho lógico do Disco de Dados ficar dessincronizado com o tamanho real do blob do VHG. Pode utilizar os comandos seguintes para verificar este problema:

#### <a name="verifying-the-issue"></a>Verificar o problema

```PowerShell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Mitigar o problema

```PowerShell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Mover uma VM para outra subscrição depois de concluir a migração

Depois de concluir o processo de migração, poderá querer mover a VM para outra subscrição. No entanto, se tiver um segredo/certificado na VM que faça referência a um recurso do Key Vault, a mudança não é atualmente suportada. As instruções abaixo permitem-lhe resolver o problema. 

#### <a name="powershell"></a>PowerShell
```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```
#### <a name="azure-cli"></a>CLI do Azure

```bash
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```

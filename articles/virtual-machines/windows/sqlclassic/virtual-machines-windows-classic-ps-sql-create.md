---
title: Criar uma máquina virtual SQL Server no Azure PowerShell (clássico) | Microsoft Docs
description: Fornece etapas e scripts do PowerShell para criar uma VM do Azure com SQL Server imagens da Galeria de máquinas virtuais. Este tópico usa o modo de implantação clássico.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a4c7c29736cdd80ef7ebe413a377aba630d61858
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101875"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Provisionar uma máquina virtual SQL Server usando Azure PowerShell (clássico)

Este artigo fornece etapas sobre como criar uma máquina virtual SQL Server no Azure usando os cmdlets do PowerShell.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Para a versão do Resource Manager deste tópico, consulte [provisionar um SQL Server máquina virtual usando o Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalar e configurar o PowerShell:
1. Se não tiver uma conta do Azure, aceda a [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Baixe e instale os comandos mais recentes do Azure PowerShell](/powershell/azure/overview).
3. Inicie o Windows PowerShell e conecte-o à sua assinatura do Azure com o comando **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Determinar sua região do Azure de destino

Sua máquina virtual SQL Server será hospedada em um serviço de nuvem que reside uma região específica do Azure. As etapas a seguir ajudam a determinar sua região, conta de armazenamento e serviço de nuvem que será usado para o restante do tutorial.

1. Determine o data center que você deseja usar para hospedar sua VM SQL Server. O comando do PowerShell a seguir exibe uma lista de nomes de região disponíveis.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Depois de identificar seu local preferido, defina uma variável chamada **$dcLocation** para essa região. Por exemplo, o comando a seguir define a região como "leste dos EUA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Definir sua assinatura e conta de armazenamento

1. Determine a assinatura do Azure que será usada para a nova máquina virtual.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Atribua sua assinatura do Azure de destino à variável **$subscr** . Em seguida, defina isso como sua assinatura atual do Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Em seguida, verifique se há contas de armazenamento existentes. O script a seguir exibe todas as contas de armazenamento que existem na sua região escolhida:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Se você precisar de uma nova conta de armazenamento, primeiro crie um nome de conta de armazenamento com todas as letras minúsculas com o comando New-AzureStorageAccount, como no exemplo a seguir:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Atribua o nome da conta de armazenamento de destino ao **$staccount**. Em seguida, use **set-AzureSubscription** para definir a assinatura e a conta de armazenamento atual.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Selecionar uma imagem de máquina virtual SQL Server

1. Descubra a lista de imagens de máquinas virtuais SQL Server disponíveis da galeria. Todas essas imagens têm uma propriedade **ImageFamily** que começa com "SQL". A consulta a seguir exibe a família de imagens disponível para você que tem SQL Server pré-instalado.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Quando você encontrar a família de imagens da máquina virtual, pode haver várias imagens publicadas nesta família. Use o script a seguir para localizar o nome da imagem de máquina virtual publicada mais recente para sua família de imagens selecionada (como **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Por fim, crie a máquina virtual com o PowerShell:

1. Crie um serviço de nuvem para hospedar a nova VM. Observe que também é possível usar um serviço de nuvem existente. Crie uma nova variável **$svcname** com o nome curto do serviço de nuvem.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Especifique o nome da máquina virtual e um tamanho. Para obter mais informações sobre tamanhos de máquina virtual, consulte [tamanhos de máquina virtual para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Especifique a conta de administrador local e a senha.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Execute o script a seguir para criar a máquina virtual.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Para obter mais explicações e opções de configuração, consulte a seção **criar seu conjunto de comandos** em [usar Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas no Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exemplo de script do PowerShell

O script a seguir fornece um exemplo de um script completo que cria um **SQL Server 2016 RTM Enterprise na máquina virtual do Windows Server 2012 R2** . Se você usar esse script, deverá personalizar as variáveis iniciais com base nas etapas anteriores neste tópico.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Conectar-se à área de trabalho remota

1. Crie os arquivos RDP na pasta de documentos do usuário atual para iniciar essas máquinas virtuais para concluir a instalação:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. No diretório documentos, inicie o arquivo RDP. Conecte-se com o nome de usuário e a senha do administrador fornecidos anteriormente (por exemplo, se o seu nome de usuário era VMAdmin, especifique "\VMAdmin" como o usuário e forneça a senha).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Concluir a configuração do computador SQL Server para acesso remoto

Depois de fazer logon no computador com a área de trabalho remota, configure SQL Server com base nas instruções em [etapas para configurar a conectividade de SQL Server em uma VM do Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Passos Seguintes

Você pode encontrar instruções adicionais para provisionar máquinas virtuais com o PowerShell na [documentação de máquinas virtuais](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Em muitos casos, a próxima etapa é migrar seus bancos de dados para essa nova SQL Server VM. Para obter diretrizes de migração de banco de dados, consulte [migrando um banco de dados para SQL Server em uma VM do Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Se você também estiver interessado em usar o portal do Azure para criar máquinas virtuais do SQL, consulte Provisionando [uma máquina virtual SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Observe que o tutorial que orienta você pelo portal cria VMs usando o modelo do Resource Manager recomendado, em vez do modelo clássico usado neste tópico do PowerShell.

Além desses recursos, recomendamos que você revise [outros tópicos relacionados à execução de SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

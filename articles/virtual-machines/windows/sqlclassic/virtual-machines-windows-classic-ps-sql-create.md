---
title: Criar um VM clássico do Servidor SQL (PowerShell)
description: Fornece passos e scripts PowerShell para criar um Azure VM com imagens de galeria de máquinas virtuais SQL Server. Este tópico utiliza o modo de implantação clássico.
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914794"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprovisionar uma máquina virtual do SQL Server no Azure PowerShell (Clássico)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Este artigo fornece passos para como criar uma máquina virtual SQL Server em Azure utilizando os cmdlets PowerShell.

> [!NOTE] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Para a versão do Gestor de Recursos deste tópico, consulte [a Provision a SQL Server virtual machine utilizando o Gestor](../sql/virtual-machines-windows-ps-sql-create.md)de Recursos Da PowerShell .

### <a name="install-and-configure-powershell"></a>Instale e configure powerShell:
1. Se não tiver uma conta do Azure, aceda a [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Descarregue e instale os mais recentes comandos Azure PowerShell](/powershell/azure/overview).
3. Inicie o Windows PowerShell e conecte-o à subscrição do Azure com o comando **Add-AzureAccount.**

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Determine o seu alvo na região de Azure

A sua Máquina Virtual SQL Server será hospedada num serviço de nuvem que reside numa região azure específica. Os seguintes passos ajudam-no a determinar a sua região, conta de armazenamento e serviço de nuvem que será usado para o resto do tutorial.

1. Determine o centro de dados que pretende utilizar para hospedar o seu VM do Servidor SQL. O comando PowerShell seguinte apresenta uma lista de nomes de região disponíveis.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Assim que identificar a sua localização preferida, detetete uma variável chamada **$dcLocation** para aquela região. Por exemplo, o seguinte comando define a região para "Leste dos EUA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Delineie a sua conta de subscrição e armazenamento

1. Determine a subscrição Azure que utilizará para a nova máquina virtual.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Atribuir a subscrição do seu alvo Azure à variável **$subscr.** Em seguida, detete isto como a sua subscrição atual do Azure.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Em seguida, verifique se existem as contas de armazenamento existentes. O seguinte script exibe todas as contas de armazenamento que existem na sua região escolhida:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Se necessitar de uma nova conta de armazenamento, crie primeiro um nome de conta de armazenamento com o comando New-AzureStorageAccount, como no seguinte exemplo:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Designar o nome da conta de armazenamento-alvo para o **$staccount**. Em seguida, utilize a **Subscrição Set-Azure** para definir a conta de subscrição e armazenamento corrente.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Selecione uma imagem virtual do Servidor SQL

1. Descubra a lista de imagens de máquinas virtuais SQL Server disponíveis na galeria. Todas estas imagens têm uma propriedade **ImageFamily** que começa com "SQL". A seguinte consulta exibe a família de imagem disponível para si que tem o SQL Server pré-instalado.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Quando encontrar a família de imagem de máquina virtual, pode haver várias imagens publicadas nesta família. Utilize o seguinte script para encontrar o mais recente nome de imagem de máquina virtual publicado para a sua família de imagens selecionada (como **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Finalmente, crie a máquina virtual com powerShell:

1. Crie um serviço na nuvem para acolher o novo VM. Note que também é possível utilizar um serviço de nuvem existente. Crie uma nova variável **$svcname** com o nome curto do serviço de nuvem.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Especifique o nome da máquina virtual e um tamanho. Para obter mais informações sobre tamanhos de máquinas virtuais, consulte [tamanhos de máquina virtual para Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Especifique a conta de administrador local e a palavra-passe.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Executar o seguinte script para criar a máquina virtual.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Para obter opções adicionais de explicação e configuração, consulte a secção **'Construir** o conjunto de comandos' em [Use Azure PowerShell para criar e reconfigurar máquinas virtuais baseadas no Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exemplo de script do PowerShell

O seguinte script fornece um exemplo de um script completo que cria um **SQL Server 2016 RTM Enterprise na máquina virtual Do Windows Server 2012 R2.** Se utilizar este script, deve personalizar as variáveis iniciais com base nos passos anteriores neste tópico.

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

## <a name="connect-with-remote-desktop"></a>Conecte-se com o ambiente de trabalho remoto

1. Crie os ficheiros RDP na pasta de documentos do utilizador atual para lançar estas máquinas virtuais para completar a configuração:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. No diretório de documentos, lance o ficheiro RDP. Conecte-se com o nome de utilizador do administrador e a palavra-passe fornecida anteriormente (por exemplo, se o seu nome de utilizador fosse VMAdmin, especifique "\VMAdmin" como utilizador e forneça a palavra-passe).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Complete a configuração da Máquina de Servidor SQL para acesso remoto

Depois de iniciar sessão na máquina com ambiente de trabalho remoto, configure o Servidor SQL com base nas instruções em [Passos para configurar a conectividade do Servidor SQL num Azure VM](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Passos seguintes

Pode encontrar instruções adicionais para o fornecimento de máquinas virtuais com powerShell na documentação das [máquinas virtuais](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Em muitos casos, o próximo passo é migrar as suas bases de dados para este novo VM do Servidor SQL. Para obter orientação de migração de base de dados, consulte migrating de uma base de [dados para o Servidor SQL num VM Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Se também estiver interessado em utilizar o portal Azure para criar máquinas virtuais SQL, consulte [o Provisioning a SQL Server Virtual Machine no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Note que o tutorial que o acompanha através do portal cria VMs usando o modelo recomendado Desemge, em vez do modelo clássico usado neste tópico PowerShell.

Além destes recursos, recomendamos que reveja [outros tópicos relacionados com a execução do Servidor SQL em Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

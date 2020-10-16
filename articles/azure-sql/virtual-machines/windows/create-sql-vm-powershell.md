---
title: Guia para utilizar o Azure PowerShell para provisões SQL Server em Azure VM
description: Fornece passos e comandos PowerShell para a criação de um Azure VM com imagens de galeria de máquinas virtuais SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bb3fd9136f78a332a22f973d211dec748c4fb260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317077"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Como utilizar o Azure PowerShell para o fornecimento de servidor SQL em Máquinas Virtuais Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este guia cobre as opções de utilização do PowerShell para o fornecimento de servidor SQL em Máquinas Virtuais Azure (VMs). Para obter um exemplo Azure PowerShell simplificado que se baseie em valores predefinidos, consulte o [arranque rápido sql VM Azure PowerShell](sql-vm-create-powershell-quickstart.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Configurar a sua subscrição

1. Abra o PowerShell e estabeleça o acesso à sua conta Azure executando o comando **Connect-AzAccount.**

   ```powershell
   Connect-AzAccount
   ```

1. Quando lhe for pedido, introduza as suas credenciais. Utilize o mesmo e-mail e palavra-passe que utiliza para iniciar sessão no portal do Azure.

## <a name="define-image-variables"></a>Definir variáveis de imagem

Para reutilizar valores e simplificar a criação de scripts, comece por definir uma série de variáveis. Altere os valores dos parâmetros como quiser, mas esteja ciente das restrições de nomeação relacionadas com os comprimentos do nome e caracteres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Localização e grupo de recursos

Defina a região de dados e o grupo de recursos onde pretende criar os outros recursos VM.

Modifique como quiser e, em seguida, executar estes cmdlets para inicializar estas variáveis.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Propriedades de armazenamento

Defina a conta de armazenamento e o tipo de armazenamento a utilizar pela máquina virtual.

Modifique como quiser e, em seguida, executar o cmdlet seguinte para rubricar estas variáveis. Recomendamos a utilização [de SSDs premium](../../../virtual-machines/disks-types.md#premium-ssd) para cargas de trabalho de produção.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriedades da rede

Defina as propriedades a utilizar pela rede na máquina virtual. 

- Interface de rede
- Método de atribuição de TCP/IP
- Nome da rede virtual
- Nome da sub-rede virtual
- Gama de endereços IP para a rede virtual
- Gama de endereços IP para a sub-rede
- Etiqueta de nome de domínio público

Modifique como quiser e, em seguida, executar este cmdlet para inicializar estas variáveis.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Propriedades de máquinas virtuais

Definir as seguintes propriedades:

- Nome da máquina virtual
- Nome do computador
- Tamanho da máquina virtual
- Nome do disco do sistema operativo para a máquina virtual

Modifique como quiser e, em seguida, executar este cmdlet para inicializar estas variáveis.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Escolha uma imagem do SqL Server

Utilize as seguintes variáveis para definir a imagem do SQL Server para utilizar para a máquina virtual. 

1. Em primeiro lugar, enuseia todas as ofertas de imagem do SQL Server com o `Get-AzVMImageOffer` comando. Este comando lista as imagens atuais que estão disponíveis no portal Azure e também imagens mais antigas que só podem ser instaladas com PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Para este tutorial, utilize as seguintes variáveis para especificar o SQL Server 2017 no Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Em seguida, enuma as edições disponíveis para a sua oferta.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Para este tutorial, utilize a edição de desenvolvimento do SQL Server 2017 **(SQLDEV).** A edição developer é livremente licenciada para testes e desenvolvimento, e você só paga pelo custo de executar o VM.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Com o modelo de implementação do Gestor de Recursos, o primeiro objeto que cria é o grupo de recursos. Utilize o [cmdlet New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos Azure e seus recursos. Especifique as variáveis que inicializou anteriormente para o nome e localização do grupo de recursos.

Executar este cmdlet para criar o seu novo grupo de recursos.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A máquina virtual requer recursos de armazenamento para o disco do sistema operativo e para os dados do SQL Server e ficheiros de registo. Para simplificar, criará um único disco para ambos. Pode anexar discos adicionais mais tarde utilizando o cmdlet [do disco Add-Azure](/powershell/module/servicemanagement/azure.service/add-azuredisk) para colocar os dados do seu SQL Server e registar ficheiros em discos dedicados. Utilize o [cmdlet New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) para criar uma conta de armazenamento padrão no seu novo grupo de recursos. Especifique as variáveis que inicializou anteriormente para o nome da conta de armazenamento, o nome SKU de armazenamento e a localização.

Gere este cmdlet para criar a sua nova conta de armazenamento.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A criação da conta de armazenamento pode demorar alguns minutos.

## <a name="create-network-resources"></a>Criar recursos de rede

A máquina virtual requer uma série de recursos de rede para a conectividade da rede.

* Cada máquina virtual requer uma rede virtual.
* Uma rede virtual deve ter pelo menos uma sub-rede definida.
* Uma interface de rede deve ser definida com um endereço IP público ou privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede de rede virtual

Comece por criar uma configuração de sub-rede para a sua rede virtual. Para este tutorial, crie uma sub-rede predefinitiva utilizando o cmdlet [New-AzVirtualNetWorkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Especifique as variáveis que inicializou anteriormente para o nome da sub-rede e prefixo de endereço.

> [!NOTE]
> Pode definir propriedades adicionais da configuração da sub-rede de rede virtual usando este cmdlet, mas que está fora do âmbito deste tutorial.

Executar este cmdlet para criar a sua configuração de sub-rede virtual.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Em seguida, crie a sua rede virtual no seu novo grupo de recursos utilizando o cmdlet [New-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Especifique as variáveis que inicializou previamente para o nome, localização e prefixo de endereço. Utilize a configuração da sub-rede que definiu no passo anterior.

Executar este cmdlet para criar a sua rede virtual.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Agora que a sua rede virtual está definida, tem de configurar um endereço IP para conectividade com a máquina virtual. Para este tutorial, crie um endereço IP público utilizando um endereço IP dinâmico para suportar a conectividade da Internet. Utilize o cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) para criar o endereço IP público no seu novo grupo de recursos. Especifique as variáveis que inicializou anteriormente para o nome, localização, método de atribuição e etiqueta de nome de domínio DNS.

> [!NOTE]
> Pode definir propriedades adicionais do endereço IP público usando este cmdlet, mas que está fora do âmbito deste tutorial inicial. Você também pode criar um endereço privado ou um endereço com um endereço estático, mas isso também está fora do âmbito deste tutorial.

Execute este cmdlet para criar o seu endereço IP público.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede

Para proteger o tráfego VM e SQL Server, crie um grupo de segurança de rede.

1. Em primeiro lugar, crie uma regra de grupo de segurança de rede para desktop remoto (RDP) para permitir ligações RDP.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configure uma regra do grupo de segurança de rede que permite o tráfego na porta TCP 1433. Ao fazê-lo, permite ligações ao SQL Server através da internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Crie o grupo de segurança da rede.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Agora está pronto para criar a interface de rede para a sua máquina virtual. Utilize o cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) para criar a interface de rede no seu novo grupo de recursos. Especifique o nome, localização, sub-rede e endereço IP público previamente definido.

Executar este cmdlet para criar a sua interface de rede.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configure um objeto VM

Agora que os recursos de armazenamento e rede estão definidos, está pronto para definir recursos computacional para a máquina virtual.

- Especifique o tamanho da máquina virtual e várias propriedades do sistema operativo.
- Especifique a interface de rede que criou anteriormente.
- Defina o armazenamento de bolhas.
- Especifique o disco do sistema operativo.

### <a name="create-the-vm-object"></a>Criar o objeto VM

Comece por especificar o tamanho da máquina virtual. Para este tutorial, especifique um DS13. Utilize o cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) para criar um objeto de máquina virtual configurável. Especifique as variáveis que inicializou anteriormente para o nome e tamanho.

Executar este cmdlet para criar o objeto da máquina virtual.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Crie um objeto credencial para manter o nome e a palavra-passe para as credenciais do administrador local

Antes de definir as propriedades do sistema operativo para a máquina virtual, deve fornecer as credenciais para a conta do administrador local como uma cadeia segura. Para isso, utilize o [cmdlet Get-Credential.](https://technet.microsoft.com/library/hh849815.aspx)

Executar o seguinte cmdlet. Terá de digitar o nome e a palavra-passe do administrador local do VM na janela de pedido de credencial PowerShell.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Desaperte as propriedades do sistema operativo para a máquina virtual

Agora está pronto para definir as propriedades do sistema operativo da máquina virtual com o [cmdlet Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Desaperte o tipo de sistema operativo como Windows.
- Exija a instalação do [agente de máquinas virtual.](../../../virtual-machines/extensions/agent-windows.md)
- Especifique que o cmdlet permite a atualização automática.
- Especifique as variáveis que inicializou anteriormente para o nome da máquina virtual, o nome do computador e a credencial.

Executar este cmdlet para definir as propriedades do sistema operativo para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicione a interface de rede à máquina virtual

Em seguida, utilize o [cmdlet Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para adicionar a interface de rede utilizando a variável que definiu anteriormente.

Executar este cmdlet para definir a interface de rede para a sua máquina virtual.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Descreva o local de armazenamento do blob para o disco a ser utilizado pela máquina virtual

Em seguida, defina o local de armazenamento do blob para o disco do VM com as variáveis que definiu anteriormente.

Executar este cmdlet para definir o local de armazenamento do blob.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Desaperte as propriedades do disco do sistema operativo para a máquina virtual

Em seguida, detenva as propriedades do disco do sistema operativo para a máquina virtual utilizando o [cmdlet Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Especifique que o sistema operativo da máquina virtual virá de uma imagem.
- Desajuste de cache para ler apenas (porque o SQL Server está a ser instalado no mesmo disco).
- Especifique as variáveis que inicializou anteriormente para o nome VM e o disco do sistema operativo.

Executar este cmdlet para definir as propriedades do disco do sistema operativo para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificar a imagem da plataforma para a máquina virtual

O último passo de configuração é especificar a imagem da plataforma para a sua máquina virtual. Para este tutorial, utilize a mais recente imagem CTP do SQL Server 2016. Utilize o [cmdlet Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) para utilizar esta imagem com as variáveis que definiu anteriormente.

Executar este cmdlet para especificar a imagem da plataforma para a sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Criar a VM do SQL

Agora que terminou os passos de configuração, está pronto para criar a máquina virtual. Utilize o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a máquina virtual utilizando as variáveis que definiu.

> [!TIP]
> A criação da VM poderá demorar alguns minutos.

Executar este cmdlet para criar a sua máquina virtual.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A máquina virtual é criada.

> [!NOTE]
> Se tiver um erro sobre diagnósticos de arranque, pode ignorá-lo. Uma conta de armazenamento padrão é criada para diagnóstico de arranque porque a conta de armazenamento especificada para o disco da máquina virtual é uma conta de armazenamento premium.

## <a name="install-the-sql-iaas-agent"></a>Instalar o Agente Iaas do SQL

As máquinas virtuais sql Server suportam funcionalidades de gestão automatizadas com a extensão do [agente iaaS do servidor SQL](sql-server-iaas-agent-extension-automate-management.md). Para instalar o agente no novo VM e registá-lo junto do fornecedor de recursos, executar o comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) após a criação da máquina virtual. Especifique o tipo de licença para o seu SQL Server VM, escolhendo entre o pay-as-you-go ou trazer a sua própria licença através do [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Para obter mais informações sobre o licenciamento, consulte [o modelo de licenciamento.](licensing-model-azure-hybrid-benefit-ahb-change.md) 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Parar ou remover um VM

Se não precisar que o VM seja executado continuamente, pode evitar cargas desnecessárias parando-a quando não estiver a ser utilizada. O comando seguinte para a VM, mas deixa-a disponível para utilização futura.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também pode eliminar permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup.** Ao fazê-lo, também elimina permanentemente a máquina virtual, por isso utilize este comando com cuidado.

## <a name="example-script"></a>Script de exemplo

O seguinte script contém o script completo do PowerShell para este tutorial. Assume que já criou a subscrição Azure para utilizar com os comandos **Connect-AzAccount** e **Select-AzSubscription.**

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Passos seguintes

Depois da máquina virtual ser criada, pode:

- Ligue-se à máquina virtual utilizando RDP
- Configurar as definições do SERVIDOR SQL no portal para o seu VM, incluindo:
   - [Definições de armazenamento](storage-configuration.md) 
   - [Tarefas de gestão automatizadas](sql-server-iaas-agent-extension-automate-management.md)
- [Configurar a conectividade](ways-to-connect-to-sql.md)
- Ligue clientes e aplicações à nova instância do SQL Server

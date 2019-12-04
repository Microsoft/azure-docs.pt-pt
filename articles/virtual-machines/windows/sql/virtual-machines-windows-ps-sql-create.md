---
title: Guia de provisionamento para VMs SQL Server com Azure PowerShell | Microsoft Docs
description: Fornece etapas e comandos do PowerShell para criar uma VM do Azure com SQL Server imagens da Galeria de máquinas virtuais.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790629"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Como provisionar SQL Server máquinas virtuais com Azure PowerShell

Este guia explica as opções para criar VMs do Windows SQL Server com Azure PowerShell. Para obter um exemplo de Azure PowerShell simplificado com mais valores padrão, consulte o [início rápido da VM do SQL Azure PowerShell](quickstart-sql-vm-create-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Configurar sua assinatura

1. Abra o PowerShell e estabeleça o acesso à sua conta do Azure executando o comando **Connect-AzAccount** .

   ```powershell
   Connect-AzAccount
   ```

1. Você deverá ver uma tela para inserir suas credenciais. Utilize o mesmo e-mail e palavra-passe que utiliza para iniciar sessão no portal do Azure.

## <a name="define-image-variables"></a>Definir variáveis de imagem
Para reutilizar valores e simplificar a criação de scripts, Comece definindo um número de variáveis. Altere os valores de parâmetro conforme desejar, mas lembre-se de restrições de nomenclatura relacionadas a comprimentos de nome e caracteres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Local e grupo de recursos
Defina a região de dados e o grupo de recursos no qual você cria os outros recursos de VM.

Modifique conforme desejado e, em seguida, execute esses cmdlets para inicializar essas variáveis.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Propriedades de armazenamento
Defina a conta de armazenamento e o tipo de armazenamento a ser usado pela máquina virtual.

Modifique conforme desejado e, em seguida, execute o cmdlet a seguir para inicializar essas variáveis. É recomendável usar o [SSDs Premium](../disks-types.md#premium-ssd) para cargas de trabalho de produção.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriedades da rede
Defina as propriedades a serem usadas pela rede na máquina virtual. 

- Interface de rede
- Método de alocação TCP/IP
- Nome da rede virtual
- Nome da sub-rede virtual
- Intervalo de endereços IP para a rede virtual
- Intervalo de endereços IP para a sub-rede
- Rótulo de nome de domínio público

Modifique conforme desejado e, em seguida, execute este cmdlet para inicializar essas variáveis.

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

### <a name="virtual-machine-properties"></a>Propriedades da máquina virtual
Defina o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco do sistema operacional para a máquina virtual.

Modifique conforme desejado e, em seguida, execute este cmdlet para inicializar essas variáveis.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Escolher uma imagem de SQL Server

Use as variáveis a seguir para definir a imagem de SQL Server a ser usada para a máquina virtual. 

1. Primeiro, liste todas as ofertas de imagem SQL Server com o comando `Get-AzVMImageOffer`. Este comando lista as imagens atuais que estão disponíveis no portal do Azure e também as imagens mais antigas que só podem ser instaladas com o PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Para este tutorial, use as seguintes variáveis para especificar SQL Server 2017 no Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Em seguida, liste as edições disponíveis para sua oferta.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Para este tutorial, use o SQL Server 2017 Developer Edition (**SQLDEV**). A edição Developer é licenciada gratuitamente para teste e desenvolvimento, e você só paga pelo custo da execução da VM.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Com o modelo de implantação do Gerenciador de recursos, o primeiro objeto que você cria é o grupo de recursos. Use o cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos do Azure e seus recursos. Especifique as variáveis que você inicializou anteriormente para o nome e o local do grupo de recursos.

Execute este cmdlet para criar seu novo grupo de recursos.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Create a storage account
A máquina virtual requer recursos de armazenamento para o disco do sistema operacional e para os SQL Server de dados e arquivos de log. Para simplificar, você criará um único disco para ambos. Você pode anexar discos adicionais mais tarde usando o cmdlet [Add-Azure Disk](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) para posicionar seus SQL Server dados e arquivos de log em discos dedicados. Use o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) para criar uma conta de armazenamento padrão em seu novo grupo de recursos. Especifique as variáveis que você inicializou anteriormente para o nome da conta de armazenamento, o nome do SKU de armazenamento e o local.

Execute este cmdlet para criar sua nova conta de armazenamento.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> A criação da conta de armazenamento pode levar alguns minutos.

## <a name="create-network-resources"></a>Criar recursos de rede
A máquina virtual requer vários recursos de rede para conectividade de rede.

* Cada máquina virtual requer uma rede virtual.
* Uma rede virtual deve ter pelo menos uma sub-rede definida.
* Uma interface de rede deve ser definida com um endereço IP público ou privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede de rede virtual
Comece criando uma configuração de sub-rede para sua rede virtual. Para este tutorial, crie uma sub-rede padrão usando o cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . Especifique as variáveis que você inicializou anteriormente para o nome da sub-rede e o prefixo do endereço.

> [!NOTE]
> Você pode definir propriedades adicionais da configuração de sub-rede da rede virtual usando este cmdlet, mas isso está além do escopo deste tutorial.

Execute este cmdlet para criar sua configuração de sub-rede virtual.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Criar rede virtual
Em seguida, crie sua rede virtual em seu novo grupo de recursos usando o cmdlet [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) . Especifique as variáveis que você inicializou anteriormente para o nome, o local e o prefixo de endereço. Use a configuração de sub-rede que você definiu na etapa anterior.

Execute este cmdlet para criar sua rede virtual.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público
Agora que sua rede virtual está definida, você deve configurar um endereço IP para conectividade com a máquina virtual. Para este tutorial, crie um endereço IP público usando o endereçamento IP dinâmico para dar suporte à conectividade com a Internet. Use o cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) para criar o endereço IP público em seu novo grupo de recursos. Especifique as variáveis que você inicializou anteriormente para o nome, o local, o método de alocação e o rótulo de nome de domínio DNS.

> [!NOTE]
> Você pode definir propriedades adicionais do endereço IP público usando esse cmdlet, mas isso está além do escopo deste tutorial inicial. Você também pode criar um endereço privado ou um endereço com um endereço estático, mas isso também está além do escopo deste tutorial.

Execute este cmdlet para criar seu endereço IP público.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede
Para proteger a VM e o tráfego de SQL Server, crie um grupo de segurança de rede.

1. Primeiro, crie uma regra de grupo de segurança de rede para o RDP para permitir conexões de área de trabalho remota.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configure uma regra de grupo de segurança de rede que permita o tráfego na porta TCP 1433. Isso permite que as conexões SQL Server pela Internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Crie o grupo de segurança de rede.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Criar a interface de rede
Agora você está pronto para criar a interface de rede para sua máquina virtual. Use o cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) para criar a interface de rede em seu novo grupo de recursos. Especifique o nome, o local, a sub-rede e o endereço IP público definido anteriormente.

Execute este cmdlet para criar a interface de rede.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configurar um objeto de VM
Agora que os recursos de armazenamento e de rede estão definidos, você está pronto para definir recursos de computação para a máquina virtual.

- Especifique o tamanho da máquina virtual e várias propriedades do sistema operacional.
- Especifique a interface de rede que você criou anteriormente.
- Defina o armazenamento de BLOBs.
- Especifique o disco do sistema operacional.

### <a name="create-the-vm-object"></a>Criar o objeto da VM
Comece especificando o tamanho da máquina virtual. Para este tutorial, especifique um DS13. Use o cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) para criar um objeto de máquina virtual configurável. Especifique as variáveis que você inicializou anteriormente para o nome e o tamanho.

Execute este cmdlet para criar o objeto de máquina virtual.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Criar um objeto de credencial para armazenar o nome e a senha das credenciais de administrador local
Antes de definir as propriedades do sistema operacional para a máquina virtual, você deve fornecer as credenciais para a conta de administrador local como uma cadeia de caracteres segura. Para fazer isso, use o cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Execute o cmdlet a seguir e, na janela solicitação de credencial do PowerShell, digite o nome e a senha a serem usados para a conta de administrador local na máquina virtual.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Definir as propriedades do sistema operacional para a máquina virtual
Agora você está pronto para definir as propriedades do sistema operacional da máquina virtual com o cmdlet [set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) .

- Defina o tipo de sistema operacional como Windows.
- Exigir que o [agente de máquina virtual](../../extensions/agent-windows.md) seja instalado.
- Especifique que o cmdlet habilita a atualização automática.
- Especifique as variáveis que você inicializou anteriormente para o nome da máquina virtual, o nome do computador e a credencial.

Execute este cmdlet para definir as propriedades do sistema operacional para sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicionar a interface de rede à máquina virtual
Em seguida, use o cmdlet [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) para adicionar a interface de rede usando a variável que você definiu anteriormente.

Execute este cmdlet para definir a interface de rede para sua máquina virtual.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Definir o local de armazenamento de BLOBs para o disco a ser usado pela máquina virtual
Em seguida, defina o local de armazenamento de BLOBs para o disco da VM usando as variáveis que você definiu anteriormente.

Execute este cmdlet para definir o local de armazenamento de BLOBs.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Definir as propriedades de disco do sistema operacional para a máquina virtual
Em seguida, defina as propriedades de disco do sistema operacional para a máquina virtual usando o cmdlet [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) . 

- Especifique que o sistema operacional da máquina virtual será proveniente de uma imagem.
- Defina o cache como somente leitura (porque SQL Server está sendo instalado no mesmo disco).
- Especifique as variáveis que você inicializou anteriormente para o nome da VM e o disco do sistema operacional.

Execute este cmdlet para definir as propriedades de disco do sistema operacional para sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificar a imagem da plataforma para a máquina virtual
A última etapa de configuração é especificar a imagem da plataforma para sua máquina virtual. Para este tutorial, use a imagem mais recente do SQL Server CTP 2016. Use o cmdlet [set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) para usar essa imagem com as variáveis que você definiu anteriormente.

Execute este cmdlet para especificar a imagem da plataforma para sua máquina virtual.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Criar a VM do SQL
Agora que você concluiu as etapas de configuração, você está pronto para criar a máquina virtual. Use o cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar a máquina virtual usando as variáveis que você definiu.

> [!TIP]
> A criação da VM pode levar alguns minutos.

Execute este cmdlet para criar sua máquina virtual.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A máquina virtual é criada.

> [!NOTE]
> Se você receber um erro sobre o diagnóstico de inicialização, poderá ignorá-lo. Uma conta de armazenamento padrão é criada para o diagnóstico de inicialização porque a conta de armazenamento especificada para o disco da máquina virtual é uma conta de armazenamento Premium.

## <a name="install-the-sql-iaas-agent"></a>Instalar o Agente Iaas do SQL
SQL Server máquinas virtuais dão suporte a recursos de gerenciamento automatizado com a [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para instalar o agente na nova VM e registrá-lo com o provedor de recursos, execute o comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) depois que a máquina virtual for criada. Especifique o tipo de licença para sua VM SQL Server, escolhendo entre pagamento conforme o uso ou traga sua própria licença por meio do [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Para obter mais informações sobre licenciamento, consulte [modelo de licenciamento](virtual-machines-windows-sql-ahb.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Parar ou remover uma VM

Se você não precisar que a VM seja executada continuamente, poderá evitar encargos desnecessários interrompendo-o quando não estiver em uso. O comando seguinte para a VM, mas deixa-a disponível para utilização futura.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Você também pode excluir permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup** . Isso também exclui permanentemente a máquina virtual, portanto, use esse comando com cuidado.

## <a name="example-script"></a>Script de exemplo
O script a seguir contém o script completo do PowerShell para este tutorial. Ele pressupõe que você já configurou a assinatura do Azure para usar com os comandos **Connect-AzAccount** e **Select-AzSubscription** .

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
Depois que a máquina virtual for criada, você poderá:

- Conectar-se à máquina virtual usando o RDP
- Defina as configurações de SQL Server no portal para sua VM, incluindo:
   - [Configurações de armazenamento](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Tarefas de gerenciamento automatizado](virtual-machines-windows-sql-server-agent-extension.md)
- [Configurar a conectividade](virtual-machines-windows-sql-connect.md)
- Conectar clientes e aplicativos à nova instância de SQL Server


---
title: Criar O Servidor SQL numa máquina virtual do Windows com Azure PowerShell Microsoft Docs
description: Este tutorial mostra como usar o Azure PowerShell para criar uma máquina virtual Windows que executa o SQL Server 2017.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356842"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Quickstart: Criar o SQL Server numa máquina virtual do Windows com Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este quickstart avança através da criação de uma máquina virtual SQL Server (VM) com Azure PowerShell.

> [!TIP]
> - Este início rápido proporciona um caminho para aprovisionar e ligar a uma VM do SQL de forma rápida. Para obter mais informações sobre outras opções do Azure PowerShell para a criação de VMs do SQL, veja o [Guia de aprovisionamento para VMs do SQL Server com o Azure PowerShell](create-sql-vm-powershell.md).
> - Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Obtenha uma assinatura Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Obter Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Configurar o PowerShell

1. Abra o PowerShell e estabeleça o acesso à sua conta Azure executando o comando **Connect-AzAccount.**

   ```powershell
   Connect-AzAccount
   ```

1. Quando vir a janela de entrada, insira as suas credenciais. Utilize o mesmo e-mail e palavra-passe que utiliza para iniciar sessão no portal do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Defina uma variável com um nome de grupo de recursos exclusivo. Para simplificar o resto do arranque rápido, os restantes comandos usam este nome como base para outros nomes de recursos.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina a localização de uma região do Azure de destino para todos os recursos da VM.

   ```powershell
   $Location = "East US"
   ```

1. Crie o grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Configurar as definições de rede

1. Crie uma rede virtual, uma sub-rede e um endereço IP público. Estes recursos são utilizados para fornecer conectividade de rede à máquina virtual e ligá-la à Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Criar um grupo de segurança de rede. Configure regras para permitir ligações de ambiente de trabalho remoto (RDP) e de SQL Server.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Crie a interface de rede.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Criar a VM do SQL

1. Defina as suas credenciais para iniciar serção no VM. O nome de utilizador é "azureadmin". Certifique-se de que muda \<password> antes de executar o comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Crie um objeto de configuração de máquina virtual e, em seguida, crie a VM. O comando seguinte cria uma VM do SQL Server 2017 Developer Edition no Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Demora alguns minutos a criar a VM.

## <a name="register-with-sql-vm-rp"></a>Registo com Fornecedor de Recursos da VM do SQL 

Para obter a integração do portal e as funcionalidades SQL VM, tem de se registar com a [extensão sql IaaS Agent](sql-agent-extension-manually-register-single-vm.md).

Para obter a funcionalidade completa, terá de se registar com a extensão em pleno modo. No entanto, ao fazê-lo reinicia o serviço SQL Server, pelo que a abordagem recomendada é registar-se em modo leve e, em seguida, atualizar para a totalidade durante uma janela de manutenção. 

Primeiro, registe o seu SQL Server VM no modo leve: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Em seguida, durante uma janela de manutenção, atualize para o modo completo: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Ambiente de trabalho remoto na VM

1. Utilize o seguinte comando para recuperar o endereço IP público para o novo VM.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Passe o endereço IP devolvido como um parâmetro de linha de comando para **mstsc** para iniciar uma sessão de Desktop Remoto no novo VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando lhe forem pedidas as credenciais, introduza credenciais para uma conta diferente. Introduza o nome de utilizador com um backslash anterior (por exemplo, `\azureadmin` ) e a palavra-passe que definiu anteriormente neste arranque rápido.

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

1. Depois de iniciar sessão no Remote Desktop, lance o **SQL Server Management Studio 2017** a partir do menu inicial.

1. Na caixa de diálogo **'Ligar ao Servidor',** mantenha as predefinições. O nome do servidor é o nome da VM. A autenticação está definida como **Autenticação do Windows**. Selecione **Ligar**.

Está agora ligado ao SQL Server localmente. Se pretender ligar-se remotamente, tem de [configurar a conectividade](ways-to-connect-to-sql.md) a partir do portal Azure ou manualmente.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não precisar que o VM seja executado continuamente, pode evitar cargas desnecessárias parando-a quando não estiver a ser utilizada. O comando seguinte para a VM, mas deixa-a disponível para utilização futura.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também pode eliminar permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup.** Ao fazê-lo, também elimina permanentemente a máquina virtual, por isso utilize este comando com cuidado.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma máquina virtual do SQL Server 2017 com o Azure PowerShell. Para saber mais sobre como migrar os seus dados para o novo SQL Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Migrar uma base de dados para uma VM de SQL](migrate-to-vm-from-sql-server.md)

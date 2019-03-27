---
title: Criar uma VM do SQL Server do Windows com o Azure PowerShell | Microsoft Docs
description: Este tutorial mostra como criar uma máquina virtual do SQL Server 2017 do Windows com o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43a5f3be9b176cf25e643d6a5231669922300b98
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485939"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Início rápido: Criar uma máquina virtual do SQL Server do Windows com o Azure PowerShell

Estes passos de início rápido explicam a criação de uma máquina virtual do SQL Server com o Azure PowerShell.

> [!TIP]
> Este início rápido proporciona um caminho para aprovisionar e ligar a uma VM do SQL de forma rápida. Para obter mais informações sobre outras opções do Azure PowerShell para a criação de VMs do SQL, veja o [Guia de aprovisionamento para VMs do SQL Server com o Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Obter uma subscrição do Azure

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a id="powershell"></a> Obter Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Configurar o PowerShell

1. Abra o PowerShell e estabeleça o acesso à sua conta do Azure ao executar o **Connect-AzAccount** comando.

   ```powershell
   Connect-AzAccount
   ```

1. Verá um ecrã para introduzir as suas credenciais. Utilize o mesmo e-mail e palavra-passe que utiliza para iniciar sessão no portal do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Defina uma variável com um nome de grupo de recursos exclusivo. Para simplificar o resto do início rápido, os comandos restantes utilizam este nome como base para outros nomes de recursos.

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

1. Crie um grupo de segurança de rede. Configure regras para permitir ligações de ambiente de trabalho remoto (RDP) e de SQL Server.

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

1. Defina as suas credenciais para iniciar sessão na VM. O nome de utilizador é "azureadmin". Certifique-se de alterar \<palavra-passe > antes de executar o comando.

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
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 | `
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Demora alguns minutos a criar a VM.

## <a name="install-the-sql-iaas-agent"></a>Instalar o agente IaaS do SQL

Para obter a integração do portal e as funcionalidades da VM do SQL, tem de instalar a [Extensão do Agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para instalar o agente na nova VM, execute o seguinte comando, após a VM é criada.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Ambiente de trabalho remoto na VM

1. Utilize o seguinte comando para obter o endereço IP público para a nova VM.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Passar o endereço IP devolvido como um parâmetro da linha de comandos **mstsc** para iniciar uma sessão de ambiente de trabalho remoto na nova VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando lhe forem pedidas as credenciais, introduza credenciais para uma conta diferente. Introduza o nome de utilizador com uma barra invertida precedente (por exemplo, `\azureadmin`) e a palavra-passe definida anteriormente neste guia de introdução.

## <a name="connect-to-sql-server"></a>Ligar ao SQL Server

1. Depois de entrar para a sessão de ambiente de trabalho remoto, inicie **SQL Server Management Studio 2017** do menu Iniciar.

1. Na **ligar ao servidor** diálogo caixa, mantenha as predefinições. O nome do servidor é o nome da VM. A autenticação está definida como **Autenticação do Windows**. Selecione **Ligar**.

Agora está ligado ao SQL Server localmente. Se quiser ligar remotamente, deve [configurar a conectividade](virtual-machines-windows-sql-connect.md) no portal ou manualmente.

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar da VM seja executada continuamente, pode evitar despesas desnecessárias ao pará-la quando não está em utilização. O comando seguinte para a VM, mas deixa-a disponível para utilização futura.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também pode eliminar permanentemente todos os recursos associados à máquina virtual com o **Remove-AzResourceGroup** comando. Se o fizer, permanentemente elimina a máquina virtual também, por isso, utilize este comando com cuidado.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma máquina virtual do SQL Server 2017 com o Azure PowerShell. Para saber mais sobre como migrar os seus dados para o novo SQL Server, consulte o seguinte artigo.

> [!div class="nextstepaction"]
> [Migrar uma base de dados para uma VM de SQL](virtual-machines-windows-migrate-sql.md)

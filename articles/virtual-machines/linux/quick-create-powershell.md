---
title: Quickstart - Criar um Linux VM com Azure PowerShell
description: Neste início rápido, vai aprender a utilizar o Azure PowerShell para criar uma máquina virtual do Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5711e3f406af04d0dc3a233031379c9ef18244ba
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549656"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Guia de Início Rápido: Criar uma máquina virtual do Linux no Azure com o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este início rápido mostra como utilizar o módulo do Azure PowerShell para implementar uma máquina virtual (VM) do Linux no Azure. Este quickstart usa a imagem de mercado Ubuntu 18.04 LTS da Canonical. Para ver a VM em ação, irá estabelecer o SSH para a VM e instalar o servidor Web NGINX.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Use [o ssh-keygen](https://www.ssh.com/ssh/keygen/) para criar um par de chaves SSH. Se já tiver um par de chaves SSH, pode ignorar este passo.


```azurepowershell-interactive
ssh-keygen -m PEM -t rsa -b 4096
```

Será solicitado que forneça um nome de ficheiro para o par de chaves ou pode bater **Enter** para utilizar a localização padrão de `/home/<username>/.ssh/id_rsa` . Também poderá criar uma senha para as teclas, se quiser.

Para obter informações mais detalhadas sobre como criar pares de chaves SSH, consulte [como utilizar as chaves SSH com o Windows](ssh-from-windows.md).

Se criar o seu par de chaves SSH utilizando o Cloud Shell, será armazenado numa [conta de armazenamento que é criada automaticamente pela Cloud Shell](../../cloud-shell/persisting-shell-storage.md). Não apague a conta de armazenamento, nem os ficheiros partilham nela, até que tenha recuperado as suas chaves ou perderá o acesso ao VM. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Criar recursos de rede virtual

Crie uma rede virtual, uma sub-rede e um endereço IP público. Estes recursos servem para fornecer conectividade de rede à VM e ligá-la à Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Crie uma regra de tráfego e um Grupo de Segurança de Rede do Azure. O Grupo de Segurança de Rede protege a VM com regras de entrada e de saída. No exemplo seguinte, é criada uma regra de entrada para a porta TCP 22 que permite ligações SSH. Para permitir o tráfego Web de entrada, é também criada uma regra de entrada para a porta TCP 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Crie um cartão de interface de rede virtual (NIC) com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). A NIC virtual liga a VM a uma sub-rede, a um Grupo de Segurança de Rede e a um endereço IP público.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma VM no PowerShell, crie uma configuração que tenha definições como a imagem para as opções de utilização, tamanho e autenticação. Em seguida, a configuração é utilizada para criar a VM.

Defina as credenciais SSH, as informações do SO e o tamanho da VM. Neste exemplo, a chave SSH é armazenada no `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Agora, combine as definições de configuração anteriores para criar com [o New-AzVM:](/powershell/module/az.compute/new-azvm)

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Irá demorar alguns minutos até a VM ser implementada. Quando a implementação estiver concluída, avance para a secção seguinte.

## <a name="connect-to-the-vm"></a>Ligar à VM

Crie uma ligação SSH com a VM com o endereço IP público. Para ver o endereço IP público do VM, utilize o cmdlet [Get-AzPublicIpAddress:](/powershell/module/az.network/get-azpublicipaddress)

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Utilizando a mesma concha que usou para criar o seu par de chaves SSH, cole o seguinte comando na concha para criar uma sessão de SSH. Substitua *o 10.111.12.123* pelo endereço IP do seu VM.

```bash
ssh azureuser@10.111.12.123
```

Quando lhe for pedido, o nome de utilizador de início de sessão é *azureuser*. Se utilizar uma frase de acesso com as chaves SSH, deverá introduzi-la quando solicitado.


## <a name="install-nginx"></a>Instalar o NGINX

Para ver a VM em ação, instale o servidor Web NGINX. Na sessão SSH, atualize as origens do pacote e, em seguida, instale o pacote NGINX mais recente.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, escreva `exit` para deixar a sessão SSH.


## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Utilize um browser à sua escolha para ver a página predefinida de boas-vindas do NGINX. Introduza o endereço IP público da VM como o endereço Web. O endereço IP público pode ser encontrado na página de descrição geral da VM ou como parte da cadeia de ligação SSH que utilizou anteriormente.

![Página de boas-vindas padrão NGINX](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue para o tutorial das VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais das máquinas virtuais do Linux do Azure](./tutorial-manage-vm.md)

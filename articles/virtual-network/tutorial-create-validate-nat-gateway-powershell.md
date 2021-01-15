---
title: Criar e testar uma porta DE NAT - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway NAT usando o Azure PowerShell e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: d9f3fa67a0d3eee303ed307f1d64d30955348869
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222501"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Tutorial: Criar uma porta de entrada NAT utilizando a Azure PowerShell e testar o serviço NAT

Neste tutorial, você vai criar uma porta de entrada NAT para fornecer conectividade de saída para máquinas virtuais em Azure. Para testar o gateway NAT, você implanta uma máquina virtual de origem e destino. Você vai testar o gateway NAT fazendo ligações de saída para um endereço IP público. Estas ligações virão da fonte para a máquina virtual de destino. Este tutorial implementa a origem e o destino em duas diferentes redes virtuais no mesmo grupo de recursos para a simplicidade.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pode completar este tutorial usando a Azure Cloud Shell ou executar os respetivos comandos localmente.  Se nunca usou a Azure Cloud Shell, deve [entrar agora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2:**


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>Criar o portal NAT

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

Para aceder à Internet, precisa de um ou mais endereços IP públicos para o gateway NAT. Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPsource** no **myResourceGroupNAT**. O resultado deste comando será armazenado numa variável denominada **$publicIPsource** para posterior utilização.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

 Utilize [o Novo Prefixo New-AzPublicIp](/powershell/module/az.network/new-azpublicipprefix?view=latest) para criar um recurso de prefixo IP público chamado **myPublicIPprefix no** **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável denominada **$publicIPPrefixsource** para posterior utilização.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool IP público e prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Altere o tempo de inatividade do padrão de 4 minutos para 10 minutos.

Crie uma porta de entrada Azure NAT global com [a New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). O resultado deste comando criará um recurso de gateway chamado **myNATgateway** que utiliza o endereço IP público **myPublicIPsource** e o prefixo ip público **myPprefixsource**. O tempo limite de marcha lenta está definido para 10 minutos.  O resultado deste comando será armazenado numa variável denominada **$natGateway** para posterior utilização.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

Neste momento, o gateway NAT está funcional e tudo o que falta é configurar quais as sub-redes de uma rede virtual que devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a fonte para o tráfego de saída

Vamos guiá-lo através de um ambiente de teste completo. Irá configurar um teste utilizando ferramentas de código aberto para verificar o gateway NAT. Começaremos com a fonte, que usará o portal NAT que criamos anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configure a rede virtual para a origem

Crie a rede virtual e associe a sub-rede ao gateway.

Crie uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mySubnetsource** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereço IP para a rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos será armazenado em variáveis denominadas **$subnetsource** e **$vnetsource** para posterior utilização.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

Todo o tráfego de saída para destinos de Internet está agora a usar o serviço NAT.  Não é necessário configurar uma UDR.

Antes de testarmos o portal NAT, precisamos de criar uma fonte VM.  Atribuiremos um recurso de endereço IP público como um IP público de nível de instância para aceder a este VM a partir do exterior. Este endereço é utilizado apenas para aceder ao mesmo para o teste.  Vamos demonstrar como o serviço NAT tem precedência sobre outras opções de saída.

Você também poderia criar este VM sem um IP público e criar outro VM para usar como uma caixa de salto sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para origem VM

Criamos um IP público para ser usado para aceder ao VM.  Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPVM** no **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável denominada **$publicIpsourceVM** para posterior utilização.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Crie um NSG e exponha o ponto final SSH para VM

Como os endereços IP públicos standard são "seguros por padrão", criamos um NSG para permitir o acesso à entrada para o ssh. O serviço NAT está consciente da direção do fluxo. Este NSG não será utilizado para saída uma vez que o gateway NAT esteja configurado na mesma sub-rede. Utilize [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) para criar um recurso NSG chamado **myNSGsource**. Utilize [o New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso **SSH nomeado ssh** no **myResourceGroupNAT**. O resultado deste comando será armazenado em variável denominada **$nsgsource** para posterior utilização.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>Criar NIC para vm de origem

Crie uma interface de rede com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) chamado **myNicsource**. Este comando irá associar o endereço IP público e o grupo de segurança da rede. O resultado deste comando será armazenado numa variável denominada **$nicsource** para posterior utilização.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Criar um VM de origem

#### <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Precisa de um par de chaves SSH para concluir este início rápido. Se já tiver um par de chaves SSH, pode ignorar este passo.

Use o ssh-keygen para criar um par de chaves SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Para obter informações mais detalhadas sobre como criar pares de chaves SSH, incluindo a utilização do PuTTy, veja [Como utilizar chaves SSH com o Windows](../virtual-machines/linux/ssh-from-windows.md).

Se criar o par de chaves SSH utilizando a Cloud Shell, o par de chaves é armazenado numa imagem de recipiente. Esta [conta de armazenamento é criada automaticamente.](../cloud-shell/persisting-shell-storage.md) Não apague a conta de armazenamento, ou a partilha de ficheiros no interior, até que recupere as suas chaves.

#### <a name="create-vm-configuration"></a>Criar configuração VM

Para criar uma VM no PowerShell, crie uma configuração que tenha definições como a imagem para as opções de utilização, tamanho e autenticação. Em seguida, a configuração é utilizada para criar a VM.

Defina as credenciais SSH, as informações do SO e o tamanho da VM. Neste exemplo, a tecla SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar um VM chamado **myVMsource** com [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) no **myResourceGroupNAT**.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

Embora o comando regresse imediatamente, pode levar alguns minutos para o VM ser acionado.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para tráfego de saída

Vamos agora criar um destino para o tráfego de saída traduzido pelo serviço NAT para permitir testá-lo.

### <a name="configure-virtual-network-for-destination"></a>Configurar rede virtual para destino

Precisamos criar uma rede virtual onde estará a máquina virtual de destino.  Estes comandos são os mesmos passos que para a fonte VM. Foram adicionadas pequenas alterações para expor o ponto final do destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede chamada **mySubnetdestination** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). O espaço de endereço IP para a rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos será armazenado em variáveis denominadas **$subnetdestination** e **$vnetdestination** para posterior utilização.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para destino VM

Criamos um IP público para ser usado para aceder ao destino VM.  Utilize [o New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** in **myResourceGroupNAT**.  O resultado deste comando será armazenado numa variável denominada **$publicIpdestinationVM** para posterior utilização.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Crie um NSG e exponha o ponto final SSH e HTTP para VM

Os endereços IP públicos padrão são "seguros por padrão", criamos um NSG para permitir o acesso à entrada para o ssh. Utilize [o New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) para criar um recurso NSG chamado **myNSGdestination**. Utilize [o New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso **SSH nomeado ssh**.  Utilize [o New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) para criar uma regra NSG para acesso HTTP com o nome **http**. Ambas as regras serão criadas no **myResourceGroupNAT**. O resultado deste comando será armazenado numa variável denominada **$nsgdestination** para posterior utilização.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para destino VM

Crie uma interface de rede com [o New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) chamado **myNicdestination**. Este comando associar-se-á ao endereço IP público e ao grupo de segurança da rede. O resultado deste comando será armazenado numa variável denominada **$nicdestination** para posterior utilização.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Criar um VM de destino

#### <a name="create-vm-configuration"></a>Criar configuração VM

Para criar uma VM no PowerShell, crie uma configuração que tenha definições como a imagem para as opções de utilização, tamanho e autenticação. Em seguida, a configuração é utilizada para criar a VM.

Defina as credenciais SSH, as informações do SO e o tamanho da VM. Neste exemplo, a tecla SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar um VM chamado **myVMdestination** com [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) no **myResourceGroupNAT**.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

Embora o comando regresse imediatamente, pode levar alguns minutos para o VM ser acionado.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Prepare um servidor web e teste a carga útil no VM de destino

Primeiro precisamos descobrir o endereço IP do destino VM.  Para obter o endereço IP público do VM, utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Inscreva-se no destino VM

As credenciais SSH devem ser armazenadas na sua Cloud Shell da operação anterior.  Abra uma [Azure Cloud Shell](https://shell.azure.com) no seu navegador. Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Copie e cole os seguintes comandos depois de ter iniciado sessão.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Estes comandos irão atualizar a sua máquina virtual, instalar nginx e criar um ficheiro de 100 KBytes. Este ficheiro será recuperado a partir da fonte VM utilizando o serviço NAT.

Feche a sessão SSH com o destino VM.

## <a name="prepare-test-on-source-vm"></a>Preparar teste na fonte VM

Primeiro, temos de descobrir o endereço IP da fonte VM.  Para obter o endereço IP público do VM, utilize [o Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa utilizá-lo nos passos seguintes. Indique que esta é a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Iniciar sessão na fonte VM

Mais uma vez, as credenciais SSH são armazenadas na Cloud Shell. Abra um novo separador para [Azure Cloud Shell](https://shell.azure.com) no seu navegador.  Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Copiar e colar os seguintes comandos para preparar o teste do serviço NAT.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Estes comandos irão atualizar a sua máquina virtual, instalar-se em movimento, instalar [hey](https://github.com/rakyll/hey) do GitHub e atualizar o ambiente da sua concha.

Está agora pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Ao iniciar sessão na origem VM, pode utilizar **curl** e **hey** para gerar pedidos para o endereço IP de destino.

Use caracóis para recuperar o ficheiro 100-KBytes.  Substitua **\<ip-address-destination>** no exemplo abaixo pelo endereço IP de destino que já copiou anteriormente.  O parâmetro **de saída** indica que o ficheiro recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

Também pode gerar uma série de pedidos usando **hey**. Mais uma vez, **\<ip-address-destination>** substitua o endereço IP de destino que já copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Este comando gerará 100 pedidos, 10 simultaneamente, com um intervalo de tempo de 30 segundos. A ligação TCP não será reutilizada.  Cada pedido recuperará 100 Kbytes.  No final da corrida, **vai** relatar algumas estatísticas sobre como o serviço NAT se saiu bem.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) para remover o grupo de recursos e todos os recursos contidos no seu interior.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, criou uma porta de entrada NAT, criou uma VM de origem e destino, e depois testou o gateway NAT.

Reveja as métricas no Azure Monitor para ver o seu serviço NAT a funcionar. Diagnosticar problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é facilmente abordado adicionando recursos adicionais de endereços IP públicos ou recursos prefixos IP públicos ou ambos.

- Saiba mais sobre [a Rede Virtual NAT](./nat-overview.md)
- Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
- Quickstart para implantar [recurso de gateway NAT utilizando O Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart para implantar [recurso de gateway NAT utilizando Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart para implantar [recurso de gateway NAT utilizando o portal Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]
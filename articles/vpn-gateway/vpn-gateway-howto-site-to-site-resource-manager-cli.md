---
title: 'Ligar a sua rede no local a uma rede virtual do Azure: VPN de site a Site: CLI | Microsoft Docs'
description: Passos para criar uma ligação IPsec da sua rede no local a uma rede virtual do Azure através da Internet pública. Estes passos ajudam-no a criar uma ligação de Gateway de Rede de VPNs em vários sites com a CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 6cf427ee1dbd47d3b762035abc2236bda65db116
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773185"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Criar uma rede virtual com uma ligação de Rede de VPNs através da CLI

Este artigo mostra-lhe como utilizar a CLI do Azure para criar uma ligação de gateway de Rede de VPNs a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:<br>

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.
* Pode utilizar o Azure Cloud Shell para executar os comandos da CLI (instruções abaixo). No entanto, se preferir executar comandos localmente, certifique-se de que instalou a versão mais recente dos comandos da CLI (2.0 ou posteriores). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli). 
 
  [!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="example"></a>Valores de exemplo

Pode utilizar os valores seguintes para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Estabelecer a ligação à subscrição

Se optar por executar a CLI localmente, ligar à sua subscrição. Se estiver a utilizar o Azure Cloud Shell no browser, não terá de se ligar à sua subscrição. Irá ligar automaticamente no Azure Cloud Shell. No entanto, pode querer Certifique-se de que está a utilizar a subscrição correta depois de se ligar.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="rg"></a>2. Criar um grupo de recursos

O exemplo seguinte cria um grupo de recursos com o nome “TestRG1” na localização “eastus”. Se já tiver um grupo de recursos na região em que pretende criar a sua VNet, pode utilizá-lo.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="VNet"></a>3. Criar uma rede virtual

Se ainda não tiver uma rede virtual, crie uma utilizando o comando [az network vnet create](/cli/azure/network/vnet). Quando criar uma rede virtual, confirme que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços que tem na sua rede no local.

>[!NOTE]
>Para esta VNet ligar a uma localização no local, terá de se coordenar com o administrador da rede no local para extrair um intervalo de endereços IP que possa utilizar especificamente para esta rede virtual. Se existir um intervalo de endereços duplicados em ambos os lados da ligação VPN, o tráfego não será encaminhado da forma esperada. Além disso, se pretender ligar esta VNet a outra VNet, o espaço de endereços não pode sobrepor-se a outra VNet. Tenha o cuidado de planear a configuração da rede em conformidade.
>
>

O exemplo seguinte cria uma rede virtual com o nome “TestVNet1” e uma sub-rede, “Subnet1”.

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## 4. <a name="gwsub"></a>Criar a sub-rede de gateway


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Utilize o comando [az network vnet subnet create](/cli/azure/network/vnet/subnet) para criar uma sub-rede de gateway.

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="localnet"></a>5. Criar o gateway de rede local

O gateway de rede local refere-se normalmente à sua localização no local. Dê um nome ao site pelo qual o Azure se possa referir ao mesmo e especifique o endereço IP do dispositivo VPN no local para o qual vai criar uma ligação. Também pode especificar os prefixos do endereço IP que vai ser encaminhado através do gateway de VPN para o dispositivo VPN. Os prefixos do endereço que especificar são os que estão localizados na sua rede no local. Se a rede no local se alterar, pode atualizar facilmente os prefixos.

Utilize os seguintes valores:

* O *--gateway-ip-address* é o endereço IP do seu dispositivo VPN no local.
* Os *--local-address-prefixes* são os seus espaços de endereços no local.

Utilize o comando [az network local-gateway create](/cli/azure/network/local-gateway) para adicionar um gateway de rede local com vários prefixos de endereços:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Solicitar um endereço IP Público

Um gateway de VPN deve ter um endereço IP público. Primeira, requeira o recurso de endereço IP e, em seguida, faça referência ao mesmo ao criar o gateway de rede virtual. O endereço IP é dinamicamente atribuído ao recurso quando o gateway de VPN é criado. O Gateway de VPN, atualmente, apenas suporta a alocação de endereços IP públicos *dinâmicos*. Não é possível pedir uma atribuição de endereço IP Público Estático. No entanto, isto não significa que o endereço IP é alterado após ser atribuído ao gateway de VPN. O endereço IP Público só é alterado quando o gateway é eliminado e recriado. Não é alterado ao redimensionar, repor ou ao realizar qualquer outra manutenção/atualização interna do gateway de VPN.

Utilize o comando [az network public-ip create](/cli/azure/network/public-ip) para pedir um endereço IP Público Dinâmico.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Criar o gateway de VPN

Crie o gateway de VPN da rede virtual. A criação de um gateway de VPN pode demorar 45 minutos ou mais a ser concluída.

Utilize os seguintes valores:

* O *--gateway-type* para uma configuração de Rede de VPNs é *Vpn*. O tipo de gateway é sempre específico da configuração que estiver a implementar. Para obter mais informações, veja [Gateway types](vpn-gateway-about-vpn-gateway-settings.md#gwtype) (Tipos de gateways).
* O *--vpn-type* pode ser *RouteBased* (conhecido como Gateway Dinâmico em alguma documentação) ou *PolicyBased* (referido como Gateway Estático em alguma documentação). A definição é específica de requisitos do dispositivo ao qual se está a tentar ligar. Para obter mais informações sobre os tipos de gateway de VPN, veja [About VPN Gateway configuration settings](vpn-gateway-about-vpn-gateway-settings.md#vpntype) (Acerca das definições de configuração do Gateway de VPN).
* Selecione o SKU do Gateway que pretende utilizar. Existem limitações de configuração para determinados SKUs. Para obter mais informações, veja [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Crie o gateway de VPN com o comando [az network vnet-gateway create](/cli/azure/network/vnet-gateway). Se executar este comando utilizando o parâmetro "--no-wait", não verá quaisquer comentários ou saída. Este parâmetro permite que o gateway crie em segundo plano. A criação de um gateway demora cerca de 45 minutos.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="VPNDevice"></a>8. Configurar o dispositivo VPN

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Neste passo, configure o seu dispositivo VPN. Quando configurar o dispositivo VPN, irá precisar do seguinte:

- Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
- O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI. Para encontrar o endereço IP público do gateway da rede virtual, utilize o comando [az network public-ip list](/cli/azure/network/public-ip). Para maior facilidade de leitura, a saída é formatada para apresentar a lista de IPs públicos no formato de tabela.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>9. Criar a ligação VPN

Crie a ligação de Rede de VPNs entre o gateway de rede virtual e o dispositivo VPN no local. Tenha particular atenção ao valor de chave partilhada, que tem de corresponder ao valor de chave partilhada configurado para o seu dispositivo VPN.

Crie a ligação utilizando o comando [az network vpn-connection create](/cli/azure/network/vpn-connection).

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Após uns breves instantes, a ligação será estabelecida.

## <a name="toverify"></a>10. Verificar a ligação VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Se quiser utilizar outro método para verificar a sua ligação, veja [Verify a VPN Gateway connection](vpn-gateway-verify-connection-resource-manager.md) (Verificar ligações de Gateway de VPN).

## <a name="connectVM"></a>Ligar a uma máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="tasks"></a>Tarefas comuns

Esta secção contém comandos comuns que são úteis quando trabalha com configurações de rede. Para obter a lista completa de comandos de redes CLI, veja [Azure CLI - Redes](/cli/azure/network).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](https://docs.microsoft.com/azure/).
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Para obter informações sobre o Túnel Forçado, veja [About Forced Tunneling](vpn-gateway-forced-tunneling-rm.md) (Sobre o Túnel Forçado).
* Para obter informações sobre ligações Altamente Disponíveis Ativo-Ativo, veja [Premissas cruzadas de disponibilidade elevada e ligação VNet para VNet](vpn-gateway-highlyavailable.md).
* Para obter uma lista dos comandos da CLI do Azure de rede, veja [Azure CLI](https://docs.microsoft.com/cli/azure/network) (CLI do Azure).
* Para obter informações sobre como criar uma ligação de VPN de site a site com o modelo do Azure Resource Manager, veja [Create a Site-to-Site VPN Connection (Criar uma Ligação de VPN de Site a Site)](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Para obter informações sobre como criar uma ligação de VPN de vnet a vnet com o modelo do Azure Resource Manager, veja [Deploy HBase geo replication (Implementar georreplicação do HBase)](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

---
title: 'Ligar uma rede virtual a outra VNet com uma ligação VNet a VNet: CLI do Azure | Documentos da Microsoft'
description: Ligue redes virtuais entre si com uma ligação VNet a VNet e a CLI do Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 24e12184070909943c5660d94d8e19ce9df1de30
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58111128"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Configurar uma ligação de gateway de VPN de VNet a VNet com a CLI do Azure

Este artigo ajuda-o a ligar redes virtuais com o tipo de ligação VNet a VNet. As redes virtuais podem estar nas mesmas regiões ou em regiões diferentes e pertencer às mesmas subscrições ou a subscrições diferentes. Ao ligar VNets de diferentes subscrições, estas não têm de estar associadas ao mesmo inquilino do Active Directory.

Os passos deste artigo aplicam-se ao modelo de implementação Resource Manager e utilizam a CLI do Azure. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ligue diferentes modelos de implementação - portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ligue diferentes modelos de implementação - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>Sobre a ligação de VNets

Existem múltiplas formas de ligar VNets. As secções abaixo descrevem as diferentes formas de ligar redes virtuais.

### <a name="vnet-to-vnet"></a>VNet a VNet

Configurar uma ligação VNet a VNet é uma boa forma de ligar facilmente as VNets. Ligar uma rede virtual a outra com o tipo de ligação VNet a VNet é semelhante a criar uma ligação IPsec Site a Site a uma localização no local. Ambos os tipos de conectividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE e funcionam da mesma forma quando estão a comunicar. A diferença entre os tipos de ligação é a forma como o gateway de rede local é configurado. Quando cria uma ligação VNet a VNet, não vê o espaço de endereços do gateway de rede local. Este é criado e preenchido automaticamente. Se atualizar o espaço de endereços de uma VNet, a outra VNet reconhece automaticamente que deve efetuar o encaminhamento para o espaço de endereços atualizado. Criar uma ligação VNet a VNet é, normalmente, mais rápido e fácil do que criar uma ligação Site a Site entre VNets.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Ligar VNets utilizando os passos de Site a Site (IPsec)

Se estiver a trabalhar com uma configuração de rede mais complicada, poderá preferir ligar as VNets utilizando os passos [Site a Site](vpn-gateway-howto-site-to-site-resource-manager-cli.md), em vez de utilizar os passos de VNet a VNet. Quando utilizar os passos de Site a Site, pode criar e configurar manualmente os gateways de rede local. O gateway de rede local para cada VNet trata a outra VNet como um site local. Desta forma, pode especificar um espaço de endereços adicional para o gateway de rede local, de modo a encaminhar o tráfego. Se o espaço de endereço para uma VNet for alterado, terá de atualizar manualmente o gateway de rede local correspondente para refletir a alteração. Não será atualizado automaticamente.

### <a name="vnet-peering"></a>VNet peering

Poderá querer considerar ligar às VNets utilização o VNet Peering. O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes. Além disso, o [preço do VNet peering](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente que os [preços dos Gateways VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para obter mais informações, veja [VNet peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Porquê criar uma ligação VNet a VNet?

Poderá pretender ligar redes virtuais utilizando uma ligação VNet a VNet pelos seguintes motivos:

* **Geopresença e georredundância entre várias regiões**

  * Pode configurar a sua própria georreplicação ou sincronização com uma conetividade segura sem passar por pontos finais com acesso à Internet.
  * Com o Gestor de Tráfego e o Balanceador de Carga do Azure, pode configurar uma carga de trabalho de elevada disponibilidade com georredundância em várias regiões do Azure. Um exemplo importante consiste em configurar o SQL Always On com Grupos de Disponibilidade propagando-se em várias regiões do Azure.
* **Aplicações multicamadas regionais com isolamento ou limites administrativos**

  * Na mesma região, pode configurar aplicações de várias camadas com várias redes virtuais ligadas em conjunto devido a requisitos de isolamento ou administrativos.

A comunicação VNet a VNet pode ser combinada com configurações multilocal. Este procedimento permite-lhe estabelecer topologias de rede que combinam uma conetividade em vários locais com uma conetividade de rede intervirtual.

## <a name="steps"></a>Que passos de VNet a VNet devo utilizar?

Neste artigo, verá dois conjuntos de passos de ligação VNet a VNet diferentes. Um conjunto de passos para [VNets que residem na mesma subscrição](#samesub) e outro para [VNets que residem em diferentes subscrições](#difsub). 

Neste exercício, pode combinar configurações ou escolher apenas aquela com que quer trabalhar. Todas as configurações utilizam o tipo de ligação VNet a VNet. O tráfego de rede flui entre as VNets que estão ligadas diretamente entre si. Neste exercício, o tráfego de TestVNet4 não é encaminhado para TestVNet5.

* [VNets que residem na mesma subscrição:](#samesub) Os passos para esta configuração utilizam TestVNet1 e TestVNet4.

  ![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [VNets que residem em subscrições diferentes:](#difsub) Os passos para esta configuração utilizam TestVNet1 e TestVNet5.

  ![Diagrama v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="samesub"></a>Ligar VNets que estão na mesma subscrição

### <a name="before-you-begin"></a>Antes de começar

Antes de começar, instale a versão mais recente dos comandos da CLI (2.0 ou posterior). Para obter informações sobre como instalar os comandos da CLI, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="Plan"></a>Planear os intervalos de endereços IP

Nos passos seguintes, vai criar duas redes virtuais, juntamente com as respetivas sub-redes de gateway e configurações. Depois, vai criar uma ligação de VPN entre as duas VNets. É importante planear os intervalos de endereços IP da configuração da rede. Nota: precisa confirmar que nenhum dos intervalos de VNet ou intervalos de rede local se sobrepõe de modo algum. Nestes exemplos, não incluímos um servidor DNS. Se pretender a resolução de nomes para as suas redes virtuais, veja [Name resolution](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Resolução de nomes).

Utilizamos os seguintes valores nos exemplos:

**Valores da TestVNet1:**

* Nome da VNet: TestVNet1
* Grupo de Recursos: TestRG1
* Localização: EUA Leste
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* IP público: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): Vnet1avnet5 (para VNets em subscrições diferentes)

**Valores da TestVNet4:**

* Nome da VNet: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Grupo de Recursos: TestRG4
* Localização: EUA Oeste
* GatewayName: VNet4GW
* IP público: VNet4GWIP
* VPNType: RouteBased
* Ligação: VNet4toVNet1

### <a name="Connect"></a>Passo 1 - Ligar à sua subscrição

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>Passo 2 - Criar e configurar a TestVNet1

1. Crie um grupo de recursos.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Crie TestVNet1 e as sub-redes da mesma. Este exemplo cria uma rede virtual com o nome TestVNet1 e uma sub-rede, com o nome FrontEnd.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Crie um espaço de endereços adicional para a sub-rede do back-end. Tenha em conta que, neste passo, especificamos o espaço de endereços criado anteriormente e o espaço novo que queremos adicionar. Isto deve-se ao facto de o comando [az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet) substituir as definições anteriores. Confirme que especifica todos os prefixos de endereços quando utilizar este comando.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Crie a sub-rede do back-end.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Crie a sub-rede de gateway. Repare que o nome da sub-rede de gateway é “GatewaySubnet”. Este nome é obrigatório. Neste exemplo, a sub-rede do gateway está a utilizar um /27. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que crie uma sub-rede maior para incluir mais endereços ao selecionar, pelo menos, /28 ou /27. Esta ação permitirá que um número suficiente de endereços suporte possíveis configurações adicionais que poderá querer no futuro.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Peça para que seja atribuído um endereço IP público ao gateway que vai criar para a VNet. Tenha em atenção que o AllocationMethod é Dinâmico. Não pode especificar o endereço IP que pretende utilizar. É atribuído dinamicamente ao seu gateway.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Crie o gateway de rede virtual para TestVNet1. As configurações VNet a VNet requerem um VpnType RouteBased. Se executar este comando utilizando o parâmetro "--no-wait", não verá quaisquer comentários ou saída. Este parâmetro permite que o gateway seja criado em segundo plano. Não significa que a criação é concluída imediatamente. Muitas vezes, a criação de um gateway demora 45 minutos ou mais, dependendo do SKU de gateway que utilizar.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="TestVNet4"></a>Passo 3 – Criar e configurar TestVNet4

1. Crie um grupo de recursos.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Criar a TestVNet4.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Crie sub-redes adicionais para TestVNet4.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Crie a sub-rede de gateway.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Peça um Endereço IP público.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Crie um gateway da rede virtual TestVNet4.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="createconnect"></a>Passo 4 - Criar as ligações

Agora, tem duas VNets com gateways de VPN. O passo seguinte é criar ligações do gateway de VPN entre os gateways das redes virtuais. Se tiver seguido os exemplos anteriores, os gateways de VNet estão em grupos de recursos diferentes. Quando os gateways estão em diferentes grupos de recursos, tem de identificar e especificar os IDs dos recurso de cada gateway durante a criação das ligações. Se as VNets estiverem no mesmo grupo de recursos, pode utilizar o [segundo conjunto de instruções](#samerg), porque não é necessário especificar os IDs dos recursos.

### <a name="diffrg"></a>Para ligar VNets que residem em diferentes grupos de recursos

1. Obtenha o ID de Recurso de VNet1GW a partir da saída do comando seguinte:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Na saída, localize a linha "id:". Os valores dentro das aspas são necessários para criar a ligação na secção seguinte. Copie estes valores para um editor de texto, como o Notepad, para colá-los facilmente quando estiver a criar a ligação.

   Exemplo de saída:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Copie os valores a seguir a **"id":** entre as aspas.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Obtenha o ID de Recurso de VNet4GW e copie os valores para um editor de texto.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Criar a ligação da TestVNet1 para a TestVNet4. Neste passo, vai criar a ligação da TestVNet1 à TestVNet4. É feita referência a uma chave partilhada nos exemplos. Pode utilizar os seus próprios valores para a chave partilhada. Importante: a chave partilhada tem de corresponder a ambas as ligações. A criação das ligações demora pouco tempo.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Criar a ligação da TestVNet4 para a TestVNet1. Este passo é semelhante ao anterior, exceto que está a criar a ligação da TestVNet4 para a TestVNet1. Verifique se as chaves partilhadas correspondem. A ligação leva algum tempo até ser estabelecida.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Verifique as suas ligações. Veja [Verificar a ligação](#verify).

### <a name="samerg"></a>Para ligar VNets que residem no mesmo grupo de recursos

1. Criar a ligação da TestVNet1 para a TestVNet4. Neste passo, vai criar a ligação da TestVNet1 à TestVNet4. Repare que os grupos de recursos são iguais nos exemplos. Também pode ver uma referência a uma chave partilhada nos exemplos. Pode utilizar os seus próprios valores na chave partilhada. Contudo, esta tem de corresponder em ambas as ligações. A criação das ligações demora pouco tempo.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Criar a ligação da TestVNet4 para a TestVNet1. Este passo é semelhante ao anterior, exceto que está a criar a ligação da TestVNet4 para a TestVNet1. Verifique se as chaves partilhadas correspondem. A ligação leva algum tempo até ser estabelecida.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Verifique as suas ligações. Veja [Verificar a ligação](#verify).

## <a name="difsub"></a>Ligar VNets que estão em subscrições diferentes

Neste cenário, vai ligar TestVNet1 e TestVNet5. As VNets residem em subscrições diferentes. As subscrições não têm de estar associadas ao mesmo inquilino do Active Directory. Os passos desta configuração acrescentam uma ligação de VNet para VNet adicional, de modo a ligar a TestVNet1 à TestVNet5.

### <a name="TestVNet1diff"></a>Passo 5 - Criar e configurar TestVNet1

Estas instruções vêm no seguimento dos passos das secções anteriores. Tem de concluir o [Passo 1](#Connect) e o [Passo 2](#TestVNet1) para criar e configurar a TestVNet1 e o VPN Gateway da TestVNet1. Para esta configuração, não tem de criar a TestVNet4 da secção anterior, embora se a criar, não entrará em conflito com estes passos. Depois de concluir o Passo 1 e o Passo 2, avance para o Passo 6 (abaixo).

### <a name="verifyranges"></a>Passo 6 – Verificar os intervalos de endereços IP

Ao criar ligações adicionais, é importante garantir que o espaço de endereços IP da nova rede virtual não se sobrepõe a nenhum dos intervalos de outras VNets ou intervalos de gateways de rede local. Para este exercício, pode utilizar os seguintes valores para a TestVNet5:

**Valores da TestVNet5:**

* Nome da VNet: TestVNet5
* Grupo de Recursos: TestRG5
* Localização: Leste do Japão
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* IP público: VNet5GWIP
* VPNType: RouteBased
* Ligação: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="TestVNet5"></a>Passo 7 – Criar e configurar TestVNet5

Este passo tem de ser realizado no contexto da subscrição nova, a Subscrição 5. Esta parte pode ser realizada pelo administrador noutra organização que seja proprietária da subscrição. Para mudar de subscrições, utilize “az account list --all” para listar todas as subscrições disponíveis para a sua conta e, em seguida, utilize “az account set --subscription <subscriptionID>“ para mudar para a que pretende utilizar.

1. Confirme que está ligado à Subscrição 5 e crie um grupo de recursos.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Criar a TestVNet5.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Adicione sub-redes.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Adicione a sub-rede do gateway.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Solicitar um endereço IP público.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. Criar o gateway da TestVNet5

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="connections5"></a>Passo 8 - Criar as ligações

Este passo foi dividido em duas sessões da CLI marcadas como **[Subscription 1]** e **[Subscription 5]**, porque os gateways estão nestas duas subscrições diferentes. Para mudar de subscrições, utilize “az account list --all” para listar todas as subscrições disponíveis para a sua conta e, em seguida, utilize “az account set --subscription <subscriptionID>“ para mudar para a que pretende utilizar.

1. **[Subscrição 1]** Inicie sessão e ligue-se a Subscrição 1. Execute o comando seguinte para obter o nome e o ID do Gateway a partir da saída:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Copie a saída de “id:”. Envie o ID e o nome do gateway de VNet (VNet1GW) ao administrador da Subscrição 5 por e-mail ou outro método.

   Exemplo de saída:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[Subscrição 1]** Inicie sessão e ligue-se a Subscrição 1. Execute o comando seguinte para obter o nome e o ID do Gateway a partir da saída:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Copie a saída de “id:”. Envie o ID e o nome do gateway de VNet (VNet1GW) ao administrador da Subscrição 5 por e-mail ou outro método.

3. **[Subscrição 1]** Neste passo, vai criar a ligação de TestVNet1 a TestVNet5. Pode utilizar os seus próprios valores na chave partilhada. Contudo, esta tem de corresponder em ambas as ligações. A criação de uma ligação pode demorar algum tempo. Verifique se estabelece ligação à Subscrição 1.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[Subscrição 5]** Este passo é semelhante ao de cima, exceto que está a criar a ligação de TestVNet5 para TestVNet1. Certifique-se de que as chaves partilhadas correspondem e que se liga à Subscrição 5.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify"></a>Verificar as ligações
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>FAQ da ligação VNet a VNet
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja a [documentação das Máquinas Virtuais](https://docs.microsoft.com/azure/).
* Para obter informações sobre o BGP, veja a [Descrição Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

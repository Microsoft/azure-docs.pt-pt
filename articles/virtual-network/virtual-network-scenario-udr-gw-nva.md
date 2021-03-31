---
title: Ligação híbrida com aplicação de 2 níveis | Microsoft Docs
description: Saiba como implantar aparelhos virtuais e UDR para criar um ambiente de aplicação de vários níveis em Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1d2dde4e77a39b114f721cd6d2be250141984e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231714"
---
# <a name="virtual-appliance-scenario"></a>Cenário de aparelho virtual
Um cenário comum entre o maior cliente Azure é a necessidade de fornecer uma aplicação de dois níveis exposta à Internet, ao mesmo tempo que permite o acesso ao nível de trás a partir de um datacenter no local. Este documento irá acompanhá-lo através de um cenário que utiliza as Rotas Definidas pelo Utilizador (UDR), um Gateway VPN e aparelhos virtuais de rede para implantar um ambiente de dois níveis que satisfaça os seguintes requisitos:

* A aplicação web deve ser acessível apenas a partir da Internet pública.
* O servidor web que hospeda a aplicação deve ser capaz de aceder a um servidor de aplicação backend.
* Todo o tráfego da Internet para a aplicação web deve passar por um aparelho virtual de firewall. Este aparelho virtual será utilizado apenas para tráfego de Internet.
* Todo o tráfego que vai para o servidor de aplicações deve passar por um aparelho virtual de firewall. Este aparelho virtual será utilizado para o acesso ao servidor final de backend e acesso a partir da rede no local através de um Gateway VPN.
* Os administradores devem poder gerir os aparelhos virtuais de firewall a partir dos seus computadores no local, utilizando um terceiro aparelho virtual de firewall utilizado exclusivamente para fins de gestão.

Este é um cenário de rede de perímetro padrão (também conhecido como DMZ) com um DMZ e uma rede protegida. Tal cenário pode ser construído em Azure usando NSGs, aparelhos virtuais de firewall ou uma combinação de ambos. A tabela abaixo mostra alguns dos prós e contras entre NSGs e aparelhos virtuais de firewall.

|  | Vantagens | Desvantagens |
| --- | --- | --- |
| **NSG** |Sem custo. <br/>Integrado no Azure RBAC. <br/>As regras podem ser criadas nos modelos do Gestor de Recursos Azure. |A complexidade pode variar em ambientes maiores. |
| **Firewall** |Controlo total sobre o plano de dados. <br/>Gestão central através da consola de firewall. |Custo do aparelho de firewall. <br/>Não integrado com a Azure RBAC. |

A solução abaixo utiliza aparelhos virtuais de firewall para implementar uma rede de perímetro (DMZ)/cenário de rede protegida.

## <a name="considerations"></a>Considerações
Pode implementar o ambiente acima explicado em Azure usando diferentes funcionalidades disponíveis hoje, da seguinte forma.

* **Rede virtual (VNet)** . Um Azure VNet atua de forma semelhante a uma rede no local, e pode ser segmentado em uma ou mais subesí redes para proporcionar isolamento de tráfego, e separação de preocupações.
* **Aparelho virtual**. Vários parceiros fornecem aparelhos virtuais no Azure Marketplace que podem ser usados para as três firewalls acima descritas. 
* **Rotas definidas pelo utilizador (UDR)**. As tabelas de rotas podem conter UDRs usados pela rede Azure para controlar o fluxo de pacotes dentro de um VNet. Estas tabelas de rota podem ser aplicadas em sub-redes. Uma das funcionalidades mais recentes em Azure é a capacidade de aplicar uma tabela de rotas para o GatewaySubnet, proporcionando a capacidade de encaminhar todo o tráfego que entra no Azure VNet de uma ligação híbrida a um aparelho virtual.
* **Encaminhamento IP**. Por predefinição, o motor de rede Azure só tem pacotes para a frente do motor de rede para cartões de interface de rede virtual (NICs) se o endereço IP do destino do pacote corresponder ao endereço IP NIC. Portanto, se um UDR definir que um pacote deve ser enviado para um determinado aparelho virtual, o motor de rede Azure deixaria cair esse pacote. Para garantir que o pacote é entregue a um VM (neste caso um aparelho virtual) que não é o destino real do pacote, é necessário ativar o encaminhamento IP para o aparelho virtual.
* **Grupos de Segurança de Rede (NSGs)**. O exemplo abaixo não utiliza os NSGs, mas pode utilizar NSGs aplicados nas sub-redes e/ou NICs nesta solução para filtrar ainda mais o tráfego dentro e fora dessas sub-redes e NICs.

![Conectividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo existe uma subscrição que contém o seguinte:

* 2 grupos de recursos, não mostrados no diagrama. 
  * **ONPREMRG.** Contém todos os recursos necessários para simular uma rede no local.
  * **AZURERG.** Contém todos os recursos necessários para o ambiente de rede virtual Azure. 
* Um VNet chamado **onpremvnet** usado para imitar um datacenter no local segmentado como listado abaixo.
  * **onpremsn1**. Sub-rede contendo uma máquina virtual (VM) executando Ubuntu para imitar um servidor no local.
  * **onpremsn2**. Sub-rede contendo um Ubuntu em execução VM para imitar um computador no local utilizado por um administrador.
* Existe um aparelho virtual de firewall chamado **OPFW** **on onpremvnet** usado para manter um túnel para **azurevnet**.
* Um VNet nomeado **azurevnet** segmentado como listado abaixo.
  * **azsn1**. Sub-rede de firewall externa utilizada exclusivamente para a firewall externa. Todo o tráfego da Internet entrará através desta sub-rede. Esta sub-rede contém apenas um NIC ligado à firewall externa.
  * **azsn2**. Sub-rede frontal que hospeda um VM em execução como um servidor web que será acedido a partir da Internet.
  * **azsn3**. Sub-rede backend hospedando um VM executando um servidor de aplicação backend que será acedido pelo servidor web frontal.
  * **azsn4**. A sub-rede de gestão utilizada exclusivamente para fornecer acesso de gestão a todos os aparelhos virtuais de firewall. Esta sub-rede contém apenas um NIC para cada aparelho virtual de firewall utilizado na solução.
  * **GatewaySubnet**. Sub-rede de conexão híbrida Azure necessária para ExpressRoute e VPN Gateway para fornecer conectividade entre VNets Azure e outras redes. 
* Existem 3 aparelhos virtuais de firewall na rede **azurevnet.** 
  * **AZF1.** Firewall externa exposta à Internet pública utilizando um recurso de endereço IP público em Azure. Tem de se certificar de que tem um modelo do Marketplace, ou diretamente do seu fornecedor de aparelhos, que prevê um aparelho virtual de 3 NIC.
  * **AZF2.** Firewall interna utilizada para controlar o tráfego entre **azsn2** e **azsn3**. Este é também um aparelho virtual de 3 NIC.
  * **AZF3.** Firewall de gestão acessível a administradores do datacenter no local, e ligado a uma sub-rede de gestão usada para gerir todos os aparelhos de firewall. Pode encontrar modelos de aparelhos virtuais de 2 NIC no Mercado ou solicitar um diretamente ao seu fornecedor de aparelhos.

## <a name="user-defined-routing-udr"></a>Encaminhamento definido pelo utilizador (UDR)
Cada sub-rede em Azure pode ser ligada a uma tabela UDR usada para definir como o tráfego iniciado nessa sub-rede é encaminhado. Se não forem definidas UDRs, o Azure utiliza rotas predefinidas para permitir que o tráfego flua de uma sub-rede para outra. Para melhor compreender os UDRs, visite [o que são rotas definidas pelo utilizador e encaminhamento IP](virtual-networks-udr-overview.md).

Para garantir que a comunicação é feita através do aparelho de firewall certo, com base no último requisito acima, é necessário criar a seguinte tabela de rotas contendo UDRs em **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Neste cenário, o único tráfego que flui do local para o Azure será utilizado para gerir as firewalls através da ligação ao **AZF3,** e que o tráfego deve passar pela firewall interna, **AZF2**. Portanto, apenas uma rota é necessária na **GatewaySubnet,** como mostrado abaixo.

| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite que o tráfego no local chegue ao firewall de gestão **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permite o tráfego para a sub-rede de backend que hospeda o servidor de aplicações através **do AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todo o tráfego seja encaminhado através **do AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que o tráfego para **azsn2** flua do servidor de aplicações para o webserver através **do AZF2** |

Também precisa de criar tabelas de rotas para as sub-redes **em onpremvnet** para imitar o datacenter no local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite o tráfego **onpremsn2** através **da OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite o tráfego para a sub-rede apoiada em Azure através da **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite o tráfego **onpremsn1** através **da OPFW** |

## <a name="ip-forwarding"></a>Reencaminhamento IP
O UDR e o IP Reencaminhamento são funcionalidades que pode utilizar em combinação para permitir a utilização de aparelhos virtuais para controlar o fluxo de tráfego num Azure VNet.  Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado. O seu aparelho virtual ainda precisa de executar algum tipo de aplicação para lidar com o tráfego de entrada e encaminhá-lo adequadamente.

Para saber mais sobre o encaminhamento IP, visite [o que são rotas definidas pelo utilizador e encaminhamento IP](virtual-networks-udr-overview.md).

Como exemplo, imagine que tem a seguinte configuração num Azure vnet:

* Subnet **onpremsn1** contém um VM nomeado **onpremvm1**.
* Subnet **onpremsn2** contém um VM nomeado **onpremvm2**.
* Um aparelho virtual chamado **OPFW** está ligado a **onpremsn1** e **onpremsn2**.
* Uma rota definida pelo utilizador ligada ao **onpremsn1** especifica que todo o tráfego para **onpremsn2** deve ser enviado para **a OPFW**.

Neste ponto, se **onpremvm1** tentar estabelecer uma ligação com **onpremvm2,** a UDR será usada e o tráfego será enviado para **OPFW** como o próximo salto. Tenha em mente que o destino do pacote real não está sendo alterado, ainda diz **que onpremvm2** é o destino. 

Sem o Encaminhamento IP habilitado para **OPFW,** a lógica de rede virtual Azure deixará cair os pacotes, uma vez que só permite que os pacotes sejam enviados para um VM se o endereço IP do VM for o destino do pacote.

Com o IP Forwarding, a lógica de rede virtual Azure irá encaminhar os pacotes para OPFW, sem alterar o seu endereço de destino original. **A OPFW** deve tratar dos pacotes e determinar o que fazer com eles.

Para que o cenário acima funcione, deve permitir o encaminhamento IP nos NICs para **OPFW**, **AZF1,** **AZF2** e **AZF3** que são utilizados para o encaminhamento (todos os NICs, exceto os ligados à sub-rede de gestão). 

## <a name="firewall-rules"></a>Regras da Firewall
Como descrito acima, o IP Reencaminhamento apenas garante que os pacotes são enviados para os aparelhos virtuais. O seu aparelho ainda precisa decidir o que fazer com esses pacotes. No cenário acima, terá de criar as seguintes regras nos seus aparelhos:

### <a name="opfw"></a>OPFW
A OPFW representa um dispositivo no local que contém as seguintes regras:

* **Rota**: Todo o tráfego para 10.0.0.0/16 **(azurevnet)** deve ser enviado através do túnel **ONPREMAZURE**.
* **Política**: Permitir todo o tráfego bidirecional entre **o porto2** e **o ONPREMAZURE**.

### <a name="azf1"></a>AZF1
O AZF1 representa um aparelho virtual Azure que contém as seguintes regras:

* **Política**: Permitir todo o tráfego bidirecional entre **o porto1** e **o porto2**.

### <a name="azf2"></a>AZF2
O AZF2 representa um aparelho virtual Azure que contém as seguintes regras:

* **Rota**: Todo o tráfego até 10.0.0.0/16 **(onpremvnet)** deve ser enviado para o endereço IP de gateway Azure (ou seja, 10.0.0.1) através do **porto1**.
* **Política**: Permitir todo o tráfego bidirecional entre **o porto1** e **o porto2**.

## <a name="network-security-groups-nsgs"></a>Grupos de Segurança de Rede (NSGs)
Neste cenário, os NSGs não estão a ser utilizados. No entanto, pode aplicar NSGs em cada sub-rede para restringir o tráfego de entrada e saída. Por exemplo, pode aplicar as seguintes regras NSG na sub-rede externa de FW.

**Dados**

* Permitir todo o tráfego TCP da Internet para a porta 80 em qualquer VM na sub-rede.
* Negar todo o tráfego da Internet.

**Saída**

* Negar todo o tráfego para a Internet.

## <a name="high-level-steps"></a>Passos de alto nível
Para implementar este cenário, siga os passos de alto nível abaixo.

1. Faça login na sua Assinatura Azure.
2. Se pretender implantar um VNet para imitar a rede no local, fornecendo os recursos que fazem parte da **ONPREMRG**.
3. Fornecimento dos recursos que fazem parte da **AZURERG.**
4. A provisionar o túnel de **onpremvnet** para **azurevnet**.
5. Uma vez abastecidos todos os recursos, inscreva-se no **onpremvm2** e no ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.


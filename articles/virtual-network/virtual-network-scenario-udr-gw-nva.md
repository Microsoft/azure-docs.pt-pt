---
title: Ligação híbrida com aplicação de 2 níveis / Microsoft Docs
description: Aprenda a implementar aparelhos virtuais e UDR para criar um ambiente de aplicação de vários níveis em Azure
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
ms.openlocfilehash: 80a9397838e90a2af504125b2dc4c4ef39251d4e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455367"
---
# <a name="virtual-appliance-scenario"></a>Cenário de aparelho virtual
Um cenário comum entre o maior cliente Azure é a necessidade de fornecer uma aplicação de dois níveis exposta à Internet, permitindo ao mesmo tempo o acesso ao nível de trás a partir de um centro de dados no local. Este documento irá acompanhá-lo através de um cenário utilizando rotas definidas pelo utilizador (UDR), um Gateway VPN e aparelhos virtuais de rede para implementar um ambiente de dois níveis que satisfaça os seguintes requisitos:

* A aplicação web deve ser acessível apenas a partir da Internet pública.
* O servidor web que hospeda a aplicação deve ter acesso a um servidor de aplicação backend.
* Todo o tráfego da Internet para a aplicação web deve passar por um aparelho virtual firewall. Este aparelho virtual será utilizado apenas para tráfego na Internet.
* Todo o tráfego que vai para o servidor de aplicações deve passar por um aparelho virtual de firewall. Este aparelho virtual será utilizado para acesso ao servidor final de backend e acesso saindo da rede no local através de um VPN Gateway.
* Os administradores devem poder gerir os aparelhos virtuais firewall a partir dos seus computadores no local, utilizando um terceiro aparelho virtual de firewall utilizado exclusivamente para fins de gestão.

Esta é uma rede de perímetro padrão (também conhecida como DMZ) com um DMZ e uma rede protegida. Tal cenário pode ser construído em Azure utilizando NSGs, firewall aparelhos virtuais, ou uma combinação de ambos. A tabela abaixo mostra alguns dos prós e contras entre NSGs e aparelhos virtuais firewall.

|  | Vantagens | Contras |
| --- | --- | --- |
| NSG |Sem custos. <br/>Integrado no Azure RBAC. <br/>As regras podem ser criadas nos modelos do Gestor de Recursos Azure. |A complexidade pode variar em ambientes maiores. |
| Firewall |Controlo total sobre o avião de dados. <br/>Gestão central através da consola de firewall. |Custo do aparelho de firewall. <br/>Não integrado com o Azure RBAC. |

A solução abaixo utiliza aparelhos virtuais de firewall para implementar uma rede de perímetro (DMZ)/cenário de rede protegida.

## <a name="considerations"></a>Considerações
Pode implementar o ambiente explicado acima em Azure utilizando diferentes funcionalidades disponíveis hoje em dia, da seguinte forma.

* **Rede virtual (VNet)**. Um Azure VNet atua de forma semelhante a uma rede no local, e pode ser segmentado em uma ou mais subnets para fornecer isolamento de tráfego, e separação de preocupações.
* **Aparelho virtual**. Vários parceiros fornecem aparelhos virtuais no Mercado Azure que podem ser usados para as três firewalls acima descritas. 
* **Vias definidas pelo utilizador (UDR)**. As tabelas de rotas podem conter UDRs utilizados pela rede Azure para controlar o fluxo de pacotes dentro de uma VNet. Estas tabelas de rotas podem ser aplicadas a subredes. Uma das mais recentes funcionalidades em Azure é a capacidade de aplicar uma tabela de rotas à GatewaySubnet, proporcionando a capacidade de encaminhar todo o tráfego que entra no Azure VNet a partir de uma ligação híbrida a um aparelho virtual.
* **Encaminhamento IP**. Por predefinição, os pacotes avançados do motor de rede Azure para cartões de interface de rede virtual (NICs) apenas se o endereço IP do destino do pacote corresponder ao endereço IP nic. Portanto, se um UDR definir que um pacote deve ser enviado para um determinado aparelho virtual, o motor de rede Azure deixaria cair esse pacote. Para garantir que o pacote é entregue a um VM (neste caso um aparelho virtual) que não seja o destino real para o pacote, é necessário ativar o ENCaminhamento IP para o aparelho virtual.
* **Grupos de Segurança da Rede (NSGs)**. O exemplo abaixo não utiliza os NSGs, mas pode utilizar NSGs aplicados às subredes e/ou NICs nesta solução para filtrar ainda mais o tráfego dentro e fora das subredes e NICs.

![Conectividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo existe uma subscrição que contém o seguinte:

* 2 grupos de recursos, não mostrados no diagrama. 
  * **ONPREMRG.** Contém todos os recursos necessários para simular uma rede no local.
  * **AZURERG.** Contém todos os recursos necessários para o ambiente de rede virtual Azure. 
* Um VNet chamado **onpremvnet** usado para imitar um datacenter no local segmentado como listado abaixo.
  * **onpremsn1**. Subnet contendo uma máquina virtual (VM) executando Ubuntu para imitar um servidor no local.
  * **onpremsn2**. Subnet contendo um VM executando Ubuntu para imitar um computador no local usado por um administrador.
* Existe um aparelho virtual de firewall chamado **OPFW** **on onpremvnet** usado para manter um túnel para **azurevnet**.
* Um VNet chamado **azurevnet** segmentado como listado abaixo.
  * **azsn1.** Sub-rede de firewall externa utilizada exclusivamente para a firewall externa. Todo o tráfego da Internet entrará através desta subnet. Esta sub-rede contém apenas um NIC ligado à firewall externa.
  * **azsn2.** A subnet frontal que acolhe um VM a funcionar como um servidor web que será acedido a partir da Internet.
  * **azsn3.** A subnet backend que acolhe um VM executando um servidor de aplicação backend que será acedido pelo servidor web frontal.
  * **azsn4.** A subnet de gestão utilizada exclusivamente para fornecer acesso à gestão a todos os aparelhos virtuais firewall. Esta sub-rede contém apenas um NIC para cada aparelho virtual de firewall utilizado na solução.
  * **GatewaySubnet**. Subnet de ligação híbrida Azure necessária para expressRoute e VPN Gateway para fornecer conectividade entre Os VNets Azure e outras redes. 
* Existem 3 aparelhos virtuais firewall na rede **azurevnet.** 
  * **AZF1.** Firewall externa exposta à Internet pública utilizando um recurso público de endereço IP em Azure. Tem de se certificar de que tem um modelo do Marketplace, ou diretamente do vendedor de aparelhos, que fornecia um aparelho virtual de 3 NIC.
  * **AZF2.** Firewall interno usado para controlar o tráfego entre **azsn2** e **azsn3**. Este é também um aparelho virtual de 3 NIC.
  * **AZF3.** Firewall de gestão acessível a administradores do centro de dados no local, e ligado a uma subnet de gestão usada para gerir todos os aparelhos de firewall. Pode encontrar modelos de aparelhos virtuais de 2 NIC no Mercado ou solicitar um diretamente ao vendedor de aparelhos.

## <a name="user-defined-routing-udr"></a>Encaminhamento Definido do Utilizador (UDR)
Cada subnetem em Azure pode ser ligada a uma tabela UDR usada para definir como o tráfego iniciado nessa sub-rede é encaminhado. Se não forem definidos UDRs, o Azure utiliza rotas predefinidas para permitir que o tráfego flua de uma subnet para outra. Para melhor entender os UDRs, visite [o que são Rotas Definidas pelo Utilizador e Reencaminhamento IP](virtual-networks-udr-overview.md).

Para garantir que a comunicação é feita através do aparelho de firewall certo, com base no último requisito acima, é necessário criar a seguinte tabela de rotas contendo UDRs em **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Neste cenário, o único tráfego que flui das instalações para o Azure será utilizado para gerir as firewalls ligando-se ao **AZF3**, e que o tráfego deve passar pela firewall interna, **AZF2**. Portanto, apenas uma rota é necessária na **GatewaySubnet,** como mostrado abaixo.

| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite que o tráfego no local atinja a firewall de gestão **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permite o tráfego na subnet backend hospedando o servidor de aplicação através do **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todo o tráfego seja encaminhado através da **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que **o** tráfego flua do servidor de aplicações para o webserver através do **AZF2** |

Também precisa de criar tabelas de rotas para as subredes em **onpremvnet** para imitar o datacenter no local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite que o tráfego **onpremsn2** através da **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite o tráfego para a subnet apoiada em Azure através da **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite que o tráfego **onpremsn1** através **da OPFW** |

## <a name="ip-forwarding"></a>Reencaminhamento IP
UDR e IP Forwarding são características que pode utilizar em combinação para permitir a utilização de aparelhos virtuais para controlar o fluxo de tráfego num Azure VNet.  Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado. O seu aparelho virtual ainda precisa de executar algum tipo de aplicação para lidar com o tráfego de entrada e encaminhá-lo adequadamente.

Para saber mais sobre o Reencaminhamento IP, visite [o que são Rotas Definidas pelo Utilizador e Reencaminhamento IP](virtual-networks-udr-overview.md).

Como exemplo, imagine que tem a seguinte configuração num vnet Azure:

* Subnet **onpremsn1** contém um VM chamado **onpremvm1**.
* Subnet **onpremsn2** contém um VM chamado **onpremvm2**.
* Um aparelho virtual chamado **OPFW** está ligado ao **onpremsn1** e **onpremsn2**.
* Uma rota definida pelo utilizador ligada ao **onpremsn1** especifica que todo o tráfego para **onpremsn2** deve ser enviado para **opfw**.

Neste ponto, se o **onpremvm1** tentar estabelecer uma ligação com o **onpremvm2,** o UDR será utilizado e o tráfego será enviado para a **OPFW** como o próximo lúpulo. Tenha em mente que o destino do pacote real não está a ser alterado, ainda diz que **onpremvm2** é o destino. 

Sem o envio ip habilitado para **OPFW,** a lógica de rede virtual Azure deixará cair os pacotes, uma vez que só permite que os pacotes sejam enviados para um VM se o endereço IP do VM for o destino do pacote.

Com o IP Forwarding, a lógica da rede virtual Azure irá encaminhar os pacotes para a OPFW, sem alterar o seu endereço de destino original. **A OPFW** deve tratar dos pacotes e determinar o que fazer com eles.

Para que o cenário acima funcione, deve permitir o reencaminhamento ip nos NICs para **OPFW,** **AZF1,** **AZF2**e **AZF3** que são utilizados para o encaminhamento (todos os NICs, exceto os ligados à sub-rede de gestão). 

## <a name="firewall-rules"></a>Regras da Firewall
Como acima descrito, o ENVIO IP apenas garante que os pacotes são enviados para os aparelhos virtuais. O seu aparelho ainda precisa de decidir o que fazer com esses pacotes. No cenário acima, terá de criar as seguintes regras nos seus aparelhos:

### <a name="opfw"></a>OPFW
A OPFW representa um dispositivo no local que contém as seguintes regras:

* **Rota**: Todo o tráfego para 10.0.0.0.0/16 **(azurevnet**) deve ser enviado através do túnel **ONPREMAZURE**.
* **Política**: Permitir todo o tráfego bidirecional entre a **porta 2** e a **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
A ZF1 representa um aparelho virtual Azure que contém as seguintes regras:

* **Política**: Permitir todo o tráfego bidirecional entre a **porta 1** e a **porta2**.

### <a name="azf2"></a>AZF2
O AZF2 representa um aparelho virtual Azure que contém as seguintes regras:

* **Rota**: Todo o tráfego para 10.0.0.0.16 **(onpremvnet**) deve ser enviado para o endereço IP de gateway Azure (ou seja, 10.0.0.1) através da **porta1**.
* **Política**: Permitir todo o tráfego bidirecional entre a **porta 1** e a **porta2**.

## <a name="network-security-groups-nsgs"></a>Grupos de Segurança de Rede (NSGs)
Neste cenário, os NSGs não estão a ser utilizados. No entanto, pode aplicar NSGs em cada subnet para restringir o tráfego de entrada e saída. Por exemplo, pode aplicar as seguintes regras de NSG à sub-rede FW externa.

**Dados**

* Permita que todo o tráfego de TCP da Internet para a porta 80 em qualquer VM na sub-rede.
* Negue todo o tráfego da Internet.

**Saída**

* Negue todo o tráfego para a Internet.

## <a name="high-level-steps"></a>Passos de alto nível
Para implementar este cenário, siga os passos de alto nível abaixo.

1. Inicie sessão na sua Assinatura Azure.
2. Se pretender implementar um VNet para imitar a rede no local, forme os recursos que fazem parte da **ONPREMRG**.
3. Fornecer os recursos que fazem parte do **AZURERG.**
4. Forme o túnel de **onpremvnet** para **azurevnet**.
5. Uma vez aprovisionados todos os recursos, inscreva-se no **onpremvm2** e no ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.


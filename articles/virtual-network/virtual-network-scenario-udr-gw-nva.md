---
title: Ligação híbrida com a aplicação de camada 2 | Documentos da Microsoft
description: Saiba como implementar aplicações virtuais e o UDR para criar um ambiente de aplicação multicamadas no Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575573"
---
# <a name="virtual-appliance-scenario"></a>Cenário de aplicação virtual
Um cenário comum entre os maiores clientes do Azure é a necessidade de fornecer uma aplicação em duas camadas, exposta à Internet, ao permitir o acesso para a camada de back-a partir de um datacenter no local. Este documento irá guiá-lo por meio de um cenário utilizar rotas definidas pelo utilizador (UDR), um Gateway de VPN e aplicações virtuais de rede para implementar um ambiente de duas camadas que cumpre os seguintes requisitos:

* Aplicação Web tem de ser acessível a partir apenas da Internet pública.
* Servidor Web que aloja a aplicação tem de ser capaz de aceder um servidor de aplicações de back-end.
* Todo o tráfego da Internet para a aplicação web deve passar por uma aplicação virtual de firewall. Esta aplicação virtual será utilizada para apenas o tráfego da Internet.
* Todo o tráfego para o servidor de aplicativo deve passar por uma aplicação virtual de firewall. Esta aplicação virtual será utilizada para acesso ao servidor de back-end end e proveniente de rede no local através de um Gateway de VPN de acesso.
* Os administradores têm de ser capazes de gerir as aplicações virtuais de firewall de seus computadores no local, através da utilização de um terceiro firewall utilizada exclusivamente para fins de gestão de aplicação virtual.

Este é um cenário de rede (também knowns como rede de Perímetro) de perímetro padrão com uma rede de Perímetro e uma rede protegida. Cenário como esse pode ser criado no Azure com NSGs, aplicações de virtual de firewall ou uma combinação de ambos. A tabela a seguir mostra alguns prós e contras entre os NSGs e aplicações virtuais de firewall.

|  | Profissionais de TI | Contras |
| --- | --- | --- |
| NSG |Sem custos. <br/>Integrados no RBAC do Azure. <br/>As regras podem ser criadas em modelos do Azure Resource Manager. |Complexidade pode variar em ambientes maiores. |
| Firewall |Controlo total sobre o plano de dados. <br/>Gerenciamento central por meio da consola da firewall. |Custo da aplicação de firewall. <br/>Não é integrado com o RBAC do Azure. |

A solução abaixo utiliza aplicações virtuais de firewall para implementar uma rede de perímetro (DMZ) / protegidos o cenário de rede.

## <a name="considerations"></a>Considerações
Pode implementar o ambiente explicado acima no Azure através de diferentes funcionalidades disponíveis hoje em dia, da seguinte forma.

* **Rede virtual (VNet)**. Uma VNet do Azure funciona de forma semelhante a uma rede no local e podem ser segmentada numa ou mais sub-redes para fornecer isolamento de tráfego e separação de preocupações.
* **Aplicação virtual**. Vários parceiros fornecem aplicações virtuais no Azure Marketplace que podem ser utilizados para as três firewalls descritas acima. 
* **(UDR) de rotas definidas pelo utilizador**. Tabelas de rotas podem conter as UDRs utilizadas pelo sistema de rede do Azure para controlar o fluxo de pacotes numa VNet. Estas tabelas de rota podem ser aplicadas a sub-redes. Um dos mais novos recursos no Azure é a capacidade de aplicar uma tabela de rotas no gatewaysubnet, fornecendo a capacidade para encaminhar todo o tráfego chegam a VNet do Azure para uma aplicação virtual a partir de uma ligação híbrida.
* **Reencaminhamento de IP**. Por predefinição, o mecanismo de funcionamento em rede do Azure reencaminhar pacotes para placas de interface de rede virtual (NICs) apenas se o endereço IP de destino do pacote corresponde ao endereço IP de NIC. Por conseguinte, se um UDR define que deve ser enviado um pacote para uma determinada aplicação virtual, o mecanismo de funcionamento em rede do Azure seria remover esse pacote. Para garantir que o pacote é entregue a uma VM (no caso uma aplicação virtual) que não é o destino real para o pacote, tem de ativar o reencaminhamento IP para a aplicação virtual.
* **Grupos de segurança (NSGs) de rede**. O exemplo a seguir não faz uso de NSGs, mas poderia usar aplicados para as sub-redes e/ou NICs esta solução de NSGs para filtrar ainda mais o tráfego dentro ou fora dessas NICs e sub-redes.

![Conectividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo, existe uma subscrição que contém o seguinte:

* 2 grupos de recursos, não é mostrados no diagrama. 
  * **ONPREMRG**. Contém todos os recursos necessários para simular uma rede no local.
  * **AZURERG**. Contém todos os recursos necessários para o ambiente de rede virtual do Azure. 
* Uma VNet com o nome **onpremvnet** usado para simular um datacenter no local segmentado conforme indicado abaixo.
  * **onpremsn1**. Sub-rede que contém uma máquina virtual (VM) com o Ubuntu para imitar um servidor no local.
  * **onpremsn2**. Sub-rede que contém uma VM com o Ubuntu para imitar um computador no local utilizado por um administrador.
* É aplicação virtual de firewall um com o nome **OPFW** nos **onpremvnet** utilizado para manter um túnel para **azurevnet**.
* Uma VNet com o nome **azurevnet** segmentados conforme indicado abaixo.
  * **azsn1**. Sub-rede de firewall externa usada exclusivamente para o firewall externa. Todo o tráfego de Internet serão recebidos através desta sub-rede. Esta sub-rede contém apenas uma NIC ligada para o firewall externa.
  * **azsn2**. Sub-rede de front-end que aloja uma VM em execução como um servidor web que será acedido a partir da Internet.
  * **azsn3**. Sub-rede de back-end que aloja uma VM a executar um servidor de aplicações de back-end que será acedido pelo servidor web front-end.
  * **azsn4**. Sub-rede de gestão utilizado exclusivamente para fornecer acesso de gestão para todas as aplicações virtuais de firewall. Esta sub-rede contém apenas uma NIC para cada aplicação virtual de firewall utilizada na solução.
  * **GatewaySubnet**. Sub-rede de ligação híbrida do Azure necessária para o ExpressRoute e Gateway de VPN para fornecer conectividade entre VNets do Azure e outras redes. 
* Existem 3 aparelhos virtuais de firewall no **azurevnet** rede. 
  * **AZF1**. Firewall externa exposto à Internet pública através de um recurso de endereço IP público no Azure. Precisa garantir que tenha um modelo do Marketplace ou diretamente a partir do seu fornecedor da aplicação, que Aprovisiona uma aplicação virtual de 3-NIC.
  * **AZF2**. Firewall interno utilizado para controlar o tráfego entre **azsn2** e **azsn3**. Também se trata de uma aplicação virtual de 3-NIC.
  * **AZF3**. Firewall de gestão para administradores acessível a partir do Centro de dados no local e ligada a uma sub-rede de gestão utilizada para gerir todas as aplicações de firewall. Pode encontrar modelos de aplicação virtual de 2 NIC no Marketplace ou pedir uma diretamente a partir do seu fornecedor da aplicação.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definido pelo utilizador
Cada sub-rede no Azure pode ser associado a uma tabela UDR usada para definir como o tráfego iniciada em que a sub-rede é encaminhada. Se não as UDRs definidas, o Azure utiliza as rotas predefinidas para permitir o tráfego para o fluxo de uma sub-rede para outra. Para compreender melhor as UDRs, visite [o que são rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

Para garantir a comunicação é feita através da aplicação de firewall corretas, com base no último requisito acima, tem de criar a seguinte tabela de rota que contém as UDRs no **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Neste cenário, apenas o tráfego que flui do local para o Azure será utilizado para gerir as firewalls conectando **AZF3**, e que o tráfego tem de aceder através da firewall interna, **AZF2**. Por conseguinte, é necessária em apenas uma rota a **GatewaySubnet** conforme mostrado abaixo.

| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite o tráfego no local chegar a firewall de gestão **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permita o tráfego para a sub-rede de back-end que aloja o servidor de aplicativo por meio de **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todos os outros tráfegos ser encaminhada através de **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que o tráfego **azsn2** para o fluxo do servidor de aplicações para o servidor Web por meio de **AZF2** |

Terá também de criar tabelas de rotas para as sub-redes na **onpremvnet** para imitar o datacenter no local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite que o tráfego **onpremsn2** através de **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite o tráfego para a sub-rede de segurança no Azure através de **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite que o tráfego **onpremsn1** através de **OPFW** |

## <a name="ip-forwarding"></a>Reencaminhamento IP
UDR e reencaminhamento IP são recursos que podem ser utilizados em conjunto para permitir que aplicações virtuais a ser utilizado para controlar o fluxo de tráfego numa VNet do Azure.  Uma aplicação virtual não é mais do que uma VM que executa uma aplicação utilizada para processar tráfego de rede de alguma forma, tal como uma firewall ou um dispositivo NAT.

Este VM de aplicação virtual deve ser capaz de receber tráfego de entrada que não esteja endereçada a si mesma. Para permitir que uma VM receba tráfego endereçado a outros destinos, tem de ativar o Reencaminhamento IP para a VM. Isto é uma definição do Azure, não se trata de uma definição no sistema operativo convidado. A aplicação virtual ainda tem de executar algum tipo de aplicação para processar o tráfego de entrada e roteá-lo adequadamente.

Para saber mais sobre o reencaminhamento de IP, visite [o que são rotas definidas pelo utilizador e reencaminhamento IP](virtual-networks-udr-overview.md).

Por exemplo, imagine que tem a seguinte configuração numa vnet do Azure:

* Sub-rede **onpremsn1** contém uma VM com o nome **onpremvm1**.
* Sub-rede **onpremsn2** contém uma VM com o nome **onpremvm2**.
* Uma aplicação virtual com o nome **OPFW** está ligada ao **onpremsn1** e **onpremsn2**.
* Uma rota definida pelo utilizador ligado ao **onpremsn1** Especifica que todo o tráfego para **onpremsn2** têm de ser enviados para **OPFW**.

Nesse ponto, se **onpremvm1** tenta estabelecer uma ligação com **onpremvm2**, será utilizado o UDR e será possível enviar o tráfego para **OPFW** como salto seguinte. Tenha em atenção que o destino do pacote real não está a ser alterado, ela ainda diz **onpremvm2** é o destino. 

Sem ativado para o reencaminhamento IP **OPFW**, a lógica de rede virtual do Azure irá ignorar os pacotes, uma vez que apenas permite que pacotes a serem enviados para uma VM, se o endereço IP da VM é o destino para o pacote.

Com o reencaminhamento de IP, a lógica de rede virtual do Azure irá reencaminhar os pacotes para OPFW, sem alterar o seu endereço de destino original. **OPFW** tem de lidar com os pacotes e determinar o que fazer com eles.

Para o cenário acima para funcionar, tem de ativar o reencaminhamento de IP em NICs para **OPFW**, **AZF1**, **AZF2**, e **AZF3** que são utilizados para encaminhamento (todos os NICs, exceto as ligadas à sub-rede de gestão). 

## <a name="firewall-rules"></a>Regras da Firewall
Conforme descrito acima, o reencaminhamento IP só garante a pacotes são enviados para as aplicações virtuais. A aplicação da ainda tem de decidir o que fazer com que esses pacotes. No cenário acima, terá de criar as regras seguintes nas suas aplicações:

### <a name="opfw"></a>OPFW
OPFW representa um dispositivo no local que contém as seguintes regras:

* **rota**: Todo o tráfego para 10.0.0.0/16 (**azurevnet**) têm de ser enviados através de túnel **ONPREMAZURE**.
* **Política**: Permitir todo o tráfego bidirecional entre **port2** e **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 representa uma aplicação virtual do Azure que contém as seguintes regras:

* **Política**: Permitir todo o tráfego bidirecional entre **IPP/port1** e **port2**.

### <a name="azf2"></a>AZF2
AZF2 representa uma aplicação virtual do Azure que contém as seguintes regras:

* **rota**: Todo o tráfego para 10.0.0.0/16 (**onpremvnet**) têm de ser enviados para o gateway do Azure (ou seja, 10.0.0.1) de endereço IP por meio **IPP/port1**.
* **Política**: Permitir todo o tráfego bidirecional entre **IPP/port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSGs)
Neste cenário, os NSGs não estão a ser utilizados. No entanto, pode aplicar NSGs para cada sub-rede para restringir o tráfego de entrada e saído. Por exemplo, pode aplicar as seguintes regras NSG para sub-rede firewall externa.

**entrada**

* Permitir todo o tráfego TCP da Internet para a porta 80 em qualquer VM na sub-rede.
* Negar todos os outros tráfegos de Internet.

**Saída**

* Negar todo o tráfego para a Internet.

## <a name="high-level-steps"></a>Passos de nível altos
Para implementar este cenário, siga os passos de alto nível abaixo.

1. Início de sessão para a sua subscrição do Azure.
2. Se pretender implementar uma VNet para imitar a rede no local, aprovisionar os recursos que fazem parte de **ONPREMRG**.
3. Aprovisionar os recursos que fazem parte de **AZURERG**.
4. Aprovisionar o túnel **onpremvnet** ao **azurevnet**.
5. Assim que todos os recursos estão a ser aprovisionados, inicie sessão no **onpremvm2** e enviar um ping 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.


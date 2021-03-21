---
title: Tutorial - Lista de verificação de planeamento de rede
description: Conheça os requisitos de rede para a conectividade da rede e portas de rede na Azure VMware Solution.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 8cee5fa24aab8bd7fe6a9527f9c8e7cdff997511
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462070"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Lista de verificação de planeamento de rede para Solução VMware Azure 

A Azure VMware Solution oferece um ambiente de nuvem privada VMware acessível a utilizadores e aplicações a partir de ambientes ou recursos baseados em Azure. A conectividade é prestada através de serviços de networking como as ligações Azure ExpressRoute e VPN. Requer gamas específicas de endereços de rede e portas de firewall para ativar os serviços. Este artigo fornece-lhe as informações necessárias para configurar a sua rede para trabalhar corretamente com a Azure VMware Solution.

Neste tutorial, ficará a conhecer:

> [!div class="checklist"]
> * Considerações de rede virtual e circuito ExpressRoute
> * Requisitos de encaminhamento e sub-rede
> * Portas de rede necessárias para comunicar com os serviços
> * DhCP e DNS considerações na Azure VMware Solution

## <a name="prerequisite"></a>Pré-requisito
Certifique-se de que todos os gateways, incluindo o serviço do prestador ExpressRoute, suportam o Número do Sistema Autónomo de 4 byte (ASN). A Azure VMware Solution utiliza ASNs públicos de 4 byte para rotas publicitárias.

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Considerações de rede virtual e circuito ExpressRoute
Quando cria uma ligação de rede virtual na sua subscrição, o circuito ExpressRoute é estabelecido através do espreitamento, utiliza uma chave de autorização e um ID de espreitar que solicita no portal Azure. O espreitamento é uma ligação privada, um a um entre a sua nuvem privada e a rede virtual.

> [!NOTE] 
> O circuito ExpressRoute não faz parte de uma nuvem privada. O circuito ExpressRoute está fora do âmbito deste documento. Se necessitar de conectividade no local para a sua nuvem privada, pode utilizar um dos circuitos ExpressRoute existentes ou comprar um no portal Azure.

Ao implementar uma nuvem privada, recebe endereços IP para vCenter e NSX-T Manager. Para aceder a essas interfaces de gestão, terá de criar mais recursos na rede virtual da sua subscrição. Você pode encontrar os procedimentos para criar esses recursos e estabelecer [o expressRoute private peering](tutorial-expressroute-global-reach-private-cloud.md) nos tutoriais.

A rede lógica de nuvem privada vem com NSX-T pré-a provisionado. Um portal tier-0 e porta de entrada tier-1 é pré-a provisionado para si. Pode criar um segmento e anexá-lo ao portal de nível 1 existente ou anexá-lo a um novo portal Tier-1 que definir. Os componentes de rede lógico NSX-T proporcionam East-West conectividade entre cargas de trabalho e fornecem conectividade North-South aos serviços de internet e Azure.

## <a name="routing-and-subnet-considerations"></a>Considerações de encaminhamento e sub-rede
A nuvem privada Azure VMware Solution está ligada à sua rede virtual Azure utilizando uma ligação Azure ExpressRoute. Esta alta largura de banda, ligação de baixa latência permite-lhe aceder a serviços em execução na sua subscrição Azure a partir do seu ambiente de nuvem privada. O encaminhamento é baseado em Protocolo de Gateway fronteiriço (BGP), automaticamente a provisionado e habilitado por padrão para cada implantação em nuvem privada. 

As nuvens privadas Azure VMware Solution requerem um mínimo de um bloco de `/22` endereços de rede CIDR para sub-redes, mostrado abaixo. Esta rede complementa as suas redes no local. O bloco de endereços não deve sobrepor-se aos blocos de endereços utilizados noutras redes virtuais nas suas redes de subscrição e no local. Dentro deste bloco de endereços, as redes de gestão, provisionamento e vMotion são aprovisionadas automaticamente.

>[!NOTE]
>Os intervalos permitidos para o seu bloco de endereços são os espaços de endereço privado RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), exceto 172.17.0.0/16.

Exemplo `/22` do bloco de endereços de rede CIDR:  `10.10.0.0/22`

As sub-redes:

| Utilização de rede             | Sub-rede | Exemplo          |
| ------------------------- | ------ | ---------------- |
| Gestão de clouds privadas  | `/26`  | `10.10.0.0/26`   |
| Migrações HCX Mgmt       | `/26`  | `10.10.0.64/26`  |
| Alcance Global Reservado     | `/26`  | `10.10.0.128/26` |
| ExpressRoute Reservado     | `/27`  | `10.10.0.192/27` |
| ExpressRoute olhando      | `/27`  | `10.10.0.224/27` |
| Gestão ESXi           | `/25`  | `10.10.1.0/25`   |
| vMotion Network           | `/25`  | `10.10.1.128/25` |
| Rede de Replicação       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX Uplink                | `/26`  | `10.10.3.0/26`   |
| Reservado                  | `/26`  | `10.10.3.64/26`  |
| Reservado                  | `/26`  | `10.10.3.128/26` |
| Reservado                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Portas de rede necessárias

| Origem | Destino | Protocolo | Porta | Description  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Servidor privado de DNS em nuvem | Servidor DNS nas instalações | UDP | 53 | CLIENTE DNS - Pedidos a prazo do PC vCenter para quaisquer consultas DNS no local (ver secção DNS abaixo) |  
| Servidor DNS no local   | Servidor privado de DNS em nuvem | UDP | 53 | CLIENTE DNS - Encaminhar pedidos de serviços no local para servidores DNS private Cloud (ver secção DNS abaixo) |  
| Rede no local  | Servidor Private Cloud vCenter  | TCP(HTTP)  | 80 | vCenter Server requer porta 80 para ligações HTTP diretas. A Porta 80 redireciona os pedidos para a porta HTTPS 443. Esta reorientação ajuda se utilizar `http://server` em vez de `https://server` .  <br><br>WS-Management (também requer a porta 443 a ser aberta) <br><br>Se utilizar uma base de dados personalizada do Microsoft SQL e não a base de dados SQL Server 2008 agregada no servidor vCenter, a porta 80 é utilizada pelos Serviços de Relatório SQL. Quando instala o vCenter Server, o instalador solicita-lhe que altere a porta HTTP para o servidor vCenter. Altere a porta HTTP do servidor vCenter para um valor personalizado para garantir uma instalação bem sucedida. O Microsoft Internet Information Services (IIS) também utiliza a porta 80. Ver Conflito entre vCenter Server e IIS para a porta 80. |  
| Rede privada de gestão de nuvem | Active Directory no local  | TCP  | 389 | Esta porta deve estar aberta no local e em todas as instâncias remotas do vCenter Server. Esta porta é o número de porta LDAP para os Serviços de Diretório para o grupo vCenter Server. O sistema vCenter Server precisa de se ligar à porta 389, mesmo que não esteja a juntar esta instância do servidor vCenter a um grupo de Modo Ligado. Se estiver a funcionar outro serviço nesta porta, poderá ser preferível removê-lo ou mudar a sua porta para uma porta diferente. Pode executar o serviço LDAP em qualquer porta de 1025 a 65535.  Se este caso estiver a servir como o Microsoft Windows Ative Directory, altere o número de porta de 389 para uma porta disponível de 1025 para 65535. Esta porta é opcional - para configurar no local a AD como uma fonte de identidade no Private Cloud vCenter. |  
| Rede no local  | Servidor Private Cloud vCenter  | TCP(HTTPS)  | 443 | Esta porta permite-lhe aceder ao vCenter a partir de uma rede no local. A porta predefinida que o sistema vCenter Server utiliza para ouvir as ligações do cliente vSphere. Para permitir que o sistema vCenter Server receba dados do vSphere Client, abra a porta 443 na firewall. O sistema vCenter Server também utiliza a porta 443 para monitorizar a transferência de dados dos clientes SDK. Esta porta também é utilizada para os seguintes serviços: WS-Management (também requer a porta 80 para ser aberta). vSphere Acesso ao cliente ao vSphere Update Manager. Ligações de clientes de gestão de rede de terceiros ao servidor vCenter. Clientes de gestão de rede de terceiros têm acesso a anfitriões. |  
| Browser  | Gestor de nuvem híbrida  | TCP(HTTPS) | 9443 | Interface de gestão de eletrodomésticos virtual do Gestor de Nuvem Híbrido para configuração do sistema Hybrid Cloud Manager. |
| Rede Admin  | Gestor de nuvem híbrida | SSH | 22 | Administrador SSH acesso ao Gestor de Nuvem Híbrida. |
| HCM | Porta de entrada de nuvens | TCP(HTTPS) | 8123 | Envie instruções de serviço de replicação baseadas no anfitrião para o Portal da Nuvem Híbrida. |
| HCM | Porta de entrada de nuvens | HTTP TCP(HTTPS) | 9443 | Envie instruções de gestão para o Gateway híbrido local utilizando a API REST. |
| Porta de entrada de nuvens | L2C | TCP(HTTPS) | 443 | Envie instruções de gestão de Cloud Gateway para L2C quando l2C usar o mesmo caminho que o Portal de Nuvem Híbrida. |
| Porta de entrada de nuvens | Anfitriões ESXi | TCP | 80,902 | Gestão e implantação de OVF. |
| Cloud Gateway (local)| Cloud Gateway (remoto) | UDP | 4500 | Requerido para o IPSEC<br>   Troca de chaves de Internet (IKEv2) para encapsular cargas de trabalho para o túnel bidirecional. O Translation-Traversal de endereço de rede (NAT-T) também é suportado. |
| Cloud Gateway (local) | Cloud Gateway (remoto)  | UDP | 500 | Requerido para o IPSEC<br> Troca de chaves de Internet (ISAKMP) para o túnel bidirecional. |
| Rede vCenter no local | Rede privada de gestão de nuvem | TCP | 8000 |  vMotion de VMs de vCenter para Private Cloud vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Considerações de resolução de DHCP e DNS
Aplicações e cargas de trabalho em execução em um ambiente de nuvem privada requerem resolução de nomes e serviços DHCP para atribuição de endereços de procura e IP. Uma infraestrutura DHCP e DNS adequada é necessária para fornecer estes serviços. Pode configurar uma máquina virtual para fornecer estes serviços no seu ambiente de nuvem privada.  

Utilize o serviço DHCP incorporado para NSX ou use um servidor DHCP local na nuvem privada em vez de encaminhar o tráfego DHCP transmitido sobre o WAN de volta para o local.

Para mais detalhes, consulte os [serviços Provide DHCP para o artigo do segmento de rede NSX-T.](deploy-azure-vmware-solution.md#optional-provide-dhcp-services-to-nsx-t-network-segment)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre as considerações e requisitos para a implementação de uma nuvem privada Azure VMware Solution. Assim que tiver a rede adequada no lugar, continue até ao próximo tutorial para criar a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Criar uma nuvem privada Azure VMware Solution](tutorial-create-private-cloud.md)

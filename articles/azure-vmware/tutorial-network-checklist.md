---
title: 'Tutorial: Lista de verificação da rede'
description: Requisitos de rede e detalhes sobre a conectividade da rede e portas de rede
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740151"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Lista de verificação de rede para a Solução Azure VMware (AVS)

A Azure VMware Solution oferece um ambiente de nuvem privada VMware, acessível para utilizadores e aplicações a partir de ambientes ou recursos baseados em Azure. A conectividade é transmitida através de serviços de networking, tais como ligações Azure ExpressRoute e VPN e exigirá algumas gamas específicas de endereços de rede e portas de firewall para permitir os serviços. Este artigo fornece-lhe a informação que precisa de saber para configurar corretamente a sua rede para trabalhar com a AVS.

Neste tutorial, aprende-se a:

> [!div class="checklist"]
> * Requisitos de conectividade da rede
> * DHCP em AVS

## <a name="network-connectivity"></a>Conectividade de rede

A nuvem privada AVS está ligada à sua rede virtual Azure utilizando uma ligação Azure ExpressRoute. Esta elevada largura de banda e baixa ligação de latência permite-lhe aceder a serviços em funcionamento na sua subscrição Azure a partir do seu ambiente privado de nuvem.

As nuvens privadas AVS requerem um mínimo de um `/22` bloco de endereços de rede CIDR para subredes, mostrado abaixo. Esta rede complementa as suas redes no local. Para se ligar aos ambientes no local e às redes virtuais, este deve ser um bloco de endereços de rede não sobreposto.

Exemplo `/22` do bloco de endereços da rede CIDR:`10.10.0.0/22`

As sub-redes:

| Utilização de rede             | Subrede | Exemplo        |
| ------------------------- | ------ | -------------- |
| Gestão de clouds privadas            | `/24`    | `10.10.0.0/24`   |
| rede vMotion       | `/24`    | `10.10.1.0/24`   |
| Cargas de trabalho de VM | `/24`   | `10.10.2.0/24`   |
| Peering ExpressRoute | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Portas de rede necessárias para comunicar com o serviço

Origem|Destino|Protocolo |Porta |Descrição  |Comentário
---|-----|:-----:|:-----:|-----|-----
Servidor DNS de Nuvem Privada  |No local, servidor DNS  |UDP |53|DNS Client - Pedidos para a frente do PC vCenter para quaisquer consultas dNS no local (ver secção DNS abaixo) |
No local, DNS Server  |Servidor DNS de Nuvem Privada  |UDP |53|DNS Client - Pedidos a prazo dos serviços no local para servidores DNS de Nuvem Privada (ver secção DNS abaixo)
Rede no local  |Servidor vCenter de Nuvem Privada  |TCP(HTTP)  |80|vCenter Server requer porta 80 para ligações HTTP diretas.Porta 80 redireciona pedidos para a porta HTTPS 443. Esta reorientação ajuda `http://server` se `https://server`utilizar em vez de .  <br><br>WS-Management (também requer a porta 443 para ser aberta) <br><br>Se utilizar uma base de dados Personalizada do Microsoft SQL e não a base de dados SQL Server 2008 no vCenter Server, a porta 80 é utilizada pelos Serviços de Informação SQL. Quando instala o VCenter Server, o instalador pede-lhe para alterar a porta HTTP para o Servidor vCenter. Mude a porta vCenter Server HTTP para um valor personalizado para garantir uma instalação bem sucedida.O Microsoft Internet Information Services (IIS) também utiliza a porta 80. Ver Conflito entre o VCenter Server e o IIS para a Porta 80.
Rede privada de gestão cloud |Active Directory no local  |TCP  |389|Esta porta deve estar aberta no local e em todas as instâncias remotas do VCenter Server. Esta porta é o número de porta LDAP para os Serviços de Diretório para o grupo vCenter Server. O sistema vCenter Server precisa de se ligar à porta 389, mesmo que não esteja a aderir a esta instância vCenter Server a um grupo de Modo Linked. Se outro serviço estiver em funcionamento nesta porta, talvez seja preferível removê-lo ou mudar a sua porta para uma porta diferente. Pode executar o serviço LDAP em qualquer porta de 1025 a 65535.Se este caso estiver a servir de Diretório Ativo microsoft Windows, mude o número da porta de 389 para uma porta disponível de 1025 a 65535. Esta porta é opcional - para configurar no local a D.C., como fonte de identidade no vCenter private Cloud
Rede no local  |Servidor vCenter de Nuvem Privada  |TCP(HTTPS)  |443|Esta porta permite-lhe aceder ao vCenter a partir da rede no local.A porta padrão que o sistema vCenter Server utiliza para ouvir ligações do Cliente vSphere. Para permitir que o sistema vCenter Server receba dados do Cliente vSphere, abra a porta 443 na firewall. O sistema vCenter Server também utiliza a porta 443 para monitorizar a transferência de dados dos clientes SDK.Esta porta também é utilizada para os seguintes serviços: WS-Management (também requer a porta 80 para ser aberta). vSphere Client acesso ao vSphere Update Manager. Ligações de clientes de gestão de rede de terceiros ao vCenter Server. Clientes de gestão de rede de terceiros acedem aos anfitriões. 
Browser  | Gestor de nuvem híbrida  | TCP(Https) | 9443 | Interface de gestão de aparelhos virtuais hybrid Cloud Manager para configuração do sistema Hybrid Cloud Manager.
Rede Admin  | Gestor de nuvem híbrida |ssh |22| Acesso ssh administrador ao Hybrid Cloud Manager.
HCM |   Cloud Gateway|TCP(HTTPS) |8123| Envie instruções de serviço de replicação baseadas no hospedeiro para o Portal da Nuvem Híbrida.
HCM |   Cloud Gateway  |    HTTP TCP(HTTPS) |    9443  |  Envie instruções de gestão para o Portal da Nuvem Híbrida local utilizando a API REST.
Cloud Gateway|      L2C |    TCP (HTTP)  |   443 |   Envie instruções de gestão de Cloud Gateway para L2C quando L2C usar o mesmo caminho que o Portal da Nuvem Híbrida.
Cloud Gateway |     Anfitriões ESXi |     TCP |    80,902  |   Gestão e implantação da OVF
Cloud Gateway (local)|     Cloud Gateway (remoto)|     UDP  |   4500 | Obrigatório para o IPSEC<br>   Troca de chaves na Internet (IKEv2) para encapsular cargas de trabalho para o túnel bidirecional. O endereço de rede Translation-Traversal (NAT-T) também é suportado.
Cloud Gateway (local)  |   Cloud Gateway (remoto)  |   UDP  |   500   | Obrigatório para o IPSEC<br> Troca de chaves na Internet (ISAKMP) para o túnel bidirecional.
Rede vCenter no local|      Rede privada de gestão cloud|      TCP  |    8000    |  vMotion de VMs de in-no-local vCenter para Private Cloud vCenter   |     

## <a name="dhcp"></a>DHCP

Aplicações e cargas de trabalho em funcionamento num ambiente privado de nuvem requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP. É necessária uma infraestrutura dHCP e DNS adequada para prestar estes serviços. Pode configurar uma máquina virtual para fornecer estes serviços no seu ambiente de nuvem privada.  
Recomenda-se utilizar o serviço DHCP incorporado no NSX ou utilizar um servidor DHCP local na nuvem privada em vez de encaminhar o tráfego dHCP de transmissão sobre o WAN de volta ao local.

Neste tutorial, aprendeste sobre:

> [!div class="checklist"]
> * Requisitos de conectividade da rede
> * DHCP em AVS

## <a name="next-steps"></a>Passos seguintes

Assim que tiver o networking adequado, continue até ao próximo tutorial para criar a sua nuvem privada AVS.

> [!div class="nextstepaction"]
> [Tutorial: Criar uma nuvem privada AVS](tutorial-create-private-cloud.md)

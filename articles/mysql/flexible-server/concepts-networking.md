---
title: Visão geral de networking - Base de dados Azure para MySQL Flexible Server
description: Saiba mais sobre opções de conectividade e networking na opção de implementação do Servidor Flexível para Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: ec835073a1fe447490f6965fe41478319a47f503
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106841"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Conceitos de conectividade e networking para Azure Database for MySQL - Servidor Flexível (Pré-visualização)

Este artigo descreve opções de conectividade pública e privada para o seu servidor. Você vai aprender em detalhe os conceitos de networking para Azure Database para o servidor Flexível MySQL para criar um servidor de forma segura em Azure.

> [!IMPORTANT]
> A base de dados Azure para MySQL - Servidor Flexível está em pré-visualização.

## <a name="choosing-a-networking-option"></a>Escolher uma opção de networking
Tem duas opções de networking para o seu Azure Database para o MySQL Flexible Server. As opções são **acesso privado (integração VNet)** e **acesso público (endereços IP permitidos)**. Na criação do servidor, tem de escolher uma opção. 

> [!NOTE]
> A sua opção de rede não pode ser alterada após a criação do servidor. 

* **Acesso privado (Integração VNet)** – Pode implantar o seu servidor flexível na sua [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As redes virtuais Azure fornecem comunicação de rede privada e segura. Os recursos numa rede virtual podem comunicar através de endereços IP privados.

   Escolha a opção de Integração VNet se quiser as seguintes capacidades:
   * Conecte-se a partir de recursos Azure na mesma rede virtual ou [rede virtual espreitada](../../virtual-network/virtual-network-peering-overview.md) para o seu servidor flexível
   * Utilize VPN ou ExpressRoute para ligar de recursos não-Azure ao seu servidor flexível
   * Sem ponto final público

* **Acesso público (endereços IP permitidos)** – O seu servidor flexível é acedido através de um ponto final público. O ponto final público é um endereço DNS publicamente resolúvel. A frase "endereços IP permitidos" refere-se a uma série de IPs que escolhe dar permissão para aceder ao seu servidor. Estas permissões são chamadas **regras de firewall.** 

   Escolha o método de acesso público se quiser as seguintes capacidades:
   * Conecte-se a partir de recursos Azure que não suportam redes virtuais
   * Conecte-se a partir de recursos fora de um Azure que não estão ligados por VPN ou ExpressRoute 
   * O servidor flexível tem um ponto final público

Aplicam-se as seguintes características quer opte por utilizar o acesso privado ou a opção de acesso público:
* As ligações a partir de endereços IP autorizados precisam de autenticar no servidor MySQL com credenciais válidas
* [A encriptação de ligação](#tls-and-ssl) está disponível para o tráfego da sua rede
* O servidor tem um nome de domínio totalmente qualificado (fqdn). Para a propriedade de nome hospedeiro em cadeias de conexão, recomendamos a utilização do fqdn em vez de um endereço IP.
* Ambas as opções controlam o acesso ao nível do servidor, não no nível da base de dados ou do nível da tabela. Usaria as propriedades de funções do MySQL para controlar a base de dados, a tabela e outros acessos a objetos.


## <a name="private-access-vnet-integration"></a>Acesso privado (Integração de VNet)
O acesso privado com integração de rede virtual (vnet) fornece comunicação privada e segura para o seu servidor flexível MySQL.

### <a name="virtual-network-concepts"></a>Conceitos de rede virtual
Aqui estão alguns conceitos a conhecer ao utilizar redes virtuais com servidores flexíveis MySQL.

* **Rede virtual** - Uma Rede Virtual Azure (VNet) contém um espaço de endereço IP privado configurado para a sua utilização. Visite a [visão geral da Rede Virtual Azure](../../virtual-network/virtual-networks-overview.md) para saber mais sobre a rede virtual Azure.

    A sua rede virtual deve estar na mesma região Azure que o seu servidor flexível.

* **Sub-rede delegada** - Uma rede virtual contém sub-redes (sub-redes). As sub-redes permitem segmentar a sua rede virtual em espaços de endereço mais pequenos. Os recursos Azure são implantados em sub-redes específicas dentro de uma rede virtual. 

   O seu servidor flexível MySQL deve estar numa sub-rede que é **delegada apenas** para uso de servidor flexível MySQL. Esta delegação significa que apenas os Servidores Flexíveis da Base de Dados do Azure para MySQL podem utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada. Delega uma sub-rede atribuindo a sua propriedade de delegação como Microsoft.DBforMySQL/flexibleServers.

* **Grupos de segurança de rede (NSG)** As regras de segurança nos grupos de segurança da rede permitem filtrar o tipo de tráfego de rede que pode fluir dentro e fora das sub-redes de rede virtuais e interfaces de rede. Reveja a [visão geral](../../virtual-network/network-security-groups-overview.md) do grupo de segurança da rede para obter mais informações.

* **Observação de rede virtual** O espreitamento de rede virtual permite-lhe ligar perfeitamente duas ou mais Redes Virtuais em Azure. As redes virtuais espreitadas aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais em redes virtuais espreitadas utiliza a infraestrutura da espinha dorsal da Microsoft. O tráfego entre a aplicação do cliente e o servidor flexível em VNets esparsos é encaminhado apenas através da rede privada da Microsoft e é isolado apenas para essa rede.

O servidor flexível suporta o espreitamento de rede virtual dentro da mesma região do Azure. Não **é suportado** o apoio de VNets em todas as regiões. Reveja os [conceitos de espreitar a rede virtual](../../virtual-network/virtual-network-peering-overview.md) para obter mais informações.

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Ligação de VNets esprevados na mesma região de Azure
Se a aplicação do cliente que tenta ligar-se ao servidor flexível estiver na rede virtual, pode não ser capaz de se ligar usando o nome de servidor flexível do servidor, uma vez que não consegue resolver o nome DNS para o servidor flexível a partir de VNet. Existem duas opções para resolver isto:
* Utilizar o endereço IP privado (Recomendado para cenário de dev/teste) - Esta opção pode ser utilizada para fins de desenvolvimento ou teste. Pode utilizar o nslookup para reverter o endereço IP privado para o seu nome de servidor flexível (nome de domínio totalmente qualificado) e utilizar o endereço IP privado para se ligar a partir da aplicação do cliente. A utilização do endereço IP privado para ligação ao servidor flexível não é recomendada para utilização de produção, uma vez que pode ser alterada durante o evento planeado ou não planeado.
* Utilizar a zona privada de DNS (recomendado para produção) - Esta opção é adequada para fins de produção. Você fornece uma [zona de DNS privada](../../dns/private-dns-getstarted-portal.md) e liga-a à rede virtual do seu cliente. Na zona privada do DNS, [adiciona-se](../../dns/dns-zones-records.md#record-types) um registo A para o seu servidor flexível utilizando o seu endereço IP privado. Em seguida, pode utilizar o registo A para ligar a partir da aplicação do cliente na rede virtual espreitada para servidor flexível.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Ligação de instalações a servidor flexível em Rede Virtual utilizando ExpressRoute ou VPN
Para cargas de trabalho que exijam acesso a servidor flexível em rede virtual a partir da rede no local, necessitará do [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) ou [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) e da rede virtual [ligada ao local.](/azure/architecture/reference-architectures/hybrid-networking/) Com esta configuração em vigor, irá necessitar de um reencaminhador DENS para resolver o nome de servidor flexível se quiser ligar-se a partir da aplicação do cliente (como o MySQL Workbench) em funcionamento na rede virtual no local. Este remetente DNS é responsável pela resolução de todas as consultas de DNS através de um reencaminhador de nível de servidor para o serviço DNS fornecido pelo Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Para configurar corretamente, precisa dos seguintes recursos:

- Rede no local
- MySQL Flexible Server a provisionado com acesso privado (integração VNet)
- Rede virtual [ligada às instalações](/azure/architecture/reference-architectures/hybrid-networking/)
- Utilizar o reencaminhador DNS [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) implantado em Azure

Em seguida, pode utilizar o nome de servidor flexível (FQDN) para ligar a partir da aplicação do cliente na rede virtual ou na rede no local para servidor flexível.

### <a name="unsupported-virtual-network-scenarios"></a>Cenários de rede virtual não suportados
* Ponto final público (ou IP público ou DNS) - Um servidor flexível implantado numa rede virtual não pode ter um ponto final público
* Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para outra rede virtual ou sub-rede. Não é possível mover a rede virtual para outro grupo de recursos ou subscrição.
* O tamanho da sub-rede (espaços de endereço) não pode ser aumentado uma vez que os recursos existem na sub-rede
* Os VNets que espreitam em todas as regiões não são suportados

Saiba como permitir o acesso privado (integração vnet) utilizando o [portal Azure](how-to-manage-virtual-network-portal.md) ou [O Azure CLI](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Se estiver a utilizar o servidor DNS personalizado, deve utilizar um reencaminhador DNS para resolver o FQDN da Base de Dados Azure para o MySQL - Servidor Flexível. Consulte a [resolução de nomes que utiliza o seu próprio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) para saber mais.

## <a name="public-access-allowed-ip-addresses"></a>Acesso público (endereços IP permitidos)
As características do método de acesso público incluem:
* Apenas os endereços IP que permite têm permissão para aceder ao seu servidor flexível MySQL. Por predefinição, não são permitidos endereços IP. Pode adicionar endereços IP durante a criação do servidor ou depois.
* O seu servidor MySQL tem um nome DNS publicamente resolúvel
* O seu servidor flexível não está numa das suas redes virtuais Azure
* O tráfego de rede de e para o seu servidor não passa por uma rede privada. O tráfego usa as vias gerais da internet.

### <a name="firewall-rules"></a>Regras da firewall
A concessão de permissão a um endereço IP é chamada regra de firewall. Se uma tentativa de ligação vier de um endereço IP que não permitiu, o cliente de origem verá um erro.


### <a name="allowing-all-azure-ip-addresses"></a>Permitindo todos os endereços IP do Azure
Se um endereço IP de saída fixo não estiver disponível para o seu serviço Azure, pode considerar ativar ligações a partir de todos os endereços IP do datacenter Azure.

> [!IMPORTANT]
> O **Permitir o acesso público a partir de serviços e recursos Azure dentro da** opção Azure configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.

Saiba como ativar e gerir o acesso público (endereços IP autorizados) utilizando o [portal Azure](how-to-manage-firewall-portal.md) ou [O Azure CLI](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Resolução de problemas de acesso público
Considere os seguintes pontos quando o acesso à Base de Dados do Microsoft Azure para o serviço MySQL Server não se comporta como espera:

* **As alterações à lista de autorizações ainda não produziram efeitos:** Pode haver um atraso de cinco minutos para que as alterações na Base de Dados Azure para a configuração de firewall do MySQL Server produzam efeitos.

* **A autenticação falhou:** Se um utilizador não tiver permissões na Base de Dados Azure para o servidor MySQL ou a palavra-passe utilizada estiver incorretamente, a ligação à Base de Dados Azure para o servidor MySQL é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentarem ligar-se ao seu servidor. Cada cliente deve ainda fornecer as credenciais de segurança necessárias.

* **Endereço IP do cliente dinâmico:** Se tiver uma ligação à Internet com endereço IP dinâmico e estiver com dificuldades em passar pela firewall, poderá experimentar uma das seguintes soluções:

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) para o intervalo de endereços IP atribuído aos seus computadores clientes que acedam à Base de Dados Azure para o MySQL Server e, em seguida, adicione o intervalo de endereço IP como regra de firewall.
   * Obtenha endereço IP estático em vez dos computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.
  
* **A regra de firewall não está disponível para o formato IPv6:** As regras de firewall devem estar no formato IPv4. Se especificar as regras de firewall no formato IPv6, apresentará o erro de validação.


## <a name="hostname"></a>Hostname (Nome do anfitrião)
Independentemente da opção de networking que escolha, recomendamos que utilize sempre um nome de domínio totalmente qualificado (FQDN) como nome de hospedeiro ao ligar-se ao seu servidor flexível. O endereço IP do servidor não é garantido permanecer estático. A utilização do FQDN irá ajudá-lo a evitar alterações na sua cadeia de ligação. 

Exemplo
* Recomendado `hostname = servername.mysql.database.azure.com`
* Sempre que possível, evite a utilização `hostname = 10.0.0.4` (de um endereço privado) ou `hostname = 40.2.45.67` (um IP público)


## <a name="tls-and-ssl"></a>TLS e SSL
A Azure Database for MySQL Flexible Server suporta ligar as aplicações do seu cliente ao serviço MySQL utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente. TLS é um protocolo atualizado da Camada de Tomadas Seguras (SSL).

A base de dados Azure para o MySQL Flexible Server suporta apenas ligações encriptadas utilizando a Segurança da Camada de Transporte (TLS 1.2). Todas as ligações recebidas com o TLS 1.0 e o TLS 1.1 serão recusadas. Não é possível desativar ou alterar a versão TLS para ligar à Base de Dados Azure para o MySQL Flexible Server. Reveja como [ligar usando SSL/TLS](how-to-connect-tls-ssl.md) para saber mais. 


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como ativar o acesso privado (integração de vnet) com o [portal do Azure](how-to-manage-virtual-network-portal.md) ou [CLI do Azure](how-to-manage-virtual-network-cli.md)
* Saiba mais sobre como ativar o acesso público (endereços IP permitidos) com o [portal do Azure](how-to-manage-firewall-portal.md) ou [CLI do Azure](how-to-manage-firewall-cli.md)
* Saiba como [usar o TLS](how-to-connect-tls-ssl.md)

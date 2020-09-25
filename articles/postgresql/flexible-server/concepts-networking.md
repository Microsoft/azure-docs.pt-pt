---
title: Visão geral de networking - Base de dados Azure para PostgreSQL - Servidor Flexível
description: Saiba mais sobre opções de conectividade e networking na opção de implementação do Servidor Flexível para Azure Database for PostgreSQL
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e4d3a594011cb57ce6dfd951215d0ae7471ae7c2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331680"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Visão geral de networking - Base de dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Este artigo descreve conceitos de conectividade e networking para Azure Database for PostgreSQL - Flexible Server. 

## <a name="choosing-a-networking-option"></a>Escolher uma opção de networking
Tem duas opções de networking para a sua Base de Dados Azure para PostgreSQL - Servidor Flexível. As opções são **acesso privado (integração VNet)** e **acesso público (endereços IP permitidos)**. Na criação do servidor, tem de escolher uma opção. 

> [!NOTE]
> A sua opção de rede não pode ser alterada após a criação do servidor. 

* **Acesso privado (Integração VNet)** – Pode implantar o seu servidor flexível na sua [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As redes virtuais Azure fornecem comunicação de rede privada e segura. Os recursos numa rede virtual podem comunicar através de endereços IP privados.

   Escolha a opção de Integração VNet se quiser as seguintes capacidades:
   * Conecte-se dos recursos Azure na mesma rede virtual ao seu servidor flexível usando endereços IP privados
   * Utilize VPN ou ExpressRoute para ligar de recursos não-Azure ao seu servidor flexível
   * O servidor flexível não tem ponto final público

* **Acesso público (endereços IP permitidos)** – O seu servidor flexível é acedido através de um ponto final público. O ponto final público é um endereço DNS publicamente resolúvel. A frase "endereços IP permitidos" refere-se a uma série de IPs que escolhe dar permissão para aceder ao seu servidor. Estas permissões são chamadas **regras de firewall.** 

   Escolha o método de acesso público se quiser as seguintes capacidades:
   * Conecte-se a partir de recursos Azure que não suportam redes virtuais
   * Conecte-se a partir de recursos fora de um Azure que não estão ligados por VPN ou ExpressRoute 
   * O servidor flexível tem um ponto final público

Aplicam-se as seguintes características quer opte por utilizar o acesso privado ou a opção de acesso público:
* As ligações a partir de endereços IP autorizados precisam de autenticar para o servidor PostgreSQL com credenciais válidas
* [A encriptação de ligação](#tls-and-ssl) está disponível para o tráfego da sua rede
* O servidor tem um nome de domínio totalmente qualificado (fqdn). Para a propriedade de nome hospedeiro em cadeias de conexão, recomendamos a utilização do fqdn em vez de um endereço IP.
* Ambas as opções controlam o acesso ao nível do servidor, não no nível da base de dados ou do nível da tabela. Utilizaria as propriedades de funções do PostgreSQL para controlar a base de dados, a tabela e outros acessos a objetos.


## <a name="private-access-vnet-integration"></a>Acesso privado (Integração de VNet)
O acesso privado com integração de rede virtual (vnet) fornece comunicação privada e segura para o seu servidor flexível PostgreSQL.

### <a name="virtual-network-concepts"></a>Conceitos de rede virtual
Aqui estão alguns conceitos a conhecer ao usar redes virtuais com servidores flexíveis PostgreSQL.

* **Rede virtual** - Uma Rede Virtual Azure (VNet) contém um espaço de endereço IP privado configurado para a sua utilização. Visite a [visão geral da Rede Virtual Azure](../../virtual-network/virtual-networks-overview.md) para saber mais sobre a rede virtual Azure.

    A sua rede virtual deve estar na mesma região Azure que o seu servidor flexível.


* **Sub-rede delegada** - Uma rede virtual contém sub-redes (sub-redes). As sub-redes permitem segmentar a sua rede virtual em espaços de endereço mais pequenos. Os recursos Azure são implantados em sub-redes específicas dentro de uma rede virtual. 

   O seu servidor flexível PostgreSQL deve estar numa sub-rede que é **delegada apenas** para uso de servidor flexível PostgreSQL. Esta delegação significa que apenas a Base de Dados Azure para servidores flexíveis postgresQL pode utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada. Delega uma sub-rede atribuindo a sua propriedade de delegação como Microsoft.DBforPostgreSQL/flexibleServers.

* **Grupos de segurança de rede (NSG)** As regras de segurança nos grupos de segurança da rede permitem filtrar o tipo de tráfego de rede que pode fluir dentro e fora das sub-redes de rede virtuais e interfaces de rede. Reveja a [visão geral](../../virtual-network/network-security-groups-overview.md) do grupo de segurança da rede para obter mais informações.


### <a name="unsupported-virtual-network-scenarios"></a>Cenários de rede virtual não suportados
* Ponto final público (ou IP público ou DNS) - Um servidor flexível implantado numa rede virtual não pode ter um ponto final público
* Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para outra rede virtual ou sub-rede. Não é possível mover a rede virtual para outro grupo de recursos ou subscrição.
* O tamanho da sub-rede (espaços de endereço) não pode ser aumentado uma vez que os recursos existem na sub-rede
* Os VNets que espreitam em todas as regiões não são suportados

Saiba como criar um servidor flexível com acesso privado (integração VNet) [no portal Azure](how-to-manage-virtual-network-portal.md) ou [no Azure CLI](how-to-manage-virtual-network-cli.md).

## <a name="public-access-allowed-ip-addresses"></a>Acesso público (endereços IP permitidos)
As características do método de acesso público incluem:
* Apenas os endereços IP que permite têm permissão para aceder ao seu servidor flexível PostgreSQL. Por predefinição, não são permitidos endereços IP. Pode adicionar endereços IP durante a criação do servidor ou depois.
* O seu servidor PostgreSQL tem um nome DNS publicamente resolvevel
* O seu servidor flexível não está numa das suas redes virtuais Azure
* O tráfego de rede de e para o seu servidor não passa por uma rede privada. O tráfego usa as vias gerais da internet.

### <a name="firewall-rules"></a>Regras da firewall
A concessão de permissão a um endereço IP é chamada regra de firewall. Se uma tentativa de ligação vier de um endereço IP que não permitiu, o cliente de origem verá um erro.

Saiba como criar um servidor flexível com acesso público (endereços IP autorizados) [no portal Azure](how-to-manage-firewall-portal.md) ou [no Azure CLI](how-to-manage-firewall-cli.md).


### <a name="allowing-all-azure-ip-addresses"></a>Permitindo todos os endereços IP do Azure
Se um endereço IP de saída fixo não estiver disponível para o seu serviço Azure, pode considerar ativar ligações a partir de todos os endereços IP do datacenter Azure.

> [!IMPORTANT]
> O **Permitir o acesso público a partir de serviços e recursos Azure dentro da** opção Azure configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.

### <a name="troubleshooting-public-access-issues"></a>Resolução de problemas de acesso público
Considere os seguintes pontos quando o acesso à Base de Dados do Microsoft Azure para o serviço PostgreSQL Server não se comporta como espera:

* **As alterações à lista de autorizações ainda não produziram efeitos:** Pode haver um atraso de cinco minutos para que as alterações na Base de Dados Azure para a configuração de firewall do Servidor PostgreSQL produzam efeitos.

* **A autenticação falhou:** Se um utilizador não tiver permissões na Base de Dados Azure para o servidor PostgreSQL ou a palavra-passe utilizada estiver incorretamente, a ligação à Base de Dados Azure para o servidor PostgreSQL é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentarem ligar-se ao seu servidor. Cada cliente deve ainda fornecer as credenciais de segurança necessárias.

* **Endereço IP do cliente dinâmico:** Se tiver uma ligação à Internet com endereço IP dinâmico e estiver com dificuldades em passar pela firewall, poderá experimentar uma das seguintes soluções:

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) para o intervalo de endereços IP atribuído aos seus computadores clientes que acedam à Base de Dados Azure para o Servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como regra de firewall.
   * Obtenha endereço IP estático em vez dos computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.


## <a name="hostname"></a>Hostname (Nome do anfitrião)
Independentemente da opção de networking que escolha, recomendamos que utilize sempre um nome de domínio totalmente qualificado (FQDN) como nome de hospedeiro ao ligar-se ao seu servidor flexível. O endereço IP do servidor não é garantido permanecer estático. A utilização do FQDN irá ajudá-lo a evitar alterações na sua cadeia de ligação. 

Exemplo
* Recomendado `hostname = servername.postgres.database.azure.com`
* Sempre que possível, evite a utilização `hostname = 10.0.0.4` (de um endereço privado) ou `hostname = 40.2.45.67` (um endereço público)


## <a name="tls-and-ssl"></a>TLS e SSL
A Azure Database for PostgreSQL - Flexible Server suporta ligar as aplicações do seu cliente ao serviço PostgreSQL utilizando a Segurança da Camada de Transporte (TLS). O TLS é um protocolo padrão da indústria que garante ligações de rede encriptadas entre o servidor da base de dados e as aplicações do cliente. TLS é um protocolo atualizado de SSL (Camada de tomadas seguras).

A base de dados Azure para PostgreSQL - Servidor Flexível suporta apenas ligações encriptadas utilizando a Segurança da Camada de Transporte. Todas as ligações recebidas com TLS 1.0 e TLS 1.1 serão negadas. 

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar um servidor flexível com **acesso privado (integração VNet)** [no portal Azure](how-to-manage-virtual-network-portal.md) ou [no Azure CLI](how-to-manage-virtual-network-cli.md).
* Saiba como criar um servidor flexível com **acesso público (endereços IP autorizados)** [no portal Azure](how-to-manage-firewall-portal.md) ou [no Azure CLI](how-to-manage-firewall-cli.md).

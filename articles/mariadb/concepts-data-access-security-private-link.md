---
title: Link Privado - Base de Dados Azure para MariaDB
description: Saiba como funciona o link privado para a Azure Database para a MariaDB.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: ec43fa995fc9772964db399baa0b6f8c2a7db1db
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241502"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Link privado para base de dados Azure para MariaDB

O Private Link permite-lhe criar pontos finais privados para a Base de Dados Azure para MariaDB e assim traz serviços Azure dentro da sua Rede Virtual privada (VNet). O ponto final privado expõe um IP privado que pode utilizar para ligar à sua Base de Dados Azure para o servidor de base de dados MariaDB, tal como qualquer outro recurso no VNet.

Para obter uma lista de serviços PaaS que suportem a funcionalidade Private Link, reveja a [documentação](../private-link/index.yml)private Link . Um ponto final privado é um endereço IP privado dentro de um [VNet](../virtual-network/virtual-networks-overview.md) e Subnet específicos.

> [!NOTE]
> A funcionalidade de ligação privada só está disponível para a Base de Dados Azure para servidores MariaDB nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de base de dados está num destes níveis de preços.

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Prevenção da transferência de dados não autorizada)

A ex-filtração de dados na Base de Dados Azure para MariaDB é quando um utilizador autorizado, como um administrador de base de dados, é capaz de extrair dados de um sistema e movê-lo para outro local ou sistema fora da organização. Por exemplo, o utilizador desloca os dados para uma conta de armazenamento detida por terceiros.

Considere um cenário com um utilizador que executa a bancada de trabalho MariaDB dentro de um Azure VM conectando-se a uma base de dados Azure para exemplo mariaDB. Este caso MariaDB está no centro de dados dos EUA. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos na Base de Dados Azure para MariaDB utilizando controlos de acesso à rede.

* Desative todo o tráfego de serviço Azure para Azure Database for MariaDB através do ponto final público, definindo Allow Azure Services to OFF. Certifique-se de que nenhum endereço ou gama IP pode aceder ao servidor através de [regras de firewall](concepts-firewall-rules.md) ou [de pontos finais de serviço de rede virtual](concepts-data-access-security-vnet.md).

* Apenas permita o tráfego na Base de Dados Azure para MariaDB utilizando o endereço IP privado do VM. Para obter mais informações, consulte os artigos sobre as regras [de Service Endpoint](concepts-data-access-security-vnet.md) e [VNet firewall](howto-manage-vnet-portal.md).

* No Azure VM, reduza o âmbito de ligação de saída utilizando grupos de segurança de rede (NSGs) e tags de serviço da seguinte forma:

    * Especifique uma regra NSG para permitir o tráfego para tag de serviço = SQL. WestUs - apenas permitindo a ligação à Base de Dados Azure para MariaDB nos EUA
    * Especifique uma regra NSG (com uma prioridade maior) para negar o tráfego para tag de serviço = SQL - negando ligações à Base de Dados MariaDB em todas as regiões</br></br>

No final desta configuração, o Azure VM só pode ligar-se à Base de Dados Azure para a MariaDB na região oeste dos EUA. No entanto, a conectividade não se restringe a uma única base de dados Azure para a MariaDB. O VM ainda pode ligar-se a qualquer Base de Dados Azure para MariaDB na região oeste dos EUA, incluindo as bases de dados que não fazem parte da subscrição. Embora tenhamos reduzido o âmbito da exfiltração de dados no cenário acima para uma região específica, não o eliminámos completamente.</br>

Com o Private Link, pode agora configurar controlos de acesso à rede como NSGs para restringir o acesso ao ponto final privado. Os recursos individuais do Azure PaaS são então mapeados para pontos finais privados específicos. Um insider malicioso só pode aceder ao recurso PaaS mapeado (por exemplo, uma Base de Dados Azure para MariaDB) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade no local sobre o espreitamento privado

Quando se conecta ao ponto final público a partir de máquinas no local, o seu endereço IP precisa de ser adicionado à firewall baseada em IP utilizando uma regra de firewall ao nível do servidor. Embora este modelo funcione bem para permitir o acesso a máquinas individuais para dev ou testar cargas de trabalho, é difícil de gerir em ambiente de produção.

Com o Private Link, pode permitir o acesso transversal ao ponto final privado utilizando a [Via Expresso](https://azure.microsoft.com/services/expressroute/) (ER), o perspitamento privado ou [o túnel VPN.](../vpn-gateway/index.yml) Podem posteriormente desativar todos os acessos através do ponto final público e não utilizar a firewall baseada em IP.

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para MariaDB e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.DBforMariaDB** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Configure Link Privado para Base de Dados Azure para MariaDB

### <a name="creation-process"></a>Processo de Criação

Os pontos finais privados são necessários para ativar o Link Privado. Isto pode ser feito usando os seguintes guias de como fazer.

* [Portal do Azure](howto-configure-privatelink-portal.md)
* [CLI](howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Processo de aprovação

Uma vez que o administrador de rede cria o ponto final privado (PE), o administrador pode gerir a ligação de ponto final privado (PEC) para Azure Database for MariaDB. Esta separação de deveres entre a administração da rede e a DBA é útil para a gestão da Base de Dados Azure para a conectividade MariaDB. 

* Navegue para a Base de Dados Azure para o recurso do servidor MariaDB no portal Azure. 
    * Selecione as ligações de ponto final privado no painel esquerdo
    * Mostra uma lista de todas as ligações privadas de ponto final (PECs)
    * Ponto final privado correspondente (PE) criado

![selecione o portal de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione um PEC individual da lista selecionando-o.

![selecione o ponto final privado pendente de aprovação](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O administrador do servidor MariaDB pode optar por aprovar ou rejeitar um PEC e opcionalmente adicionar uma resposta de texto curta.

![selecione a mensagem de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após aprovação ou rejeição, a lista refletirá o estado apropriado juntamente com o texto de resposta

![selecionar o estado final do ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Utilizar casos de Link Privado para Base de Dados Azure para MariaDB

Os clientes podem ligar-se ao ponto final privado a partir do mesmo VNet, vNet esprevado na mesma região, ou através da ligação VNet-to-VNet entre regiões. Além disso, os clientes podem conectar-se a partir de instalações usando expressRoute, peering privado ou túneis VPN. Abaixo está um diagrama simplificado mostrando os casos de uso comum.

![selecione a visão geral do ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ligação a partir de um Azure VM em Rede Virtual Peered (VNet)
Configure [VNet olhando](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer conectividade com a Base de Dados Azure para MariaDB a partir de um VM Azure em um VNet esprevado.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ligação a partir de um VM Azure em ambiente VNet-to-VNet
Configure [a ligação de gateway VNet-to-VNet VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer conectividade com uma Base de Dados Azure para MariaDB a partir de um VM Azure em uma região ou subscrição diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ligação a partir de um ambiente no local sobre VPN
Para estabelecer conectividade de um ambiente no local até à Base de Dados Azure para MariaDB, escolha e implemente uma das opções:

* [Ligação ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Ligação VPN Site a Site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Ligação privada combinada com regras de firewall

As seguintes situações e resultados são possíveis quando utiliza o Private Link em combinação com as regras de firewall:

* Se não configurar quaisquer regras de firewall, então, por padrão, nenhum tráfego será capaz de aceder à Base de Dados Azure para MariaDB.

* Se configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada são autorizados pela regra de firewall correspondente.

* Se não configurar qualquer ponto final de tráfego público ou serviço e criar pontos finais privados, então a Base de Dados Azure para MariaDB só é acessível através dos pontos finais privados. Se não configurar o tráfego público ou um ponto final de serviço, depois de todos os pontos finais privados aprovados serem rejeitados ou eliminados, nenhum tráfego poderá aceder à Base de Dados Azure para MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Negar acesso público à Base de Dados de Azure para MariaDB

Se pretender confiar apenas em pontos finais privados para aceder à sua Base de Dados Azure para MariaDB, pode desativar a definição de todos os pontos finais públicos [(regras de firewall](concepts-firewall-rules.md) e [pontos finais do serviço VNet)](concepts-data-access-security-vnet.md)definindo a configuração **de Acesso à Rede Pública de Negação** no servidor de base de dados. 

Quando esta definição é definida como *SIM,* apenas as ligações através de pontos finais privados são permitidas na sua Base de Dados Azure para MariaDB. Quando esta definição estiver definida para *NO,* os clientes podem ligar-se à sua Base de Dados Azure para MariaDB com base nas definições do seu ponto final de firewall ou de serviço VNet. Além disso, uma vez definido o valor do acesso à rede privada, os clientes não podem adicionar e/ou atualizar as "regras de firewall" e 'regras de ponto final de serviço VNet'.

> [!Note]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para PostgreSQL - Servidor único suporta os níveis de preços otimizados para fins gerais e memória.
>
> Esta definição não tem qualquer impacto nas configurações SSL e TLS para a sua Base de Dados Azure para MariaDB.

Para saber como configurar o Acesso à **Rede Pública de Negação** para a sua Base de Dados de Azure para MariaDB a partir do portal Azure, consulte como configurar o [Acesso à Rede Pública de Negação](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Base de Dados Azure para funcionalidades de segurança MariaDB, consulte os seguintes artigos:

* Para configurar uma firewall para Azure Database for MariaDB, consulte [o suporte do Firewall](concepts-firewall-rules.md).

* Para aprender a configurar um ponto final de serviço de rede virtual para a sua Base de Dados Azure para MariaDB, consulte [o acesso configurado a partir de redes virtuais.](concepts-data-access-security-vnet.md)

* Para uma visão geral da Base de Dados Azure para a conectividade MariaDB, consulte [a Base de Dados Azure para a Arquitetura de Conectividade MariaDB](concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

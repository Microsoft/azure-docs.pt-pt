---
title: Private Link - Base de Dados Azure para MariaDB
description: Saiba como funciona o link privado para a Base de Dados Azure para o MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b05a202537492fe54a76cf40a3b15987e099a7e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367725"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Link privado para base de dados Azure para MariaDB

O Private Link permite-lhe criar pontos finais privados para a Base de Dados Azure para o MariaDB e por isso traz serviços Azure dentro da sua Rede Virtual Privada (VNet). O ponto final privado expõe um IP privado que pode utilizar para ligar à sua Base de Dados Azure para servidor de base de dados MariaDB, tal como qualquer outro recurso no VNet.

Para obter uma lista de serviços PaaS que suportam a funcionalidade Private Link, reveja a [documentação](https://docs.microsoft.com/azure/private-link/index)do Private Link. Um ponto final privado é um endereço IP privado dentro de uma [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e Subnet específicas.

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para MariaDB suporta níveis de preços otimizados para fins gerais e memória.

## <a name="data-exfiltration-prevention"></a>Prevenção da exfiltração de dados

A ex-filtração de dados na Base de Dados Azure para o MariaDB é quando um utilizador autorizado, como um administrador de base de dados, é capaz de extrair dados de um sistema e movê-lo para outro local ou sistema fora da organização. Por exemplo, o utilizador transfere os dados para uma conta de armazenamento propriedade de terceiros.

Considere um cenário com um utilizador que executa a bancada mariaDB dentro de um Azure VM que liga a uma Base de Dados Azure para a instância MariaDB. Esta instância MariaDB está no centro de dados dos EUA Ocidentais. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos na Base de Dados Azure para MariaDB utilizando controlos de acesso à rede.

* Desative todo o tráfego de serviço azure para a Base de Dados Azure para MariaDB através do ponto final do público, definindo os Serviços De Acesso Azure para OFF. Certifique-se de que não são permitidos endereços IP ou intervalos para aceder ao servidor através de regras de [firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) ou [pontos finais de serviço](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)de rede virtual .

* Apenas permita o tráfego na Base de Dados Azure para MariaDB utilizando o endereço IP privado do VM. Para mais informações, consulte os artigos sobre as regras de firewall [do Service Endpoint](concepts-data-access-security-vnet.md) e [VNet](howto-manage-vnet-portal.md).

* No VM Azure, reduza o âmbito de ligação de saída utilizando grupos de segurança de rede (NSGs) e etiquetas de serviço da seguinte forma:

    * Especifique uma regra NSG para permitir o tráfego para etiqueta de serviço = SQL. WestUs - apenas permitindo a ligação à Base de Dados Azure para MariaDB nos EUA Ocidentais
    * Especificar uma regra nsg (com uma prioridade maior) para negar o tráfego para etiqueta de serviço = SQL - negando ligações à Base de Dados MariaDB em todas as regiões</br></br>

No final desta configuração, o Azure VM só pode ligar-se à Base de Dados Azure para o MariaDB na região dos EUA Ocidentais. No entanto, a conectividade não se restringe a uma única Base de Dados Azure para mariadb. O VM ainda pode ligar a qualquer Base de Dados Azure para MariaDB na região dos EUA Ocidentais, incluindo as bases de dados que não fazem parte da subscrição. Embora tenhamos reduzido o âmbito da exfiltração de dados no cenário acima para uma região específica, não o eliminámos completamente.</br>

Com o Private Link, pode agora configurar controlos de acesso à rede como NSGs para restringir o acesso ao ponto final privado. Os recursos individuais do Azure PaaS são então mapeados para pontos finais privados específicos. Um insider malicioso só pode aceder ao recurso PaaS mapeado (por exemplo, uma Base de Dados Azure para MariaDB) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade no local sobre o peering privado

Quando se liga ao ponto final público a partir de máquinas no local, o seu endereço IP precisa de ser adicionado à firewall baseada em IP utilizando uma regra de firewall ao nível do servidor. Embora este modelo funcione bem para permitir o acesso a máquinas individuais para dev ou trabalhos de trabalho de teste, é difícil de gerir em um ambiente de produção.

Com private link, você pode permitir acesso cross-premises ao ponto final privado usando [a Rota Expresso](https://azure.microsoft.com/services/expressroute/) (ER), espreitar privado ou [túnel VPN](https://docs.microsoft.com/azure/vpn-gateway/). Posteriormente, podem desativar todos os acessos através de ponto final público e não utilizar a firewall baseada em IP.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Configure link privado para base de dados Azure para MariaDB

### <a name="creation-process"></a>Processo de Criação

Os pontos finais privados são necessários para ativar o Private Link. Isto pode ser feito utilizando os seguintes guias de como fazer.

* [Portal Azure](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo de Aprovação

Uma vez que o administrador de rede cria o ponto final privado (PE), o administrador pode gerir a ligação de ponto final privado (PEC) à Base de Dados Azure para MariaDB. Esta separação de deveres entre o administrador da rede e o DBA é útil para a gestão da Base de Dados Azure para a conectividade MariaDB. 

* Navegue na Base de Dados Azure para o recurso do servidor MariaDB no portal Azure. 
    * Selecione as ligações de ponto final privado no painel esquerdo
    * Mostra uma lista de todas as ligações de ponto final privado (PECs)
    * Ponto final privado correspondente (PE) criado

![selecionar o portal de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione um PEC individual da lista selecionando-o.

![selecionar o ponto final privado pendente de aprovação](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O administrador do servidor MariaDB pode optar por aprovar ou rejeitar um PEC e opcionalmente adicionar uma resposta de texto curta.

![selecionar a mensagem de ponto final privado](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após aprovação ou rejeição, a lista refletirá o estado apropriado juntamente com o texto de resposta

![selecionar o estado final de ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Utilize casos de Link Privado para Base de Dados Azure para MariaDB

Os clientes podem ligar-se ao ponto final privado a partir do mesmo VNet, peered VNet na mesma região, ou através da ligação VNet-to-VNet através de regiões. Além disso, os clientes podem ligar-se a partir de instalações utilizando expressRoute, peering privado ou túnel VPN. Abaixo está um diagrama simplificado que mostra os casos de uso comuns.

![selecionar a visão geral do ponto final privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ligação a partir de um VM Azure em Rede Virtual Peered (VNet)
Configure [o vNet que procura](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) estabelecer conectividade com a Base de Dados Azure para MariaDB a partir de um VM Azure num VNet com um VNet com um VNet com um vNet com um vNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ligação a partir de um VM Azure em ambiente VNet-to-VNet
Configure a ligação de [gateway VNet-to-VNet VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) para estabelecer conectividade com uma Base de Dados Azure para MariaDB a partir de um VM Azure numa região ou subscrição diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ligação a partir de um ambiente no local sobre VPN
Para estabelecer conectividade desde um ambiente no local até à Base de Dados Azure para MariaDB, escolha e implemente uma das opções:

* [Ligação ponto-a-local](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Ligação VPN Site a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Link Privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando utiliza o Private Link em combinação com as regras de firewall:

* Se não configurar quaisquer regras de firewall, então, por padrão, nenhum tráfego poderá aceder à Base de Dados Azure para MariaDB.

* Se configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada são autorizados pelo tipo correspondente de regra de firewall.

* Se não configurar qualquer ponto final de tráfego público ou de serviço e criar pontos finais privados, então a Base de Dados Azure para MariaDB só é acessível através dos pontos finais privados. Se não configurar o tráfego público ou um ponto final de serviço, depois de todos os pontos finais privados aprovados serem rejeitados ou eliminados, nenhum tráfego poderá aceder à Base de Dados Azure para MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Negar acesso público à Base de Dados Azure para MariaDB

Se pretender confiar completamente apenas em pontos finais privados para aceder à sua Base de Dados Azure para O DIfDb, pode desativar a definição de todos os pontos finais públicos[(regras](concepts-firewall-rules.md) de firewall e [pontos finais](concepts-data-access-security-vnet.md)do serviço VNet ) definindo a configuração de Acesso à **Rede Pública Denegação** no servidor de base de dados. 

Quando esta definição estiver definida para *SIM,* apenas as ligações através de pontos finais privados são permitidas à sua Base de Dados Azure para MariaDB. Quando esta definição estiver definida para *NO,* os clientes podem ligar-se à sua Base de Dados Azure para MariaDB com base nas definições de ponto final do seu ponto final de firewall ou vNet. Além disso, uma vez definido o valor do acesso à rede Privada, não é possível adicionar e/ou atualizar as regras existentes de firewall e ponto final de serviço VNet.

> [!Note]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para PostgreSQL - Servidor Único suporta níveis de preços otimizados para fins gerais e memória.
>
> Esta definição não tem qualquer impacto nas configurações SSL e TLS para a sua Base de Dados Azure para MariaDB.

Para saber como definir o Acesso à **Rede Pública Deny** para a sua Base de Dados Azure para MariaDB a partir do portal Azure, consulte como configurar o Acesso à Rede Pública [DeNega](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Base de Dados Azure para funcionalidades de segurança MariaDB, consulte os seguintes artigos:

* Para configurar uma firewall para Azure Database para MariaDB, consulte o [suporte firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules).

* Para aprender a configurar um ponto final de serviço de rede virtual para a sua Base de Dados Azure para MariaDB, consulte o [acesso configure a partir de redes virtuais](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet).

* Para uma visão geral da Base de Dados Azure para conectividade MariaDB, consulte Base de [Dados Azure para Arquitetura de Conectividade MariaDB](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)

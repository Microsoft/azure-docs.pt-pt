---
title: Link privado para o banco de dados do Azure para MariaDB (versão prévia)
description: Saiba como o link privado funciona para o banco de dados do Azure para MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 40aa35e9f9d40a8a021797a0a2a4af9216d90618
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898173"
---
# <a name="private-link-for-azure-database-for-mariadb-preview"></a>Link privado para o banco de dados do Azure para MariaDB (versão prévia)

O link privado permite que você se conecte a vários serviços de PaaS no Azure por meio de um ponto de extremidade privado. O link privado do Azure essencialmente traz os serviços do Azure dentro de sua VNet (rede virtual privada). Os recursos de PaaS podem ser acessados usando o endereço IP privado, assim como qualquer outro recurso na VNet.

Para obter uma lista dos serviços de PaaS que dão suporte à funcionalidade de link privado, examine a [documentação](https://docs.microsoft.com/azure/private-link/index)do link privado. Um ponto de extremidade privado é um endereço IP privado em uma [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e sub-rede específicas.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MariaDB dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="data-exfiltration-prevention"></a>Prevenção de vazamento de dados

Data ex-filtragem no banco de dados do Azure para MariaDB é quando um usuário autorizado, como um administrador de banco de dados, é capaz de extrair dados de um sistema e movê-los para outro local ou sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário executando o MariaDB Workbench dentro de uma VM do Azure conectando-se a uma instância do banco de dados do Azure para MariaDB. Essa instância do MariaDB está no data center do oeste dos EUA. O exemplo a seguir mostra como limitar o acesso com pontos de extremidade públicos no banco de dados do Azure para MariaDB usando controles de acesso à rede.

* Desabilite todo o tráfego de serviço do Azure para o banco de dados do Azure para MariaDB por meio do ponto de extremidade público, configurando permitir serviços do Azure para Certifique-se de que nenhum endereço IP ou intervalo tenha permissão para acessar o servidor por meio de [regras de firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) ou pontos de extremidade de [serviço de rede virtual](https://docs.microsoft.com/azure/mariadb/concepts-data-access-and-security-vnet).

* Permita o tráfego somente para o banco de dados do Azure para MariaDB usando o endereço IP privado da VM. Para obter mais informações, consulte os artigos sobre as regras de [ponto de extremidade de serviço](concepts-data-access-security-vnet.md) e [Firewall VNet](howto-manage-vnet-portal.md).

* Na VM do Azure, restrinja o escopo da conexão de saída usando NSGs (grupos de segurança de rede) e marcas de serviço da seguinte maneira:

    * Especifique uma regra de NSG para permitir o tráfego para a marca de serviço = SQL. Westus-permitindo somente a conexão com o banco de dados do Azure para MariaDB no oeste dos EUA
    * Especifique uma regra de NSG (com prioridade mais alta) para negar o tráfego para a marca de serviço = SQL negando conexões com o banco de dados MariaDB em todas as regiões</br></br>

No final desta configuração, a VM do Azure pode se conectar somente ao banco de dados do Azure para MariaDB na região oeste dos EUA. No entanto, a conectividade não é restrita a um único banco de dados do Azure para MariaDB. A VM ainda pode se conectar a qualquer banco de dados do Azure para MariaDB na região oeste dos EUA, incluindo os bancos que não fazem parte da assinatura. Embora tenhamos reduzido o escopo dos dados vazamento no cenário acima para uma região específica, nós não o eliminamos completamente.</br>

Com o link privado, agora você pode configurar controles de acesso à rede como NSGs para restringir o acesso ao ponto de extremidade privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um insider mal-intencionado só pode acessar o recurso de PaaS mapeado (por exemplo, um banco de dados do Azure para MariaDB) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local sobre emparelhamento privado

Quando você se conecta ao ponto de extremidade público de computadores locais, seu endereço IP precisa ser adicionado ao firewall baseado em IP usando uma regra de firewall de nível de servidor. Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciar em um ambiente de produção.

Com o link privado, você pode habilitar o acesso entre instalações ao ponto de extremidade privado usando o er ( [rota expressa](https://azure.microsoft.com/services/expressroute/) ), o emparelhamento privado ou o [túnel VPN](https://docs.microsoft.com/azure/vpn-gateway/). Em seguida, eles podem desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Configurar o link privado para o banco de dados do Azure para MariaDB

### <a name="creation-process"></a>Processo de criação

Pontos de extremidade privados são necessários para habilitar o link privado. Isso pode ser feito usando os seguintes guias de instruções.

* [Portal do Azure](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo de aprovação

Depois que o administrador de rede cria o PE (ponto de extremidade particular), o administrador pode gerenciar o PEC (conexão de ponto de extremidade particular) para o banco de dados do Azure para MariaDB.

> [!NOTE]
> Atualmente, o banco de dados do Azure para MariaDB dá suporte apenas à aprovação automática para o ponto de extremidade privado.

* Navegue até o recurso de servidor do banco de dados do Azure para MariaDB no portal do Azure. 
    * Selecione as conexões de ponto de extremidade privado no painel esquerdo
    * Mostra uma lista de todas as conexões de ponto de extremidade privado (PECs)
    * Ponto de extremidade particular correspondente (PE) criado

![Selecione o portal de ponto de extremidade privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione um PEC individual na lista selecionando-o.

![Selecione a aprovação de ponto de extremidade particular pendente](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O administrador do servidor MariaDB pode optar por aprovar ou rejeitar um PEC e, opcionalmente, adicionar uma resposta de texto curto.

![Selecione a mensagem de ponto de extremidade particular](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após a aprovação ou rejeição, a lista refletirá o estado apropriado junto com o texto de resposta

![Selecione o estado final do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Casos de uso de link privado para o banco de dados do Azure para MariaDB

Os clientes podem se conectar ao ponto de extremidade privado da mesma VNet, rede virtual emparelhada na mesma região ou via conexão VNet a VNet entre regiões. Além disso, os clientes podem se conectar do local usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

![Selecione a visão geral do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Conectando de uma VM do Azure na VNet (rede virtual emparelhada)
Configure o [emparelhamento VNet](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) para estabelecer a conectividade com o banco de dados do Azure para MariaDB de uma VM do Azure em uma VNet emparelhada.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Conectando de uma VM do Azure no ambiente de VNet a VNet
Configure a [conexão de gateway VPN de vnet para vnet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) para estabelecer a conectividade com um banco de dados do Azure para MariaDB de uma VM do Azure em uma região ou assinatura diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Conectando de um ambiente local pela VPN
Para estabelecer a conectividade de um ambiente local para o banco de dados do Azure para MariaDB, escolha e implemente uma das opções:

* [Conexão ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Conexão VPN site a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Link privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o link privado em combinação com regras de firewall:

* Se você não configurar nenhuma regra de firewall, por padrão, nenhum tráfego será capaz de acessar o banco de dados do Azure para MariaDB.

* Se você configurar o tráfego público ou um ponto de extremidade de serviço e criar pontos de extremidades privados, diferentes tipos de tráfego de entrada serão autorizados pelo tipo de regra de firewall correspondente.

* Se você não configurar nenhum tráfego público ou ponto de extremidade de serviço e criar pontos de extremidades privados, o banco de dados do Azure para MariaDB será acessível somente por meio dos pontos de extremidade privados. Se você não configurar o tráfego público ou um ponto de extremidade de serviço, depois que todos os pontos de extremidades particulares aprovados forem rejeitados ou excluídos, nenhum tráfego poderá acessar o banco de dados do Azure para MariaDB.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os recursos de segurança do banco de dados do Azure para MariaDB, consulte os seguintes artigos:

* Para configurar um firewall para o banco de dados do Azure para MariaDB, consulte [suporte a firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules).

* Para saber como configurar um ponto de extremidade de serviço de rede virtual para o banco de dados do Azure para MariaDB, consulte [Configurar o acesso de redes virtuais](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet).

* Para obter uma visão geral do banco de dados do Azure para conectividade MariaDB, consulte [arquitetura de conectividade do banco de dados do Azure para MariaDB](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture)

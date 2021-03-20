---
title: Link Privado - Base de Dados Azure para MySQL
description: Saiba como funciona o link privado para a Azure Database para o MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 1ae35d1ac35dacfab2690980d57973dce050382b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242862"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Link privado para base de dados Azure para MySQL

A Ligação Privada permite-lhe estabelecer ligação a vários serviços PaaS no Azure através de um ponto final privado. O Azure Private Link essencialmente traz os serviços do Azure dentro da sua Rede Privada Virtual (VNet). Os recursos de PaaS podem ser acedidos através do endereço IP privado, como qualquer outro recurso na VNet.

Para obter uma lista de serviços PaaS que suportem a funcionalidade Private Link, reveja a [documentação](../private-link/index.yml)private Link . Um ponto final privado é um endereço IP privado dentro de um [VNet](../virtual-network/virtual-networks-overview.md) e Subnet específicos.

> [!NOTE]
> A funcionalidade de ligação privada só está disponível para a Base de Dados Azure para servidores MySQL nos níveis de preços otimizados para fins gerais ou memória. Certifique-se de que o servidor de base de dados está num destes níveis de preços.

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Prevenção da transferência de dados não autorizada)

A ex-filtração de dados na Base de Dados Azure para o MySQL é quando um utilizador autorizado, como um administrador de base de dados, é capaz de extrair dados de um sistema e movê-lo para outro local ou sistema fora da organização. Por exemplo, o utilizador desloca os dados para uma conta de armazenamento detida por terceiros.

Considere um cenário com um utilizador a executar a workbench MySQL dentro de uma Máquina Virtual Azure (VM) que está a ligar-se a uma Base de Dados Azure para servidor MySQL a provisionada nos EUA Ocidentais. O exemplo abaixo mostra como limitar o acesso com pontos finais públicos na Base de Dados Azure para o MySQL utilizando controlos de acesso à rede.

* Desative todo o tráfego de serviço Azure para Azure Database for MySQL através do ponto final público, definindo *Allow Azure Services* to OFF. Certifique-se de que nenhum endereço ou gama IP pode aceder ao servidor através de [regras de firewall](./concepts-firewall-rules.md) ou [de pontos finais de serviço de rede virtual](./concepts-data-access-and-security-vnet.md).

* Apenas permita o tráfego para a Base de Dados Azure para o MySQL utilizando o endereço IP privado do VM. Para obter mais informações, consulte os artigos sobre as regras [de Service Endpoint](concepts-data-access-and-security-vnet.md) e [VNet firewall](howto-manage-vnet-using-portal.md).

* No Azure VM, reduza o âmbito de ligação de saída utilizando grupos de segurança de rede (NSGs) e tags de serviço da seguinte forma

    * Especifique uma regra NSG para permitir o tráfego para *tag de serviço = SQL. WestUs* - apenas permitindo a ligação à Base de Dados Azure para o MySQL nos EUA
    * Especifique uma regra NSG (com uma prioridade maior) para negar o tráfego para *Tag de Serviço = SQL* - negando ligações à Atualização da Base de Dados de Azure para o MySQL em todas as regiões</br></br>

No final desta configuração, o Azure VM pode ligar-se apenas à Base de Dados Azure para o MySQL na região oeste dos EUA. No entanto, a conectividade não se restringe a uma única Base de Dados Azure para o MySQL. O VM ainda pode ligar-se a qualquer Base de Dados Azure para o MySQL na região oeste dos EUA, incluindo as bases de dados que não fazem parte da subscrição. Embora tenhamos reduzido o âmbito da exfiltração de dados no cenário acima para uma região específica, não o eliminámos completamente.</br>

Com o Private Link, pode agora configurar controlos de acesso à rede como NSGs para restringir o acesso ao ponto final privado. Os recursos individuais do Azure PaaS são então mapeados para pontos finais privados específicos. Um insider malicioso só pode aceder ao recurso PaaS mapeado (por exemplo, uma Base de Dados Azure para o MySQL) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade no local em peering privado

Quando se conecta ao ponto final público a partir de máquinas no local, o seu endereço IP precisa de ser adicionado à firewall baseada em IP utilizando uma regra de firewall ao nível do servidor. Embora este modelo funcione bem para permitir o acesso a máquinas individuais para dev ou testar cargas de trabalho, é difícil de gerir em ambiente de produção.

Com o Private Link, pode permitir o acesso transversal ao ponto final privado utilizando a [Via Expresso](https://azure.microsoft.com/services/expressroute/) (ER), o perspitamento privado ou [o túnel VPN.](../vpn-gateway/index.yml) Podem posteriormente desativar todos os acessos através do ponto final público e não utilizar a firewall baseada em IP.

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para o MySQL e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.DBforMySQL** registado. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Configure Link Privado para Base de Dados Azure para MySQL

### <a name="creation-process"></a>Processo de Criação

Os pontos finais privados são necessários para ativar o Link Privado. Isto pode ser feito usando os seguintes guias de como fazer.

* [Portal do Azure](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Processo de aprovação
Uma vez que o administrador de rede cria o ponto final privado (PE), o administrador MySQL pode gerir a ligação de ponto final privado (PEC) para Azure Database para o MySQL. Esta separação de direitos entre a administração da rede e a DBA é útil para a gestão da Base de Dados Azure para a conectividade MySQL. 

* Navegue para a Base de Dados Azure para o recurso do servidor MySQL no portal Azure. 
    * Selecione as ligações de ponto final privado no painel esquerdo
    * Mostra uma lista de todas as ligações privadas de ponto final (PECs)
    * Ponto final privado correspondente (PE) criado

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="selecione o portal de ponto final privado":::

* Selecione um PEC individual da lista selecionando-o.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="selecione o ponto final privado pendente de aprovação":::

* O administrador do servidor MySQL pode optar por aprovar ou rejeitar um PEC e opcionalmente adicionar uma resposta de texto curta.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="selecione a mensagem de ponto final privado":::

* Após aprovação ou rejeição, a lista refletirá o estado apropriado juntamente com o texto de resposta

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="selecionar o estado final do ponto final privado":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Utilize casos de Link Privado para Base de Dados Azure para o MySQL

Os clientes podem ligar-se ao ponto final privado a partir do mesmo VNet, vNet esprevado na mesma região, ou através da ligação VNet-to-VNet entre regiões. Além disso, os clientes podem conectar-se a partir de instalações usando expressRoute, peering privado ou túneis VPN. Abaixo está um diagrama simplificado mostrando os casos de uso comum.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="selecione a visão geral do ponto final privado":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Ligação a partir de um Azure VM em Rede Virtual Peered (VNet)
Configure [VNet olhando](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer conectividade com a Base de Dados Azure para o MySQL a partir de um VM Azure em um VNet esprevado.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Ligação a partir de um VM Azure em ambiente VNet-to-VNet
Configure [a ligação de gateway VNet-to-VNet VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer conectividade com uma Base de Dados Azure para o MySQL a partir de um VM Azure numa região ou subscrição diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Ligação a partir de um ambiente no local sobre VPN
Para estabelecer conectividade de um ambiente no local até à Base de Dados Azure para o MySQL, escolha e implemente uma das opções:

* [Ligação ponto-a-local](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Ligação VPN Site a Site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Ligação privada combinada com regras de firewall

As seguintes situações e resultados são possíveis quando utiliza o Private Link em combinação com as regras de firewall:

* Se não configurar quaisquer regras de firewall, então, por padrão, nenhum tráfego será capaz de aceder à Base de Dados Azure para o MySQL.

* Se configurar o tráfego público ou um ponto final de serviço e criar pontos finais privados, então diferentes tipos de tráfego de entrada são autorizados pela regra de firewall correspondente.

* Se não configurar qualquer ponto final de tráfego público ou serviço e criar pontos finais privados, então a Base de Dados Azure para o MySQL só está acessível através dos pontos finais privados. Se não configurar o tráfego público ou um ponto final de serviço, depois de todos os pontos finais privados aprovados serem rejeitados ou eliminados, nenhum tráfego poderá aceder à Base de Dados Azure para o MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Negar acesso público à Base de Dados Azure para o MySQL

Se pretender confiar apenas em pontos finais privados para aceder à sua Base de Dados Azure para o MySQL, pode desativar a definição de todos os pontos finais públicos (isto é, [regras de firewall](concepts-firewall-rules.md) e [pontos finais do serviço VNet)](concepts-data-access-and-security-vnet.md)definindo a configuração **de Acesso à Rede Pública de Negação** no servidor de base de dados. 

Quando esta definição é definida como *SIM,* apenas as ligações através de pontos finais privados são permitidas na sua Base de Dados Azure para o MySQL. Quando esta definição estiver definida para *NO,* os clientes podem ligar-se à sua Base de Dados Azure para o MySQL com base nas definições do seu ponto final de firewall ou de serviço VNet. Além disso, uma vez definido o valor do acesso à rede privada, os clientes não podem adicionar e/ou atualizar as "regras de firewall" e 'regras de ponto final de serviço VNet'.

> [!Note]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para PostgreSQL - Servidor único suporta os níveis de preços otimizados para fins gerais e memória.
>
> Esta definição não tem qualquer impacto nas configurações SSL e TLS para a sua Base de Dados Azure para o MySQL.

Para saber como configurar o Acesso à **Rede Pública de Negação** para a sua Base de Dados Azure para o MySQL a partir do portal Azure, consulte como configurar o [Acesso à Rede Pública de Negação](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Base de Dados Azure para funcionalidades de segurança MySQL, consulte os seguintes artigos:

* Para configurar uma firewall para Azure Database para mySQL, consulte [o suporte de Firewall](./concepts-firewall-rules.md).

* Para aprender a configurar um ponto final de serviço de rede virtual para a sua Base de Dados Azure para o MySQL, consulte [o acesso configurar a partir de redes virtuais.](./concepts-data-access-and-security-vnet.md)

* Para obter uma visão geral da Base de Dados Azure para a conectividade MySQL, consulte [a Base de Dados Azure para a Arquitetura de Conectividade MySQL](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
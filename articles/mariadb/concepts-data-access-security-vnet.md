---
title: Descrição geral do ponto de extremidade dos serviços de base de dados do Azure para MariaDB servidor VNet | Documentos da Microsoft
description: Descreve como funcionam os pontos finais de serviço de VNet para a base de dados do Azure para MariaDB server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: fdf2d3aeea32beba0b8e95c1816a80140d7cf6be
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958920"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Utilizar pontos finais de serviço de rede Virtual e regras para a base de dados do Azure para MariaDB

*Regras de rede virtual* são um recurso de segurança de firewall que controla se a sua base de dados do Azure para MariaDB server aceita comunicações que são enviadas as sub-redes específicas nas redes virtuais. Este artigo explica por que o recurso de regra de rede virtual, às vezes, é sua melhor opção para permitir com segurança a comunicação para a base de dados do Azure para MariaDB server.

Para criar uma regra de rede virtual, tem primeiro de existir uma [rede virtual] [ vm-virtual-network-overview] (VNet) e um [ponto final de serviço de rede virtual] [ vm-virtual-network-service-endpoints-overview-649d] para o regra para referência. A imagem seguinte ilustra como um ponto de extremidade do serviço de rede Virtual funciona com a base de dados do Azure para MariaDB:

![Exemplo de como funciona um ponto de extremidade do serviço de VNet](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure em que a base de dados do Azure para MariaDB é implementada para os servidores de fins gerais e memória otimizada.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Pode ter redes virtuais associadas à subscrição do Azure.

**Sub-rede:** Contém uma rede virtual **sub-redes**. Quaisquer máquinas virtuais (VMs) do Azure que tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Computação de nós que estão fora da sua rede virtual não é possível aceder à sua rede virtual, a menos que configura a sua segurança para permitir o acesso.

**Endpoint de serviço de rede virtual:** R [ponto final de serviço de rede Virtual] [ vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo de serviço do Azure formal. Neste artigo estamos interessados em nome do tipo de **Microsoft. SQL**, que faz referência ao serviço do Azure com o nome da base de dados SQL. Esta etiqueta de serviço também se aplica à base de dados do Azure para serviços MariaDB, MySQL e PostgreSQL. É importante ter em conta ao aplicar a **Microsoft. SQL** etiqueta de serviço para um ponto de extremidade do serviço de VNet-irá configurar o tráfego de ponto final de serviço para todos os SQL Database do Azure, base de dados do Azure para MariaDB, base de dados do Azure para MySQL e o Azure Base de dados para servidores PostgreSQL na sub-rede.

**Regra de rede virtual:** Uma regra de rede virtual para a base de dados do Azure para MariaDB server é uma sub-rede que está listada na lista de controlo de acesso (ACL) de sua base de dados do Azure para MariaDB server. Para ser na ACL da base de dados do Azure para MariaDB server, a sub-rede tem de conter o **Microsoft. SQL** nome do tipo.

Uma regra de rede virtual informa ao seu banco de dados do Azure para MariaDB servidor para aceitar comunicações de cada nó que está na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que o faça, as VMs nas suas sub-redes não consegue comunicar com a base de dados do Azure para MariaDB server. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A lógica para escolher a abordagem de regra de VNet requer uma discussão de comparação e contraste que envolvem as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>R. Permitir acesso aos serviços do Azure

O painel de segurança de ligação tem um **ON/OFF** botão assinalada como **permitir o acesso aos serviços do Azure**. O **ON** definição permite comunicações a partir de todos os endereços IP do Azure e todas as sub-redes do Azure. Estes IPs do Azure ou a sub-redes não podem ser propriedade por si. Isso **ON** definição é provavelmente mais aberta que pretende que a base de dados do Azure para MariaDB base de dados ser. O recurso de regra de rede virtual oferece muito controle mais granular.

### <a name="b-ip-rules"></a>B. Regras de IP

A base de dados do Azure para MariaDB firewall permite que especifique os intervalos de endereços IP do qual comunicações são aceites no banco de dados do Azure para MariaDB server. Esta abordagem é adequada para os endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos de nós dentro da rede privada do Azure estão configurados com *dinâmica* endereços IP. Endereços IP dinâmicos podem ser alteradas, por exemplo, quando a sua VM é reiniciada. Seria ilusório para especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode recuperá-los a opção de IP, obtendo uma *estático* endereço IP para a sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual com o portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem IP estática pode se tornar difíceis de gerenciar, e é dispendioso, quando Feito em escala. Regras de rede virtual são mais fáceis de estabelecer e gerir.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Não é possível ainda tem a base de dados do Azure para MariaDB numa sub-rede sem definir um ponto de extremidade de serviço

Se sua **Microsoft. SQL** server era um nó numa sub-rede na sua rede virtual, todos os nós na rede virtual foi possível comunicar com a base de dados do Azure para MariaDB server. Neste caso, as suas VMs foi possível comunicar com a base de dados do Azure para MariaDB sem a necessidade de quaisquer regras IP ou regras de rede virtual.

No entanto a partir de Agosto de 2018, a base de dados do Azure para MariaDB serviço ainda não está entre os serviços que podem ser atribuídos diretamente a uma sub-rede.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade do serviço de rede Virtual aplica-se apenas numa região do Azure. O ponto final não ativa noutras regiões aceitar comunicações de sub-rede.

Qualquer regra de rede virtual é limitada à região que o seu ponto de extremidade subjacente aplica-se a.

### <a name="server-level-not-database-level"></a>Ao nível do servidor, não ao nível da base de dados

Cada regra de rede virtual aplica-se à base de dados inteira do Azure para MariaDB server, não apenas para um determinado banco de dados no servidor. Em outras palavras, a regra de rede virtual aplica-se no nível do servidor, não ao nível da base de dados.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação de funções de segurança na administração de pontos finais de serviço de rede Virtual. É necessária ação da cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; Ative o ponto final.
- **Administrador da base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a sub-rede especificada para a base de dados do Azure para MariaDB server.

*Alternativa RBAC:*

As funções de administrador de rede e o administrador de banco de dados têm mais recursos do que são necessárias para gerir as regras de rede virtual. É necessário apenas um subconjunto de seus recursos.

Tem a opção de usar [controlo de acesso baseado em funções (RBAC)] [ rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto de capacidades necessário. A função personalizada poderia ser usada em vez de envolvendo o administrador de rede ou o administrador da base de dados. A área de superfície de sua exposição de segurança é mais baixa de se adicionar um utilizador a uma função personalizada, em vez de adicionar o utilizador para as outras duas funções de administrador principais.

> [!NOTE]
> Em alguns casos, a base de dados do Azure para MariaDB e a sub-rede de VNet estão em subscrições diferentes. Nestes casos, deve verificar as seguintes configurações:
> - Ambas as subscrições têm de estar no mesmo inquilino do Azure Active Directory.
> - O utilizador tem as permissões necessárias para iniciar as operações, como ativar os pontos finais de serviço e adicionar uma sub-rede de VNet para o servidor especificado.

## <a name="limitations"></a>Limitações

Base de dados do Azure para MariaDB, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Na firewall da base de dados do Azure para MariaDB, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas tem de estar alojadas na mesma região geográfica que aloja a base de dados do Azure para MariaDB.

- Cada base de dados do Azure para MariaDB server pode ter até 128 entradas ACL para qualquer determinada rede virtual.

- Regras de rede virtual são aplicadas apenas a redes virtuais do Azure Resource Manager; e não à [modelo de implementação clássica] [ resource-manager-deployment-model-568f] redes.

- Diante de ativar a rede virtual pontos finais de serviço à base de dados do Azure para MariaDB utilizando o **Microsoft. SQL** etiqueta de serviço também permite que os pontos finais para todos os serviços de base de dados do Azure: Base de dados do Azure para MariaDB, base de dados do Azure para MySQL, base de dados do Azure para PostgreSQL, base de dados SQL do Azure e Azure SQL Data Warehouse.

- Suporte para pontos finais de serviço da VNet é apenas para fins gerais e memória otimizada de servidores.

- Na firewall, intervalos de endereços IP que se aplicam para os seguintes itens de rede, mas as regras de rede virtual não:
    - [Rede privada virtual (VPN) site a Site (S2S)][vpn-gateway-indexmd-608y]
    - No local através de [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se a sua rede está ligada à rede do Azure através da utilização dos [ExpressRoute][expressroute-indexmd-744v], cada circuito está configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são utilizados para ligar ao Microsoft Services, como e o armazenamento do Azure, utilizando o Peering público do Azure.

Para permitir a comunicação do seu circuito a base de dados do Azure para MariaDB, tem de criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, abra um pedido de suporte com o ExpressRoute com o portal do Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra de Firewall da VNET ao seu servidor sem ativar na VNET pontos finais de serviço

Simplesmente definir uma regra de Firewall não ajudam a proteger o servidor para a VNet. Também tem de ativar pontos finais de serviço de VNet **no** para garantir a segurança entrem em vigor. Quando ativar pontos finais de serviço **nos**, a sub-rede da VNet ocorrer no tempo de inatividade até concluir a transição do **desativar** para **no**. Isso é especialmente verdadeiro no contexto de VNets grandes. Pode utilizar o **IgnoreMissingServiceEndpoint** sinalizador para reduzir ou eliminar o período de indisponibilidade durante a transição.

Pode definir o **IgnoreMissingServiceEndpoint** sinalizador com o CLI do Azure ou o portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais do Azure][vm-virtual-network-overview]
- [Pontos finais de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Passos Seguintes
Para artigos sobre a criação de regras de VNet, veja:
- [Criar e gerir a base de dados do Azure para as regras de VNet de MariaDB com o portal do Azure](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

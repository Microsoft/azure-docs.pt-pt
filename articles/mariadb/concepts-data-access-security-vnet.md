---
title: Pontos finais do serviço VNet - Base de Dados Azure para MariaDB
description: Descreve como os pontos finais do serviço VNet funcionam para a sua Base de Dados Azure para o servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 54379c65850fa210e5523b53a64fe89705ed1f15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532115"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Utilizar regras e pontos finais de serviço da Rede Virtual para o Azure Database for MariaDB

*As regras* de rede virtual são uma característica de segurança de firewall que controla se a base de dados Azure para o servidor MariaDB aceita comunicações que são enviadas de determinadas subredes em redes virtuais. Este artigo explica porque é que a funcionalidade de regra da rede virtual é, por vezes, a sua melhor opção para permitir a comunicação segura à sua Base de Dados Azure para o servidor MariaDB.

Para criar uma regra de rede virtual, deve primeiro existir uma [rede virtual][vm-virtual-network-overview] (VNet) e um ponto final de serviço de [rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra de referência. A imagem seguinte ilustra como um ponto final de serviço de rede virtual funciona com a Base de Dados Azure para MariaDB:

![Exemplo de como funciona um Endpoint de Serviço VNet](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para MariaDB está implantada para servidores otimizados para Fins Gerais e Memória.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Pode ter redes virtuais associadas à sua subscrição Azure.

**Subnet:** Uma rede virtual contém **subredes.** Quaisquer máquinas virtuais Azure (VMs) que tenha sacado a subredes. Uma subnet pode conter vários VMs ou outros nódosos de computação. Os nódosos computacionais que estão fora da sua rede virtual não podem aceder à sua rede virtual a menos que configure a sua segurança para permitir o acesso.

Ponto final do serviço de **rede virtual:** Um ponto final de [serviço de Rede Virtual][vm-virtual-network-service-endpoints-overview-649d] é uma subnet cujos valores de propriedade incluem um ou mais nomes formais do tipo de serviço Azure. Neste artigo estamos interessados no nome tipo **microsoft.Sql**, que se refere ao serviço Azure chamado SQL Database. Esta etiqueta de serviço também se aplica à Base de Dados Azure para serviços MariaDB, MySQL e PostgreSQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** a um ponto final do serviço VNet irá configurar o tráfego final do serviço para toda a Base de Dados Azure SQL, Base de Dados Azure para MariaDB, Base de Dados Azure para MySQL e Base de Dados Azure para servidores PostgreSQL na subnet.

**Regra da rede virtual:** Uma regra de rede virtual para o seu servidor De base de dados Azure para o servidor MariaDB é uma subnet listada na lista de controlo de acesso (ACL) da sua Base de Dados Azure para servidor MariaDB. Para estar na ACL para a sua Base de Dados Azure para o servidor MariaDB, a sub-rede deve conter o nome do tipo **Microsoft.Sql.**

Uma regra de rede virtual diz ao seu Servidor Azure para o servidor MariaDB aceitar comunicações de todos os nós que estão na subnet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que tome medidas, os VMs nas suas subredes não podem comunicar com a sua Base de Dados Azure para o servidor MariaDB. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A razão para escolher a abordagem da regra VNet requer uma discussão de comparação e contraste envolvendo as opções de segurança concorrentes oferecidas pela firewall.

### <a name="a-allow-access-to-azure-services"></a>R. Permitir acesso aos serviços do Azure

O painel de segurança de ligação tem um botão **ON/OFF** que está rotulado **Permitir o acesso aos serviços Azure**. A definição **ON** permite comunicações de todos os endereços IP Azure e todas as subredes Azure. Estes IPs ou subredes Azure podem não ser propriedade de si. Esta definição **ON** é provavelmente mais aberta do que deseja que a sua Base de Dados Azure para a Base de Dados MariaDB seja. A função de regra da rede virtual oferece um controlo granular muito mais fino.

### <a name="b-ip-rules"></a>B. Regras ip

A base de dados Azure para firewall MariaDB permite especificar intervalos de endereçoIP a partir dos quais as comunicações são aceites na Base de Dados Azure para servidor MariaDB. Esta abordagem é boa para endereços IP estáveis que estão fora da rede privada Azure. Mas muitos nódosos dentro da rede privada Azure estão configurados com endereços IP *dinâmicos.* Os endereços IP dinâmicos podem mudar, como quando o vM é reiniciado. Seria uma loucura especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode salvar a opção IP obtendo um endereço IP *estático* para o seu VM. Para mais detalhes, consulte os [endereços IP privados para uma máquina virtual utilizando o portal Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem ip estática pode tornar-se difícil de gerir, e é dispendiosa quando feita em escala. As regras de rede virtual são mais fáceis de estabelecer e gerir.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Ainda não é possível ter base de dados Azure para MariaDB numa subnet sem definir um ponto final de serviço

Se o seu servidor **Microsoft.Sql** fosse um nó numa subnet na sua rede virtual, todos os nós dentro da rede virtual poderiam comunicar com a sua Base de Dados Azure para servidor MariaDB. Neste caso, os seus VMs poderiam comunicar com a Base de Dados Azure para o MariaDB sem precisar de quaisquer regras de rede virtual ou regras ip.

No entanto, a partir de agosto de 2018, a Base de Dados Azure para o serviço MariaDB ainda não está entre os serviços que podem ser atribuídos diretamente a uma subnet.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta secção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final do serviço de Rede Virtual aplica-se a apenas uma região azure. O objetivo final não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra de rede virtual limita-se à região a que o seu ponto final subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de base de dados

Cada regra de rede virtual aplica-se a toda a sua Base de Dados Azure para servidor MariaDB, e não apenas a uma determinada base de dados no servidor. Por outras palavras, a regra da rede virtual aplica-se ao nível do servidor, e não ao nível da base de dados.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação das funções de segurança na administração de pontos finais de serviço da Rede Virtual. São necessárias ações de cada uma das seguintes funções:

- **Administração da rede:** &nbsp; Ligue o ponto final.
- **Administração da base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a subnet dada à Base de Dados Azure para o servidor MariaDB.

*Alternativa RBAC:*

Os papéis da Administração de Rede e da Base de Dados Admin têm mais capacidades do que as necessárias para gerir as regras de rede virtuais. Só é necessário um subconjunto das suas capacidades.

Tem a opção de utilizar o controlo de [acesso baseado em funções (RBAC)][rbac-what-is-813s] em Azure para criar uma única função personalizada que tenha apenas o subconjunto necessário de capacidades. O papel personalizado poderia ser usado em vez de envolver o Administrador da Rede ou o Administrador de Base de Dados. A área de superfície da sua exposição à segurança é menor se adicionar um utilizador a uma função personalizada, versus adicionar o utilizador às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para MariaDB e a VNet-subnet estão em diferentes subscrições. Nestes casos deve assegurar as seguintes configurações:
> - Ambas as subscrições devem estar no mesmo inquilino do Azure Ative Directory.
> - O utilizador possui as permissões necessárias para iniciar operações, tais como permitir pontos finais de serviço e adicionar uma sub-rede VNet ao servidor dado.
> - Certifique-se de que ambas as subscrições têm o fornecedor de recursos **Microsoft.Sql** registado. Para mais informações consulte o [registo de recursos-gestor][resource-manager-portal]

## <a name="limitations"></a>Limitações

Para a Base de Dados Azure para MariaDB, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Uma Aplicação Web pode ser mapeada para um IP privado numa VNet/subnet. Mesmo que os pontos finais do serviço sejam ligados a partir da dada VNet/subnet, as ligações da Web App para o servidor terão uma fonte IP pública Azure, e não uma fonte VNet/sub-rede. Para permitir a conectividade de uma Web App a um servidor que tenha regras de firewall VNet, deve permitir que os serviços Azure acedam ao servidor no servidor.

- Na firewall para a sua Base de Dados Azure para MariaDB, cada regra de rede virtual refere uma subnet. Todas estas subredes referenciadas devem ser alojadas na mesma região geográfica que acolhe a Base de Dados Azure para o MariaDB.

- Cada Base de Dados Azure para servidor MariaDB pode ter até 128 entradas ACL para qualquer rede virtual.

- As regras de rede virtual aplicam-se apenas às redes virtuais do Gestor de Recursos Do Azure; e não para redes de modelos de [implantação clássicas.][resource-manager-deployment-model-568f]

- Ligar os pontos finais do serviço de rede virtual para a Base de Dados Azure para O DNDb utilizando a etiqueta de serviço **Microsoft.Sql** também permite os pontos finais de todos os serviços de Base de Dados Azure: Base de Dados Azure para MariaDB, Base de Dados Azure para PostgreSQL, Base de Dados Azure SQL e Armazém de Dados Azure SQL.

- O suporte para os pontos finais do serviço VNet destina-se apenas a servidores otimizados para fins gerais e memória.

- Na firewall, as gamas de endereços IP aplicam-se aos seguintes itens de rede, mas as regras de rede virtuais não:
    - [Rede privada virtual site-to-site (S2S) (VPN)][vpn-gateway-indexmd-608y]
    - No local via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se a sua rede estiver ligada à rede Azure através da utilização do [ExpressRoute,][expressroute-indexmd-744v]cada circuito está configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são usados para ligar aos Serviços microsoft, como o Azure Storage, utilizando o Azure Public Peering.

Para permitir a comunicação do seu circuito à Base de Dados Azure para O MariaDB, tem de criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para encontrar os endereços IP públicos do seu circuito ExpressRoute, abra um bilhete de apoio com a ExpressRoute utilizando o portal Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra de Firewall VNET ao seu servidor sem ligar pontos finais do serviço VNET

Apenas definir uma regra de Firewall não ajuda a fixar o servidor no VNet. Também deve ligar **os** pontos finais do serviço VNet para que a segurança entre em vigor. Quando ligar os pontos finais do **serviço,** a sua subnet VNet experimenta tempo de inatividade até completar a transição de **Off** para **On**. Isto é especialmente verdade no contexto dos grandes VNets. Pode utilizar a bandeira **IgnoreMissingServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Pode definir a bandeira **IgnoreMissingServiceEndpoint** utilizando o Azure CLI ou portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais Azure][vm-virtual-network-overview]
- [Pontos finais do serviço de rede virtual Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras VNet, consulte:
- [Crie e gerea Base de Dados Azure para as regras Da VNet MariaDB utilizando o portal Azure](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

---
title: Pontos finais do serviço VNet - Base de Dados Azure para o MySQL
description: Descreve como os pontos finais do serviço VNet funcionam para o seu Azure Database para o servidor MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: 364d9c20c0bfce1f31103ddf6df0c8d7b228eca2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461784"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mysql"></a>Utilizar os pontos finais de serviço e regras de Rede Virtual para Base de Dados do Azure para MySQL

*As regras de rede virtual* são uma funcionalidade de segurança de firewall que controla se a sua Base de Dados Azure para o servidor MySQL aceita comunicações que são enviadas a partir de sub-redes específicas em redes virtuais. Este artigo explica porque é que a funcionalidade de regra de rede virtual é, por vezes, a sua melhor opção para permitir a comunicação com a sua Base de Dados Azure para o servidor MySQL.

Para criar uma regra de rede virtual, deve primeiro existir uma [rede virtual][vm-virtual-network-overview] (VNet) e um ponto final de serviço de [rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a referência. A imagem a seguir ilustra como um ponto final de serviço de Rede Virtual funciona com a Base de Dados Azure para o MySQL:

:::image type="content" source="media/concepts-data-access-and-security-vnet/vnet-concept.png" alt-text="Exemplo de como funciona um VNet Service Endpoint":::

> [!NOTE]
> Esta funcionalidade encontra-se disponível em todas as regiões do Azure onde a Base de Dados Azure para o MySQL é implantada para servidores otimizados para fins gerais e memória.
> Em caso de observação de VNet, se o tráfego estiver a fluir através de um VNet Gateway comum com pontos finais de serviço e for suposto fluir para o par, por favor crie uma regra ACL/VNet para permitir que as Máquinas Virtuais Azure no Gateway VNet acedam à Base de Dados Azure para o servidor MySQL.

Também pode considerar a utilização [de Link Privado](concepts-data-access-security-private-link.md) para ligações. O Private Link fornece um endereço IP privado no seu VNet para a Base de Dados Azure para o servidor MySQL.

<a name="anch-terminology-and-description-82f"></a>

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Pode ter redes virtuais associadas à sua subscrição Azure.

**Sub-rede:** Uma rede virtual contém **sub-redes.** Quaisquer máquinas virtuais Azure (VMs) que tenha estão atribuídas a sub-redes. Uma sub-rede pode conter vários VMs ou outros nós computacional. Os nós computacional que estão fora da sua rede virtual não podem aceder à sua rede virtual a menos que configuure a sua segurança para permitir o acesso.

**Ponto final do serviço de rede virtual:** Um [ponto final de serviço de Rede Virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço Azure. Neste artigo estamos interessados no nome-tipo do **Microsoft.Sql,** que se refere ao serviço Azure denominado SQL Database. Esta etiqueta de serviço também se aplica à Base de Dados Azure para os serviços MySQL e PostgreSQL. É importante notar que ao aplicar a etiqueta de serviço **Microsoft.Sql** num ponto final de serviço VNet irá configurar o tráfego de ponto final de serviço para toda a Base de Dados Azure SQL, Base de Dados Azure para MySQL e Base de Dados Azure para servidores PostgreSQL na sub-rede. 

**Regra de rede virtual:** Uma regra de rede virtual para o seu servidor Azure Database for MySQL é uma sub-rede listada na lista de controlo de acesso (ACL) do seu servidor Azure Database for MySQL. Para estar no ACL para o seu servidor Azure Database para o servidor MySQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql.**

Uma regra de rede virtual diz ao seu Azure Database para o servidor MySQL aceitar comunicações de todos os nó que estiverem na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até tomar medidas, os VMs nas suas sub-redes não podem comunicar com a sua Base de Dados Azure para o servidor MySQL. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A razão para escolher a abordagem da regra VNet requer uma discussão de comparação e contraste envolvendo as opções de segurança concorrentes oferecidas pela firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Permitir acesso aos serviços do Azure

O painel de segurança Connection tem um botão **ON/OFF** com a etiqueta **Permitir o acesso aos serviços Azure**. A definição **ON** permite comunicações a partir de todos os endereços Azure IP e de todas as sub-redes Azure. Estes IPs ou sub-redes Azure podem não ser propriedade de si. Esta definição **ON** é provavelmente mais aberta do que pretende que a sua Base de Dados Azure para a Base de Dados MySQL seja. A funcionalidade de regra de rede virtual oferece um controlo granular muito mais fino.

### <a name="b-ip-rules"></a>B. Regras de IP

A Base de Dados Azure para firewall MySQL permite especificar as gamas de endereços IP a partir das quais as comunicações são aceites na Base de Dados Azure para a Base de Dados MySQL. Esta abordagem é boa para endereços IP estáveis que estão fora da rede privada Azure. Mas muitos nós dentro da rede privada Azure estão configurados com endereços IP *dinâmicos.* Os endereços IP dinâmicos podem mudar, como quando o seu VM é reiniciado. Seria uma loucura especificar um endereço IP dinâmico numa regra de firewall, num ambiente de produção.

Pode salvar a opção IP obtendo um endereço IP *estático* para o seu VM. Para mais informações, consulte [endereços IP privados configurar uma máquina virtual utilizando o portal Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem ip estática pode tornar-se difícil de gerir, e é dispendiosa quando feita em escala. As regras de rede virtual são mais fáceis de estabelecer e gerir.

<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta secção descreve vários detalhes sobre as regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto final de serviço da Rede Virtual aplica-se apenas a uma região de Azure. O ponto final não permite que outras regiões aceitem a comunicação a partir da sub-rede.

Qualquer regra de rede virtual limita-se à região a que se aplica o seu ponto final subjacente.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de base de dados

Cada regra de rede virtual aplica-se a toda a sua Base de Dados Azure para o servidor MySQL, e não apenas a uma base de dados específica no servidor. Por outras palavras, a regra da rede virtual aplica-se ao nível do servidor e não ao nível da base de dados.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Existe uma separação de funções de segurança na administração dos pontos finais de serviço de Rede Virtual. São necessárias medidas de cada uma das seguintes funções:

- **Administração de rede:** &nbsp; Ligue o ponto final.
- **Administração de base de dados:** &nbsp; Atualize a lista de controlo de acesso (ACL) para adicionar a sub-rede dada à base de dados Azure para o servidor MySQL.

*Alternativa Azure RBAC:*

Os papéis de Administração de Rede e Administração de Bases de Dados têm mais capacidades do que as necessárias para gerir as regras de rede virtuais. Apenas um subconjunto das suas capacidades é necessário.

Você tem a opção de usar o [controle de acesso baseado em funções Azure (Azure RBAC)][rbac-what-is-813s] em Azure para criar um único papel personalizado que tem apenas o subconjunto necessário de capacidades. O papel personalizado poderia ser usado em vez de envolver o Administrador de Rede ou o Administrador de Base de Dados. A área de superfície da sua exposição à segurança é menor se adicionar um utilizador a uma função personalizada, em vez de adicionar o utilizador às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, a Base de Dados Azure para o MySQL e a sub-rede VNet estão em diferentes subscrições. Nestes casos, deve assegurar as seguintes configurações:
> - Ambas as subscrições devem estar no mesmo inquilino do Azure Ative Directory.
> - O utilizador tem as permissões necessárias para iniciar operações, tais como ativar pontos finais de serviço e adicionar uma sub-rede VNet ao servidor dado.
> - Certifique-se de que ambos os fornecedores de recursos **Microsoft.Sql** e **Microsoft.DBforMySQL** estão registados. Para mais informações, consulte [o gestor de recursos-registo][resource-manager-portal]

## <a name="limitations"></a>Limitações

Para a base de dados Azure para o MySQL, a funcionalidade de regras de rede virtual tem as seguintes limitações:

- Uma Aplicação Web pode ser mapeada para um IP privado numa VNet/sub-rede. Mesmo que os pontos finais de serviço sejam ligados a partir da determinada VNet/sub-rede, as ligações da Web App para o servidor terão uma fonte IP pública Azure, e não uma fonte VNet/sub-rede. Para ativar a conectividade de uma Web App para um servidor que tenha regras de firewall VNet, tem de permitir que os serviços do Azure acedam ao servidor no servidor.

- Na firewall da sua Base de Dados Azure para o MySQL, cada regra de rede virtual faz referência a uma sub-rede. Todas estas sub-redes referenciadas devem ser alojadas na mesma região geográfica que acolhe a Base de Dados Azure para o MySQL.

- Cada Base de Dados Azure para servidor MySQL pode ter até 128 entradas ACL para qualquer rede virtual.

- As regras de rede virtuais aplicam-se apenas às redes virtuais do Azure Resource Manager; e não para redes [de modelos de implantação clássicas.][arm-deployment-model-568f]

- Ligar os pontos finais do serviço de rede virtual ON para Azure Database para o MySQL utilizando a tag de serviço **Microsoft.Sql** também permite os pontos finais para todos os serviços de Base de Dados Azure: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database e Azure Synapse Analytics.

- O suporte para os pontos finais do serviço VNet é apenas para servidores otimizados para fins gerais e memória.

- Se **o Microsoft.Sql** estiver ativado numa sub-rede, indica que só pretende utilizar as regras VNet para se ligar. [As regras de recursos de firewall não-VNet](concepts-firewall-rules.md) nessa sub-rede não funcionarão.

- Na firewall, as gamas de endereços IP aplicam-se aos seguintes itens de rede, mas as regras de rede virtuais não:
    - [Rede privada virtual local-a-local (S2S) (VPN)][vpn-gateway-indexmd-608y]
    - No local via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se a sua rede estiver ligada à rede Azure através da utilização do [ExpressRoute,][expressroute-indexmd-744v]cada circuito está configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são utilizados para se conectarem aos Serviços microsoft, como o Azure Storage, utilizando o Azure Public Peering.

Para permitir a comunicação do seu circuito para Azure Database para o MySQL, tem de criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para encontrar os endereços IP públicos do seu circuito ExpressRoute, abra um bilhete de apoio com o ExpressRoute utilizando o portal Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionar uma regra VNET Firewall ao seu servidor sem ligar pontos finais de serviço VNET

Apenas a definição de uma regra de firewall VNet não ajuda a fixar o servidor ao VNet. Também tem de ligar os **pontos** finais do serviço VNet para que a segurança produza efeitos. Quando gira os pontos finais de serviço **On,** a sua sub-rede VNet experimenta tempo de inatividade até completar a transição de **Off** para **On**. Isto é especialmente verdade no contexto dos grandes VNets. Pode utilizar a bandeira **IgnoreMissingServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Pode definir a bandeira **IgnoreMissingServiceEndpoint** utilizando o Azure CLI ou portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais do Azure][vm-virtual-network-overview]
- [Pontos finais de serviço de rede virtual Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras VNet, consulte:
- [Criar e gerir a Base de Dados Azure para as regras do MySQL VNet utilizando o portal Azure](howto-manage-vnet-using-portal.md)
- [Criar e gerir a Base de Dados de Azure para as regras do MySQL VNet utilizando o Azure CLI](howto-manage-vnet-using-cli.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

---
title: Regras de rede virtual-banco de dados do Azure para PostgreSQL-servidor único
description: Saiba como usar pontos de extremidade de serviço de rede virtual (vnet) para se conectar ao banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 11ffd323c5f775a795899cc35706493cba6d933b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768661"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Usar pontos de extremidade de serviço de rede virtual e regras para o banco de dados do Azure para PostgreSQL-servidor único

*As regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor do banco de dados do Azure para PostgreSQL aceita comunicações enviadas de sub-redes específicas em redes virtuais. Este artigo explica por que o recurso de regra de rede virtual às vezes é a melhor opção para permitir a comunicação com segurança com o servidor do banco de dados do Azure para PostgreSQL.

Para criar uma regra de rede virtual, primeiro deve haver uma VNet ( [rede virtual][vm-virtual-network-overview] ) e um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a ser referenciada. A imagem a seguir ilustra como um ponto de extremidade de serviço de rede virtual funciona com o banco de dados do Azure para PostgreSQL:

![Exemplo de como funciona um ponto de extremidade de serviço de VNet](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Esse recurso está disponível em todas as regiões da nuvem pública do Azure, em que o banco de dados do Azure para PostgreSQL é implantado para servidores Uso Geral e com otimização de memória.
> No caso de emparelhamento VNet, se o tráfego estiver fluindo por um gateway de VNet comum com pontos de extremidade de serviço e for supostamente fluir para o par, crie uma regra de ACL/VNet para permitir que as máquinas virtuais do Azure na VNet do gateway acessem o servidor de banco de dados do Azure para PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrição

**Rede virtual:** Você pode ter redes virtuais associadas à sua assinatura do Azure.

**Sub-rede:** Uma rede virtual contém **sub-redes**. Todas as VMs (máquinas virtuais) do Azure que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computação. Os nós de computação que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure sua segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** Um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo do **Microsoft. SQL**, que se refere ao serviço do Azure chamado banco de dados SQL. Essa marca de serviço também se aplica aos serviços de banco de dados do Azure para PostgreSQL e MySQL. É importante observar ao aplicar a marca de serviço **Microsoft. SQL** a um ponto de extremidade de serviço de VNet. ele configurará o tráfego do ponto de extremidade de serviço para todos os servidores do banco de dados SQL do Azure, banco de dados do Azure para PostgreSQL e banco de dados do Azure para MySQL 

**Regra de rede virtual:** Uma regra de rede virtual para o servidor do banco de dados do Azure para PostgreSQL é uma sub-rede listada na lista de controle de acesso (ACL) do seu banco de dados do Azure para o servidor PostgreSQL. Para estar na ACL para o servidor do banco de dados do Azure para PostgreSQL, a sub-rede deve conter o nome do tipo **Microsoft. SQL** .

Uma regra de rede virtual informa o servidor do banco de dados do Azure para PostgreSQL para aceitar comunicações de cada nó que está na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que você execute uma ação, as VMs em suas sub-redes não podem se comunicar com o servidor do banco de dados do Azure para PostgreSQL. Uma ação que estabelece a comunicação é a criação de uma regra de rede virtual. A lógica para escolher a abordagem de regra de VNet requer uma discussão de comparação e contraste envolvendo as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>R. Permitir acesso aos serviços do Azure

O painel segurança de conexão tem um botão **ligar/desligar** que é rotulado como **permitir o acesso aos serviços do Azure**. A configuração **on** permite a comunicação de todos os endereços IP do Azure e de todas as sub-redes do Azure. Esses IPs ou sub-redes do Azure podem não ser de propriedade de você. Essa configuração **on** provavelmente é mais aberta do que você deseja que o banco de dados do banco de dados do Azure para PostgreSQL seja. O recurso de regra de rede virtual oferece um controle granular muito mais detalhado.

### <a name="b-ip-rules"></a>B. Regras de IP

O Firewall do banco de dados do Azure para PostgreSQL permite que você especifique os intervalos de endereços IP dos quais as comunicações são aceitas no banco de dados do Azure para PostgreSQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure são configurados com endereços IP *dinâmicos* . Os endereços IP dinâmicos podem ser alterados, como quando a VM é reiniciada. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall em um ambiente de produção.

Você pode recuperar a opção de IP obtendo um endereço IP *estático* para sua VM. Para obter detalhes, consulte [configurar endereços IP privados para uma máquina virtual usando o portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é dispendiosa quando feita em escala. As regras de rede virtual são mais fáceis de estabelecer e gerenciar.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Ainda não é possível ter o banco de dados do Azure para PostgreSQL em uma sub-rede sem definir um ponto de extremidade de serviço

Se seu **Microsoft. SQL** Server era um nó em uma sub-rede em sua rede virtual, todos os nós na rede virtual podem se comunicar com o servidor do banco de dados do Azure para PostgreSQL. Nesse caso, suas VMs podem se comunicar com o banco de dados do Azure para PostgreSQL sem a necessidade de nenhuma regra de rede virtual ou regras de IP.

No entanto, a partir de agosto de 2018, o serviço banco de dados do Azure para PostgreSQL ainda não está entre os serviços que podem ser atribuídos diretamente a uma sub-rede.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre regras de rede virtual

Esta seção descreve vários detalhes sobre as regras de rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual aplica-se a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem comunicação da sub-rede.

Qualquer regra de rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não nível de banco de dados

Cada regra de rede virtual se aplica a todo o banco de dados do Azure para servidor PostgreSQL, não apenas a um banco de dados específico no servidor. Em outras palavras, a regra de rede virtual se aplica no nível do servidor, não no nível do banco de dados.

#### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária de cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; ativar o ponto de extremidade.
- **Administrador de banco de dados:** &nbsp; atualizar a lista de controle de acesso (ACL) para adicionar a sub-rede fornecida ao banco de dados do Azure para o servidor PostgreSQL.

*Alternativa de RBAC:*

As funções de administrador de rede e administrador de banco de dados têm mais recursos do que o necessário para gerenciar regras de rede virtual. Apenas um subconjunto de seus recursos é necessário.

Você tem a opção de usar o [controle de acesso baseado em função (RBAC)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o administrador de rede ou o administrador de banco de dados. A área da superfície de sua exposição de segurança será menor se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, o banco de dados do Azure para PostgreSQL e a sub-rede VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Ambas as assinaturas devem estar no mesmo locatário Azure Active Directory.
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede de rede virtual ao servidor especificado.
> - Verifique se a assinatura tem o provedor de recursos **Microsoft. SQL** registrado. Para obter mais informações, consulte [Resource-Manager-Registration][resource-manager-portal]

## <a name="limitations"></a>Limitações

Para o banco de dados do Azure para PostgreSQL, o recurso de regras de rede virtual tem as seguintes limitações:

- Um aplicativo Web pode ser mapeado para um IP privado em uma VNet/sub-rede. Mesmo que os pontos de extremidade de serviço sejam ativados da VNet/sub-rede determinada, as conexões do aplicativo Web para o servidor terão uma origem de IP público do Azure, não uma origem de VNet/sub-rede. Para habilitar a conectividade de um aplicativo Web para um servidor que tem regras de firewall de VNet, você deve permitir que os serviços do Azure acessem o servidor no servidor.

- No firewall do banco de dados do Azure para PostgreSQL, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o banco de dados do Azure para PostgreSQL.

- Cada servidor de banco de dados do Azure para PostgreSQL pode ter até 128 entradas de ACL para qualquer rede virtual específica.

- As regras de rede virtual se aplicam somente a redes virtuais Azure Resource Manager; e não para redes de [modelo de implantação clássica][arm-deployment-model-568f] .

- Ativar pontos de extremidade de serviço de rede virtual no banco de dados do Azure para PostgreSQL usando a marca de serviço **Microsoft. SQL** também habilita os pontos de extremidade para todos os serviços de banco de dados do Azure: banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL, banco de dados SQL do azure e SQL data warehouse do Azure.

- O suporte para pontos de extremidade de serviço de VNet é apenas para servidores Uso Geral e com otimização de memória.

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras de rede virtual não:
    - [VPN (rede virtual privada) site a site (S2S)][vpn-gateway-indexmd-608y]
    - Local via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se sua rede estiver conectada à rede do Azure por meio do uso do [ExpressRoute][expressroute-indexmd-744v], cada circuito será configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são usados para se conectar aos serviços da Microsoft, como o armazenamento do Azure, usando o emparelhamento público do Azure.

Para permitir a comunicação do seu circuito com o banco de dados do Azure para PostgreSQL, você deve criar regras de rede IP para os endereços IP públicos de seus circuitos. Para localizar os endereços IP públicos do circuito do ExpressRoute, abra um tíquete de suporte com o ExpressRoute usando o portal do Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionando uma regra de firewall de VNET ao seu servidor sem ativar os pontos de extremidade de serviço de VNET

Simplesmente definir uma regra de firewall não ajuda a proteger o servidor para a VNet. Você também deve ativar os pontos de **extremidade de serviço da VNet para que** a segurança entre em vigor. Quando você ativa os pontos de extremidade **de**serviço, sua sub-rede VNet experimenta o tempo de inatividade até que conclua a transição de **desativado** para **ativado**. Isso é especialmente verdadeiro no contexto de grandes VNets. Você pode usar o sinalizador **IgnoreMissingServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Você pode definir o sinalizador **IgnoreMissingServiceEndpoint** usando o CLI do Azure ou o Portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais do Azure][vm-virtual-network-overview]
- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Passos seguintes
Para obter artigos sobre a criação de regras de VNet, consulte:
- [Criar e gerenciar regras de VNet do banco de dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-vnet-using-portal.md)
- [Criar e gerenciar regras de VNet do banco de dados do Azure para PostgreSQL usando o CLI do Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md

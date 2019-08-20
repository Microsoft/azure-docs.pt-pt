---
title: Planejamento de rede e conexões para Azure AD Domain Services | Microsoft Docs
description: Saiba mais sobre algumas das considerações de design de rede virtual e os recursos usados para conectividade quando você executa o Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: iainfou
ms.openlocfilehash: 506967fc4cecd322c694d31789cf09bec22ad3d4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617314"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Considerações de design de rede virtual e opções de configuração para Azure AD Domain Services

Como Azure Active Directory Domain Services (AD DS) fornece serviços de autenticação e gerenciamento para outros aplicativos e cargas de trabalho, a conectividade de rede é um componente fundamental. Sem os recursos de rede virtual configurados adequadamente, os aplicativos e as cargas de trabalho não podem se comunicar e usar os recursos fornecidos pelo Azure AD DS. Se você planejar sua rede virtual corretamente, certifique-se de que o Azure AD DS possa fornecer seus aplicativos e cargas de trabalho, conforme necessário.

Este artigo descreve as considerações de design e os requisitos para uma rede virtual do Azure que dá suporte ao Azure AD DS.

## <a name="azure-virtual-network-design"></a>Design de rede virtual do Azure

Para fornecer conectividade de rede e permitir que aplicativos e serviços se autentiquem no Azure AD DS, você usa uma rede virtual e uma sub-rede do Azure. O ideal é que o AD DS do Azure seja implantado em sua própria rede virtual. Você pode incluir uma sub-rede de aplicativo separada na mesma rede virtual para hospedar sua VM de gerenciamento ou cargas de trabalho de aplicativo leves. Uma rede virtual separada para cargas de trabalho de aplicativos maiores ou complexas, emparelhadas para a rede virtual do Azure AD DS, geralmente é o design mais apropriado. Outras opções de designs são válidas, desde que você atenda aos requisitos descritos nas seções a seguir para a rede virtual e a sub-rede.

Conforme você projeta a rede virtual para o Azure AD DS, as seguintes considerações se aplicam:

* O Azure AD DS deve ser implantado na mesma região do Azure que sua rede virtual.
    * Neste momento, você só pode implantar um domínio gerenciado do Azure AD DS por locatário do Azure AD. O domínio gerenciado AD DS do Azure é implantado em uma única região. Certifique-se de criar ou selecionar uma rede virtual em uma [região que dê suporte ao Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Considere a proximidade de outras regiões do Azure e as redes virtuais que hospedam suas cargas de trabalho de aplicativo.
    * Para minimizar a latência, mantenha seus aplicativos principais próximos ou na mesma região que a sub-rede da rede virtual para seu domínio gerenciado do AD DS do Azure. Você pode usar o emparelhamento de rede virtual ou conexões de rede virtual privada (VPN) entre redes virtuais do Azure.
* A rede virtual não pode depender de serviços DNS diferentes daqueles fornecidos pelo Azure AD DS.
    * O Azure AD DS fornece seu próprio serviço DNS. A rede virtual deve ser configurada para usar esses endereços de serviço DNS. A resolução de nomes para namespaces adicionais pode ser realizada usando encaminhadores condicionais.
    * Você não pode usar configurações personalizadas do servidor DNS para direcionar consultas a outros servidores DNS, incluindo em VMs. Os recursos na rede virtual devem usar o serviço DNS fornecido pelo Azure AD DS.

> [!IMPORTANT]
> Você não pode mover o Azure AD DS para uma rede virtual diferente depois de habilitar o serviço.

Um domínio gerenciado do Azure AD DS se conecta a uma sub-rede em uma rede virtual do Azure. Crie essa sub-rede para o Azure AD DS com as seguintes considerações:

* O Azure AD DS deve ser implantado em sua própria sub-rede. Não use uma sub-rede existente ou uma sub-rede de gateway.
* Um grupo de segurança de rede é criado durante a implantação de um domínio gerenciado AD DS do Azure. Esse grupo de segurança de rede contém as regras necessárias para a comunicação correta do serviço.
    * Não crie ou use um grupo de segurança de rede existente com suas próprias regras personalizadas.
* O AD DS do Azure requer entre cinco e sete endereços IP. Verifique se o intervalo de endereços IP da sub-rede pode fornecer esse número de endereços.
    * Restringir os endereços IP disponíveis pode impedir Azure AD Domain Services de manter dois controladores de domínio.

O diagrama de exemplo a seguir descreve um design válido onde o Azure AD DS tem sua própria sub-rede, há uma sub-rede de gateway para conectividade externa e as cargas de trabalho do aplicativo estão em uma sub-rede conectada dentro da rede virtual:

![Design de sub-rede recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Conexões com a rede virtual AD DS do Azure

Conforme observado na seção anterior, você só pode criar um Azure AD Domain Services domínio gerenciado em uma única rede virtual no Azure, e apenas um domínio gerenciado pode ser criado por locatário do Azure AD. Com base nessa arquitetura, talvez seja necessário conectar uma ou mais redes virtuais que hospedem suas cargas de trabalho de aplicativo para sua rede virtual do Azure AD DS.

Você pode conectar cargas de trabalho de aplicativo hospedadas em outras redes virtuais do Azure usando um dos seguintes métodos:

* Peering de rede virtual
* VPN (rede virtual privada)

### <a name="virtual-network-peering"></a>Peering de Rede Virtual

O emparelhamento de rede virtual é um mecanismo que conecta duas redes virtuais na mesma região por meio da rede de backbone do Azure. O emparelhamento de rede virtual global pode conectar a rede virtual entre regiões do Azure. Uma vez emparelhadas, as duas redes virtuais permitem que recursos, como VMs, se comuniquem entre si diretamente usando endereços IP privados. Usar o emparelhamento de rede virtual permite implantar um domínio gerenciado do Azure AD DS com as cargas de trabalho do aplicativo implantadas em outras redes virtuais.

![Conectividade de rede virtual usando emparelhamento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Para obter mais informações, consulte [visão geral de emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking"></a>Rede virtual privada

Você pode conectar uma rede virtual a outra rede virtual (VNet a VNet) da mesma maneira que você pode configurar uma rede virtual para um local do site. Ambas as conexões usam um gateway de VPN para criar um túnel seguro usando IPsec/IKE. Esse modelo de conexão permite que você implante o Azure AD DS em uma rede virtual do Azure e, em seguida, conecte locais ou outras nuvens no local.

![Conectividade de rede virtual usando um gateway de VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Para obter mais informações sobre como usar a rede virtual privada, leia [Configurar uma conexão de gateway de VPN de vnet para vnet usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Resolução de nomes ao conectar redes virtuais

As redes virtuais conectadas ao Azure AD Domain Services rede virtual normalmente têm suas próprias configurações de DNS. Quando você conecta redes virtuais, ele não configura automaticamente a resolução de nomes para a rede virtual conectada para resolver os serviços fornecidos pelo domínio gerenciado AD DS do Azure. A resolução de nomes nas redes virtuais de conexão deve ser configurada para permitir que as cargas de trabalho do aplicativo localizem Azure AD Domain Services.

Você pode habilitar a resolução de nomes usando encaminhadores DNS condicionais no servidor DNS que dão suporte à conexão de redes virtuais ou usando os mesmos endereços IP DNS da rede virtual do serviço de domínio do Azure AD.

## <a name="network-resources-used-by-azure-ad-ds"></a>Recursos de rede usados pelo Azure AD DS

Um domínio gerenciado do Azure AD DS cria alguns recursos de rede durante a implantação. Esses recursos são necessários para uma operação bem-sucedida e o gerenciamento do domínio gerenciado AD DS do Azure e não devem ser manualmente configurados.

| Recurso do Azure                          | Descrição |
|:----------------------------------------|:---|
| Placa de interface de rede                  | O Azure AD DS hospeda o domínio gerenciado em dois controladores de domínio (DCs) executados no Windows Server como VMs do Azure. Cada VM tem uma interface de rede virtual que se conecta à sua sub-rede de rede virtual. |
| Endereço IP público básico dinâmico         | O Azure AD DS se comunica com o serviço de sincronização e gerenciamento usando um endereço IP público de SKU básico. Para obter mais informações sobre endereços IP públicos, consulte [tipos de endereço IP e métodos de alocação no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Balanceador de carga básico do Azure               | O Azure AD DS usa um balanceador de carga de SKU básico para NAT (conversão de endereços de rede) e balanceamento de carga (quando usado com o LDAP seguro). Para obter mais informações sobre os balanceadores de carga do Azure, consulte [o que é Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Regras de conversão de endereços de rede (NAT) | O Azure AD DS cria e usa três regras NAT na regra de balanceador de carga-uma para o tráfego HTTP seguro e duas regras para proteger a comunicação remota do PowerShell. |
| Regras do balanceador de carga                     | Quando um domínio gerenciado do Azure AD DS é configurado para LDAP seguro na porta TCP 636, três regras são criadas e usadas em um balanceador de carga para distribuir o tráfego. |

> [!WARNING]
> Não exclua nenhum recurso de rede criado pelo Azure AD DS. Se você excluir qualquer um dos recursos de rede, ocorrerá uma interrupção do serviço de AD DS do Azure.

## <a name="network-security-groups-and-required-ports"></a>Grupos de segurança de rede e portas necessárias

Um [NSG (grupo de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contém uma lista de regras que permitem ou negam o tráfego de rede para o tráfego em uma rede virtual do Azure. Um grupo de segurança de rede é criado quando você implanta AD DS do Azure que contém um conjunto de regras que permitem que o serviço forneça funções de autenticação e gerenciamento. Esse grupo de segurança de rede padrão é associado à sub-rede da rede virtual em que o domínio gerenciado do Azure AD DS é implantado.

As regras do grupo de segurança de rede a seguir são necessárias para que o Azure AD DS forneça serviços de autenticação e gerenciamento. Não edite ou exclua essas regras de grupo de segurança de rede para a sub-rede da rede virtual em que o domínio gerenciado do Azure AD DS está implantado.

| Número da porta | Protocol | Origem                             | Destino | Action | Requerido | Objetivo |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Sim      | Sincronização com seu locatário do Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Any         | Allow  | Sim      | Gerenciamento do seu domínio. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Sim      | Gerenciamento do seu domínio. |
| 636         | TCP      | Any                                | Any         | Allow  | Não       | Habilitado somente quando você configura LDAPS seguros (LDAPs). |

> [!WARNING]
> Não edite manualmente esses recursos e configurações de rede. Quando você associa um grupo de segurança de rede configurado incorretamente ou uma tabela de rotas definida pelo usuário à sub-rede na qual o Azure AD DS é implantado, você pode interromper a capacidade de serviço e gerenciar o domínio da Microsoft. A sincronização entre seu locatário do Azure AD e seu domínio gerenciado do Azure AD DS também é interrompida.
>
> As regras padrão para *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*e *DenyAllOutBound* também existem para o grupo de segurança de rede. Não edite ou exclua essas regras padrão.
>
> O SLA do Azure não se aplica a implantações em que um grupo de segurança de rede configurado incorretamente e/ou tabelas de rotas definidas pelo usuário foram aplicadas, o que impede que o Azure AD DS atualize e gerencie seu domínio.

### <a name="port-443---synchronization-with-azure-ad"></a>Porta 443-sincronização com o Azure AD

* Usado para sincronizar seu locatário do Azure AD com seu domínio gerenciado AD DS do Azure.
* Sem acesso a essa porta, seu domínio gerenciado AD DS do Azure não pode sincronizar com seu locatário do Azure AD. Os usuários podem não conseguir entrar, pois as alterações às suas senhas não serão sincronizadas com o domínio gerenciado do Azure AD DS.
* O acesso de entrada a essa porta para endereços IP é restringido por padrão usando a marca de serviço **AzureActiveDirectoryDomainServices** .
* Não restrinja o acesso de saída desta porta.

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389-gerenciamento usando a área de trabalho remota

* Usado para conexões de área de trabalho remota para controladores de domínio em seu domínio gerenciado AD DS do Azure.
* A regra de grupo de segurança de rede padrão usa a marca de serviço *CorpNetSaw* para restringir ainda mais o tráfego.
    * Essa marca de serviço permite que somente as estações de trabalho de acesso seguro na rede corporativa da Microsoft usem a área de trabalho remota para o domínio gerenciado AD DS do Azure.
    * O acesso é permitido somente com justificativa de negócios, como para cenários de gerenciamento ou solução de problemas.
* Essa regra pode ser definida como *Deny*e somente definida como *permitir* quando necessário. A maioria das tarefas de gerenciamento e monitoramento são executadas usando a comunicação remota do PowerShell. O RDP é usado apenas no caso raro que a Microsoft precise se conectar remotamente ao seu domínio gerenciado para solução de problemas avançada.

> [!NOTE]
> Você não poderá selecionar manualmente a marca de serviço *CorpNetSaw* no portal se tentar editar essa regra de grupo de segurança de rede. Você deve usar Azure PowerShell ou a CLI do Azure para configurar manualmente uma regra que usa a marca de serviço *CorpNetSaw* .

### <a name="port-5986---management-using-powershell-remoting"></a>Porta 5986-gerenciamento usando comunicação remota do PowerShell

* Usado para executar tarefas de gerenciamento usando a comunicação remota do PowerShell em seu domínio gerenciado AD DS do Azure.
* Sem acesso a essa porta, seu domínio gerenciado AD DS do Azure não pode ser atualizado, configurado, submetido a backup ou monitorado.
* Para os domínios gerenciados do Azure AD DS que usam uma rede virtual baseada no Resource Manager, você pode restringir o acesso de entrada a essa porta para a marca de serviço *AzureActiveDirectoryDomainServices* .
    * Para domínios gerenciados herdados do Azure AD DS usando uma rede virtual baseada em clássico, você pode restringir o acesso de entrada a essa porta para os seguintes endereços IP de origem: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*e *104.40.87.209*.

## <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

As rotas definidas pelo usuário não são criadas por padrão e não são necessárias para que o Azure AD DS funcione corretamente. Se for necessário usar tabelas de rotas, evite fazer alterações na rota *0.0.0.0* . As alterações a essa rota podem ser interrompidas Azure AD Domain Services.

Você também deve rotear o tráfego de entrada dos endereços IP incluídos nas respectivas marcas de serviço do Azure para a sub-rede Azure AD Domain Services. Para obter mais informações sobre marcas de serviço e seu endereço IP associado do, consulte [intervalos de IP e marcas de serviço do Azure – nuvem pública](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Esses intervalos de IP do datacenter do Azure podem ser alterados sem aviso prévio. Verifique se você tem processos para validar se tem os endereços IP mais recentes.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre alguns dos recursos de rede e opções de conexão usados pelo Azure AD DS, consulte os seguintes artigos:

* [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
* [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Grupos de segurança de rede do Azure](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->

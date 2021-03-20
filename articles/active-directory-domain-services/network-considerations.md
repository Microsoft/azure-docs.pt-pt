---
title: Planeamento de rede e conexões para serviços de domínio AZure AD | Microsoft Docs
description: Conheça algumas das considerações e recursos de design de rede virtuais utilizados para a conectividade quando executar os Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: d1a3ab5face03754bf84f442ac0fa73768b0fc80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97615823"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Considerações de design de rede virtual e opções de configuração para serviços de domínio de diretório ativo Azure

A Azure Ative Directory Domain Services (Azure AD DS) presta serviços de autenticação e gestão a outras aplicações e cargas de trabalho. A conectividade da rede é um componente chave. Sem recursos de rede virtuais corretamente configurados, as aplicações e cargas de trabalho não conseguem comunicar e utilizar as funcionalidades fornecidas pela Azure AD DS. Planeie os seus requisitos de rede virtual para garantir que o Azure AD DS possa servir as suas aplicações e cargas de trabalho conforme necessário.

Este artigo descreve considerações e requisitos de design para uma rede virtual Azure para apoiar Azure AD DS.

## <a name="azure-virtual-network-design"></a>Design de rede virtual Azure

Para fornecer conectividade de rede e permitir que aplicações e serviços autensem contra um domínio gerido AZure AD DS, utilize uma rede virtual Azure e uma sub-rede. Idealmente, o domínio gerido deve ser implantado na sua própria rede virtual.

Pode incluir uma sub-rede de aplicações separada na mesma rede virtual para hospedar o seu VM de gestão ou cargas de trabalho de aplicação leve. Uma rede virtual separada para cargas de trabalho de aplicações maiores ou complexas, espreitadas para a rede virtual Azure AD DS, é geralmente o design mais adequado.

Outras escolhas de design são válidas, desde que cumpra os requisitos descritos nas seguintes secções para a rede virtual e sub-rede.

Ao conceber a rede virtual para Azure AD DS, aplicam-se as seguintes considerações:

* O Azure AD DS deve ser implantado na mesma região do Azure que a sua rede virtual.
    * Neste momento, você só pode implementar um domínio gerido por inquilino AZure AD. O domínio gerido é implantado numa única região. Certifique-se de que cria ou seleciona uma rede virtual numa [região que suporta O AZURE AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Considere a proximidade de outras regiões do Azure e as redes virtuais que acolhem as cargas de trabalho da sua aplicação.
    * Para minimizar a latência, mantenha as suas aplicações centrais perto ou na mesma região que a sub-rede de rede virtual para o seu domínio gerido. Pode utilizar ligações de rede virtual ou de rede privada virtual (VPN) entre redes virtuais Azure. Estas opções de ligação são discutidas numa secção seguinte.
* A rede virtual não pode contar com serviços DNS que não os serviços prestados pelo domínio gerido.
    * A Azure AD DS fornece o seu próprio serviço DNS. A rede virtual deve ser configurada para utilizar estes endereços de serviço DNS. A resolução de nomes para espaços de nome adicionais pode ser realizada usando reencaminhadores condicionalistas.
    * Não é possível utilizar as definições personalizadas do servidor DNS para consultas diretas de outros servidores DNS, incluindo em VMs. Os recursos na rede virtual devem utilizar o serviço DNS fornecido pelo domínio gerido.

> [!IMPORTANT]
> Não é possível mover o Azure AD DS para uma rede virtual diferente depois de ter ativado o serviço.

Um domínio gerido conecta-se a uma sub-rede numa rede virtual Azure. Desenhe esta sub-rede para Azure AD DS com as seguintes considerações:

* Um domínio gerido deve ser implantado na sua própria sub-rede. Não utilize uma sub-rede existente ou uma sub-rede de gateway.
* Um grupo de segurança de rede é criado durante a implantação de um domínio gerido. Este grupo de segurança de rede contém as regras necessárias para uma comunicação correta do serviço.
    * Não crie nem utilize um grupo de segurança de rede existente com as suas próprias regras personalizadas.
* Um domínio gerido requer endereços IP 3-5. Certifique-se de que o intervalo de endereços IP da sua sub-rede pode fornecer este número de endereços.
    * Restringir os endereços IP disponíveis pode impedir que o domínio gerido mantenha dois controladores de domínio.

O diagrama de exemplo a seguir descreve um design válido onde o domínio gerido tem a sua própria sub-rede, há uma sub-rede de gateway para conectividade externa, e as cargas de trabalho de aplicação estão numa sub-rede conectada dentro da rede virtual:

![Design recomendado de sub-redes](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Ligações à rede virtual Azure AD DS

Como indicado na secção anterior, só é possível criar um domínio gerido numa única rede virtual em Azure, e apenas um domínio gerido pode ser criado por inquilino AZure AD. Com base nesta arquitetura, poderá ter de ligar uma ou mais redes virtuais que hospedam as cargas de trabalho da sua aplicação à rede virtual do seu domínio gerido.

Pode ligar cargas de trabalho de aplicação hospedadas noutras redes virtuais Azure utilizando um dos seguintes métodos:

* Peering de rede virtual
* Rede privada virtual (VPN)

### <a name="virtual-network-peering"></a>Peering de rede virtual

O espreitamento de rede virtual é um mecanismo que liga duas redes virtuais na mesma região através da rede de espinha dorsal do Azure. O espreitamento global da rede virtual pode ligar a rede virtual através das regiões de Azure. Uma vez espreitadas, as duas redes virtuais permitem que recursos, como VMs, comuniquem-se diretamente usando endereços IP privados. A utilização de um perspício de rede virtual permite-lhe implementar um domínio gerido com as cargas de trabalho da sua aplicação implementadas noutras redes virtuais.

![Conectividade de rede virtual usando o espreitamento](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Para obter mais informações, consulte [a visão geral da rede virtual Azure.](../virtual-network/virtual-network-peering-overview.md)

### <a name="virtual-private-networking-vpn"></a>Rede Privada Virtual (VPN)

Pode ligar uma rede virtual a outra rede virtual (VNet-to-VNet) da mesma forma que pode configurar uma rede virtual para uma localização no local. Ambas as ligações utilizam uma porta de entrada VPN para criar um túnel seguro utilizando o IPsec/IKE. Este modelo de ligação permite-lhe implantar o domínio gerido numa rede virtual Azure e, em seguida, ligar locais no local ou outras nuvens.

![Conectividade de rede virtual usando um Gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Para obter mais informações sobre a utilização de redes privadas virtuais, leia [configurar uma ligação de gateway VNet-to-VNet VPN utilizando o portal Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Resolução de nomes ao ligar redes virtuais

As redes virtuais ligadas à rede virtual do domínio gerido normalmente têm as suas próprias definições de DNS. Quando liga redes virtuais, não configura automaticamente a resolução de nomes para a rede virtual de ligação para resolver os serviços prestados pelo domínio gerido. A resolução de nomes nas redes virtuais de ligação deve ser configurada para permitir que as cargas de trabalho da aplicação localizem o domínio gerido.

Pode ativar a resolução de nomes utilizando reencaminhadores DENS condicional no servidor DNS que suportam as redes virtuais de ligação ou utilizando os mesmos endereços DNS IP da rede virtual do domínio gerido.

## <a name="network-resources-used-by-azure-ad-ds"></a>Recursos de rede utilizados pela Azure AD DS

Um domínio gerido cria alguns recursos de networking durante a implantação. Estes recursos são necessários para o funcionamento e gestão bem sucedidos do domínio gerido, e não devem ser configurados manualmente.

| Recurso do Azure                          | Description |
|:----------------------------------------|:---|
| Cartão de interface de rede                  | O Azure AD DS acolhe o domínio gerido em dois controladores de domínio (DCs) que funcionam no Windows Server como VMs Azure. Cada VM tem uma interface de rede virtual que se conecta à sua sub-rede de rede virtual. |
| Endereço IP público padrão dinâmico      | O Azure AD DS comunica com o serviço de sincronização e gestão utilizando um endereço IP público SKU padrão. Para obter mais informações sobre endereços IP públicos, consulte [os tipos de endereços IP e os métodos de atribuição em Azure](../virtual-network/public-ip-addresses.md). |
| Equilibrador de carga padrão Azure            | O Azure AD DS utiliza um balanceador de carga SKU padrão para tradução de endereços de rede (NAT) e equilíbrio de carga (quando utilizado com LDAP seguro). Para obter mais informações sobre os equilibradores de carga Azure, veja [o que é o Equilibr de Carga Azure?](../load-balancer/load-balancer-overview.md) |
| Regras de tradução de endereços de rede (NAT) | AZure AD DS cria e utiliza três regras NAT no equilibrador de carga - uma regra para tráfego HTTP seguro e duas regras para a fixação da PowerShell. |
| Regras do balanceador de carga                     | Quando um domínio gerido é configurado para LDAP seguro na porta TCP 636, três regras são criadas e usadas num equilibrador de carga para distribuir o tráfego. |

> [!WARNING]
> Não elimine nem modifique nenhum dos recursos de rede criados pela Azure AD DS, tais como configurar manualmente o esquilibrador de carga ou as regras. Se eliminar ou modificar algum dos recursos da rede, poderá ocorrer uma falha de serviço Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Grupos de segurança de rede e portas necessárias

Um [grupo de segurança de rede (NSG)](../virtual-network/network-security-groups-overview.md) contém uma lista de regras que permitem ou negam o tráfego de rede ao tráfego numa rede virtual Azure. Um grupo de segurança de rede é criado quando implementa um domínio gerido que contém um conjunto de regras que permitem que o serviço forneça funções de autenticação e gestão. Este grupo de segurança de rede padrão está associado à sub-rede de rede virtual em que o seu domínio gerido é implantado.

São necessárias as seguintes regras do grupo de segurança da rede para que o domínio gerido forneça serviços de autenticação e gestão. Não edite ou elimine estas regras do grupo de segurança de rede para a sub-rede de rede virtual em que o seu domínio gerido é implantado.

| Número da porta | Protocolo | Origem                             | Destino | Ação | Necessário | Objetivo |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Qualquer         | Permitir  | Yes      | Gestão do seu domínio. |
| 3389        | TCP      | Serra CorpNet                         | Qualquer         | Permitir  | Opcional      | Depurando para o apoio. |

É criado um equilibrador de carga padrão Azure que exige que estas regras sejam postas em prática. Este grupo de segurança de rede assegura o Azure AD DS e é necessário para que o domínio gerido funcione corretamente. Não apague este grupo de segurança de rede. O equilibrador de carga não funcionará corretamente sem ele.

Se necessário, pode criar o grupo de segurança de [rede e as regras necessárias utilizando a Azure PowerShell](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Não edite manualmente estes recursos e configurações de rede. Quando associar um grupo de segurança de rede mal configurado ou uma tabela de rotas definida pelo utilizador com a sub-rede em que o domínio gerido é implantado, poderá perturbar a capacidade da Microsoft de servir e gerir o domínio. A sincronização entre o seu inquilino Azure AD e o seu domínio gerido também é interrompida.
>
> Se utilizar LDAP seguro, pode adicionar a regra 636 da porta TCP necessária para permitir o tráfego externo, se necessário. Adicionar esta regra não coloca as regras do seu grupo de segurança de rede num estado não apoiado. Para mais informações, consulte [Lock Down Secure Acesso LDAP através da internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> Regras predefinidas para *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* e *DenyAllOutBound* também existem para o grupo de segurança da rede. Não edite nem elimine estas regras predefinições.
>
> O Azure SLA não se aplica a implementações em que tenha sido aplicado um grupo de segurança de rede e/ou tabelas de rotas definidas pelo utilizador que bloqueiam o Azure AD DS de atualizar e gerir o seu domínio.

### <a name="port-5986---management-using-powershell-remoting"></a>Porto 5986 - gestão utilizando a remoting PowerShell

* Utilizado para executar tarefas de gestão utilizando o remoing PowerShell no seu domínio gerido.
* Sem acesso a esta porta, o seu domínio gerido não pode ser atualizado, configurado, apoiado ou monitorizado.
* Para domínios geridos que utilizem uma rede virtual baseada em Gestor de Recursos, pode restringir o acesso de entrada a esta porta à tag de serviço *AzureActiveDirectoryDomainServices.*
    * Para domínios geridos pelo legado utilizando uma rede virtual baseada em clássicos, pode restringir o acesso à entrada nesta porta aos seguintes endereços IP de *origem: 52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *1 3.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, e *104.40.87.209*.

    > [!NOTE]
    > Em 2017, a Azure AD Domain Services tornou-se disponível para acolher numa rede Azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Como as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
    >
    > Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389 - gestão utilizando ambiente de trabalho remoto

* Utilizado para ligações remotas de ambiente de trabalho a controladores de domínio no seu domínio gerido.
* A regra do grupo de segurança de rede padrão utiliza a etiqueta de serviço *CorpNetSaw* para restringir ainda mais o tráfego.
    * Esta etiqueta de serviço permite apenas estações de trabalho de acesso segura na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto para o domínio gerido.
    * O acesso só é permitido com justificação empresarial, como por exemplo para cenários de gestão ou resolução de problemas.
* Esta regra pode ser definida como *Deny*, e apenas definida para *Permitir* quando necessário. A maioria das tarefas de gestão e monitorização são executadas utilizando a remoagem PowerShell. O RDP só é utilizado no caso raro em que a Microsoft necessita de ligar-se remotamente ao seu domínio gerido para uma resolução avançada de problemas.

> [!NOTE]
> Não é possível selecionar manualmente a etiqueta de serviço *Do CorpNetSaw* a partir do portal se tentar editar esta regra do grupo de segurança de rede. Tem de utilizar o Azure PowerShell ou o Azure CLI para configurar manualmente uma regra que utiliza a etiqueta de serviço *CorpNetSaw.*
>
> Por exemplo, pode utilizar o seguinte script para criar uma regra que permite rdp: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

## <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

As rotas definidas pelo utilizador não são criadas por padrão e não são necessárias para que o Azure AD DS funcione corretamente. Se for obrigado a utilizar as tabelas de rotas, evite fazer alterações na rota *0.0.0.0.* As alterações nesta rota perturbam o Azure AD DS e colocam o domínio gerido num estado não suportado.

Também deve encaminhar o tráfego de entrada dos endereços IP incluídos nas respetivas etiquetas de serviço Azure para a sub-rede do domínio gerido. Para obter mais informações sobre as etiquetas de serviço e o endereço IP associado, consulte [as Gamas IP E Tags de Serviço Azure - Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Estes intervalos IP do datacenter Azure podem ser alterados sem aviso prévio. Certifique-se de que tem processos para validar que tem os mais recentes endereços IP.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre alguns dos recursos de rede e opções de conexão utilizadas pela Azure AD DS, consulte os seguintes artigos:

* [Observação da rede virtual Azure](../virtual-network/virtual-network-peering-overview.md)
* [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Grupos de segurança de rede do Azure](../virtual-network/network-security-groups-overview.md)
---
title: Planeamento de rede e ligações para serviços de domínio azure AD [ Microsoft Docs
description: Conheça algumas das considerações de design de rede virtual e recursos utilizados para a conectividade quando executa os Serviços de Domínio de Diretório Ativo Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 69f8cd0f78a45c6c5e53368edc5902c4b6695701
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408822"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Considerações de design de rede virtual e opções de configuração para Serviços de Domínio AD Azure

Como o Azure Ative Directory Domain Services (AD DS) fornece serviços de autenticação e gestão a outras aplicações e cargas de trabalho, a conectividade da rede é um componente chave. Sem recursos de rede virtuais corretamente configurados, aplicações e cargas de trabalho não conseguem comunicar e utilizar as funcionalidades fornecidas pelo Azure AD DS. Planeje os seus requisitos de rede virtual para garantir que o Azure AD DS possa servir as suas aplicações e cargas de trabalho conforme necessário.

Este artigo descreve considerações e requisitos de design para uma rede virtual Azure para apoiar o Azure AD DS.

## <a name="azure-virtual-network-design"></a>Design de rede virtual Azure

Para fornecer conectividade de rede e permitir que aplicações e serviços se autentiquem contra o Azure AD DS, utilize uma rede virtual Azure e uma subnet. Idealmente, o Azure AD DS deve ser implantado na sua própria rede virtual. Pode incluir uma sub-rede de aplicação separada na mesma rede virtual para acolher o seu VM de gestão ou cargas de trabalho de aplicação ligeira. Uma rede virtual separada para cargas de trabalho de aplicação maiores ou complexas, observada para a rede virtual Azure AD DS, é geralmente o design mais apropriado. Outras escolhas de designs são válidas, desde que cumpra os requisitos descritos nas seguintes secções para a rede virtual e sub-rede.

À medida que projeta a rede virtual para AD DS Azure, aplicam-se as seguintes considerações:

* O Azure AD DS deve ser implantado na mesma região do Azure que a sua rede virtual.
    * Neste momento, só pode implantar um domínio gerido pela Azure AD DS por inquilino da AD Azure. O domínio gerido pela AD DS Azure é implantado numa região única. Certifique-se de que cria ou seleciona uma rede virtual numa região que suporta o [Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Considere a proximidade de outras regiões do Azure e as redes virtuais que acolhem as suas cargas horárias de aplicação.
    * Para minimizar a latência, mantenha as suas aplicações centrais perto ou na mesma região que a subnet de rede virtual para o seu domínio gerido pelo Azure AD DS. Pode utilizar ligações de rede virtual ou rede privada virtual (VPN) entre redes virtuais Azure. Estas opções de ligação são discutidas numa secção seguinte.
* A rede virtual não pode contar com serviços DNS que não os fornecidos pela Azure AD DS.
    * A Azure AD DS fornece o seu próprio serviço DNS. A rede virtual deve ser configurada para utilizar estes endereços de serviço DNS. A resolução de nomes para espaços de nome sois adicionais pode ser realizada utilizando avançados condicional.
    * Não é possível utilizar as definições personalizadas do servidor DNS para direcionar consultas de outros servidores DNS, incluindo em VMs. Os recursos na rede virtual devem utilizar o serviço DNS fornecido pela Azure AD DS.

> [!IMPORTANT]
> Não pode mover o Azure AD DS para uma rede virtual diferente depois de ter ativado o serviço.

Um domínio gerido por Azure AD DS liga-se a uma subnet numa rede virtual Azure. Conceber esta sub-rede para AD DS Azure com as seguintes considerações:

* O Azure AD DS deve ser implantado na sua própria sub-rede. Não utilize uma sub-rede existente ou uma sub-rede de porta.
* Um grupo de segurança de rede é criado durante a implantação de um domínio gerido pelo Azure AD DS. Este grupo de segurança da rede contém as regras necessárias para uma comunicação correta do serviço.
    * Não crie ou use um grupo de segurança de rede existente com as suas próprias regras personalizadas.
* O Azure AD DS requer endereços IP 3-5. Certifique-se de que o seu intervalo de endereços IP da sub-rede pode fornecer este número de endereços.
    * Restringir os endereços IP disponíveis pode impedir que os Serviços de Domínio AD do Azure mantenham dois controladores de domínio.

O diagrama de exemplo seguinte descreve um design válido onde o Azure AD DS tem a sua própria sub-rede, há uma subnet de gateway para conectividade externa, e as cargas de trabalho de aplicação estão numa subnet conectada dentro da rede virtual:

![Design de sub-rede recomendado](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Conexões à rede virtual Azure AD DS

Como notado na secção anterior, só é possível criar um domínio gerido pela Azure AD Domain Services numa única rede virtual em Azure, e apenas um domínio gerido pode ser criado por inquilino da AD Azure. Com base nesta arquitetura, poderá ter de ligar uma ou mais redes virtuais que acolhem as suas cargas de trabalho de aplicação à sua rede virtual Azure AD DS.

Pode ligar as cargas de trabalho de aplicação alojadas noutras redes virtuais do Azure utilizando um dos seguintes métodos:

* Peering de rede virtual
* Rede privada virtual (VPN)

### <a name="virtual-network-peering"></a>Peering de rede virtual

O epeering de rede virtual é um mecanismo que liga duas redes virtuais na mesma região através da rede de espinha dorsal Azure. O epeering global de rede virtual pode ligar a rede virtual através das regiões de Azure. Uma vez espreitadas, as duas redes virtuais permitem que recursos, como VMs, comuniquem uns com os outros diretamente usando endereços IP privados. A utilização de um epeerte de rede virtual permite-lhe implementar um domínio gerido pelo Azure AD DS com as cargas de trabalho da sua aplicação implementadas noutras redes virtuais.

![Conectividade de rede virtual usando o peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Para mais informações, consulte a [visão geral da rede virtual Azure.](../virtual-network/virtual-network-peering-overview.md)

### <a name="virtual-private-networking-vpn"></a>Rede Privada Virtual (VPN)

Pode ligar uma rede virtual a outra rede virtual (VNet-to-VNet) da mesma forma que pode configurar uma rede virtual a uma localização no local. Ambas as ligações utilizam um porta-portas VPN para criar um túnel seguro utilizando iPsec/IKE. Este modelo de ligação permite-lhe implantar o Azure AD DS numa rede virtual Azure e, em seguida, ligar locais no local ou outras nuvens.

![Conectividade de rede virtual usando um Gateway VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Para obter mais informações sobre a utilização de redes privadas virtuais, leia [Configure uma ligação de gateway VNet-to-VNet VPN utilizando o portal Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Resolução de nomes ao ligar redes virtuais

As redes virtuais ligadas à rede virtual Azure AD Domain Services têm normalmente as suas próprias definições de DNS. Quando liga redes virtuais, não configura automaticamente a resolução de nomes para a rede virtual de ligação para resolver os serviços fornecidos pelo domínio gerido pelo Azure AD DS. A resolução de nomes nas redes virtuais de ligação deve ser configurada para permitir que as cargas de trabalho da aplicação localizem os Serviços de Domínio Azure AD.

Pode ativar a resolução de nomes utilizando os avançados DNS condicionados no servidor DNS que suporta as redes virtuais de ligação ou utilizando os mesmos endereços IP DNS da rede virtual do Serviço de Domínio AD Azure.

## <a name="network-resources-used-by-azure-ad-ds"></a>Recursos de rede utilizados pela Azure AD DS

Um domínio gerido por AD DS Azure cria alguns recursos de networking durante a implantação. Estes recursos são necessários para uma operação e gestão bem sucedidas do domínio gerido pelo Azure AD DS, e não devem ser configurados manualmente.

| Recurso Azure                          | Descrição |
|:----------------------------------------|:---|
| Cartão de interface de rede                  | O Azure AD DS acolhe o domínio gerido em dois controladores de domínio (DCs) que funcionam no Windows Server como VMs Azure. Cada VM tem uma interface de rede virtual que se conecta à sua subnet de rede virtual. |
| Endereço IP público padrão dinâmico      | O Azure AD DS comunica com o serviço de sincronização e gestão utilizando um endereço IP público padrão SKU. Para mais informações sobre endereços IP públicos, consulte os tipos de [endereços IP e os métodos](../virtual-network/virtual-network-ip-addresses-overview-arm.md)de atribuição em Azure . |
| Equilibrador de carga padrão Azure            | O Azure AD DS utiliza um equilíbrio de carga SKU padrão para tradução de endereços de rede (NAT) e equilíbrio de carga (quando utilizado com LDAP seguro). Para obter mais informações sobre os equilibradores de carga Azure, consulte [o que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Regras de tradução de endereços de rede (NAT) | O Azure AD DS cria e utiliza três regras NAT no equilibrista de carga - uma regra para o tráfego http seguro, e duas regras para o remo powerShell seguro. |
| Regras do balanceador de carga                     | Quando um domínio gerido por DS Azure AD é configurado para LDAP seguro na porta TCP 636, são criadas três regras e usadas num equilibrista de carga para distribuir o tráfego. |

> [!WARNING]
> Não elimine ou modifique nenhum dos recursos de rede criados pelo Azure AD DS, tais como configurar manualmente o equilibrador de carga ou as regras. Se eliminar ou modificar qualquer um dos recursos da rede, pode ocorrer uma falha no serviço Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Grupos de segurança da rede e portos obrigatórios

Um grupo de segurança de [rede (NSG)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras que permitem ou negam o tráfego de rede ao tráfego numa rede virtual Azure. Um grupo de segurança de rede é criado quando implementa o Azure AD DS que contém um conjunto de regras que permitem ao serviço fornecer funções de autenticação e gestão. Este grupo de segurança de rede padrão está associado à subnet de rede virtual em que o seu domínio gerido pelo Azure AD DS está implantado.

São necessárias as seguintes regras do grupo de segurança da rede para que o Azure AD DS forneça serviços de autenticação e gestão. Não edite ou elimine estas regras do grupo de segurança da rede para a subnet de rede virtual em que o seu domínio gerido pelo Azure AD DS é implantado.

| Número da porta | Protocolo | Origem                             | Destino | Ação | Necessário | Objetivo |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Qualquer         | Permitir  | Sim      | Sincronização com o seu inquilino Azure AD. |
| 3389        | TCP      | Corpnetsaw                         | Qualquer         | Permitir  | Sim      | Gestão do seu domínio. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Qualquer         | Permitir  | Sim      | Gestão do seu domínio. |
| 636         | TCP      | Qualquer                                | Qualquer         | Permitir  | Não       | Só ativado quando configurar o LDAP seguro (LDAPS). |

> [!WARNING]
> Não edite manualmente estes recursos e configurações de rede. Quando associar um grupo de segurança de rede mal configurado ou uma tabela de rotas definida pelo utilizador com a subnet na qual o Azure AD DS é implantado, poderá perturbar a capacidade da Microsoft de servir e gerir o domínio. A sincronização entre o seu inquilino Azure AD e o seu domínio gerido pela Azure AD DS também é interrompida.
>
> As regras predefinidas para *AllowVnetInBound,* *AllowAzureLoadBalancerInBound,* *DenyAllInBound,* *AllowVnetOutBound,* *AllowInternetOutBound*e *DenyAllOutBound* também existem para o grupo de segurança da rede. Não edite ou elimine estas regras predefinidas.
>
> O Azure SLA não se aplica a implementações em que tenha sido aplicado um grupo de segurança de rede configurado indevidamente e/ou tabelas de rotas definidas pelo utilizador que bloqueiam o Azure AD DS de atualizar e gerir o seu domínio.

### <a name="port-443---synchronization-with-azure-ad"></a>Porto 443 - sincronização com Azure AD

* Usado para sincronizar o seu inquilino Azure AD com o seu domínio gerido Azure AD DS.
* Sem acesso a este porto, o seu domínio gerido pelo Azure AD DS não pode sincronizar com o seu inquilino Azure AD. Os utilizadores podem não conseguir iniciar sessão, uma vez que as alterações às suas palavras-passe não seriam sincronizadas no domínio gerido pelo Azure AD DS.
* O acesso à entrada desta porta para endereços IP é restringido por padrão utilizando a etiqueta de serviço **AzureActiveDirectoryDomainServices.**
* Não restrinja o acesso de saída a partir desta porta.

### <a name="port-3389---management-using-remote-desktop"></a>Porta 3389 - gestão utilizando ambiente de trabalho remoto

* Utilizado para ligações remotas de ambiente de trabalho a controladores de domínio no seu domínio gerido pelo Azure AD DS.
* A regra do grupo de segurança da rede padrão usa a etiqueta de serviço *CorpNetSaw* para restringir ainda mais o tráfego.
    * Esta etiqueta de serviço permite apenas postos de trabalho de acesso seguros na rede corporativa da Microsoft para utilizar o ambiente de trabalho remoto para o domínio gerido pelo Azure AD DS.
    * O acesso só é permitido com justificação empresarial, como para cenários de gestão ou resolução de problemas.
* Esta regra pode ser definida para *Negar*, e apenas definida para *permitir* quando necessário. A maioria das tarefas de gestão e monitorização são realizadas usando o remo powerShell. O RDP só é utilizado no caso raro de a Microsoft precisar de se ligar remotamente ao seu domínio gerido para resolução avançada de problemas.

> [!NOTE]
> Não é possível selecionar manualmente a etiqueta de serviço *CorpNetSaw* do portal se tentar editar esta regra do grupo de segurança da rede. Deve utilizar o Azure PowerShell ou o Azure CLI para configurar manualmente uma regra que utiliza a etiqueta de serviço *CorpNetSaw.*

### <a name="port-5986---management-using-powershell-remoting"></a>Porta 5986 - gestão usando remopowerShell

* Usado para executar tarefas de gestão usando o remo PowerShell no seu domínio gerido azure AD DS.
* Sem acesso a esta porta, o seu domínio gerido pelo Azure AD DS não pode ser atualizado, configurado, apoiado ou monitorizado.
* Para domínios geridos pela Azure AD DS que utilizam uma rede virtual baseada em Recursos, pode restringir o acesso a esta porta à etiqueta de serviço *AzureActiveDirectoryDomainServices.*
    * Para domínios geridos pelo legado Azure AD DS utilizando uma rede virtual baseada em clássicos, pode restringir o acesso à entrada a esta porta aos seguintes endereços IP de origem: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*, e *104.40.87.209*.

    > [!NOTE]
    > Em 2017, a Azure AD Domain Services tornou-se disponível para hospedar numa rede azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Uma vez que as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
    >
    > Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Rotas definidas pelo utilizador

As rotas definidas pelo utilizador não são criadas por padrão e não são necessárias para que o Azure AD DS funcione corretamente. Se for obrigado a utilizar tabelas de rotas, evite fazer alterações na rota *0.0.0.0.* As alterações a esta rota perturbam os Serviços de Domínio Azure AD e colocam o domínio gerido num estado não apoiado.

Deve também encaminhar o tráfego de entrada a partir dos endereços IP incluídos nas respetivas etiquetas de serviço Azure para a subnet Azure AD Domain Services. Para obter mais informações sobre etiquetas de serviço e seu endereço IP associado a partir de, consulte [As Gamas e Etiquetas](https://www.microsoft.com/en-us/download/details.aspx?id=56519)de Serviço Azure IP - Nuvem Pública .

> [!CAUTION]
> Estas gamas IP do centro de dados Azure podem ser alteradas sem aviso prévio. Certifique-se de que tem processos para validar que tem os mais recentes endereços IP.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre alguns dos recursos de rede e opções de conexão utilizadas pela Azure AD DS, consulte os seguintes artigos:

* [Peering de rede virtual Azure](../virtual-network/virtual-network-peering-overview.md)
* [Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Grupos de segurança de rede do Azure](../virtual-network/security-overview.md)

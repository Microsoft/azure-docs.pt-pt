---
title: Arquitetura de referência empresarial para Azure DevTest Labs
description: Este artigo fornece orientação de arquitetura de referência para a Azure DevTest Labs em uma empresa.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328849"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs referencia arquitetura para empresas
Este artigo fornece arquitetura de referência para ajudá-lo a implementar uma solução baseada na Azure DevTest Labs em uma empresa. Inclui o seguinte:
- Conectividade no local via Azure ExpressRoute
- Uma porta de entrada remota para iniciar snuisticamente em máquinas virtuais
- Conectividade a um repositório de artefactos para artefactos privados
- Outros serviços PaaS que são usados em laboratórios

![Diagrama de arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os elementos-chave da arquitetura de referência:

- **Azure Ative Directory (Azure AD)**: A DevTest Labs utiliza o [serviço Azure AD para gestão de identidades](../active-directory/fundamentals/active-directory-whatis.md). Considere estes dois aspetos fundamentais quando dá aos utilizadores acesso a um ambiente baseado em DevTest Labs:
    - **Gestão de recursos**: Proporciona acesso ao portal Azure para gerir recursos (criar máquinas virtuais; criar ambientes; iniciar, parar, reiniciar, excluir e aplicar artefactos; e assim por diante). A gestão de recursos é feita utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). Atribui funções aos utilizadores e define permissões de recursos e de nível de acesso.
    - **Máquinas virtuais (nível de rede)**: Na configuração predefinida, as máquinas virtuais utilizam uma conta de administração local. Se houver um domínio disponível[(Azure AD Domain Services](../active-directory-domain-services/overview.md), um domínio no local ou um domínio baseado na nuvem), as máquinas podem ser unidas ao domínio. Os utilizadores podem então utilizar as suas identidades baseadas em domínios para se conectarem aos VMs.
- **Conectividade no local**: No nosso diagrama de arquitetura, o [ExpressRoute](../expressroute/expressroute-introduction.md) é utilizado. Mas também pode utilizar uma [VPN site-to-site.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) Embora o ExpressRoute não seja necessário para a DevTest Labs, é comumente usado em empresas. O ExpressRoute só é necessário se precisar de acesso aos recursos corporativos. Os cenários comuns são:
    - Tem dados no local que não podem ser movidos para a nuvem.
    - Prefere juntar as máquinas virtuais do laboratório ao domínio do local.
    - Você pretende forçar todo o tráfego de rede dentro e fora do ambiente de nuvem através de uma firewall no local para segurança/conformidade.
- **Grupos de segurança de rede**: Uma forma comum de restringir o tráfego ao ambiente em nuvem (ou dentro do ambiente de nuvem) com base em endereços IP de origem e destino é utilizar um grupo de segurança de [rede](../virtual-network/network-security-groups-overview.md). Por exemplo, só pretende permitir o tráfego originário da rede corporativa para as redes do laboratório.
- **Gateway remoto do ambiente de trabalho**: As empresas normalmente bloqueiam ligações de ambiente de trabalho remotos de saída na firewall corporativa. Existem várias opções para permitir a conectividade ao ambiente baseado na nuvem em DevTest Labs, incluindo:
  - Utilize um [gateway remoto de secretária](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e permita o endereço IP estático do equilibrador de carga gateway.
  - [Direcione todo](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o tráfego RDP de entrada sobre a ligação ExpressRoute/site-to-site VPN. Esta funcionalidade é uma consideração comum quando as empresas planeiam uma implantação da DevTest Labs.
- **Serviços de rede (redes virtuais, sub-redes)**: A topologia [de rede Azure](../networking/networking-overview.md) é outro elemento-chave na arquitetura DevTest Labs. Controla se os recursos do laboratório podem comunicar e ter acesso às instalações e à internet. O nosso diagrama de arquitetura inclui as formas mais comuns que os clientes usam DevTest Labs: Todos os laboratórios se conectam através da [rede virtual, observando](../virtual-network/virtual-network-peering-overview.md) utilizando um [modelo de hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a ligação VPN expressRoute/site-to-site ao local. Mas a DevTest Labs utiliza a Rede Virtual Azure diretamente, pelo que não existem restrições na forma como configura a infraestrutura de networking.
- **DevTest Labs**: DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte [About DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**: As máquinas virtuais são uma carga de trabalho fundamental que a DevTest Labs suporta juntamente com outros recursos Azure. A DevTest Labs torna fácil e rápido que uma empresa forneça acesso aos recursos da Azure (incluindo VMs e outros recursos Azure). Saiba mais sobre o acesso ao Azure para [programadores](devtest-lab-developer-lab.md) e [testadores.](devtest-lab-test-env.md)

## <a name="scalability-considerations"></a>Considerações de escalabilidade
Embora a DevTest Labs não tenha quotas ou limites incorporados, outros recursos Azure que são usados na operação típica de um laboratório têm [quotas de nível de subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md) Então, numa implementação típica da empresa, você precisa de várias subscrições Azure para cobrir uma grande implementação de DevTest Labs. As quotas que as empresas mais atingem são:

- **Grupos de recursos**: Na configuração padrão, a DevTest Labs cria um grupo de recursos para cada nova máquina virtual, ou o utilizador cria um ambiente utilizando o serviço. As subscrições podem conter [até 980 grupos de recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Então, este é o limite de máquinas virtuais e ambientes numa subscrição. Há duas outras configurações que deve considerar:
    - **[Todas as máquinas virtuais vão para o mesmo grupo](resource-group-control.md)** de recursos : Embora esta configuração o ajude a cumprir o limite do grupo de recursos, afeta o limite do grupo de recursos por recurso.
    - **Utilização de IPs públicos partilhados**: Todos os VMs do mesmo tamanho e região vão para o mesmo grupo de recursos. Esta configuração é um "meio termo" entre as quotas de grupo de recursos e as quotas de grupo tipo de recursos por recurso, se as máquinas virtuais tiverem endereços IP públicos.
- **Recursos por grupo de recursos por tipo de recurso**: O limite de predefinição para recursos por grupo de recursos por tipo de recurso é de [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando utiliza *todos os VMs para a mesma* configuração do grupo de recursos, os utilizadores atingem este limite de subscrição muito mais cedo, especialmente se os VMs tiverem muitos discos extra.
- **Contas de armazenamento**: Um laboratório em DevTest Labs vem com uma conta de armazenamento. A quota Azure para [o número de contas de armazenamento por região por subscrição é de 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O número máximo de Laboratórios DevTest na mesma região também é de 250.
- **Atribuições de funções**: Uma atribuição de funções é como dá a um utilizador ou ao principal acesso a um recurso (proprietário, recurso, nível de permissão). Em Azure, há um [limite de 2.000 atribuições por subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits) Por predefinição, o serviço DevTest Labs cria um grupo de recursos para cada VM. O proprietário tem permissão *do proprietário* para o DevTest Labs VM e permissão do *leitor* para o grupo de recursos. Desta forma, cada novo VM que cria utiliza duas atribuições de funções para além das atribuições que são usadas quando dá permissão aos utilizadores para o laboratório.
- **A API lê/escreve**: Existem várias formas de automatizar Azure e DevTest Labs, incluindo REST APIs, PowerShell, Azure CLI e Azure SDK. Através da automatização, poderá atingir outro limite nos pedidos da API: Cada subscrição permite até [12.000 pedidos de leitura e 1.200 pedidos de escrita por hora.](../azure-resource-manager/management/request-limits-and-throttling.md) Esteja ciente deste limite quando automatizar a DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão
A DevTest Labs tem uma excelente interface de utilizador administrativa para trabalhar com um único laboratório. Mas numa empresa, provavelmente tem várias assinaturas do Azure e muitos laboratórios. Fazer alterações consistentemente em todos os seus laboratórios requer script/automação. Aqui estão alguns exemplos e boas práticas de gestão para uma implementação da DevTest Labs:

- **Alterações nas definições do laboratório**: Um cenário comum é atualizar uma configuração específica do laboratório em todos os laboratórios da implantação. Por exemplo, um novo tamanho de instância VM está disponível, e todos os laboratórios devem ser atualizados para permitir. É melhor automatizar estas alterações utilizando scripts PowerShell, CLI ou REST APIs.  
- **Símbolo de acesso pessoal repositório de artefactos**: Normalmente, os tokens de acesso pessoal para um repositório de Git expiram em 90 dias, um ano, ou dois anos. Para garantir a continuidade, é importante alargar o token de acesso pessoal ou criar um novo. Em seguida, use a automação para aplicar o novo símbolo de acesso pessoal a todos os laboratórios.
- **Restringir as alterações a uma configuração de laboratório**: Muitas vezes, uma determinada definição deve ser restringida (como permitir a utilização de imagens do mercado). Pode utilizar a Política Azure para evitar alterações a um tipo de recurso. Ou pode criar um papel personalizado, e conceder aos utilizadores esse papel em vez do papel de *proprietário* para o laboratório. Pode fazê-lo para a maioria das configurações do laboratório (suporte interno, anúncio de laboratório, tamanhos de VM permitidos, e assim por diante).
- **Exige que os VM sigam uma convenção de nomeação**: Os gestores geralmente querem identificar facilmente VMs que fazem parte de um ambiente de desenvolvimento e teste baseado na nuvem. Pode fazê-lo utilizando [a Política Azure.](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)

É importante notar que a DevTest Labs utiliza recursos Azure subjacentes que são geridos da mesma forma: networking, discos, computação, e assim por diante. Por exemplo, a Política Azure aplica-se a máquinas virtuais que são criadas em laboratório. O Centro de Segurança Azure pode reportar a conformidade com o VM. E o serviço de backup Azure pode fornecer cópias de segurança regulares para os VMs no laboratório.

## <a name="security-considerations"></a>Considerações de segurança
A Azure DevTest Labs utiliza recursos existentes em Azure (computação, networking, e assim por diante). Assim, beneficia automaticamente das funcionalidades de segurança que são incorporadas na plataforma. Por exemplo, para exigir que as ligações remotas de ambiente de trabalho tenham origem apenas a partir da rede corporativa, basta adicionar um grupo de segurança de rede à rede virtual no gateway remoto do ambiente de trabalho. A única consideração adicional de segurança é o nível de permissões que concede aos membros da equipa que usam os laboratórios no dia-a-dia. As permissões mais comuns são [ *o proprietário* e *o utilizador.*](devtest-lab-add-devtest-user.md) Para obter mais informações sobre estas funções, consulte [adicionar proprietários e utilizadores em Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Dimensione a sua infraestrutura Azure DevTest Labs](devtest-lab-guidance-scale.md).
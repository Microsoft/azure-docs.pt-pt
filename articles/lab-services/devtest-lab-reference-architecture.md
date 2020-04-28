---
title: Arquitetura de referência empresarial para Azure DevTest Labs
description: Este artigo fornece orientação de arquitetura de referência para a Azure DevTest Labs numa empresa.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77132840"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs arquitetura de referência para empresas
Este artigo fornece arquitetura de referência para ajudá-lo a implementar uma solução baseada em Azure DevTest Labs em uma empresa. Inclui o seguinte:
- Conectividade no local via Azure ExpressRoute
- Uma porta de entrada remota para iniciar sessão remotamente em máquinas virtuais
- Conectividade com um repositório de artefactos para artefactos privados
- Outros serviços PaaS que são usados em laboratórios

![Diagrama de arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os elementos-chave da arquitetura de referência:

- **Azure Ative Directory (Azure AD)**: A DevTest Labs utiliza o [serviço Azure AD para gestão de identidade.](../active-directory/fundamentals/active-directory-whatis.md) Considere estes dois aspetos fundamentais quando dá aos utilizadores acesso a um ambiente baseado em DevTest Labs:
    - **Gestão de recursos**: Dá acesso ao portal Azure para gerir recursos (criar máquinas virtuais; criar ambientes; iniciar, parar, reiniciar, apagar e aplicar artefactos; e assim por diante). A gestão de recursos é feita em Azure utilizando o controlo de acesso baseado em funções (RBAC). Atribui funções aos utilizadores e define permissões de recursos e de acesso.
    - **Máquinas virtuais (nível de rede)**: Na configuração predefinida, as máquinas virtuais utilizam uma conta de administração local. Se houver um domínio disponível[(Azure AD Domain Services](../active-directory-domain-services/overview.md), um domínio no local, ou um domínio baseado em nuvem), as máquinas podem ser unidas ao domínio. Os utilizadores podem então utilizar as suas identidades baseadas em domínios para se conectarem aos VMs.
- **Conectividade no local**: No nosso diagrama de arquitetura, é utilizada a [ExpressRoute.](../expressroute/expressroute-introduction.md) Mas também pode usar uma [VPN site-to-site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora o ExpressRoute não seja necessário para a DevTest Labs, é comumente usado em empresas. O ExpressRoute só é necessário se precisar de acesso aos recursos corporativos. Os cenários comuns são:
    - Tem dados no local que não podem ser movidos para a nuvem.
    - Prefere juntar-se às máquinas virtuais do laboratório para o domínio no local.
    - Você quer forçar todo o tráfego de rede dentro e fora do ambiente de nuvem através de uma firewall no local para segurança/conformidade.
- **Grupos**de segurança da rede : Uma forma comum de restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de nuvem) com base em endereços IP de origem e destino é utilizar um grupo de segurança da [rede](../virtual-network/security-overview.md). Por exemplo, você quer permitir apenas tráfego que vem da rede corporativa para as redes do laboratório.
- **Gateway de ambiente**de trabalho remoto : As empresas normalmente bloqueiam ligações remotas de ambiente de trabalho na firewall corporativa. Existem várias opções para permitir a conectividade com o ambiente baseado na nuvem em DevTest Labs, incluindo:
  - Utilize um [gateway remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)para desktop e deixe o endereço IP estático do equilíbrio de carga de gateway.
  - [Direcione todo o tráfego de RDP que entra](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) através da ligação VPN ExpressRoute/site-to-site. Esta funcionalidade é uma consideração comum quando as empresas planeiam uma implementação de DevTest Labs.
- **Serviços de rede (redes virtuais, subnets)**: A topologia de [rede Azure](../networking/networking-overview.md) é outro elemento-chave na arquitetura DevTest Labs. Controla se os recursos do laboratório podem comunicar e ter acesso às instalações e à internet. O nosso diagrama de arquitetura inclui as formas mais comuns de os clientes usarem DevTest Labs: Todos os laboratórios se conectam através de [um epeering](../virtual-network/virtual-network-peering-overview.md) de rede virtual utilizando um [modelo de hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a ligação VPN ExpressRoute/site-to-site para as instalações. Mas a DevTest Labs utiliza diretamente a Rede Virtual Azure, pelo que não existem restrições à forma como configura a infraestrutura de rede.
- **DevTest Labs**: DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte sobre a [DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**: As máquinas virtuais são uma carga de trabalho chave que a DevTest Labs suporta juntamente com outros recursos azure. A DevTest Labs facilita e rapidamente para uma empresa fornecer acesso aos recursos Azure (incluindo VMs e outros recursos Azure). Saiba mais sobre o acesso ao Azure para [programadores](devtest-lab-developer-lab.md) e [testadores.](devtest-lab-test-env.md)

## <a name="scalability-considerations"></a>Considerações de escalabilidade
Embora a DevTest Labs não tenha quotas ou limites incorporados, outros recursos Azure que são usados no funcionamento típico de um laboratório têm [quotas de nível de subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md) Então, numa implantação típica da empresa, você precisa de várias subscrições Azure para cobrir uma grande implementação de DevTest Labs. As quotas que as empresas mais frequentemente alcançam são:

- **Grupos**de recursos : Na configuração padrão, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual, ou o utilizador cria um ambiente utilizando o serviço. As subscrições podem conter [até 980 grupos](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)de recursos . Então, este é o limite de máquinas virtuais e ambientes numa subscrição. Há duas outras configurações que deve considerar:
    - **[Todas as máquinas virtuais vão para o mesmo grupo](resource-group-control.md)** de recursos : Embora esta configuração o ajude a cumprir o limite do grupo de recursos, afeta o limite do tipo de recurso por grupo de recursos.
    - **Utilização de IPs públicos partilhados**: Todos os VMs do mesmo tamanho e região entram no mesmo grupo de recursos. Esta configuração é um "meio termo" entre quotas de grupo de recursos e quotas de recursos-tipo-por-grupo de recursos, se as máquinas virtuais forem autorizadas a ter endereços IP públicos.
- **Recursos por grupo de recursos por tipo de recurso**: O limite de predefinição de recursos por grupo de recursos por tipo de recurso é de [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando utiliza todos *os VMs para a mesma* configuração do grupo de recursos, os utilizadores atingem este limite de subscrição muito mais cedo, especialmente se os VMs tiverem muitos discos extras.
- **Contas de armazenamento:** Um laboratório em DevTest Labs vem com uma conta de armazenamento. O contingente Azure para [o número de contas de armazenamento por região por subscrição é de 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O número máximo de Laboratórios DevTest na mesma região também é de 250.
- **Atribuição de funções**: Uma atribuição de funções é como dá acesso a um recurso (proprietário, recurso, nível de permissão). Em Azure, há um [limite de 2.000 atribuições por subscrição.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Por padrão, o serviço DevTest Labs cria um grupo de recursos para cada VM. O proprietário tem permissão do *proprietário* para o VM de Laboratórios DevTest e permissão do *leitor* para o grupo de recursos. Desta forma, cada novo VM que cria utiliza duas atribuições de funções para além das atribuições que são usadas quando dá permissão aos utilizadores para o laboratório.
- **API lê/escreve**: Existem várias formas de automatizar o Azure e o DevTest Labs, incluindo ASAP REST, PowerShell, Azure CLI e Azure SDK. Através da automação, pode atingir outro limite nos pedidos de API: Cada subscrição permite até [12.000 pedidos de leitura e 1.200 pedidos de escrita por hora](../azure-resource-manager/management/request-limits-and-throttling.md). Esteja ciente deste limite quando automatizar os Laboratórios DevTest.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão
A DevTest Labs tem uma excelente interface administrativa de utilizador para trabalhar com um único laboratório. Mas numa empresa, provavelmente tem várias subscrições azure e muitos laboratórios. Fazer alterações consistentemente em todos os seus laboratórios requer scripting/automação. Aqui estão alguns exemplos e melhores práticas de gestão para uma implementação de DevTest Labs:

- **Alterações nas definições**do laboratório : Um cenário comum é atualizar um cenário específico de laboratório em todos os laboratórios da implementação. Por exemplo, um novo tamanho de instância VM está disponível, e todos os laboratórios devem ser atualizados para permitir. É melhor automatizar estas alterações usando scripts PowerShell, o CLI ou AS APIs REST.  
- Ficha de **acesso pessoal de artefactos:** Tipicamente, os tokens de acesso pessoal para um repositório Git expiram em 90 dias, um ano ou dois anos. Para garantir a continuidade, é importante estender o sinal de acesso pessoal ou criar um novo. Em seguida, use a automação para aplicar o novo sinal de acesso pessoal a todos os laboratórios.
- **Restringir as alterações a uma definição**de laboratório : Muitas vezes, uma determinada definição deve ser restringida (como permitir a utilização de imagens de mercado). Pode utilizar a Política Azure para evitar alterações num tipo de recurso. Ou pode criar um papel personalizado, e conceder aos utilizadores esse papel em vez do papel do *proprietário* para o laboratório. Pode fazê-lo para a maioria das configurações do laboratório (suporte interno, anúncio de laboratório, tamanhos de VM permitidos, e assim por diante).
- Exijam que os **VMs sigam uma convenção**de nomeação : Os gestores geralmente querem identificar facilmente VMs que fazem parte de um ambiente de desenvolvimento e teste baseado na nuvem. Pode fazê-lo utilizando a [Política Azure.](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)

É importante notar que a DevTest Labs utiliza recursos Azure subjacentes que são geridos da mesma forma: networking, discos, computação, e assim por diante. Por exemplo, a Política Azure aplica-se a máquinas virtuais que são criadas num laboratório. O Centro de Segurança Azure pode reportar sobre a conformidade com a VM. E o serviço de backup Azure pode fornecer cópias de segurança regulares para os VMs no laboratório.

## <a name="security-considerations"></a>Considerações de segurança
A Azure DevTest Labs utiliza os recursos existentes em Azure (computação, networking, e assim por diante). Assim, beneficia automaticamente das funcionalidades de segurança que são incorporadas na plataforma. Por exemplo, para exigir que as ligações remotas de ambiente de trabalho sejam originárias apenas da rede corporativa, basta adicionar um grupo de segurança de rede à rede virtual no gateway remoto do ambiente de trabalho. A única consideração adicional de segurança é o nível de permissões que concedes aos membros da equipa que usam os laboratórios no dia-a-dia. As permissões mais comuns são o proprietário e o [ *owner* *utilizador.*](devtest-lab-add-devtest-user.md) Para obter mais informações sobre estas funções, consulte [Adicionar proprietários e utilizadores em Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Dimensione a sua infraestrutura Azure DevTest Labs](devtest-lab-guidance-scale.md).

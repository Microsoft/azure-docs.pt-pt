---
title: Arquitetura de referência de Enterprise para Azure DevTest Labs
description: Este artigo fornece orientações de arquitetura de referência para o Azure DevTest Labs numa empresa.
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
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705879"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Arquitetura de referência do Azure DevTest Labs para empresas
Este artigo fornece uma arquitetura de referência para ajudar a implementar uma solução baseada no Azure DevTest Labs numa empresa. Ele inclui o seguinte:
- Conectividade no local através do ExpressRoute do Azure
- Um gateway de ambiente de trabalho remoto para iniciar sessão remotamente em máquinas virtuais
- Conectividade para um repositório de artefactos para artefactos privados
- Outros serviços de PaaS que são utilizados nos laboratórios

![Diagrama de arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os principais elementos de arquitetura de referência:

- **Azure Active Directory (Azure AD)**: DevTest Labs utiliza a [serviço do Azure AD para a gestão de identidade](../active-directory/fundamentals/active-directory-whatis.md). Quando concede aos utilizadores acesso a um ambiente com base no DevTest Labs, considere estes dois aspetos fundamentais:
    - **Gestão de recursos**: Ele fornece acesso ao portal do Azure para gerir os recursos (criar máquinas virtuais; criar ambientes; iniciar, parar, reiniciar, eliminar e aplicam-se de artefactos; e assim por diante). Gestão de recursos é feita no Azure utilizando o controlo de acesso baseado em funções (RBAC). Atribuir funções a utilizadores e definir permissões de nível de acesso e de recursos.
    - **Máquinas virtuais (nível de rede)**: Na configuração padrão, as máquinas virtuais utilizam uma conta de administrador local. Se existir um domínio disponível ([Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), um domínio no local ou um domínio com base na cloud), as máquinas que podem ser associadas ao domínio. Os utilizadores, em seguida, podem utilizar suas identidades baseado no domínio para ligar às VMs.
- **Conectividade no local**: No nosso diagrama de arquitetura [ExpressRoute](../expressroute/expressroute-introduction.md) é utilizado. Mas também pode utilizar um [VPN site a site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora o ExpressRoute não é necessário para o DevTest Labs, é normalmente utilizada nas empresas. O ExpressRoute é necessário apenas se precisar de acesso aos recursos empresariais. Cenários comuns são:
    - Tem dados no local que não podem ser movidos para a cloud.
    - Prefere associar máquinas de virtuais do laboratório ao domínio no local.
    - Quer forçar todo o tráfego de rede e para o ambiente de nuvem por meio de uma firewall no local de segurança/conformidade.
- **Grupos de segurança de rede**: Uma forma comum de restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de cloud) com base na origem e endereços IP de destino está a utilizar um [grupo de segurança de rede](../virtual-network/security-overview.md). Por exemplo, pretende permitir apenas o tráfego que provém da rede da empresa em redes do laboratório.
- **Gateway de ambiente de trabalho remoto**: As empresas normalmente bloqueiam ligações de ambiente de trabalho remotas saída à firewall Corporativo. Existem várias opções para permitir a conectividade para o ambiente com base na cloud no DevTest Labs, incluindo:
  - Utilize um [gateway de ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e lista de permissões o endereço IP estático do gateway de Balanceador de carga.
  - [Direcionar todo o tráfego de entrada da RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) através da ligação de ExpressRoute/site-site VPN. Esta funcionalidade é uma consideração comuns quando as empresas a planejar uma implantação do DevTest Labs.
- **Serviços (redes virtuais, sub-redes) de rede**: O [redes do Azure](../networking/networking-overview.md) topologia é outro elemento essencial da arquitetura do DevTest Labs. Controla se os recursos do laboratório podem se comunicar e ter acesso a aplicações no local e na internet. Nosso diagrama de arquitetura inclui as formas mais comuns que os clientes utilizem o DevTest Labs: Todos os laboratórios ligam através de [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md) ao utilizar um [modelo hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a ligação de ExpressRoute/site-site VPN no local. Mas o DevTest Labs utiliza a rede Virtual do Azure diretamente, portanto, não há restrições sobre como configurar a infraestrutura de rede.
- **Os laboratórios DevTest**:  DevTest Labs é uma parte essencial da arquitetura geral. Para saber mais sobre o serviço, veja [sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**:  Máquinas virtuais são uma carga de trabalho principais que suporte o DevTest Labs, juntamente com outros recursos do Azure. Laboratórios Dev/Test torna fácil e rápida para uma empresa fornecer acesso aos recursos do Azure (incluindo as VMs e outros recursos do Azure). Saiba mais sobre o acesso ao Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerações de escalabilidade
Embora não tem o DevTest Labs incorporadas quotas ou limites, outros recursos do Azure que são utilizados na operação típica de um laboratório ter [quotas de nível de assinatura](../azure-subscription-service-limits.md). Então, numa implementação empresarial típica, terá várias subscrições do Azure para abranger uma grande implantação de DevTest Labs. As quotas de que as empresas a alcançar mais comumente são:

- **Grupos de recursos**: Na configuração predefinida, DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou o utilizador cria um ambiente com o serviço. As assinaturas podem conter [até 980 grupos de recursos](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Por isso, o que é o limite de máquinas virtuais e ambientes numa subscrição. Existem duas outras configurações que deve considerar:
    - **[Todas as máquinas virtuais aceder ao mesmo grupo de recursos](resource-group-control.md)**: Embora esta configuração ajuda-o a cumprir o limite do grupo de recursos, afeta o limite de tipo de recurso por grupo recursos.
    - **Utilizar a partilhado IPs públicos**: Todas as VMs do mesmo tamanho e região entram no mesmo grupo de recursos. Esta configuração é um "meio-termo" entre as quotas de grupo de recursos e quotas de tipo de recurso por grupo recursos, se as máquinas virtuais estão autorizadas a ter endereços IP públicos.
- **Grupo de recursos por recurso por tipo de recurso**: O limite predefinido para [recursos por grupo de recursos por tipo de recurso é 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando utiliza a *todas as VMs Ir para o mesmo grupo de recursos* configuração, o impacto de utilizadores desta subscrição limitar muito antes, especialmente se as VMs têm muitos discos extras.
- **Contas de armazenamento**: Um laboratório no DevTest Labs é fornecido com uma conta de armazenamento. A quota do Azure para [número de contas de armazenamento por região por subscrição é 250](../azure-subscription-service-limits.md#storage-limits). O número máximo de DevTest Labs na mesma região também é 250.
- **Atribuições de funções**: Uma atribuição de função é como dar a um utilizador ou principal de acesso a um recurso (proprietário, recursos, nível de permissão). No Azure, há uma [limite de 2.000 atribuições de funções por subscrição](../azure-subscription-service-limits.md#role-based-access-control-limits). Por predefinição, o serviço de DevTest Labs cria um grupo de recursos para cada VM. O proprietário é concedido *proprietário* permissão para a VM de laboratórios Dev/Test e *leitor* permissão para o grupo de recursos. Dessa forma, cada VM nova que tenha criado utiliza duas atribuições de funções, além das atribuições que são utilizadas quando concede aos utilizadores permissão para o laboratório.
- **Leituras/escritas da API**: Existem várias formas para automatizar o Azure e o DevTest Labs, incluindo REST APIs, PowerShell, CLI do Azure e SDK do Azure. Através da automatização, que pode usar outro limite de pedidos da API: Cada subscrição permite até [ler de 12 000 pedidos por hora de escrita de pedidos e 1200](../azure-resource-manager/resource-manager-request-limits.md). Tenha em atenção este limite quando é automatizar o DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão
DevTest Labs tem uma interface de utilizador administrativo excelente para trabalhar com um único laboratório. Mas, numa empresa, provavelmente terá várias subscrições do Azure e muitos laboratórios. Fazer alterações de forma consistente em todos os seus laboratórios opções requer automação/criação de scripts. Aqui estão alguns exemplos e melhores práticas de gestão para uma implementação de DevTest Labs:

- **Alterações às definições de laboratório**: Um cenário comum é atualizar uma configuração de laboratório específico em todos os laboratórios na implementação. Por exemplo, um novo tamanho de instância VM está disponível e todos os laboratórios têm de ser atualizados para permitir que ele. É melhor automatizar essas alterações utilizando scripts do PowerShell, CLI ou REST APIs.  
- **Token de acesso pessoal do repositório de artefactos**:  Normalmente, os tokens de acesso pessoal para um repositório de Git expirarem dentro de 90 dias, um ano ou dois anos. Para assegurar a continuidade, é importante estender o token de acesso pessoal ou criar um novo. Em seguida, utilize a automatização para aplicar o novo token de acesso pessoal para todos os laboratórios.
- **Restringir alterações ao ambiente de laboratório**: Muitas vezes, uma determinada definição tem de ser restrita (por exemplo, permitindo a utilização de imagens do marketplace). Pode utilizar o Azure Policy para impedir alterações para um tipo de recurso. Ou pode criar uma função personalizada e conceder aos utilizadores essa função, em vez do *proprietário* função para o laboratório. Pode fazê-lo para a maioria das definições no laboratório (suporte interno, anúncio de laboratório, permitido tamanhos de VM e assim por diante).
- **Exigir VMs a seguir uma convenção de nomenclatura**: Gerentes normalmente pretendem identificar facilmente as VMs que fazem parte de um ambiente de teste e desenvolvimento com base na cloud. Pode fazer isso usando [do Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante observar que o DevTest Labs utiliza recursos do Azure subjacentes que são geridos da mesma forma: rede, discos, computação e assim por diante. Por exemplo, o Azure Policy aplica-se a máquinas virtuais que são criadas num laboratório. Centro de segurança do Azure pode reportar a conformidade de VM. E o serviço Azure Backup pode fornecer cópias de segurança regulares para as VMs no laboratório.

## <a name="security-considerations"></a>Considerações de segurança
O Azure DevTest Labs utiliza recursos existentes no Azure (computação, rede e assim por diante). Por isso, ele automaticamente se beneficia dos recursos de segurança que são incorporados na plataforma. Por exemplo, para exigir ligações de ambiente de trabalho remotas recebidas para originar apenas a partir da rede empresarial, basta adicione um grupo de segurança de rede para a rede virtual no gateway de ambiente de trabalho remoto. A consideração de segurança apenas adicional é o nível de permissões que concede aos membros da Equipe que usam os laboratórios diariamente. As permissões mais comuns são [ *proprietário* e *utilizador*](devtest-lab-add-devtest-user.md). Para obter mais informações sobre estas funções, consulte [adicionar proprietários e utilizadores no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Passos Seguintes
Consulte o artigo seguinte nesta série: [Dimensionar a sua infraestrutura do Azure DevTest Labs](devtest-lab-guidance-scale.md).

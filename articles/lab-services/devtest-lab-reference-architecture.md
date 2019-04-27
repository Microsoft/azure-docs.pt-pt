---
title: Arquitetura de referência para o Azure DevTest Labs para uma empresa
description: Este artigo fornece uma orientação de arquitetura de referência para o Azure DevTest Labs numa empresa.
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
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561528"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>O Azure DevTest Labs - arquitetura de referência para uma empresa
Este artigo fornece uma arquitetura de referência para implantar uma solução baseada no Azure DevTest Labs numa empresa. Ele inclui a conectividade no local através do Expressroute, um gateway de ambiente de trabalho remoto, início de sessão remotamente em máquinas virtuais, a ligação a um repositório de artefactos para artefactos privados e outros serviços de PaaS usados num laboratório.

![Arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Os elementos essenciais numa arquitetura de referência são:

- **O Azure Active Directory (AAD)**: O Azure DevTest Labs utiliza a [serviço do Azure Active Directory para gestão de identidades](../active-directory/fundamentals/active-directory-whatis.md). Existem duas coisas principais a considerar no fornecimento de acesso a um ambiente com base no DevTest Labs para os utilizadores:
    - **Gestão de recursos**:  Ele fornece acesso ao portal do Azure para gerir os recursos (criar máquinas virtuais, criar ambientes, iniciar/parar/reinício/delete/applyartifacts e assim por diante). Isso é feito no Azure utilizando o controlo de acesso baseado em Roble (RBAC) e ao aplicar uma atribuição de função para o utilizador, o recurso de configuração e permissões de nível de acesso.
    - **Máquinas virtuais (nível de rede)**:  Na configuração padrão, as máquinas virtuais utilizam uma conta de administrador local.  Se existir um domínio disponível ([serviços de domínio do AAD](../active-directory-domain-services/active-directory-ds-overview.md), um domínio no local ou um domínio com base na cloud), as máquinas que podem ser associadas ao domínio. Depois de combinado, os utilizadores usaria suas identidades baseados em domínio, para ligar às VMs.
- **Conectividade no local**: No diagrama de arquitetura acima, [Express Route](../expressroute/expressroute-introduction.md) i utilizado, mas também pode utilizar um [VPN Site a Site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora não seja necessário para laboratórios Dev/Test, é frequentemente visto em empresas. É necessário apenas se há um motivo para se ligar aos recursos empresariais. Motivos comuns são: 
    - Dados que não podem ser movidos para a cloud mais ainda no local
    - Preferência para associar máquinas de virtuais do laboratório ao domínio no local
    - Forçar todo o tráfego de rede e para o ambiente de nuvem por meio de uma firewall no local por motivos de segurança/conformidade
- **Grupos de segurança de rede**: Uma forma comum de restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de cloud) com base na origem e endereços IP de destino está a utilizar um [grupo de segurança de rede](../virtual-network/security-overview.md). Por exemplo, permitindo que apenas o tráfego de rede com a origem da rede empresarial em redes do laboratório.
- **Gateway de ambiente de trabalho remoto**:  As empresas normalmente bloqueiam ligações de ambiente de trabalho remotas saída à firewall Corporativo. Para ativar a conectividade para o ambiente com base na cloud no DevTest Labs, existem várias opções, como a utilização uma [gateway de ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (lista branca de IP estático para o Balanceador de carga de gateway) ou [direcionar todos recebidos Tráfego RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) através da ligação de Express Route/Site-Site VPN. É uma consideração comuns ao planear uma implementação do DevTest Labs na empresa.
- **Redes do Azure (VNets, sub-redes)**:  O [redes do Azure](../networking/networking-overview.md) topologia é outro elemento essencial da arquitetura geral do DevTest Labs. Ele permite que os recursos de laboratórios para comunicar (ou não), acesso para o local (ou não) e de acesso à internet (ou não). O diagrama de arquitetura inclui a forma mais comum que os clientes estão a utilizar os laboratórios DevTest (ligados através de todos os laboratórios [VNet Peering](../virtual-network/virtual-network-peering-overview.md) usando um [modelo hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a ligação de Express Route/Site-Site VPN para o local), mas, desde o DevTest Labs utiliza redes do Azure diretamente não existem quaisquer restrições sobre como configurar a infraestrutura de rede.
- **Os laboratórios DevTest**:  DevTest Labs é uma parte essencial da arquitetura geral. Para saber mais sobre o serviço, veja [sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**:  Uma das cargas de trabalho importantes, suportadas pela DevTest Labs são máquinas de virtuais juntamente com outros recursos do Azure.  Laboratórios Dev/Test torna mais fácil e rápida para uma empresa dar acesso aos recursos do Azure (incluindo máquinas virtuais e outros recursos do Azure).  Saiba mais sobre o acesso ao Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerações de escalabilidade
Embora o DevTest Labs não tem qualquer incorporadas quotas ou limites, outros recursos do Azure são utilizados em típica tem operações de um laboratório [quotas de nível de assinatura](../azure-subscription-service-limits.md). Como resultado, numa implementação empresarial típica, tem de ter várias subscrições do Azure para abranger uma grande implantação de DevTest Labs. As quotas atingidas mais comum que as empresas são:

- **Grupos de recursos**:  Na configuração predefinida, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou um ambiente que um utilizador cria com o serviço. As assinaturas podem conter um [máximo de grupos de recursos 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), pelo que este limite é o limite superior de máquinas virtuais e ambientes numa subscrição. Existem duas outras configurações que deve considerar:
    - **[Todas as máquinas virtuais aceder ao mesmo grupo de recursos](resource-group-control.md)**:  Embora ajuda-o com o limite do grupo de recursos, tem impacto sobre o tipo de recurso por limite de grupo de recursos.
    - **Utilizar a partilhado IPs públicos**:  Todas as VMs do mesmo tamanho e na mesma região entram no mesmo grupo de recursos. É um "meio-termo" entre as quotas de grupo de recursos e o tipo de recurso por quotas de grupo de recursos se as máquinas virtuais estão autorizadas a ter endereços IP públicos. 
- **Grupo de recursos por recurso por tipo de recurso**: O limite predefinido para [recursos por grupo de recurso por tipo de recurso é 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando todas as VMs a utilizar for a mesma configuração do grupo de recursos, os utilizadores irão aceder este limite de subscrição muito mais cedo, especialmente se as VMs têm muitos discos extras.
- **Contas de armazenamento**: Um laboratório no DevTest Labs é fornecido com uma conta de armazenamento e a quota do Azure para [número de contas de armazenamento por região por subscrição é 250](../azure-subscription-service-limits.md#storage-limits). Isso significa que o limite superior para o número de DevTest Labs na mesma região também é 250.
- **Atribuições de funções**: Uma atribuição de função é como conceder um acesso de utilizador ou principal a um recurso (proprietário, recursos, nível de permissão). No Azure, há uma [limite de atribuições de funções de 2000 por subscrição](../azure-subscription-service-limits.md#role-based-access-control-limits). O serviço de laboratórios DevTest (na configuração padrão) cria um grupo de recursos para cada VM e o proprietário vai ser concedido a **proprietário** permissão para a VM de laboratórios Dev/Test e **leitor** permissão para o grupo de recursos.  Dessa forma, cada nova VM criada utiliza duas atribuições de função adicionais para atribuições de funções criadas quando a dar aos utilizadores permissão para o laboratório.
- **Leituras/escritas da API**: Existem várias formas (REST APIs, PowerShell, CLI, SDK do Azure e assim por diante) para automatizar o Azure e o DevTest Labs e com a automação é possível atingir o limite de outro nos pedidos de API. Cada subscrição permite até [12000 ler pedidos por hora de escrita de pedidos e 1200](../azure-resource-manager/resource-manager-request-limits.md).  É um limite para ser considerado ao automatizar o DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão
DevTest Labs tem uma interface de utilizador administrativo excelente ao trabalhar com um único laboratório. Numa empresa, provavelmente existem várias subscrições do Azure e muitos laboratórios. Isso significa que efetuar alterações para todos os laboratórios consistentemente requer scripts/Automatização.  Aqui estão alguns exemplos e melhores maneiras de gerenciar a implantação do DevTest Labs:

- **Alterações às definições de laboratório**: Um cenário comum é atualizar uma configuração de laboratório específico em todos os laboratórios na implementação. Por exemplo, um novo tamanho de instância VM está disponível e todos os laboratórios têm de ser atualizados para permitir que ele.  É melhor automatizar essas alterações utilizando scripts do Azure PowerShell, CLI do Azure ou REST APIs.  
- **Token de acesso pessoal do repositório de artefactos**:  Normalmente, os tokens de acesso pessoal para um repositório de Git expirarem (90 dias, 1 ano, 2 yrs). Para assegurar a continuidade, é importante estender o token de acesso pessoal ou criar uma nova e utilizar a automatização para aplicar o novo token de acesso pessoal para todos os laboratórios.
- **Restringir alterações a uma configuração de laboratório**:  Ele é muitas vezes o caso onde existe uma definição específica (por exemplo, que permite imagens do Marketplace para ser utilizado) que deve ser restrita. Isso pode ser feito através da política do Azure (alterações a impedir a esse tipo de recurso) ou ao criar uma função personalizada e conceder essa função em vez de "proprietário" para o laboratório. Isso pode ser feito para a maioria das definições no laboratório (suporte interno, anúncio de laboratório, tamanhos de VM permitidos e assim por diante)
- **Exigir que as VMs seguem uma convenção de nomenclatura**: É uma solicitação comum para o identificar facilmente as VMs que fazem parte do desenvolvimento com base na cloud e ambiente de teste. Pode fazê-lo [com o Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante observar que o DevTest Labs usa recursos subjacentes no Azure (sistema de rede, discos, computação, assim por diante), que são geridos da mesma maneira no Azure.  Por exemplo, o Azure policy se aplica a máquinas virtuais criadas no laboratório. O Centro de segurança do Azure pode reportar a conformidade de VM. O serviço de cópia de segurança do Azure pode fornecer cópias de segurança regulares para VMs do laboratório e assim por diante. 

## <a name="security-considerations"></a>Considerações de segurança
O Azure DevTest Labs utiliza recursos existentes no Azure (computação, rede e assim por diante) e, portanto, automaticamente se beneficia de todas as funcionalidades de segurança excelentes incorporadas na plataforma. Por exemplo, para proteger quaisquer ligações de ambiente de trabalho remotas de entrada para apenas ser originado da rede empresarial, é tão simples quanto adicionar um grupo de segurança de rede para a rede virtual no gateway de ambiente de trabalho remoto. A consideração de segurança adicionais apenas para o Azure DevTest Labs é o nível de permissões fornecida a membros de equipe que irão utilizar os laboratórios de forma do dia a dia.  São as permissões comuns dadas ["Proprietário" e "Utilizador de DevTest Labs"](devtest-lab-add-devtest-user.md). Para obter mais informações sobre estas funções, consulte [adicionar proprietários e utilizadores no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Próximos Passos
Consulte o artigo seguinte nesta série: [Dimensionar a sua infraestrutura do Azure DevTest Labs](devtest-lab-guidance-scale.md)

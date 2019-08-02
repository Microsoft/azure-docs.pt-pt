---
title: Práticas recomendadas de segurança para proteger seus ativos-Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: e815c038cce2e792fe7ea39f1ee43342be0b679f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726903"
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de segurança operacional do Azure
Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.

As práticas recomendadas baseiam-se em um consenso de opinião e funcionam com os recursos atuais da plataforma Azure e os conjuntos de recursos. As opiniões e tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implantar práticas fortes de segurança operacional
A segurança operacional do Azure refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Azure. A segurança operacional do Azure é criada em uma estrutura que incorpora o conhecimento obtido por meio de recursos que são exclusivos à Microsoft, incluindo o [SDL (ciclo de vida de desenvolvimento da segurança)](https://www.microsoft.com/sdl), o [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programa e conscientização profunda do panorama de ameaças do segurança cibernética.

## <a name="manage-and-monitor-user-passwords"></a>Gerenciar e monitorar senhas de usuário
A tabela a seguir lista algumas das práticas recomendadas relacionadas ao gerenciamento de senhas de usuário:

**Prática recomendada**: Verifique se você tem o nível apropriado de proteção por senha na nuvem.   
**Detalhe**: Siga as orientações em [diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/), que tem como escopo os usuários das plataformas de identidade da microsoft (Azure Active Directory, Active Directory e conta Microsoft).

**Prática recomendada**: Monitorar ações suspeitas relacionadas às suas contas de usuário.   
**Detalhe**: Monitore para [usuários em risco](/azure/active-directory/reports-monitoring/concept-user-at-risk) e [entradas arriscadas](../../active-directory/reports-monitoring/concept-risk-events.md) usando os relatórios de segurança do Azure AD.

**Prática recomendada**: Detectar e corrigir automaticamente as senhas de alto risco.   
**Detalhe**: [Azure ad Identity Protection](/azure/active-directory/identity-protection/overview) é um recurso da edição Azure ad Premium P2 que permite:

- Detectar possíveis vulnerabilidades que afetam as identidades da sua organização
- Configurar respostas automatizadas para ações suspeitas detectadas que estão relacionadas às identidades da sua organização
- Investigue incidentes suspeitos e execute as ações apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receber notificações de incidentes da Microsoft
Certifique-se de que sua equipe de operações de segurança receba notificações de incidentes do Azure da Microsoft. Uma notificação de incidente permite que sua equipe de segurança saiba que você comprometeu os recursos do Azure para que eles possam responder rapidamente e corrigir possíveis riscos de segurança.

No portal de registro do Azure, você pode garantir que as informações de contato do administrador incluem detalhes que notificam as operações de segurança. As informações de contato são um endereço de email e um número de telefone.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizar assinaturas do Azure em grupos de gerenciamento
Se sua organização tiver muitas assinaturas, talvez seja necessário uma maneira de gerenciar o acesso, as políticas e a conformidade com eficiência para essas assinaturas. Os [grupos de gerenciamento do Azure](/azure/governance/management-groups/create) fornecem um nível de escopo acima das assinaturas. Você organiza as assinaturas em contêineres chamados grupos de gerenciamento e aplica suas condições de governança aos grupos de gerenciamento. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Você pode criar uma estrutura flexível de grupos de gerenciamento e assinaturas em um diretório. Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. O grupo de gerenciamento raiz permite que as políticas globais e as atribuições de RBAC sejam aplicadas no nível do diretório.

Aqui estão algumas práticas recomendadas para o uso de grupos de gerenciamento:

**Prática recomendada**: Verifique se as novas assinaturas aplicam elementos de governança como políticas e permissões à medida que são adicionadas.   
**Detalhe**: Use o grupo de gerenciamento raiz para atribuir elementos de segurança de toda a empresa que se aplicam a todos os ativos do Azure. Políticas e permissões são exemplos de elementos.

**Prática recomendada**: Alinhe os principais níveis de grupos de gerenciamento com a estratégia de segmentação para fornecer um ponto de controle e consistência de política dentro de cada segmento.   
**Detalhe**: Crie um único grupo de gerenciamento para cada segmento no grupo de gerenciamento raiz. Não crie nenhum outro grupo de gerenciamento na raiz.

**Prática recomendada**: Limite a profundidade do grupo de gerenciamento para evitar confusão que atrasa as operações e a segurança.   
**Detalhe**: Limite sua hierarquia a três níveis, incluindo a raiz.

**Prática recomendada**: Selecione cuidadosamente quais itens aplicar a toda a empresa com o grupo de gerenciamento raiz.   
**Detalhe**: Verifique se os elementos do grupo de gerenciamento raiz têm uma clara necessidade de serem aplicados em cada recurso e se eles têm baixo impacto.

Os bons candidatos incluem:

- Requisitos regulatórios que têm um impacto comercial claro (por exemplo, restrições relacionadas à soberania de dados)
- Os requisitos com um impacto negativo quase zero potencial em operações, como política com efeito de auditoria ou atribuições de permissão de RBAC que foram revisadas cuidadosamente

**Prática recomendada**: Planeje e teste cuidadosamente todas as alterações em toda a empresa no grupo de gerenciamento raiz antes de aplicá-las (política, modelo de RBAC e assim por diante).   
**Detalhe**: As alterações no grupo de gerenciamento raiz podem afetar todos os recursos no Azure. Embora eles forneçam uma maneira poderosa de garantir a consistência em toda a empresa, os erros ou o uso incorreto podem afetar negativamente as operações de produção. Testar todas as alterações no grupo de gerenciamento raiz em um laboratório de teste ou em um piloto de produção.

## <a name="streamline-environment-creation-with-blueprints"></a>Simplifique a criação do ambiente com plantas
[O serviço de plantas do Azure](/azure/governance/blueprints/overview) permite que os arquitetos de nuvem e os grupos de tecnologia da informação central definam um conjunto repetível de recursos do Azure que implementam e aderem aos padrões, padrões e requisitos de uma organização. As plantas do Azure possibilitam que as equipes de desenvolvimento compilem e criem rapidamente novos ambientes com um conjunto de componentes internos e a confiança de que eles estão criando esses ambientes dentro da conformidade organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorar os serviços de armazenamento para alterações inesperadas no comportamento
Diagnosticar e solucionar problemas em um aplicativo distribuído hospedado em um ambiente de nuvem pode ser mais complexo do que em ambientes tradicionais. Os aplicativos podem ser implantados em uma infraestrutura de PaaS ou IaaS, no local, em um dispositivo móvel ou em alguma combinação desses ambientes. O tráfego de rede do aplicativo pode atravessar redes públicas e privadas, e seu aplicativo pode usar várias tecnologias de armazenamento.

Você deve monitorar continuamente os serviços de armazenamento que seu aplicativo usa para quaisquer alterações inesperadas no comportamento (como tempos de resposta mais lentos). Use o registro em log para coletar dados mais detalhados e analisar um problema em profundidade. As informações de diagnóstico obtidas do monitoramento e do log ajudam a determinar a causa raiz do problema encontrado no aplicativo. Em seguida, você pode solucionar o problema e determinar as etapas apropriadas para corrigi-lo.

[Análise de armazenamento do Azure](../../storage/common/storage-analytics.md) executa o registro em log e fornece dados de métrica para uma conta de armazenamento do Azure. Recomendamos que você use esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenir, detectar e responder a ameaças
A [central de segurança do Azure](../../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças, fornecendo maior visibilidade sobre (e controlar) a segurança dos recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com várias soluções de segurança.

A camada gratuita da central de segurança oferece segurança limitada apenas para os recursos do Azure. A camada Standard estende esses recursos para locais e outras nuvens. A central de segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando análise e inteligência e responder rapidamente quando sob ataque. Pode experimentar o Centro de Segurança Standard sem custos durante os primeiros 60 dias. Recomendamos que você [Atualize sua assinatura do Azure para a central de segurança Standard](../../security-center/security-center-get-started.md).

Use a central de segurança para obter uma exibição central do estado de segurança de todos os seus recursos do Azure. Em um relance, verifique se os controles de segurança apropriados estão em vigor e configurados corretamente e identifique rapidamente os recursos que precisam de atenção.

A central de segurança também se integra à [ATP (proteção avançada contra ameaças) do Windows Defender](../../security-center/security-center-wdatp.md), que fornece recursos de EDR (detecção e resposta de ponto de extremidade) abrangentes. Com a integração do Windows Defender ATP, você pode identificar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela central de segurança.

Quase todas as organizações empresariais têm um sistema SIEM (gerenciamento de informações e eventos de segurança) para ajudar a identificar ameaças emergentes, consolidando informações de log de diferentes dispositivos de coleta de sinais. Os logs são então analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todas as soluções de coleta e análise de logs.

O [Azure Sentinel](/azure/sentinel/overview) é uma solução de disparar (gerenciamento de informações de segurança e segurança) escalonável, nativa de nuvem, de Siem (Information and Event Management). O Azure Sentinel fornece análise de segurança inteligente e inteligência contra ameaças por meio de detecção de alertas, visibilidade de ameaças, busca proativa e resposta automatizada contra ameaças.

Aqui estão algumas práticas recomendadas para impedir, detectar e responder a ameaças:

**Prática recomendada**: Aumente a velocidade e a escalabilidade de sua solução SIEM usando um SIEM baseado em nuvem.   
**Detalhe**: Investigue os recursos e as capacidades do [Azure Sentinel](/azure/sentinel/overview) e compare-os com os recursos do que você está usando no momento no local. Considere a adoção do Azure Sentinel se ele atender aos requisitos de SIEM de sua organização.

**Prática recomendada**: Encontre as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação.   
**Detalhe**: Examine sua [Pontuação de segurança do Azure](../../security-center/security-center-secure-score.md) para ver as recomendações resultantes das políticas e iniciativas do Azure incorporadas à central de segurança do Azure. Essas recomendações ajudam a resolver os principais riscos, como atualizações de segurança, proteção de ponto de extremidade, criptografia, configurações de segurança, WAF ausentes, VMs conectadas à Internet e muito mais.

A pontuação segura, que é baseada em controles de CIS (Center for Internet Security), permite que você benchmark a segurança do Azure de sua organização em relação a fontes externas. A validação externa ajuda a validar e enriquecer a estratégia de segurança da sua equipe.

**Prática recomendada**: Monitore a postura de segurança de máquinas, redes, armazenamento e serviços de dados e aplicativos para descobrir e priorizar possíveis problemas de segurança.  
**Detalhe**: Siga as [recomendações de segurança](../../security-center/security-center-recommendations.md) na central de segurança começando, com os itens de prioridade mais alta.

**Prática recomendada**: Integre alertas da central de segurança à sua solução SIEM (gerenciamento de eventos e informações de segurança).   
**Detalhe**: A maioria das organizações com um SIEM a utiliza como uma câmara de compensação central para alertas de segurança que exigem uma resposta de analista. Os eventos processados produzidos pela central de segurança são publicados no log de atividades do Azure, um dos logs disponíveis por meio de Azure Monitor. O Azure Monitor oferece um pipeline consolidado para roteamento de qualquer um dos seus dados de monitoramento para uma ferramenta SIEM. Consulte [integrar soluções de segurança na central de segurança](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) para obter instruções. Se você estiver usando o Azure Sentinel, consulte [conectar a central de segurança do Azure](../../sentinel/connect-azure-security-center.md).

**Prática recomendada**: Integre os logs do Azure ao SIEM.   
**Detalhe**: Use [Azure monitor para coletar e exportar dados](/azure/azure-monitor/overview.md#integrate-and-export-data). Essa prática é essencial para habilitar a investigação de incidentes de segurança e a retenção de log online é limitada. Se você estiver usando o Azure Sentinel, consulte [conectar fontes de dados](../../sentinel/connect-data-sources.md).

**Prática recomendada**: Acelere seus processos de investigação e busca e reduza os falsos positivos integrando recursos de EDR (detecção de ponto de extremidade e resposta) à sua investigação de ataque.   
**Detalhe**: [Habilite a integração do Windows Defender ATP](../../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) por meio da sua política de segurança da central de segurança. Considere usar o Azure Sentinel para a busca de ameaças e a resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorar o monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure combinando recursos de rede como uma rede virtual, ExpressRoute, gateway de aplicativo e balanceadores de carga. O monitoramento está disponível em cada um dos recursos de rede.

O observador de [rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Use suas ferramentas de diagnóstico e visualização para monitorar e diagnosticar condições em um nível de cenário de rede em, para e do Azure.

Veja a seguir as práticas recomendadas para o monitoramento de rede e as ferramentas disponíveis.

**Prática recomendada**: Automatizar o monitoramento de rede remota com a captura de pacotes.  
**Detalhe**: Monitore e diagnostique problemas de rede sem fazer logon em suas VMs usando o observador de rede. Dispare a [captura de pacotes](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) definindo alertas e tenha acesso a informações de desempenho em tempo real no nível de pacote. Quando se deparar com um problema, pode investigar em pormenor para obter melhores diagnósticos.

**Prática recomendada**: Obter informações sobre o tráfego de rede usando logs de fluxo.  
**Detalhe**: Crie uma compreensão mais profunda dos padrões de tráfego de rede usando [os logs de fluxo do grupo de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). As informações nos logs de fluxo ajudam a coletar dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

**Prática recomendada**: Diagnosticar problemas de conectividade de VPN.  
**Detalhe**: Use o observador de rede para [diagnosticar os problemas mais comuns de gateway de VPN e conexão](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Você não só pode identificar o problema, mas também usar logs detalhados para investigar ainda mais.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implantação segura usando as ferramentas comprovadas do DevOps
Use as seguintes práticas recomendadas do DevOps para garantir que sua empresa e suas equipes sejam produtivas e eficientes.

**Prática recomendada**: Automatize a compilação e a implantação de serviços.  
**Detalhe**: A [Infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas que ajudam os profissionais de ti a removerem a carga da criação e do gerenciamento cotidianos da infraestrutura modular. Ele permite que os profissionais de ti criem e mantenham seu ambiente de servidor moderno de uma maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo.

Você pode usar [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para provisionar seus aplicativos usando um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Você usa o mesmo modelo para implantar repetidamente seu aplicativo em cada estágio do ciclo de vida do aplicativo.

**Prática recomendada**: Crie e implante automaticamente em aplicativos Web ou serviços de nuvem do Azure.  
**Detalhe**: Você pode configurar seu Azure DevOps Projects para [Compilar e implantar automaticamente](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) em aplicativos Web ou serviços de nuvem do Azure. O Azure DevOps implanta automaticamente os binários depois de fazer uma compilação no Azure após cada check-in de código. O processo de compilação do pacote é equivalente ao comando Package no Visual Studio, e as etapas de publicação são equivalentes ao comando publish no Visual Studio.

**Prática recomendada**: Automatize o gerenciamento de liberações.  
**Detalhe**: [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) é uma solução para automatizar a implantação de vários estágios e gerenciar o processo de liberação. Crie pipelines de implantação contínua gerenciados para liberar com rapidez, facilidade e frequência. Com o Azure Pipelines, você pode automatizar o processo de liberação e pode ter fluxos de trabalho de aprovação predefinidos. Implante no local e na nuvem, estenda e personalize conforme necessário.

**Prática recomendada**: Verifique o desempenho do aplicativo antes de iniciá-lo ou implantar atualizações na produção.  
**Detalhe**: Executar [testes de carga](/azure/devops/test/load-test/overview#alternatives) baseados em nuvem para:

- Encontre problemas de desempenho em seu aplicativo.
- Melhorar a qualidade da implantação.
- Certifique-se de que seu aplicativo está sempre disponível.
- Certifique-se de que seu aplicativo possa lidar com o tráfego para sua próxima campanha de marketing ou lançamento.

O [Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de software livre gratuita e popular com um forte suporte de comunidade.

**Prática recomendada**: Monitorar o desempenho do aplicativo.  
**Detalhe**: O [aplicativo Azure](../../azure-monitor/app/app-insights-overview.md) insights é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Use Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os usuários realmente fazem com seu aplicativo. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="mitigate-and-protect-against-ddos"></a>Reduzir e proteger contra DDoS
O DDoS (negação de serviço distribuído) é um tipo de ataque que tenta esgotar os recursos do aplicativo. O objetivo é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Esses ataques estão se tornando mais sofisticados e maiores em tamanho e impacto. Eles podem ser direcionados a qualquer ponto de extremidade publicamente acessível pela Internet.

A criação e a criação de resiliência de DDoS exigem planejamento e design para uma variedade de modos de falha. A seguir estão as práticas recomendadas para a criação de serviços resistentes a DDoS no Azure.

**Prática recomendada**: Garanta que a segurança seja uma prioridade em todo o ciclo de vida de um aplicativo, desde o design e a implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitem um volume relativamente baixo de solicitações para usar muitos recursos, resultando em uma interrupção do serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura do aplicativo e concentrar-se nos [cinco pilares da qualidade do software](https://docs.microsoft.com/azure/architecture/guide/pillars). Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

Garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionada ao próprio aplicativo é o mais importante. A segurança e a privacidade são criadas na plataforma Azure, começando com o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). O SDL aborda a segurança em cada fase de desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

**Prática recomendada**: Projete seus aplicativos para [dimensionar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente no caso de um ataque de DDoS. Se seu aplicativo depende de uma única instância de um serviço, ele cria um ponto único de falha. O provisionamento de várias instâncias torna seu sistema mais resiliente e mais escalonável.  
**Detalhe**: Para [Azure app serviço](/azure/app-service/app-service-value-prop-what-is), selecione um [plano do serviço de aplicativo](../../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para os serviços de nuvem do Azure, configure cada uma de suas funções para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md).

Para [máquinas virtuais do Azure](/azure/virtual-machines/windows/overview), verifique se sua arquitetura de VM inclui mais de uma VM e se cada VM está incluída em um [conjunto de disponibilidade](/azure/virtual-machines/virtual-machines-windows-manage-availability). É recomendável usar conjuntos de dimensionamento de máquinas virtuais para recursos de dimensionamento automático.

**Prática recomendada**: A disposição em camadas de defesas de segurança em um aplicativo reduz a chance de um ataque bem-sucedido. Implemente designs seguros para seus aplicativos usando os recursos internos da plataforma Azure.  
**Detalhe**: O risco de ataque aumenta com o tamanho (área da superfície) do aplicativo. Você pode reduzir a área da superfície usando a lista de permissões para fechar o espaço de endereço IP exposto e portas de escuta que não são necessárias nos balanceadores de carga ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [aplicativo Azure gateway](/azure/application-gateway/application-gateway-create-probe-portal)).

Os [grupos de segurança de rede](../../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Você pode usar [marcas de serviço](../../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança de rede, como uma extensão natural da estrutura de um aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Essa prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego de serviço do Azure de uma rede virtual usa endereços IP públicos como endereços IP de origem por padrão.

O uso de [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) alterna o tráfego de serviço para usar endereços privados de rede virtual como os endereços IP de origem quando eles estão acessando o serviço do Azure de uma rede virtual.

Frequentemente vemos os recursos locais dos clientes sendo atacados junto com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, minimize a exposição de recursos locais à Internet pública.

O Azure tem duas [ofertas de serviço](../../virtual-network/ddos-protection-overview.md) de DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada ao Azure por padrão sem custo adicional. A escala e a capacidade da rede do Azure implantada globalmente fornecem defesa contra ataques comuns de camada de rede por meio do monitoramento de tráfego AlwaysOn e mitigação em tempo real. O básico não requer nenhuma configuração de usuário ou alteração de aplicativo e ajuda a proteger todos os serviços do Azure, incluindo serviços PaaS, como o DNS do Azure.
- A proteção padrão fornece recursos avançados de mitigação de DDoS contra ataques de rede. Ele é ajustado automaticamente para proteger seus recursos específicos do Azure. A proteção é simples de habilitar durante a criação de redes virtuais. Ele também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou de recurso.

## <a name="enable-azure-policy"></a>Habilitar Azure Policy
[Azure Policy](/azure/governance/policy/overview) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem regras e efeitos sobre seus recursos, para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. O Azure Policy atende a essa necessidade através da avaliação de seus recursos de não conformidade com as políticas atribuídas.

Habilite Azure Policy para monitorar e impor a política de escrita da sua organização. Isso garantirá a conformidade com os requisitos de sua empresa ou de segurança regulatória gerenciando centralmente políticas de segurança em suas cargas de trabalho de nuvem híbrida. Saiba como [criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md). Consulte [estrutura de definição de Azure Policy](../../governance/policy/concepts/definition-structure.md) para obter uma visão geral dos elementos de uma política.

Aqui estão algumas práticas recomendadas de segurança a serem seguidas depois que você adotar Azure Policy:

**Prática recomendada**: A política dá suporte a vários tipos de efeitos. Você pode ler sobre eles na [estrutura de definição de Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). As operações de negócios podem ser afetadas negativamente pelo efeito de **negação** e o efeito de **correção** , portanto, comece com o efeito de **auditoria** para limitar o risco de impacto negativo da política.   
**Detalhe**: [Inicie as implantações de política no modo de auditoria](../../governance/policy/concepts/definition-structure.md#policy-rule) e, posteriormente, progresso para **negar** ou **corrigir**. Teste e examine os resultados do efeito de auditoria antes de mover para **negar** ou **corrigir**.

Para obter mais informações, consulte [criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md).

**Prática recomendada**: Identifique as funções responsáveis pelo monitoramento de violações de política e garanta que a ação de correção correta seja executada rapidamente.   
**Detalhe**: Ter a conformidade do monitor de função atribuída por meio da [portal do Azure](../../governance/policy/how-to/get-compliance-data.md#portal) ou por meio da [linha de comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Prática recomendada**: Azure Policy é uma representação técnica das políticas escritas de uma organização. Mapeie todas as políticas do Azure para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: O mapeamento de documentos na documentação da sua organização ou na própria política do Azure adicionando uma referência à política organizacional na descrição da [política](../../governance/policy/concepts/definition-structure.md#display-name-and-description) do Azure ou na descrição da [iniciativa](../../governance/policy/concepts/definition-structure.md#initiatives) de política do Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorar relatórios de risco do Azure AD
A grande maioria das violações de segurança ocorre quando os invasores têm acesso a um ambiente, roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure AD usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar ações suspeitas relacionadas às suas contas de usuário. Cada ação suspeita detectada é armazenada em um registro chamado [evento de risco](../../active-directory/reports-monitoring/concept-risk-events.md). Os eventos de risco são registrados nos relatórios de segurança do Azure AD. Para obter mais informações, leia sobre o [relatório de segurança de usuários em risco](../../active-directory/reports-monitoring/concept-user-at-risk.md) e o [relatório de segurança de entradas arriscadas](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passos Seguintes
Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre segurança do Azure e serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre o mais recente na segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email parasecure@microsoft.com

---
title: Boas práticas de segurança para os seus ativos Azure
titleSuffix: Azure security
description: Este artigo fornece um conjunto de boas práticas operacionais para proteger os seus dados, aplicações e outros ativos no Azure.
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
ms.openlocfilehash: 5724a9e4308f05a82df84ae6a7d5602747f5a140
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757368"
---
# <a name="azure-operational-security-best-practices"></a>Boas práticas de Segurança Operacional Azure
Este artigo fornece um conjunto de boas práticas operacionais para proteger os seus dados, aplicações e outros ativos no Azure.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades atuais da plataforma Azure e conjuntos de funcionalidades. As opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implementar fortes práticas de segurança operacional
A segurança operacional azure refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Azure. A segurança operacional azure baseia-se num quadro que incorpora o conhecimento adquirido através de capacidades exclusivas da Microsoft, incluindo o Ciclo de Vida de Desenvolvimento de [Segurança (SDL),](https://www.microsoft.com/sdl)o programa [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e uma profunda consciência do panorama das ameaças à cibersegurança.

## <a name="manage-and-monitor-user-passwords"></a>Gerir e monitorizar as palavras-passe dos utilizadores
A tabela seguinte enumera algumas boas práticas relacionadas com a gestão das palavras-passe dos utilizadores:

**Boas práticas**: Certifique-se de que tem o nível adequado de proteção de senhas na nuvem.   
**Detalhe**: Siga as orientações na [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), que é direcionada para os utilizadores das plataformas identitárias da Microsoft (Diretório Ativo Azure, Diretório Ativo e conta Microsoft).

**Boas práticas**: Monitorize para ações suspeitas relacionadas com as suas contas de utilizador.   
**Detalhe**: Monitor para [utilizadores em risco](/azure/active-directory/reports-monitoring/concept-user-at-risk) e [inscrições arriscadas](../../active-directory/reports-monitoring/concept-risk-events.md) utilizando relatórios de segurança da AD Azure.

**Boas práticas**: Detete e readismeta automaticamente palavras-passe de alto risco.   
**Detalhe**: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) é uma característica da edição Azure AD Premium P2 que lhe permite:

- Detete potenciais vulnerabilidades que afetam as identidades da sua organização
- Configure respostas automatizadas a ações suspeitas detetadas que estejam relacionadas com as identidades da sua organização
- Investigue incidentes suspeitos e tome as medidas apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receba notificações de incidentes da Microsoft
Certifique-se de que a sua equipa de operações de segurança recebe notificações de incidentes do Azure da Microsoft. Uma notificação de incidente supõe que a sua equipa de segurança tenha comprometido os recursos do Azure para que possam responder e remediar rapidamente potenciais riscos de segurança.

No portal de inscrição do Azure, pode garantir que as informações de contacto da administração incluem detalhes que notificam as operações de segurança. A informação de contacto é um endereço de e-mail e número de telefone.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizar assinaturas do Azure em grupos de gestão
Se a sua organização tiver muitas subscrições, poderá precisar de uma forma de gerir o acesso, as políticas e a conformidade dessas subscrições. [Os grupos de gestão Azure](/azure/governance/management-groups/create) fornecem um nível de âmbito acima das subscrições. Organiza assinaturas em contentores chamados grupos de gestão e aplica as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Você pode construir uma estrutura flexível de grupos de gestão e subscrições em um diretório. Cada diretório recebe um único grupo de gestão de alto nível chamado grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. O grupo de gestão de raízes permite que políticas globais e atribuições RBAC sejam aplicadas ao nível do diretório.

Aqui estão algumas boas práticas para usar grupos de gestão:

**Boas práticas**: Garantir que novas subscrições aplicam elementos de governação, como políticas e permissões à medida que são adicionadas.   
**Detalhe**: Utilize o grupo de gestão de raízes para atribuir elementos de segurança a nível empresarial que se aplicam a todos os ativos do Azure. Políticas e permissões são exemplos de elementos.

**Boas práticas**: Alinhar os níveis mais altos dos grupos de gestão com a estratégia de segmentação para fornecer um ponto de controlo e consistência política dentro de cada segmento.   
**Detalhe**: Criar um único grupo de gestão para cada segmento no âmbito do grupo de gestão de raízes. Não crie outros grupos de gestão sob a raiz.

**Boas práticas**: Limite a profundidade do grupo de gestão para evitar confusões que dificultem tanto as operações como a segurança.   
**Detalhe**: Limite a sua hierarquia a três níveis, incluindo a raiz.

**Boas práticas**: Selecione cuidadosamente quais os itens a aplicar a toda a empresa com o grupo de gestão de raízes.   
**Detalhe**: Certifique-se de que os elementos do grupo de gestão de raízes têm uma necessidade clara de ser aplicado em todos os recursos e que são de baixo impacto.

Os bons candidatos incluem:

- Requisitos regulamentares que tenham um impacto comercial claro (por exemplo, restrições relacionadas com a soberania de dados)
- Requisitos com efeitos negativos potencialmente quase nulos nas operações, como a política com efeito de auditoria ou atribuição de permissões RBAC que tenham sido cuidadosamente revistas

**Boas práticas**: Planeie e teste cuidadosamente todas as alterações em toda a empresa no grupo de gestão de raízes antes de as aplicar (política, modelo RBAC, e assim por diante).   
**Detalhe**: As alterações no grupo de gestão de raízes podem afetar todos os recursos do Azure. Embora ofereçam uma forma poderosa de garantir a consistência em toda a empresa, erros ou utilização incorreta podem afetar negativamente as operações de produção. Teste todas as alterações ao grupo de gestão de raízes num laboratório de teste ou piloto de produção.

## <a name="streamline-environment-creation-with-blueprints"></a>Dinamizar a criação de ambiente com plantas
O serviço [Azure Blueprints](/azure/governance/blueprints/overview) permite aos arquitetos da nuvem e aos grupos centrais de tecnologias da informação definir um conjunto repetível de recursos Azure que implementa e adere aos padrões, padrões e requisitos de uma organização. O Azure Blueprints permite que as equipas de desenvolvimento construam e defendam rapidamente novos ambientes com um conjunto de componentes incorporados e a confiança de que estão a criar esses ambientes dentro do cumprimento organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorize serviços de armazenamento para mudanças inesperadas de comportamento
Diagnosticar e resolver problemas numa aplicação distribuída alojada num ambiente de nuvem pode ser mais complexo do que em ambientes tradicionais. As aplicações podem ser implantadas numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel, ou em alguma combinação destes ambientes. O tráfego de rede da sua aplicação pode atravessar redes públicas e privadas, e a sua aplicação pode usar várias tecnologias de armazenamento.

Deve monitorizar continuamente os serviços de armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas de comportamento (como tempos de resposta mais lentos). Utilize o registo para recolher dados mais detalhados e analisar um problema em profundidade. A informação de diagnóstico que obtém tanto da monitorização como do registo ajuda-o a determinar a causa principal do problema que a sua aplicação encontrou. Então pode resolver o problema e determinar os passos adequados para remediar.

[O Azure Storage Analytics](../../storage/common/storage-analytics.md) realiza a exploração madeireira e fornece dados métricos para uma conta de armazenamento Azure. Recomendamos que utilize estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenir, detetar e responder a ameaças
[O Azure Security Center](../../security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder às ameaças, proporcionando uma maior visibilidade (e controlo sobre) a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure, ajuda a detetar ameaças que de outra forma podem passar despercebidas, e trabalha com várias soluções de segurança.

O Free tier of Security Center oferece uma segurança limitada apenas para os seus recursos Azure. O nível Standard estende estas capacidades a instalações e outras nuvens. O Security Center Standard ajuda-o a encontrar e corrigir vulnerabilidades de segurança, a aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças usando análises e inteligência, e responder rapidamente quando está sob ataque. Pode experimentar o Centro de Segurança Standard sem custos durante os primeiros 60 dias. Recomendamos que [atualize a sua subscrição Azure para o Security Center Standard](../../security-center/security-center-get-started.md).

Use o Centro de Segurança para ter uma visão central do estado de segurança de todos os seus recursos Azure. Num ápice, verifique se os controlos de segurança adequados estão instalados e configurados corretamente, e identifique rapidamente quaisquer recursos que necessitem de atenção.

O Security Center também se integra com a [Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)que fornece capacidades abrangentes de deteção e resposta de pontofinal (EDR). Com a integração ATP do Microsoft Defender, pode detetar anomalias. Também pode detetar e responder a ataques avançados em pontos finais do servidor monitorizados pelo Security Center.

Quase todas as organizações empresariais têm um sistema de informação de segurança e gestão de eventos (SIEM) para ajudar a identificar ameaças emergentes, consolidando informações de registo de diversos dispositivos de recolha de sinais. Os registos são então analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todas as soluções de recolha de registos e análises.

[O Azure Sentinel](/azure/sentinel/overview) é uma solução de resposta automatizada de salção de segurança (SOAR) escalável, nativa da nuvem, informação de segurança e gestão de eventos (SIEM) e orquestração de segurança. O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças através da deteção de alerta, visibilidade de ameaças, caça proativa e resposta automatizada à ameaça.

Aqui estão algumas boas práticas para prevenir, detetar e responder a ameaças:

**Boas práticas**: Aumente a velocidade e a escalabilidade da sua solução SIEM utilizando um SIEM baseado em nuvem.   
**Detalhe**: Investigue as funcionalidades e capacidades do [Azure Sentinel](/azure/sentinel/overview) e compare-as com as capacidades do que está a utilizar no local. Considere adotar o Azure Sentinel se cumprir os requisitos do SIEM da sua organização.

**Boas práticas**: Encontre as vulnerabilidades de segurança mais graves para que possa priorizar a investigação.   
**Detalhe**: Reveja a sua [pontuação segura azure](../../security-center/secure-score-security-controls.md) para ver as recomendações resultantes das políticas e iniciativas do Azure incorporadas no Azure Security Center. Estas recomendações ajudam a resolver os principais riscos como atualizações de segurança, proteção de pontos finais, encriptação, configurações de segurança, WAF desaparecido, VMs ligados à Internet, e muito mais.

A pontuação segura, baseada nos controlos do Center for Internet Security (CIS), permite-lhe comparar a segurança Azure da sua organização contra fontes externas. A validação externa ajuda a validar e enriquecer a estratégia de segurança da sua equipa.

**Boas práticas**: Monitorize a postura de segurança de máquinas, redes, serviços de armazenamento e dados, bem como aplicações para descobrir e priorizar potenciais problemas de segurança.  
**Detalhe**: Siga as [recomendações de segurança](../../security-center/security-center-recommendations.md) no Centro de Segurança a partir de agora, com os itens prioritários mais elevados.

**Boas práticas**: Integre os alertas do Centro de Segurança na sua solução de informação de segurança e gestão de eventos (SIEM).   
**Detalhe**: A maioria das organizações com um SIEM usam-no como um centro de compensação para alertas de segurança que requerem uma resposta analista. Os eventos processados produzidos pelo Security Center são publicados no Registo de Atividades do Azure, um dos registos disponíveis através do Azure Monitor. O Azure Monitor oferece um pipeline consolidado para encaminhamento de qualquer um dos seus dados de monitorização para uma ferramenta SIEM. Consulte [alertas de segurança de exportação e recomendações](../../security-center/continuous-export.md#configuring-siem-integration-via-azure-event-hubs) para instruções.. Se estiver a utilizar o Azure Sentinel, consulte [o Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Boas práticas**: Integre os registos Azure com o seu SIEM.   
**Detalhe**: Utilize [o Monitor Azure para recolher e exportar dados.](/azure/azure-monitor/overview#integrate-and-export-data) Esta prática é fundamental para permitir a investigação de incidentes de segurança, e a retenção de registos on-line é limitada. Se estiver a utilizar o Azure Sentinel, consulte [a Connect](../../sentinel/connect-data-sources.md)sources .

**Boas práticas**: Acelere a sua investigação e processos de caça e reduza falsos positivos integrando as capacidades de Deteção e Resposta de Endpoint (EDR) na sua investigação de ataque.   
**Detalhe**: Ativar a [integração ATP do Microsoft Defender](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) através da sua política de segurança do Security Center. Considere usar o Azure Sentinel para a caça à ameaça e resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorizar a monitorização da rede baseada no cenário de ponta a ponta
Os clientes constroem uma rede de ponta a ponta em Azure, combinando recursos de rede como uma rede virtual, ExpressRoute, Application Gateway e equilibradores de carga. Está disponível o acompanhamento em cada um dos recursos da rede.

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Utilize as suas ferramentas de diagnóstico e visualização para monitorizar e diagnosticar condições ao nível do cenário da rede dentro e a partir do Azure.

Seguem-se as melhores práticas de monitorização da rede e ferramentas disponíveis.

**Melhores práticas**: Automatizar a monitorização remota da rede com a captura de pacotes.  
**Detalhe**: Monitorize e diagnostice problemas de rede sem iniciar sessão nos seus VMs utilizando o Observador de Rede. Trigger captura de [pacote](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) sintetizando alertas e acesso a informações de desempenho em tempo real ao nível do pacote. Quando vê um problema, pode investigar em detalhe para melhores diagnósticos.

**Boas práticas**: Obtenha informações sobre o tráfego da sua rede utilizando registos de fluxo.  
**Detalhe**: Construa uma compreensão mais profunda dos padrões de tráfego da sua rede utilizando registos de [fluxo de grupode de segurança](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)de rede . A informação nos registos de fluxo ajuda-o a recolher dados para a conformidade, auditoria e monitorização do seu perfil de segurança da rede.

**Boas práticas**: Diagnosticar problemas de conectividade VPN.  
**Detalhe**: Utilize o Observador de Rede para [diagnosticar os seus problemas de gateway VPN mais comuns e problemas](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)de ligação . Não só pode identificar o problema como também utilizar registos detalhados para investigar mais aprofundadamente.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implementação segura utilizando ferramentas comprovadas de DevOps
Utilize as seguintes práticas DevOps para garantir que a sua empresa e equipas sejam produtivas e eficientes.

**Boas práticas**: Automatizar a construção e implantação de serviços.  
**Detalhe**: [A infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas que ajudam os profissionais de TI a eliminar o fardo da construção e gestão diárias de infraestruturas modulares. Permite que os profissionais de TI construam e mantenham o seu ambiente de servidor moderno de uma forma que é como os desenvolvedores de software constroem e mantêm o código de aplicação.

Pode utilizar o Gestor de [Recursos Azure](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para fornecer as suas aplicações utilizando um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Usa o mesmo modelo para implementar repetidamente a sua aplicação em todas as fases do ciclo de vida da aplicação.

**Boas práticas**: Construa e implemente automaticamente para aplicações web do Azure ou serviços na nuvem.  
**Detalhe**: Pode configurar os seus Projetos Azure DevOps para [construir e implementar automaticamente](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) para aplicações web Do Azure ou serviços na nuvem. A Azure DevOps implanta automaticamente os binários depois de fazer uma construção para o Azure após cada check-in de código. O processo de construção do pacote é equivalente ao comando pacote no Estúdio Visual, e os passos de publicação são equivalentes ao comando Publish em Visual Studio.

**Boas práticas**: Gestão de lançamento automate.  
**Detalhe**: [A Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) é uma solução para automatizar a implantação em várias fases e gerir o processo de libertação. Crie gasodutos de implantação contínua geridos para libertar rapidamente, facilmente e frequentemente. Com os Pipelines Azure, pode automatizar o seu processo de libertação e pode ter fluxos de trabalho de aprovação predefinidos. Desloque-se no local e na nuvem, estenda e personalize conforme necessário.

**Boas práticas**: Verifique o desempenho da sua aplicação antes de a lançar ou implementar atualizações para produção.  
**Detalhe**: Executar testes de [carga baseados](/azure/devops/test/load-test/overview#alternatives) em nuvem para:

- Encontre problemas de desempenho na sua aplicação.
- Melhorar a qualidade de implantação.
- Certifique-se de que a sua aplicação está sempre disponível.
- Certifique-se de que a sua aplicação pode lidar com o tráfego para a sua próxima campanha de lançamento ou marketing.

[Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de código aberto livre e popular com um forte apoio comunitário.

**Boas práticas**: Monitorizar o desempenho da aplicação.  
**Detalhe**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de gestão de desempenho de aplicações (APM) para desenvolvedores web em várias plataformas. Utilize insights de aplicação para monitorizar a sua aplicação web ao vivo. Deteta automaticamente anomalias de desempenho. Inclui ferramentas de análise para o ajudar a diagnosticar problemas e a perceber o que os utilizadores realmente fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="mitigate-and-protect-against-ddos"></a>Mitigar e proteger contra dDoS
A negação distribuída do serviço (DDoS) é um tipo de ataque que tenta esgotar os recursos de aplicação. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de lidar com pedidos legítimos. Estes ataques estão a tornar-se mais sofisticados e maiores em tamanho e impacto. Podem ser visados em qualquer ponto final que seja publicamente acessível através da internet.

Projetar e construir para a resiliência do DDoS requer planeamento e conceção para uma variedade de modos de falha. Seguem-se as melhores práticas para a construção de serviços dDoS resilientes em Azure.

**Boas práticas**: Garantir que a segurança é uma prioridade ao longo de todo o ciclo de vida de uma aplicação, desde a conceção e implementação até à implantação e operações. As aplicações podem ter bugs que permitem um volume relativamente baixo de pedidos para usar muitos recursos, resultando numa interrupção de serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, deve ter uma boa compreensão da sua arquitetura de aplicação e focar-se nos [cinco pilares da qualidade do software.](https://docs.microsoft.com/azure/architecture/guide/pillars) Deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre a aplicação e outras aplicações, e os pontos finais de serviço que estão expostos à internet pública.

Garantir que uma aplicação é suficientemente resistente para lidar com uma negação de serviço direcionada à aplicação em si é o mais importante. A segurança e a privacidade são integradas na plataforma Azure, começando pelo Ciclo de Vida de Desenvolvimento de [Segurança (SDL)](https://www.microsoft.com/sdl). O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para torná-lo ainda mais seguro.

**Boas práticas**: Desenhe as suas aplicações para [escalar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplificada, especificamente em caso de ataque DDoS. Se a sua aplicação depender de uma única instância de um serviço, cria um único ponto de falha. O fornecimento de múltiplas instâncias torna o seu sistema mais resistente e escalável.  
**Detalhe**: Para o Serviço de [Aplicações Azure,](/azure/app-service/app-service-value-prop-what-is)selecione um plano de serviço de [aplicações](../../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para os Serviços Azure Cloud, configure cada uma das suas funções para utilizar [várias instâncias](../../cloud-services/cloud-services-choose-me.md).

Para [máquinas virtuais Azure,](/azure/virtual-machines/windows/overview)certifique-se de que a sua arquitetura VM inclui mais de um VM e que cada VM está incluído num conjunto de [disponibilidade](/azure/virtual-machines/virtual-machines-windows-manage-availability). Recomendamos a utilização de conjuntos de escala de máquinas virtuais para capacidades de autoscalcificação.

**Boas práticas**: Camadas de defesas de segurança numa aplicação reduz a possibilidade de um ataque bem sucedido. Implemente designs seguros para as suas aplicações utilizando as capacidades incorporadas da plataforma Azure.  
**Detalhe**: O risco de ataque aumenta com o tamanho (área de superfície) da aplicação. Pode reduzir a área de superfície utilizando a lista branca para fechar o espaço de endereçoIP exposto e as portas de audição que não são necessárias nos equilibradores de carga[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal)

[Os grupos de segurança da rede](../../virtual-network/security-overview.md) são outra forma de reduzir a superfície de ataque. Pode utilizar etiquetas de serviço e [grupos](../../virtual-network/security-overview.md#service-tags) de segurança de [aplicações](../../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança da rede, como uma extensão natural da estrutura de uma aplicação.

Deve implantar serviços Azure numa [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem através de endereços IP privados. O tráfego de serviço saque a partir de uma rede virtual utiliza endereços IP públicos como endereços IP de origem por padrão.

A utilização de [pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md) de serviço muda o tráfego de serviços para utilizar endereços privados de rede virtual como endereços IP de origem quando estão a aceder ao serviço Azure a partir de uma rede virtual.

Vemos muitas vezes os recursos dos clientes no local a serem atacados juntamente com os seus recursos em Azure. Se estiver a ligar um ambiente no local ao Azure, minimize a exposição dos recursos no local à internet pública.

O Azure tem duas ofertas de [serviços](../../virtual-network/ddos-protection-overview.md) DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada no Azure por defeito sem custos adicionais. A escala e a capacidade da rede Azure globalmente implantada fornecem defesa contra ataques comuns em camadas de rede através de monitorização de tráfego sempre on-on e mitigação em tempo real. O Básico não requer alterações na configuração do utilizador ou na aplicação e ajuda a proteger todos os serviços Azure, incluindo serviços PaaS como o Azure DNS.
- A proteção padrão fornece capacidades avançadas de mitigação do DDoS contra ataques de rede. É automaticamente sintonizado para proteger os seus recursos específicos do Azure. A proteção é simples de permitir durante a criação de redes virtuais. Também pode ser feito após a criação e não requer alterações na aplicação ou recursos.

## <a name="enable-azure-policy"></a>Ativar a política azure
[A Azure Policy](/azure/governance/policy/overview) é um serviço em Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas aplicam regras e efeitos sobre os seus recursos, para que esses recursos se mantenham em conformidade com os seus padrões corporativos e acordos de nível de serviço. O Azure Policy responde a esta necessidade ao avaliar os seus recursos para detetar os que não estão em conformidade com as políticas atribuídas.

Enable Azure Policy para monitorizar e aplicar a política escrita da sua organização. Isto garantirá o cumprimento dos requisitos de segurança da sua empresa ou regulamentares, gerindo centralmente as políticas de segurança em todas as suas cargas de trabalho híbridas em nuvem. Aprenda a [criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Consulte a estrutura de definição da [Política Azure](../../governance/policy/concepts/definition-structure.md) para uma visão geral dos elementos de uma política.

Aqui estão algumas boas práticas de segurança a seguir depois de adotar a Política Azure:

**Boas práticas**: A política suporta vários tipos de efeitos. Pode ler sobre eles na estrutura de definição de [política azure](../../governance/policy/concepts/definition-structure.md#policy-rule). As operações empresariais podem ser negativamente afetadas pelo efeito **de negação** e pelo efeito **de remediação,** pelo que comece com o efeito **de auditoria** para limitar o risco de impacto negativo da política.   
**Detalhe**: [Iniciar implementações de políticas no modo de auditoria](../../governance/policy/concepts/definition-structure.md#policy-rule) e, posteriormente, avançar para **negar** ou **remediar**. Teste e reveja os resultados do efeito de auditoria antes de avançar para **negar** ou **remediar**.

Para mais informações, consulte [Criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md)

**Boas práticas**: Identifique as funções responsáveis pelo acompanhamento das violações das políticas e garanta que as medidas de reparação adequadas são tomadas rapidamente.   
**Detalhe**: Tenha o monitor de funções atribuído através do [portal Azure](../../governance/policy/how-to/get-compliance-data.md#portal) ou através da [linha de comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Boas práticas**: A Política Azure é uma representação técnica das políticas escritas de uma organização. Mapeie todas as políticas do Azure para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: Mapeamento documental na documentação da sua organização ou na própria política do Azure, adicionando uma referência à política organizacional na [descrição](../../governance/policy/concepts/definition-structure.md#display-name-and-description) da política azure ou na descrição da [iniciativa](../../governance/policy/concepts/definition-structure.md#initiatives) política Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitor Azure Relatórios de risco da AD
A grande maioria das falhas de segurança ocorrem quando os atacantes têm acesso a um ambiente roubando a identidade de um utilizador. Descobrir identidades comprometidas não é tarefa fácil. O Azure AD utiliza algoritmos adaptáveis de aprendizagem automática e heurística para detetar ações suspeitas relacionadas com as suas contas de utilizador. Cada ação suspeita detetada é armazenada num registo chamado [deteção de risco.](../../active-directory/reports-monitoring/concept-risk-events.md) As deteções de risco são registadas nos relatórios de segurança da AD Azure. Para mais informações, leia sobre os [utilizadores do relatório](../../active-directory/reports-monitoring/concept-user-at-risk.md) de segurança de risco e o relatório de segurança de [insões de risco](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre segurança Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - para informações atualizadas sobre as últimas novidades em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail parasecure@microsoft.com

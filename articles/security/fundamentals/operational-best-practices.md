---
title: Melhores práticas de segurança para os seus ativos Azure
titleSuffix: Azure security
description: Este artigo fornece um conjunto de boas práticas operacionais para proteger os seus dados, aplicações e outros ativos em Azure.
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
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498851"
---
# <a name="azure-operational-security-best-practices"></a>Melhores práticas de Segurança Operacional da Azure
Este artigo fornece um conjunto de boas práticas operacionais para proteger os seus dados, aplicações e outros ativos em Azure.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades e conjuntos de funcionalidades atuais da plataforma Azure. As opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implementar práticas de segurança operacional fortes
A segurança operacional da Azure refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos em Azure. A segurança operacional do Azure baseia-se num quadro que incorpora os conhecimentos adquiridos através de capacidades únicas da Microsoft, incluindo o [Ciclo de Vida para Desenvolvimento de Segurança (SDL),](https://www.microsoft.com/sdl)o programa do Microsoft Security Response [Center](https://www.microsoft.com/msrc?rtc=1) e uma profunda consciência do panorama da ameaça à cibersegurança.

## <a name="manage-and-monitor-user-passwords"></a>Gerir e monitorizar as palavras-passe do utilizador
A tabela que se segue lista algumas boas práticas relacionadas com a gestão das palavras-passe dos utilizadores:

**Melhores práticas**: Certifique-se de que tem o nível adequado de proteção de senhas na nuvem.   
**Detalhe**: Siga a orientação no [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), que é traçado para os utilizadores das plataformas de identidade da Microsoft (Azure Ative Directory, Ative Directory e Microsoft).

**Melhores práticas**: Monitorize para ações suspeitas relacionadas com as suas contas de utilizador.   
**Detalhe**: Monitorize [para utilizadores em risco](../../active-directory/identity-protection/overview-identity-protection.md) e [inscrições arriscadas](../../active-directory/identity-protection/overview-identity-protection.md) utilizando relatórios de segurança Azure AD.

**Melhores práticas**: Detetar e remediar automaticamente senhas de alto risco.   
**Detalhe**: [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é uma característica da edição Azure AD Premium P2 que lhe permite:

- Detetar potenciais vulnerabilidades que afetam as identidades da sua organização
- Configure respostas automatizadas para detetar ações suspeitas relacionadas com as identidades da sua organização
- Investigue incidentes suspeitos e tome as medidas apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receber notificações de incidentes da Microsoft
Certifique-se de que a sua equipa de operações de segurança recebe notificações de incidentes da Azure da Microsoft. Uma notificação de incidentes permite à sua equipa de segurança saber que comprometeu os recursos do Azure para que possam responder rapidamente e corrigir potenciais riscos de segurança.

No portal de inscrição Azure, pode garantir que as informações de contacto da administração incluem detalhes que notificam as operações de segurança. As informações de contacto são um endereço de e-mail e número de telefone.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organize assinaturas Azure em grupos de gestão
Se a sua organização tiver muitas subscrições, poderá precisar de uma forma de gerir o acesso, as políticas e a conformidade dessas subscrições. [Os grupos de gestão Azure](../../governance/management-groups/create-management-group-portal.md) fornecem um nível de âmbito acima das subscrições. Estes permitem-lhe organizar as subscrições em contentores chamados "grupos de gestão" e aplicar as suas condições de governação aos grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Você pode construir uma estrutura flexível de grupos de gestão e subscrições em um diretório. Cada diretório recebe um único grupo de gestão de alto nível chamado grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. O grupo de gestão de raízes permite que as políticas globais e atribuições de funções Azure sejam aplicadas ao nível do diretório.

Aqui estão algumas boas práticas para a utilização de grupos de gestão:

**Melhores práticas**: Certifique-se de que as novas assinaturas aplicam elementos de governação como políticas e permissões à medida que são adicionadas.   
**Detalhe**: Utilize o grupo de gestão de raiz para atribuir elementos de segurança a nível empresarial que se aplicam a todos os ativos da Azure. Políticas e permissões são exemplos de elementos.

**Melhores práticas**: Alinhar os níveis superiores dos grupos de gestão com a estratégia de segmentação para proporcionar um ponto de controlo e consistência política em cada segmento.   
**Detalhe**: Criar um único grupo de gestão para cada segmento no âmbito do grupo de gestão de raiz. Não crie outros grupos de gestão sob a raiz.

**Boas práticas**: Limitar a profundidade do grupo de gestão para evitar confusões que dificultam tanto as operações como a segurança.   
**Detalhe**: Limite a sua hierarquia a três níveis, incluindo a raiz.

**Melhores práticas**: Selecione cuidadosamente quais os itens a aplicar a toda a empresa com o grupo de gestão de raiz.   
**Detalhe**: Certifique-se de que os elementos do grupo de gestão de raízes têm uma necessidade clara de ser aplicados em todos os recursos e que são de baixo impacto.

Os bons candidatos incluem:

- Requisitos regulamentares que têm um impacto claro no negócio (por exemplo, restrições relacionadas com a soberania de dados)
- Requisitos com efeitos negativos potenciais quase nulos nas operações, como a política com efeito de auditoria ou as atribuições de permissão do RBAC do Azure que foram cuidadosamente revistas

**Melhores práticas**: Planeie cuidadosamente e teste todas as alterações em toda a empresa no grupo de gestão de raízes antes de as aplicar (política, modelo Azure RBAC, e assim por diante).   
**Detalhe**: As alterações no grupo de gestão de raiz podem afetar todos os recursos do Azure. Embora forneçam uma forma poderosa de garantir a consistência em toda a empresa, erros ou utilização incorreta podem afetar negativamente as operações de produção. Teste todas as alterações ao grupo de gestão de raízes num laboratório de teste ou piloto de produção.

## <a name="streamline-environment-creation-with-blueprints"></a>Dinamizar a criação do ambiente com plantas
O serviço [Azure Blueprints](../../governance/blueprints/overview.md) permite que arquitetos em nuvem e grupos centrais de tecnologias da informação definam um conjunto repetível de recursos Azure que implemente e adere aos padrões, padrões e requisitos de uma organização. A Azure Blueprints permite que as equipas de desenvolvimento construam e suportem rapidamente novos ambientes com um conjunto de componentes incorporados e a confiança de que estão a criar esses ambientes dentro do cumprimento organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorize os serviços de armazenamento para mudanças inesperadas no comportamento
Diagnosticar e resolver problemas numa aplicação distribuída hospedada num ambiente em nuvem pode ser mais complexo do que em ambientes tradicionais. As aplicações podem ser implantadas numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel ou em alguma combinação destes ambientes. O tráfego de rede da sua aplicação pode atravessar redes públicas e privadas, e a sua aplicação pode usar várias tecnologias de armazenamento.

Deve monitorizar continuamente os serviços de armazenamento que a sua aplicação utiliza para quaisquer alterações inesperadas de comportamento (como tempos de resposta mais lentos). Utilize o registo para recolher dados mais detalhados e para analisar um problema em profundidade. A informação de diagnóstico que obtém tanto da monitorização como do registo ajuda-o a determinar a causa principal do problema que a sua aplicação encontrou. Em seguida, pode resolver o problema e determinar os passos adequados para remediar o problema.

[A Azure Storage Analytics](../../storage/common/storage-analytics.md) realiza o registo e fornece dados de métricas para uma conta de armazenamento Azure. Recomendamos que utilize estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenir, detetar e responder a ameaças
[O Azure Security Center](../../security-center/security-center-introduction.md) ajuda a prevenir, detetar e responder a ameaças, proporcionando uma maior visibilidade para (e controlar) a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure, ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e trabalha com várias soluções de segurança.

O Free Tier of Security Center oferece uma segurança limitada apenas para os seus recursos Azure. O nível Standard alarga estas capacidades a instalações e outras nuvens. O Security Center Standard ajuda-o a encontrar e corrigir vulnerabilidades de segurança, a aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças usando análises e inteligência e responder rapidamente quando está sob ataque. Pode experimentar o Centro de Segurança Standard sem custos durante os primeiros 60 dias. Recomendamos que [atualize a sua assinatura Azure para o Security Center Standard](../../security-center/security-center-get-started.md).

Use o Centro de Segurança para ter uma visão central do estado de segurança de todos os seus recursos Azure. Resumindo, verifique se os controlos de segurança adequados estão em vigor e configurados corretamente, e identifique rapidamente quaisquer recursos que precisem de atenção.

O Security Center também se integra com [o Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)que fornece capacidades abrangentes de Deteção e Resposta de Pontos De Final (EDR). Com a integração ATP do Microsoft Defender, pode detetar anomalias. Também é possível detetar e responder a ataques avançados em pontos finais do servidor monitorizados pelo Security Center.

Quase todas as organizações empresariais têm um sistema de informação de segurança e gestão de eventos (SIEM) para ajudar a identificar ameaças emergentes, consolidando informações de registos de diversos dispositivos de recolha de sinais. Os registos são então analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todas as soluções de recolha de registos e análise.

[Azure Sentinel](../../sentinel/overview.md) é uma solução de resposta automatizada de resposta automatizada (SOAR) de informação e gestão de eventos (SOAR) e de orquestração de segurança. O Azure Sentinel fornece análise inteligente de segurança e inteligência de ameaça através da deteção de alertas, visibilidade da ameaça, caça proativa e resposta automática de ameaças.

Eis algumas boas práticas para prevenir, detetar e responder a ameaças:

**Melhores práticas**: Aumente a velocidade e a escalabilidade da sua solução SIEM utilizando um SIEM baseado na nuvem.   
**Detalhe**: Investigue as funcionalidades e capacidades do [Azure Sentinel](../../sentinel/overview.md) e compare-os com as capacidades do que está a utilizar no local. Considere adotar o Azure Sentinel se cumprir os requisitos do SIEM da sua organização.

**Melhores práticas**: Encontre as vulnerabilidades de segurança mais graves para que possa priorizar a investigação.   
**Detalhe**: Reveja a sua [pontuação segura do Azure](../../security-center/secure-score-security-controls.md) para ver as recomendações resultantes das políticas e iniciativas do Azure incorporadas no Centro de Segurança Azure. Estas recomendações ajudam a resolver riscos de topo como atualizações de segurança, proteção de pontos finais, encriptação, configurações de segurança, WAF em falta, VMs ligados à Internet, e muito mais.

A pontuação segura, que se baseia nos controlos do Center for Internet Security (CIS), permite comparar a segurança Azure da sua organização contra fontes externas. A validação externa ajuda a validar e enriquecer a estratégia de segurança da sua equipa.

**Melhores práticas**: Monitorize a postura de segurança de máquinas, redes, serviços de armazenamento e dados e aplicações para descobrir e priorizar potenciais problemas de segurança.  
**Detalhe**: Siga as [recomendações de segurança](../../security-center/security-center-recommendations.md) no Centro de Segurança a partir de início, com os itens de maior prioridade.

**Melhores práticas**: Integrar alertas do Centro de Segurança na sua solução de informação de segurança e gestão de eventos (SIEM).   
**Detalhe**: A maioria das organizações com um SIEM usam-na como centro de compensação para alertas de segurança que requerem uma resposta analista. Os eventos processados produzidos pelo Security Center são publicados no Registo de Atividades do Azure, um dos registos disponíveis através do Azure Monitor. O Azure Monitor oferece um oleoduto consolidado para encaminhar qualquer um dos seus dados de monitorização para uma ferramenta SIEM. Consulte [alertas stream para uma solução siem, soar ou gestão de serviços de TI](../../security-center/export-to-siem.md) para obter instruções. Se estiver a utilizar o Azure Sentinel, consulte [o Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Melhores práticas**: Integre os registos Azure com o seu SIEM.   
**Detalhe**: Utilize [o Monitor Azure para recolher e exportar dados](../../azure-monitor/overview.md#integrate-and-export-data). Esta prática é fundamental para permitir a investigação de incidentes de segurança, e a retenção de registos on-line é limitada. Se estiver a utilizar o Azure Sentinel, consulte [as fontes de dados do Connect](../../sentinel/connect-data-sources.md).

**Melhores práticas**: Acelere os seus processos de investigação e caça e reduza os falsos positivos integrando as capacidades de Deteção e Resposta de Pontos Finais (EDR) na sua investigação de ataque.   
**Detalhe**: [Ativar o Microsoft Defender para integração de Endpoint](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) através da sua política de segurança do Security Center. Considere usar Azure Sentinel para caçar ameaças e resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorar monitorização de rede baseada em cenários
Os clientes constroem uma rede de ponta a ponta no Azure, combinando recursos de rede como uma rede virtual, ExpressRoute, Application Gateway e equilibradores de carga. A monitorização está disponível em cada um dos recursos da rede.

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Utilize as suas ferramentas de diagnóstico e visualização para monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure.

Seguem-se as melhores práticas de monitorização da rede e de ferramentas disponíveis.

**Melhores práticas**: Automatizar a monitorização da rede remota com a captura de pacotes.  
**Detalhe**: Monitorize e diagnostice problemas de rede sem iniciar sessão nos seus VMs utilizando o Network Watcher. Captura [de pacotes](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) de gatilho definindo alertas e obter acesso a informações de desempenho em tempo real ao nível do pacote. Quando vir um problema, pode investigar em detalhe para obter melhores diagnósticos.

**Melhores práticas**: Obtenha informações sobre o tráfego da sua rede utilizando registos de fluxo.  
**Detalhe**: Construa uma compreensão mais profunda dos seus padrões de tráfego de rede utilizando [registos de fluxo de grupo de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). A informação nos registos de fluxos ajuda-o a recolher dados para a conformidade, auditoria e monitorização do seu perfil de segurança da rede.

**Melhores práticas**: Diagnosticar problemas de conectividade VPN.  
**Detalhe**: Utilize o Observador de Rede para [diagnosticar os seus problemas de gateway VPN mais comuns e problemas de conexão](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Não só pode identificar o problema, mas também usar registos detalhados para investigar mais aprofundadamente.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implementação segura utilizando ferramentas de DevOps comprovadas
Use as seguintes devOps para garantir que a sua empresa e equipas são produtivas e eficientes.

**Melhores práticas**: Automatizar a construção e implantação de serviços.  
**Detalhe**: [A infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas que ajudam os profissionais de TI a eliminar o fardo da construção e gestão do dia-a-dia da infraestrutura modular. Permite que os profissionais de TI construam e mantenham o seu ambiente moderno de servidor de uma forma que é como a forma como os desenvolvedores de software constroem e mantêm o código de aplicação.

Pode utilizar [o Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para forrê-lo utilizando um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Utilize o mesmo modelo para implementar repetidamente a sua aplicação em todas as fases do ciclo de vida da aplicação.

**Melhores práticas**: Construa e implemente automaticamente para aplicações web Azure ou serviços na nuvem.  
**Detalhe**: Pode configurar os seus Projetos Azure DevOps para  [construir e implementar automaticamente](/azure/devops/pipelines/index) para aplicações web Azure ou serviços na nuvem. O Azure DevOps implanta automaticamente os binários depois de fazer uma construção para a Azure após cada check-in de código. O processo de construção de pacotes é equivalente ao comando Package em Visual Studio, e os passos de publicação são equivalentes ao comando Publicar em Visual Studio.

**Melhores práticas**: Gestão de lançamentos automatizada.  
**Detalhe**: [A azure Pipelines](/azure/devops/pipelines/index) é uma solução para automatizar a implantação em várias fases e gerir o processo de libertação. Crie oleodutos de implantação contínua geridos para libertar rapidamente, facilmente e com frequência. Com a Azure Pipelines, pode automatizar o seu processo de libertação e pode ter fluxos de trabalho de aprovação predefinidos. Implementar no local e na nuvem, estender e personalizar conforme necessário.

**Melhores práticas**: Verifique o desempenho da sua aplicação antes de a lançar ou implemente atualizações para a produção.  
**Detalhe**: Executar [testes](/azure/devops/test/load-test/overview#alternatives) de carga baseados em nuvem para:

- Encontre problemas de desempenho na sua aplicação.
- Melhorar a qualidade de implantação.
- Certifique-se de que a sua aplicação está sempre disponível.
- Certifique-se de que a sua aplicação pode lidar com o tráfego para a sua próxima campanha de lançamento ou marketing.

[Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de código aberto livre e popular com um forte apoio comunitário.

**Melhores práticas:** Monitorizar o desempenho da aplicação.  
**Detalhe**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço extensível de gestão de desempenho de aplicações (APM) para desenvolvedores web em várias plataformas. Utilize insights de aplicação para monitorizar a sua aplicação web ao vivo. Deteta automaticamente anomalias de desempenho. Inclui ferramentas de análise para ajudá-lo a diagnosticar problemas e a entender o que os utilizadores realmente fazem com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="mitigate-and-protect-against-ddos"></a>Mitigar e proteger contra o DDoS
A negação de serviço distribuída (DDoS) é um tipo de ataque que tenta esgotar os recursos de aplicação. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de lidar com pedidos legítimos. Estes ataques estão a tornar-se mais sofisticados e maiores em tamanho e impacto. Podem ser direcionados para qualquer ponto final que seja acessível publicamente através da internet.

Projetar e construir para a resiliência do DDoS requer planeamento e conceção para uma variedade de modos de falha. Seguem-se as melhores práticas para a construção de serviços de resiliência dDoS em Azure.

**Melhores práticas**: Garantir que a segurança é uma prioridade ao longo de todo o ciclo de vida de uma aplicação, desde a conceção e implementação até à implantação e operações. As aplicações podem ter bugs que permitem um volume relativamente baixo de pedidos para usar um monte de recursos, resultando numa paragem de serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, deverá ter uma boa compreensão da arquitetura da sua aplicação e focar-se nos [cinco pilares da qualidade do software.](/azure/architecture/guide/pillars) Deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre a aplicação e outras aplicações, e os pontos finais de serviço que estão expostos à internet pública.

Garantir que uma aplicação é resistente o suficiente para lidar com uma negação de serviço que é direcionada para a aplicação em si é o mais importante. A segurança e a privacidade são integradas na plataforma Azure, a começar pelo Ciclo de [Vida para o Desenvolvimento de Segurança (SDL).](https://www.microsoft.com/sdl) O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para torná-lo ainda mais seguro.

**Melhores práticas**: Desenhe as suas aplicações para [escalar horizontalmente](/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplificada, especificamente em caso de ataque DDoS. Se a sua aplicação depender de uma única instância de um serviço, cria um único ponto de falha. A provisionar várias instâncias torna o seu sistema mais resistente e escalável.  
**Detalhe**: Para [o Azure App Service,](../../app-service/overview.md)selecione um [plano de Serviço de Aplicações](../../app-service/overview-hosting-plans.md) que oferece várias instâncias.

Para a Azure Cloud Services, configuure cada um dos seus papéis para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md).

Para [máquinas virtuais Azure](../../virtual-machines/windows/overview.md), certifique-se de que a sua arquitetura VM inclui mais de um VM e que cada VM está incluído num [conjunto de disponibilidade.](../../virtual-machines/windows/tutorial-availability-sets.md) Recomendamos a utilização de conjuntos de escala de máquina virtual para capacidades de autoscalagem.

**Boas práticas**: Camadas de defesas de segurança numa aplicação reduz a possibilidade de um ataque bem sucedido. Implemente designs seguros para as suas aplicações utilizando as capacidades incorporadas da plataforma Azure.  
**Pormenor**: O risco de ataque aumenta com o tamanho (área de superfície) da aplicação. Pode reduzir a área de superfície utilizando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de audição que não são necessárias nos equilibros de carga[(Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [Azure Application Gateway).](../../application-gateway/application-gateway-create-probe-portal.md)

[Os grupos de segurança da](../../virtual-network/network-security-groups-overview.md) rede são outra forma de reduzir a superfície de ataque. Pode utilizar [etiquetas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) e grupos de segurança de [aplicações](../../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade para criar regras de segurança e configurar a segurança da rede, como uma extensão natural da estrutura de uma aplicação.

Deve implementar os serviços Azure numa [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem através de endereços IP privados. O tráfego de serviço Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem por padrão.

A utilização [de pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md) de serviço muda o tráfego de serviço para utilizar endereços privados de rede virtual como endereços IP de origem quando estão a aceder ao serviço Azure a partir de uma rede virtual.

Vemos muitas vezes os recursos dos clientes no local a serem atacados juntamente com os seus recursos em Azure. Se estiver a ligar um ambiente no local ao Azure, minimize a exposição dos recursos no local à internet pública.

A Azure tem [duas ofertas](../../ddos-protection/ddos-protection-overview.md) de serviços DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada no Azure por padrão sem custos adicionais. A escala e a capacidade da rede Azure implantada globalmente fornece defesa contra ataques comuns de camadas de rede através de monitorização de tráfego sempre on e mitigação em tempo real. O básico não requer configuração do utilizador ou alterações de aplicações e ajuda a proteger todos os serviços Azure, incluindo serviços PaaS como o Azure DNS.
- A proteção padrão fornece capacidades avançadas de mitigação do DDoS contra ataques de rede. Está automaticamente sintonizado para proteger os seus recursos específicos do Azure. A proteção é simples de permitir durante a criação de redes virtuais. Também pode ser feito após a criação e não requer nenhuma aplicação ou alterações de recursos.

## <a name="enable-azure-policy"></a>Ativar a política do Azure
[A Azure Policy](../../governance/policy/overview.md) é um serviço em Azure que usa para criar, atribuir e gerir políticas. Estas políticas impõem regras e efeitos sobre os seus recursos, para que esses recursos permaneçam conformes com os seus padrões corporativos e acordos de nível de serviço. O Azure Policy responde a esta necessidade ao avaliar os seus recursos para detetar os que não estão em conformidade com as políticas atribuídas.

Enable Azure Policy para monitorizar e aplicar a política escrita da sua organização. Isto garantirá o cumprimento dos requisitos de segurança da sua empresa ou regulamentares, gerindo centralmente as políticas de segurança através das suas cargas de trabalho em nuvem híbrida. Saiba como [criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Consulte [a estrutura de definição de política Azure](../../governance/policy/concepts/definition-structure.md) para uma visão geral dos elementos de uma política.

Eis algumas boas práticas de segurança a seguir depois de adotar a Política Azure:

**Boas práticas**: A política apoia vários tipos de efeitos. Pode ler sobre eles na estrutura de [definição de Política Azure.](../../governance/policy/concepts/definition-structure.md#policy-rule) As operações comerciais podem ser negativamente afetadas pelo efeito **de negação** e pelo efeito **corretiva,** pelo que comece com o efeito **de auditoria** para limitar o risco de impacto negativo da política.   
**Detalhe**: [Inicie as implementações de políticas em modo de auditoria](../../governance/policy/concepts/definition-structure.md#policy-rule) e, posteriormente, progrida para **negar** ou **remediar**. Teste e reveja os resultados do efeito de auditoria antes de passar a **negar** ou **remediar**.

Para obter mais informações, consulte [Criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md)

**Melhores práticas**: Identifique as funções responsáveis pela monitorização das violações das políticas e garanta que as medidas de reparação adequadas sejam tomadas rapidamente.   
**Detalhe**: Tenha a conformidade do monitor de função atribuído através do [portal Azure](../../governance/policy/how-to/get-compliance-data.md#portal) ou através da [linha de comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Boas práticas**: A política de azure é uma representação técnica das políticas escritas de uma organização. Mapear todas as definições da Política Azure para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: Mapear na documentação da sua organização ou na própria definição de Política Azure adicionando uma referência à política organizacional na [definição de política](../../governance/policy/concepts/definition-structure.md#display-name-and-description) ou na descrição da definição de [iniciativa.](../../governance/policy/concepts/initiative-definition-structure.md#metadata)

## <a name="monitor-azure-ad-risk-reports"></a>Monitor Azure Relatórios de risco AD
A grande maioria das falhas de segurança ocorre quando os atacantes têm acesso a um ambiente roubando a identidade de um utilizador. Descobrir identidades comprometidas não é tarefa fácil. O Azure AD utiliza algoritmos de aprendizagem automática adaptativa e heurística para detetar ações suspeitas relacionadas com as suas contas de utilizador. Cada ação suspeita detetada é armazenada num registo chamado deteção de [risco.](../../active-directory/identity-protection/overview-identity-protection.md) As deteções de risco são registadas nos relatórios de segurança da AZure. Para mais informações, leia sobre os [utilizadores em relatório](../../active-directory/identity-protection/overview-identity-protection.md) de segurança de risco e o [relatório de segurança de entradas de risco](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Estão disponíveis os seguintes recursos para fornecer informações mais gerais sobre a segurança da Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](/archive/blogs/azuresecurity/) - para informações atualizadas sobre as últimas informações em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail para secure@microsoft.com
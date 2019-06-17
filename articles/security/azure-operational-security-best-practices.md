---
title: Melhores práticas de segurança para proteger seus ativos - Microsoft Azure
description: Este artigo fornece um conjunto de melhores práticas operacionais para proteger seus dados, aplicativos e outros recursos no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0418d325f3b3719549181a48fc0432a677f695d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795894"
---
# <a name="azure-operational-security-best-practices"></a>Segurança operacional melhores práticas do Azure
Este artigo fornece um conjunto de melhores práticas operacionais para proteger seus dados, aplicativos e outros recursos no Azure.

As melhores práticas baseiam-se um consenso de opinião, e trabalhar com recursos atuais da plataforma do Azure e conjuntos de recursos. Opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implementar práticas de segurança operacional forte
Segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicativos e outros recursos no Azure. Segurança operacional do Azure baseia-se uma estrutura que incorpore os dados recolhidos através de capacidades que são exclusivas da Microsoft, incluindo o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), o [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programa e profundo entendimento do cenário de ameaças de cibersegurança.

## <a name="manage-and-monitor-user-passwords"></a>Gerir e monitorizar as palavras-passe do utilizador
A tabela seguinte lista algumas das melhores práticas relacionadas com a gestão de palavras-passe de utilizador:

**Melhor prática**: Certifique-se de que tem o nível adequado de proteção de palavra-passe na cloud.   
**Detalhe**: Siga as orientações no [orientações de palavra-passe do Microsoft](https://www.microsoft.com/research/publication/password-guidance/), que tem um âmbito para utilizadores das plataformas de identidade da Microsoft (conta Microsoft, Active Directory e Azure Active Directory).

**Melhor prática**: Monitorizar as ações suspeitas relacionadas às contas de utilizador.   
**Detalhe**: Monitorar [utilizadores em risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) e [inícios de sessão arriscados](../active-directory/reports-monitoring/concept-risk-events.md) ao utilizar a segurança do Azure AD relatórios.

**Melhor prática**: Detetar e remediar as palavras-passe de alto risco automaticamente.   
**Detalhe**: [O Azure AD Identity Protection](../active-directory/identity-protection/overview.md) é uma funcionalidade da edição Azure AD Premium P2 que lhe permite:

- Detetar potenciais vulnerabilidades que afetam as identidades da sua organização
- Configurar respostas automáticas para as ações suspeitas detetadas relacionadas com identidades da sua organização
- Investigar incidentes suspeitos e executar as ações apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receber notificações de incidentes da Microsoft
Certifique-se de que sua equipe de operações de segurança recebe notificações de incidentes do Azure da Microsoft. Um permite de notificação de incidentes sua equipe de segurança saiba ter comprometida recursos do Azure para que possam responder e remediar potenciais riscos de segurança rapidamente.

No portal de inscrição do Azure, pode certificar-se de informações de contacto do administrador incluem detalhes que o notificam operações de segurança. Informações de contacto são um e-mail endereço e número de telefone.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizar as subscrições do Azure para grupos de gestão
Se sua organização tiver várias subscrições, poderá ter uma forma de gerenciar com eficiência o acesso, políticas e conformidade para nessas subscrições. [Grupos de gestão do Azure](../governance/management-groups/create.md) fornecer um nível de âmbito que se encontra acima subscrições. Organizar subscrições em chamados grupos de gestão de contentores e aplicar suas condições de governação para os grupos de gestão. Todas as subscrições num grupo de gestão herdam automaticamente as condições aplicadas ao grupo de gestão.

Pode criar uma estrutura flexível de grupos de gestão e das assinaturas para um diretório. Cada diretório é atribuído um grupo de gestão único de nível superior denominado o grupo de gestão de raiz. Este grupo de gestão de raiz está incorporado na hierarquia para ter todos os grupos de gestão e subscrições associados ao mesmo. O grupo de gestão de raiz permite políticas globais e atribuições de RBAC a ser aplicado ao nível do diretório.

Seguem-se algumas melhores práticas para utilizar grupos de gestão:

**Melhor prática**: Certifique-se de que as subscrições novas aplicam-se os elementos de governação como diretivas e permissões à medida que eles são adicionados.   
**Detalhe**: Utilize o grupo de gestão de raiz para atribuir os elementos de segurança de toda a empresa que se aplicam a todos os recursos do Azure. Diretivas e permissões são exemplos de elementos.

**Melhor prática**: Alinhe os níveis principais de grupos de gestão com a estratégia de segmentação para fornecer um ponto de controlo e a política de consistência em cada segmento.   
**Detalhe**: Crie um grupo de gestão única para cada segmento sob o grupo de gestão de raiz. Não crie quaisquer outros grupos de gestão na raiz.

**Melhor prática**: Limitar a profundidade de grupo de gestão para evitar confusões frequência dificulta o operações e segurança.   
**Detalhe**: Limite a hierarquia de três níveis, incluindo a raiz.

**Melhor prática**: Selecione cuidadosamente os itens a serem se aplicam a toda a empresa com o grupo de gestão de raiz.   
**Detalhe**: Certifique-se de elementos de grupo de gestão de raiz tem um claro têm de ser aplicadas em todos os recursos e que eles baixo impacto.

Bons candidatos incluem:

- Requisitos de regulamentação que têm um impacto sobre o negócio claro (por exemplo, restrições relacionadas com a soberania dos dados)
- Os requisitos com potencial de mínima negativo afetam em operações, como a política com o efeito de auditoria ou RBAC atribuições de permissão que foram revistas cuidadosamente

**Melhor prática**: Cuidadosamente, planejar e testar todas as alterações de toda a empresa no grupo de gestão de raiz antes de aplicá-los (política de modelo RBAC e assim por diante).   
**Detalhe**: Alterações no grupo de gestão de raiz podem afetar todos os recursos no Azure. Enquanto que fornecem um meio eficaz de garantir a consistência em toda a empresa, erros ou de utilização incorreta pode afetar negativamente as operações de produção. Testar todas as alterações ao grupo de gestão de raiz num laboratório de teste ou produção piloto.

## <a name="streamline-environment-creation-with-blueprints"></a>Simplifique a criação do ambiente com esquemas
[Os esquemas de Azure](../governance/blueprints/overview.md) serviço permite que arquitetos de nuvem e os grupos de tecnologia da central de informações definir um conjunto passível de repetição dos recursos do Azure que implementa e em conformidade com padrões, padrões e requisitos da organização. Planos gráficos do Azure torna possível para as equipes de desenvolvimento compilar e criar novos ambientes com um conjunto de componentes internos e a confiança de que está a criar esses ambientes dentro de conformidade organizacional rapidamente.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorizar os serviços de armazenamento para alterações de comportamento inesperados
Diagnosticar e resolver problemas numa aplicação distribuída alojada num ambiente de nuvem podem ser mais complexas do que está a ser ambientes tradicionais. Aplicativos podem ser implementados numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel, ou em alguma combinação destes ambientes. Tráfego de rede da sua aplicação poderá percorrer a redes públicas e privadas e seu aplicativo pode usar várias tecnologias de armazenamento.

Deve monitorizar continuamente os serviços de armazenamento que a aplicação utiliza para efetuar quaisquer alterações inesperadas no comportamento (por exemplo, tempos de resposta mais lentos). Utilize o registo para recolher dados de mais detalhados e analisar um problema em profundidade. As informações de diagnóstico que obter de monitorização e registo ajudam-o a determinar a causa de raiz do problema que encontrou a sua aplicação. Em seguida, pode resolver o problema e determinar os passos adequados para resolvê-lo.

[Análise de armazenamento do Azure](../storage/storage-analytics.md) efetua o registo e fornece dados de métricas para uma conta de armazenamento do Azure. Recomendamos que utilize estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenir, detetar e responder a ameaças
[Centro de segurança do Azure](../security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder a ameaças ao fornecer uma maior visibilidade (e controlo sobre) a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com várias soluções de segurança.

O escalão gratuito do Centro de segurança oferece segurança limitada para apenas os recursos do Azure. O escalão Standard expande estas capacidades no local e noutras clouds. Centro de segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controlos de acesso e aplicação para bloquear atividades maliciosas, detetar ameaças com análise e inteligência e responder rapidamente quando sob ataque. Pode experimentar o Centro de Segurança Standard sem custos durante os primeiros 60 dias. Recomendamos que [atualizar a sua subscrição do Azure para o Centro de segurança Standard](../security-center/security-center-get-started.md).

Utilize o Centro de segurança para obter uma visão central do Estado de segurança de todos os seus recursos do Azure. Visão geral, certifique-se de que os controlos de segurança adequados estão implementados e configurado corretamente e identifiquem rapidamente quaisquer recursos que necessitam de atenção.

Centro de segurança também se integra [proteção de ameaças avançada do Windows Defender (ATP)](../security-center/security-center-wdatp.md), que fornece funcionalidades completas de ponto final de deteção e resposta (EDR). Com a integração do Windows Defender ATP, detetar anomalias. Também pode detetar e responder a ataques avançados em pontos finais de servidor monitorizados pelo centro de segurança.

Quase todas as empresas têm um sistema de gerenciamento (SIEM) de segurança informações e eventos, para ajudar a identificar ameaças emergentes por meio da consolidação informações de registo de dispositivos de coleta de sinal diversificado. Os registos, em seguida, estão a ser analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todos os registos de recolha e soluções de análise.

[Azure Sentinel](../sentinel/overview.md) é uma solução de resposta (SOAR) de orquestração automatizada de segurança e segurança dimensionável e com nativa da cloud, informações e gestão de eventos (SIEM). Sentinel do Azure fornece segurança inteligente análises e informações sobre ameaças através de deteção de alerta, visibilidade de ameaças, buscar proativa e resposta a ameaças automatizada.

Aqui estão algumas práticas recomendadas para prevenir, detetar e responder a ameaças:

**Melhor prática**: Aumente a velocidade e escalabilidade da sua solução SIEM através da utilização de um SIEM com base na cloud.   
**Detalhe**: Investigar as funcionalidades e capacidades de [Azure sentinela](../sentinel/overview.md) e compará-los com as capacidades do que está atualmente a utilizar no local. Considere adotar o Azure sentinela se cumprirem os requisitos de SIEM da sua organização.

**Melhor prática**: Encontre as vulnerabilidades de segurança mais sérias para que pode priorizar a investigação.   
**Detalhe**: Reveja os [pontuação segura do Azure](../security-center/security-center-secure-score.md) para ver as recomendações resultantes das políticas do Azure e iniciativas incorporadas no Centro de segurança do Azure. Estas recomendações que ajudam a principais riscos de endereço, como atualizações de segurança, o endpoint protection, encriptação, configurações de segurança, o WAF em falta, VMs ligados à internet e muito mais.

A pontuação segura, que se baseia no Centro de controles de segurança de Internet (CIS), permite-lhe efetuar testes de desempenho a segurança da organização do Azure relativamente a origens externas. Validação externa ajuda a validar e melhorar a estratégia de segurança da sua equipa.

**Melhor prática**: Monitorize a postura de segurança de máquinas, redes, armazenamento e serviços de dados e aplicações para detetar e atribuir prioridades a problemas potenciais de segurança.  
**Detalhe**: Siga os [recomendações de segurança](../security-center/security-center-recommendations.md) no Centro de segurança a partir de, com os itens de prioridade mais altos.

**Melhor prática**: Integre alertas do Centro de segurança em sua solução (SIEM gestão) de informações e eventos da segurança.   
**Detalhe**: A maioria das organizações com um SIEM utilizá-la como uma câmara de compensação central para os alertas de segurança que exigem uma resposta do analista. Os eventos processados produzidos pelo centro de segurança são publicados no registo de atividade do Azure, um dos logs de através do Azure Monitor. Monitor do Azure oferece um pipeline consolidado para o encaminhamento qualquer um dos seus dados de monitorização para uma ferramenta SIEM. Ver [integrar soluções de segurança no Centro de segurança](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) para obter instruções. Se estiver a utilizar o Azure sentinela, veja [Centro de segurança do Azure Connect](../sentinel/connect-azure-security-center.md).

**Melhor prática**: Integração do Azure registos com o seu SIEM.   
**Detalhe**: Uso [do Azure Monitor para recolher e exportar dados](../azure-monitor/overview.md#integrate-and-export-data). Esta prática é essencial para habilitar a investigação de incidentes de segurança e retenção do registo online é limitada. Se estiver a utilizar o Azure sentinela, veja [ligar a origens de dados](../sentinel/connect-data-sources.md).

**Melhor prática**: Acelere a sua investigação e os processos de detecção e reduzir os falsos positivos, integrando recursos de ponto final de deteção e resposta (EDR) em sua investigação de ataque.   
**Detalhe**: [Ativar a integração do Windows Defender ATP](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) por meio de sua política de segurança do Centro de segurança. Considere utilizar o Azure sentinela para detecção de ameaças e resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorizar a monitorização de rede com base no cenário de ponto a ponto
Os clientes criam uma rede ponto a ponto no Azure através da combinação de recursos de rede, como uma rede virtual, ExpressRoute, o Gateway de aplicação e Balanceadores de carga. A monitorização está disponível em cada um dos recursos de rede.

[O observador de rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Utilize as suas ferramentas de diagnóstico e de visualização para monitorizar e diagnosticar condições a um nível de cenário de rede no, a e do Azure.

Seguem-se as práticas recomendadas para as ferramentas de monitorização e de disponibilidade de rede.

**Melhor prática**: Automatize a monitorização remota de redes com captura de pacotes.  
**Detalhe**: Monitorizar e diagnosticar problemas de rede sem iniciar sessão nas VMS com o observador de rede. Acionador [captura de pacotes](../network-watcher/network-watcher-alert-triggered-packet-capture.md) ao definir alertas e obter acesso a informações de desempenho em tempo real ao nível do pacote. Quando se deparar com um problema, pode investigar em pormenor para obter melhores diagnósticos.

**Melhor prática**: Obtenha informações sobre o seu tráfego de rede ao utilizar os registos de fluxo.  
**Detalhe**: Criar padrões de tráfego de uma compreensão mais aprofundada da sua rede usando [registos de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informações nos registos de fluxo ajudam-o a recolher dados de conformidade, auditoria e monitorização do seu perfil de segurança de rede.

**Melhor prática**: Diagnostica problemas de conectividade VPN.  
**Detalhe**: Utilizar o observador de rede para [diagnosticar os problemas mais comuns de Gateway de VPN e ligação](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Pode não só identificar o problema, mas também utilizar registos detalhados para continuar a investigar.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Proteger a implementação com as ferramentas de DevOps comprovadas
Utilize as seguintes práticas recomendadas do DevOps para se certificar de que suas equipas de enterprise e produtiva e eficiente.

**Melhor prática**: Automatize a compilação e implementação de serviços.  
**Detalhe**: [Infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI remover o fardo da compilação diária e a gestão de infraestrutura modular. Permite que os profissionais de TI criar e manter seu ambiente de servidor modernas de forma que é semelhante a como os desenvolvedores de software criar e manter o código da aplicação.

Pode usar [do Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para aprovisionar as suas aplicações ao utilizar um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Utilize o mesmo modelo para implementar repetidamente a sua aplicação em cada fase do ciclo de vida do aplicativo.

**Melhor prática**: Automaticamente a criar e implementar aplicações web do Azure ou serviços em nuvem.  
**Detalhe**: Pode configurar seus projetos de DevOps do Azure para [automaticamente, criar e implementar](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) para aplicações web do Azure ou serviços cloud. DevOps do Azure implementa automaticamente os binários depois de fazer uma compilação para o Azure após cada check-in de código. O processo de compilação do pacote é equivalente ao comando do pacote no Visual Studio e os passos de publicação são equivalentes para o comando Publish no Visual Studio.

**Melhor prática**: Automatize a gestão de versões.  
**Detalhe**: [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) é uma solução para automatizar a implementação de vários estágios e gerenciar o processo de lançamento. Crie pipelines de implementação contínua gerido para libertar rapidez, facilidade e, muitas vezes. Com os Pipelines do Azure, pode automatizar o processo de liberação e pode predefinida fluxos de trabalho de aprovação. Implemente no local e na nuvem, expandir e personalizar conforme necessário.

**Melhor prática**: Verifique o desempenho da sua aplicação antes de iniciá-lo ou implementar atualizações em produção.  
**Detalhe**: Executar com base na cloud [testes de carga](/azure/devops/test/load-test/overview#alternatives) para:

- Encontre problemas de desempenho na sua aplicação.
- Melhore a qualidade de implementação.
- Certifique-se de que a aplicação está sempre disponível.
- Certifique-se de que a aplicação consegue processar o tráfego para a sua campanha de lançamento ou de marketing seguinte.

[Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de código aberto populares, gratuitamente com uma forte Comunidade de segurança.

**Melhor prática**: Monitorizar o desempenho do aplicativo.  
**Detalhe**: [O Azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço de gestão (APM) de desempenho de aplicações extensível para desenvolvedores da web em várias plataformas. Utilize o Application Insights para monitorizar a sua aplicação web em direto. Deteta automaticamente anomalias de desempenho. Ele inclui ferramentas de análise para ajudar a diagnosticar problemas e para compreender o que os utilizadores fazem realmente com a sua aplicação. Foi concebido para o ajudar a melhorar continuamente o desempenho e a usabilidade.

## <a name="mitigate-and-protect-against-ddos"></a>Mitigar e proteção contra DDoS
Ataques denial of service (DDoS) é um tipo de ataque que tenta a esgotar os recursos de aplicativos. O objetivo é afetar a disponibilidade da aplicação e a sua capacidade de processar pedidos legítimos. Esses ataques estão se tornando mais sofisticados e maior tamanho e o impacto. Eles podem ser direcionados para qualquer ponto final que está publicamente acessível através da internet.

Projetando e criando para resiliência contra DDoS requerem um planeamento e conceção para uma variedade de modos de falha. Seguem-se as práticas recomendadas para criação de serviços de DDoS resilientes no Azure.

**Melhor prática**: Certifique-se de que a segurança é uma prioridade ao longo de todo o ciclo de vida de um aplicativo, desde a conceção e implementação para implantação e operações. As aplicações podem ter bugs que permitem que um volume relativamente baixo de pedidos para utilizar muitos recursos, resultando numa indisponibilidade do serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, deve ter uma boa compreensão da sua arquitetura de aplicativo e enfocar o [cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars). Deve saber volumes de tráfego típico, o modelo de conectividade entre a aplicação e outras aplicações e os pontos finais de serviço são expostos à Internet pública.

É mais importante garantir que um aplicativo é resiliente para lidar com uma negação de serviço que é direcionada para a própria aplicação. Segurança e a privacidade são incorporadas na plataforma do Azure, começando com o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). O SDL aborda a segurança em todas as fases de desenvolvimento e garante que o Azure é continuamente atualizado para que seja ainda mais seguro.

**Melhor prática**: Conceber a sua aplicação para [Dimensionar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para satisfazer a procura de uma carga amplified, especificamente no caso de um ataque DDoS. Se seu aplicativo depende de uma única instância de um serviço, ele cria um ponto único de falha. Várias instâncias de aprovisionamento torna o seu sistema, mais escalonável e mais resiliente.  
**Detalhe**: Para [App Service do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [plano do App Service](../app-service/overview-hosting-plans.md) que oferece várias instâncias.

Serviços Cloud do Azure, configure cada uma das suas funções para utilizar [várias instâncias](../cloud-services/cloud-services-choose-me.md).

Para [máquinas virtuais do Azure](../virtual-machines/windows/overview.md), certifique-se de que a sua arquitetura VM inclui mais de uma VM e que cada VM está incluído num [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). É recomendável utilizar o dimensionamento de máquinas virtuais conjuntos para capacidades de dimensionamento automático.

**Melhor prática**: Colocação de defesas de segurança num aplicativo reduz a chance de um ataque bem-sucedido. Implementar designs seguros para as suas aplicações com os recursos internos da plataforma do Azure.  
**Detalhe**: O risco de ataque aumenta com o tamanho (área de superfície) do aplicativo. Pode reduzir a área de superfície ao utilizar listas de permissões para fechar o espaço de endereços IP exposto e escutar as portas que não são necessários em balanceadores de carga ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [oGatewaydeaplicaçãodoAzure](../application-gateway/application-gateway-create-probe-portal.md)).

[Grupos de segurança de rede](../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Pode usar [etiquetas de serviço](../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade para criar as regras de segurança e configuração de segurança de rede, como uma extensão natural da estrutura de uma aplicação.

Deve implantar os serviços do Azure numa [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço para comunicar através de endereços IP privados. Por predefinição, o tráfego de serviço do Azure de uma rede virtual utiliza endereços IP públicos como endereços IP de origem.

Usando [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) comutadores tráfego para utilizar endereços privados da rede virtual como endereços IP de origem, quando estão a aceder ao serviço do Azure de uma rede virtual do serviço.

Vemos com freqüência dos clientes recursos no local ataques juntamente com seus recursos no Azure. Se estiver a ligar um ambiente no local para o Azure, minimize a exposição dos recursos no local para a internet pública.

O Azure tem dois DDoS [ofertas de serviço](../virtual-network/ddos-protection-overview.md) que fornecer proteção contra ataques à rede:

- Proteção básica está integrada no Azure, por padrão, sem custos adicionais. O dimensionamento e a capacidade da rede implementada globalmente do Azure fornece defesa contra ataques comuns de camada de rede por meio de atenuação de monitorização e em tempo real de tráfego sempre ativa. Basic não requer nenhuma alteração de configuração ou aplicação do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS como o DNS do Azure.
- Proteção padrão fornece capacidades avançadas de mitigação de DDoS contra ataques de rede. Ele automaticamente é ajustado para proteger os seus recursos do Azure específicos. Proteção é simples para ativar durante a criação de redes virtuais. Ele também pode ser feito após a criação e exige sem alterações de aplicação ou recurso.

## <a name="enable-azure-policy"></a>Ativar o Azure Policy
[O Azure Policy](../governance/policy/overview.md) é um serviço no Azure que utilizar para criar, atribuir e gerir políticas. Estas políticas impõem regras e efeitos aos recursos, para que esses recursos mantêm-se em conformidade com os seus padrões empresariais e contratos de nível de serviço. O Azure Policy atende a essa necessidade através da avaliação de seus recursos de não conformidade com as políticas atribuídas.

Ative a política do Azure monitorizar e impor a política de escrita da sua organização. Isto irá garantir o cumprimento da sua empresa ou requisitos de normas de segurança ao gerir centralmente políticas de segurança em suas cargas de trabalho de cloud híbrida. Saiba como [criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md). Ver [estrutura de definição de política do Azure](../governance/policy/concepts/definition-structure.md) para uma descrição geral dos elementos de uma política.

Aqui estão algumas práticas recomendadas de segurança a seguir depois de adotar o Azure Policy:

**Melhor prática**: Política suporta vários tipos de efeitos. Pode ler sobre os mesmos em [estrutura de definição de política do Azure](../governance/policy/concepts/definition-structure.md#policy-rule). Operações de negócio podem ser afetadas negativamente pela **negar** efeito e o **remediar** efeitos, portanto, comece com o **de auditoria** efeito para limitar o risco de impacto negativo do política.   
**Detalhe**: [Iniciar as implementações de política no modo de auditoria](../governance/policy/concepts/definition-structure.md#policy-rule) e, em seguida, avançar mais tarde para **negar** ou **remediar**. Testar e analisar os resultados do efeito de auditoria, antes de prosseguir para **negar** ou **remediar**.

Para obter mais informações, consulte [criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

**Melhor prática**: Identifique as funções responsáveis pela monitorização de violações de políticas e assegurar que a ação de remediação certo é executada rapidamente.   
**Detalhe**: A compatibilidade de monitor de função atribuída por meio de ter o [portal do Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou através da [linha de comandos](../governance/policy/how-to/get-compliance-data.md#command-line).

**Melhor prática**: Política do Azure é uma representação técnica de políticas de escrita de uma organização. Mapear todas as políticas do Azure para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: Mapeamento de documentos na documentação da sua organização ou na política do Azure em si, adicionando uma referência para a política organizacional do Azure [descrição da política](../governance/policy/concepts/definition-structure.md#display-name-and-description) ou a política do Azure [iniciativa](../governance/policy/concepts/definition-structure.md#initiatives) Descrição.

## <a name="monitor-azure-ad-risk-reports"></a>Relatórios de risco de monitor do Azure AD
A grande maioria das falhas de segurança ocorrem quando os atacantes obtêm acesso a um ambiente, por roubo de identidade de um utilizador. Detetar identidades comprometidas não é tarefa fácil. Azure AD utiliza de machine learning e heurística de algoritmos para detectar ações suspeitas relacionadas com as contas de utilizador. Cada detetado, ação suspeita é armazenada num registo denominado um [evento de risco](../active-directory/reports-monitoring/concept-risk-events.md). Eventos de risco são registrados na segurança do Azure AD relatórios. Para obter mais informações, leia sobre o [utilizadores no relatório de segurança de risco](../active-directory/reports-monitoring/concept-user-at-risk.md) e o [relatório de inícios de sessão de risco de segurança](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passos Seguintes
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com

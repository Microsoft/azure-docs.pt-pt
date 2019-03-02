---
title: Descrição geral da segurança operacionais do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da segurança operacional do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: 38054d6ee3799296887726954ef1f096945aeaeb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241090"
---
# <a name="azure-operational-security-overview"></a>Descrição geral da segurança operacionais do Azure

[Segurança operacional Azure](https://docs.microsoft.com/azure/security/azure-operational-security) refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicações e outros ativos no Microsoft Azure. É uma estrutura que incorpore os dados recolhidos através de uma variedade de funcionalidades que são exclusivos da Microsoft. Esses recursos incluem o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e profundo entendimento do cenário de ameaças de cibersegurança.

## <a name="azure-management-services"></a>Serviços de gestão do Azure

Uma equipe de operações de TI é responsável por gerenciar a infraestrutura de datacenters, aplicativos e dados, incluindo a estabilidade e a segurança desses sistemas. No entanto, a ganhar informações de segurança em toda a aumentar, muitas vezes, os ambientes complexos de TI requer que as organizações que improvisar dados de vários sistemas de gerenciamento e segurança.

[Registos do Microsoft Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é uma solução baseada na cloud IT management que ajuda a gerenciar e proteger no local e a infraestrutura na cloud. Funcionalidade central é fornecida pelos serviços seguintes que são executadas no Azure. Azure inclui vários serviços que ajudam a gerenciar e proteger no local e a infraestrutura na cloud. Cada serviço fornece uma função de gestão específico. Pode combinar serviços para alcançar cenários de gestão diferentes. 

### <a name="azure-monitor"></a>Azure Monitor

[O Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) recolhe dados de origens geridas em arquivos de dados central. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos através da API. Depois dos dados são recolhidos, está disponível para alertas, análises e exportação. 

Pode consolidar dados de várias origens e combinar dados dos seus serviços do Azure com o seu ambiente no local existente. Registos de Monitor do Azure separa também claramente a recolha dos dados das ações tomadas nesses dados, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automatização

[A automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) fornece uma forma de automatizar as tarefas manuais, de execução longa, propenso a erros e repetidas com frequência, que normalmente são executadas num ambiente de nuvem e empresa. Ele economiza tempo e aumenta a fiabilidade das tarefas administrativas. Ele até mesmo agenda estas tarefas para serem executadas automaticamente em intervalos regulares. Pode automatizar processos utilizando runbooks ou automatizar a gestão de configuração através da utilização de Desired State Configuration.

### <a name="backup"></a>Cópia de segurança

[O Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é um serviço baseado no Azure que pode utilizar para criar cópias de segurança (ou proteger) e restaurar os dados na Cloud da Microsoft. O Azure Backup substitui a solução de cópia de segurança fora do local ou um existente no local com uma solução baseada na nuvem que é fiável, segura e competitiva em termos de custo. 

O Azure Backup oferece componentes que são transferidos e implementados no computador adequado ou no servidor ou na cloud. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes de cópia de segurança do Azure (quer esteja a proteger dados no local ou na cloud) podem ser utilizados para fazer cópias de segurança de dados para um cofre de serviços de recuperação do Azure no Azure. 

Para obter mais informações, consulte a [tabela de componentes de cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[O Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidade do negócio ao orquestrar a replicação de máquinas no local físico e virtual para o Azure ou para um site secundário. Se o site primário não estiver disponível, efetuar a ativação pós-falha para a localização secundária, para que os utilizadores podem continuar a trabalhar. Efetuar a ativação pós-falha quando os sistemas estiverem a funcionar novamente. Centro de segurança do Azure Utilize para efetuar mais a deteção de ameaças inteligente e eficiente.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) é uma solução identidade de serviço que:

-   Ativa a gestão de identidades e acesso (IAM) como um serviço cloud.
-   Fornece gestão de acesso central, início de sessão único (SSO) e relatórios.
-   Suporta a gestão de acesso integrada para [milhares de aplicações](https://azure.microsoft.com/marketplace/active-directory/) no Azure Marketplace, incluindo o Salesforce, o Google Apps, o caixa e o Concur.

O Azure AD também inclui um conjunto completo de [capacidades de gestão de identidades](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), incluindo estes:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Gestão de palavras-passe self-service](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gestão de grupos self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Gestão de contas com privilégios](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Monitorização da utilização de aplicações](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Auditoria de avançadas](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorização de segurança e alertas](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Com o Azure Active Directory, todos os aplicativos que publica para seus parceiros e clientes (consumidor ou negócio) tenham a mesma identidade e aceder a recursos de gerenciamento. Isto permite-lhe reduzir significativamente os custos operacionais.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade (e controlo sobre) a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições. Ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

[Salvaguardar os dados da máquina virtual (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) no Azure, oferecendo visibilidade sobre as definições de segurança da sua máquina virtual e monitorização de ameaças. O Centro de Segurança pode monitorizar as máquinas virtuais relativamente a:

- Definições de segurança do sistema operativo com as regras de configuração recomendada.
- Segurança do sistema e atualizações críticas em falta.
- Recomendações do Endpoint protection.
- Validação de encriptação de disco.
- Ataques baseados na rede.

Centro de segurança utiliza [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Fornece o RBAC [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, verá informações relacionadas com a um recurso apenas quando estiver atribuído a função de proprietário, contribuinte ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

>[!Note]
>Para saber mais sobre as funções e permitido ações no Centro de segurança, consulte [permissões no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Centro de segurança utiliza o Microsoft Monitoring Agent. Este é o mesmo agente que utiliza o serviço do Azure Monitor. Dados recolhidos por este agente são armazenados em qualquer um de um existentes do Log Analytics [área de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) associado à sua subscrição do Azure ou uma nova área de trabalho, tendo em conta a geolocalização da VM.

## <a name="azure-monitor"></a>Azure Monitor

Problemas de desempenho na sua aplicação na cloud podem afetar o seu negócio. Com vários componentes interconectados que têm e freqüentes lançamentos, degradações podem acontecer em qualquer altura. E se estiver a desenvolver uma aplicação, os usuários normalmente Descubra problemas que não foram encontrados no teste. Deve saber sobre estes problemas imediatamente, e deve ter as ferramentas para diagnosticar e corrigir os problemas.

[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) é a ferramenta básica para monitorizar serviços em execução no Azure. Ele fornece dados ao nível da infraestrutura relativamente ao débito de um serviço e o ambiente. Se estiver a gerir as suas aplicações em Azure e decidir se deve aumentar ou reduzir recursos verticalmente, o Azure Monitor é o ponto de partida.

Também pode utilizar dados de monitorização para obter informações aprofundadas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual. 

Monitor do Azure inclui os seguintes componentes.

### <a name="azure-activity-log"></a>Registo de atividades do Azure

O [registo de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornece informações sobre as operações executadas nos recursos na sua subscrição. Anteriormente era conhecido como "Registo de auditoria" ou "Registo operacional", porque comunica eventos de plano de controlo para as suas subscrições.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

[Registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso e fornecer dados avançados e frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Registos de sistema de eventos do Windows são uma categoria de registos de diagnóstico para as VMs. BLOBs, tabelas e registos de fila são categorias dos registos de diagnóstico para contas de armazenamento.

Os registos de diagnóstico diferem da [registo de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O registo de atividades fornece informações sobre as operações executadas nos recursos na sua subscrição. Os registos de diagnóstico fornecem informações sobre operações que o seu recurso efetuadas em si.

### <a name="metrics"></a>Métricas

O Azure Monitor proporciona telemetria, que permite-lhe ver o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure é o [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (também contadores de desempenho chamado) emitidos pelos recursos mais do Azure. O Azure Monitor proporciona várias formas de configurar e consumir estas métricas de monitorização e resolução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Diagnóstico do Azure permite a recolha de dados de diagnóstico num aplicativo implantado. Pode utilizar a extensão de diagnóstico de várias origens. São suportados atualmente [funções do serviço cloud do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) com o Microsoft Windows, e [do Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Observador de rede do Azure

Os clientes criam uma rede ponto a ponto no Azure através da orquestração e composição de recursos de rede individuais como redes virtuais, Azure ExpressRoute, o Gateway de aplicação do Azure e Balanceadores de carga. A monitorização está disponível em cada um dos recursos de rede.

A rede ponto a ponto pode ter configurações complexas e interações entre os recursos. O resultado é cenários complexos que precisam baseados em cenários de monitorização através de [observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Observador de rede simplifica a monitorização e diagnóstico de rede do Azure. Pode usar as ferramentas de diagnóstico e de visualização no observador de rede para:

- Fazer capturas de pacotes remoto numa máquina virtual do Azure.
- Obter informações sobre o tráfego através da utilização de registos de fluxo.
- Diagnosticar o Gateway de VPN do Azure e ligações.

Observador de rede atualmente tem as seguintes capacidades:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Fornece uma vista de várias interconexões e associações entre recursos de rede num grupo de recursos.
- [Captura de pacote variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Captura de dados de pacote dentro e fora de uma máquina virtual. As opções de filtragem avançadas e os controlos otimizados, como a possibilidade de definir limites de tempo e tamanho, proporcionam versatilidade. Os dados de pacotes podem ser armazenados num armazenamento de BLOBs ou no disco local no formato. cap.
- [Verificação do fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base nos parâmetros de pacote de 5 cadeias de identificação para obter informações de fluxo (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se um grupo de segurança nega o pacote, são devolvidas a regra e o grupo que negou o pacote.
- [Do próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para pacotes que está a ser encaminhado nos recursos de infraestrutura de rede do Azure, para que pode diagnosticar quaisquer rotas definidas pelo utilizador configurado incorretamente.
- [Vista de grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Obtém as regras de segurança aplicada e eficiente que são aplicadas numa VM.
- [Registos de fluxo NSG para grupos de segurança de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Permitem-lhe capturar registos relacionados com o tráfego que é permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por informações de 5 cadeias de identificação: IP, IP de destino, porta de origem, porta de destino e protocolo de origem.
- [Gateway de rede virtual e a resolução de problemas de ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Fornece a capacidade de resolver problemas de ligações e gateways de rede virtual.
- [Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Permite-lhe ver a utilização de recursos de rede contra limites.
- [Os registos de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Fornece um único painel para ativar ou desativar os registos de diagnóstico para os recursos de rede num grupo de recursos.

Para obter mais informações, consulte [configurar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Transparência de acesso de fornecedor de serviço cloud

[Cofre de cliente para o Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) é um serviço integrado no portal do Azure que lhe dá controle explícito nas raras instâncias ao engenheiro de suporte da Microsoft poderá ter acesso aos seus dados para resolver um problema. Existem muito poucas instâncias, como um problema de acesso remoto depuração, em que o engenheiro de suporte da Microsoft requer permissões elevadas para resolver este problema. Nesses casos, os engenheiros da Microsoft utilizam o serviço de acesso just-in-time, que fornece autorização limitada, com limite de tempo limitado para o serviço de acesso.  
Embora a Microsoft tenha sempre obtida consentimento de cliente para o acesso, o Cofre de cliente agora dá-lhe a capacidade para rever e aprovar ou negar esses pedidos a partir do Portal do Azure. Não vão ser concedidos acesso a engenheiros de suporte da Microsoft até que a aprovação do pedido.

## <a name="standardized-and-compliant-deployments"></a>Implementações de padronizada e em conformidade

[Planos gráficos do Azure](../governance/blueprints/overview.md) ativar grupos de tecnologia da central de informações e arquitetos de nuvem definir um conjunto passível de repetição dos recursos do Azure que implementar e aderir aos padrões, padrões e requisitos da organização.  
Isto torna possível para as equipas de DevOps criar rapidamente e criar novos ambientes e confiar que eles estão criando-os com a infraestrutura de que mantém a conformidade organizacional. Planos gráficos proporcionam uma forma declarativa para orquestrar a implementação de vários modelos de recursos e outros artefactos, tais como: 

- Atribuições de Funções
- Atribuições de Política
- Modelos do Azure Resource Manager
- Grupos de Recursos

## <a name="devops"></a>DevOps

Antes de [operações de desenvolvedor (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) foram de equipes de desenvolvimento de aplicativos, responsável pela coleta de requisitos comerciais de um programa de software e escrever código. Em seguida, uma equipa separada do controle de qualidade testou o programa num ambiente de desenvolvimento isolado. Se os requisitos foram satisfeitos, a equipe de controle de qualidade lançou o código para operações de implementar. As equipes de implantação foram ainda mais fragmentadas em grupos, como o sistema de rede e base de dados. Sempre que um programa de software foi "emitido por cima da muro" para uma equipe independente, ele adicionou afunilamentos.

O DevOps permite que as equipes a fornecer soluções mais seguras e de qualidade superior mais depressa e dispendiosa. Os clientes esperam uma experiência de dinâmica e fiável ao consumir o software e serviços. As equipas rapidamente devem iterar sobre atualizações de software e medir o impacto das atualizações. Eles tem de responder rapidamente com novas iterações de desenvolvimento para resolver problemas ou fornecer mais valor.  

Plataformas de cloud como o Microsoft Azure tem removido afunilamentos tradicionais e ajudou commoditize infraestrutura. Software reigns todas as empresas como a importante diferenciador e o fator dos resultados empresariais. Nenhuma organização, desenvolvedor ou de trabalho IT pode ou deve evitar o movimento de DevOps.

Profissionais de DevOps maduros adotam várias das seguintes práticas. Essas práticas [envolvem pessoas](https://www.visualstudio.com/learn/what-is-devops-culture/) estratégias de formulário com base em cenários de negócios. As ferramentas podem ajudar a automatizar as várias práticas.

- [Gerenciamento de projetos e de planejamento agile](https://www.visualstudio.com/learn/what-is-agile/) técnicas são usadas para planejar e isolar o trabalho em sprints, gerir a capacidade da Equipe e ajudar as equipes rapidamente se adaptar às necessidades empresarias em mudança.
- [Controle de versão, normalmente, com o Git](https://www.visualstudio.com/learn/what-is-git/), permite que as equipes localizadas em qualquer lugar do mundo para partilhar a origem e integrar com ferramentas de desenvolvimento de software para automatizar o pipeline de lançamento.
- [Integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/) unidades de intercalação em curso e o teste de código, o que leva a localizar defeitos no início.  Outros benefícios incluem menos tempo gasto no lutando contra problemas de mesclagem e comentários para as equipes de desenvolvimento.
- [Entrega contínua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) das soluções de software para produção e teste de ambientes de ajuda as organizações rapidamente corrigir bugs e responder a constante mudança dos requisitos comerciais.
- [Monitorização](https://www.visualstudio.com/learn/what-is-monitoring/) de aplicativos em execução – ambientes para o estado de funcionamento do aplicativo, bem como a utilização do cliente – ajuda as organizações uma hipótese de formulário e validar rapidamente ou conteste estratégias incluindo produção.  Dados ricos são capturados e armazenados em vários formatos de registo.
- [Infraestrutura como código (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) é uma prática que permite a automatização e a validação da criação e a subdivisão do redes e máquinas virtuais para o ajudar a entrega de plataformas de hospedagem de aplicativo seguro e estável.
- [Microsserviços](https://www.visualstudio.com/learn/what-are-microservices/) arquitetura é utilizada para isolar os casos de utilização empresarial em pequenas serviços reutilizáveis.  Esta arquitetura permite a escalabilidade e eficiência.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a solução de segurança e auditoria, veja os artigos seguintes:

- [Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)

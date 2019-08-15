---
title: Visão geral da segurança operacional do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da segurança operacional do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: 31e59f913fa29c92bc1b97029d8712bbd9b68ab8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934667"
---
# <a name="azure-operational-security-overview"></a>Visão geral da segurança operacional do Azure

[Segurança operacional do Azure] (/Azure/Security/Fundamentals/Operational-Security refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure. É uma estrutura que incorpora o conhecimento obtido por meio de uma variedade de recursos que são exclusivos da Microsoft. Esses recursos incluem o SDL (ciclo de vida de desenvolvimento de segurança da Microsoft), o programa Microsoft Security Response Center e uma percepção profunda do panorama de ameaças do segurança cibernética.

## <a name="azure-management-services"></a>Serviços de gerenciamento do Azure

Uma equipe de operações de ti é responsável por gerenciar a infraestrutura, os aplicativos e os dados do datacenter, incluindo a estabilidade e a segurança desses sistemas. No entanto, obter informações de segurança ao aumentar ambientes de ti complexos geralmente exige que as organizações montamm dados de vários sistemas de segurança e gerenciamento.

[Microsoft Azure logs de monitoramento](/azure/operations-management-suite/operations-management-suite-overview) é uma solução de gerenciamento de ti baseada em nuvem que ajuda você a gerenciar e proteger sua infraestrutura local e na nuvem. Sua funcionalidade principal é fornecida pelos seguintes serviços que são executados no Azure. O Azure inclui vários serviços que ajudam a gerenciar e proteger sua infraestrutura local e na nuvem. Cada serviço fornece uma função de gerenciamento específica. Você pode combinar serviços para alcançar diferentes cenários de gerenciamento. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](/azure/azure-monitor/overview) coleta dados de fontes gerenciadas em armazenamentos de dados centrais. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos por meio da API. Depois que os dados são coletados, eles estão disponíveis para alertas, análises e exportação. 

Você pode consolidar dados de uma variedade de fontes e combinar dados de seus serviços do Azure com seu ambiente local existente. Os logs de Azure Monitor também separam claramente a coleta dos dados da ação executada nesses dados, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automatização

A [automação do Azure](/azure/automation/automation-intro) fornece uma maneira de automatizar as tarefas manuais, demoradas, propensas a erros e repetidas com frequência que normalmente são executadas em um ambiente corporativo e de nuvem. Ele economiza tempo e aumenta a confiabilidade das tarefas administrativas. Ele até agenda essas tarefas para serem executadas automaticamente em intervalos regulares. Você pode automatizar processos usando runbooks ou automatizar o gerenciamento de configuração usando a configuração de estado desejado.

### <a name="backup"></a>Criar cópia de segurança

O [backup do Azure](/azure/backup/backup-introduction-to-azure-backup) é o serviço baseado no Azure que você pode usar para fazer backup (ou proteger) e restaurar seus dados no Microsoft Cloud. O backup do Azure substitui sua solução existente de backup local ou fora do local por uma solução baseada em nuvem que é confiável, segura e de custo competitivo. 

O backup do Azure oferece componentes que você baixa e implanta no computador ou servidor apropriado ou na nuvem. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes de backup do Azure (se você estiver protegendo dados locais ou na nuvem) podem ser usados para fazer backup de dados em um cofre dos serviços de recuperação do Azure no Azure. 

Para obter mais informações, consulte a [tabela componentes de backup do Azure](/azure/backup/backup-introduction-to-azure-backup#which-backup-agent-should-i-use).

### <a name="site-recovery"></a>Site Recovery

O [Azure site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) fornece continuidade de negócios ao orquestrar a replicação de máquinas virtuais e físicas locais para o Azure ou para um site secundário. Se o seu site primário não estiver disponível, você faz failover para o local secundário para que os usuários possam continuar trabalhando. Você faz failback quando os sistemas retornam para a ordem de trabalho. Use a central de segurança do Azure para realizar uma detecção de ameaças mais inteligente e eficaz.

## <a name="azure-active-directory"></a>Azure Active Directory

O [Azure Active Directory (AD do Azure)](/azure/active-directory/active-directory-enable-sso-scenario) é um serviço de identidade abrangente que:

-   Habilita o IAM (gerenciamento de acesso e identidade) como um serviço de nuvem.
-   Fornece gerenciamento de acesso central, SSO (logon único) e relatórios.
-   Dá suporte ao gerenciamento de acesso integrado para [milhares de aplicativos](https://azure.microsoft.com/marketplace/active-directory/) no Azure Marketplace, incluindo Salesforce, Google Apps, Box e concur.

O Azure AD também inclui um pacote completo de [recursos de gerenciamento](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)de identidades, incluindo:

- [Multi-Factor Authentication](/azure/multi-factor-authentication/multi-factor-authentication)
- [Gerenciamento de senhas de autoatendimento](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gerenciamento de grupo de autoatendimento](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Gerenciamento de conta com privilégios](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Controlo de acesso baseado em funções](/azure/role-based-access-control/overview)
- [Monitoramento de uso do aplicativo](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Auditoria avançada](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitoramento e alertas de segurança](/azure/operations-management-suite/oms-security-responding-alerts)

Com o Azure Active Directory, todos os aplicativos que você publica para seus parceiros e clientes (negócios ou consumidores) têm os mesmos recursos de gerenciamento de identidade e acesso. Isso permite que você reduza significativamente os custos operacionais.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

A [central de segurança do Azure](/azure/security-center/security-center-intro) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade no (e controle sobre) a segurança de seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas. Ele ajuda a detectar ameaças que poderiam passar despercebidas e trabalha com um amplo ecossistema de soluções de segurança.

[Proteja os dados da VM (máquina virtual)](/azure/security-center/security-center-linux-virtual-machine) no Azure fornecendo visibilidade das configurações de segurança da sua máquina virtual e monitoramento de ameaças. O Centro de Segurança pode monitorizar as máquinas virtuais relativamente a:

- Configurações de segurança do sistema operacional com as regras de configuração recomendadas.
- Segurança do sistema e atualizações críticas ausentes.
- Recomendações do Endpoint Protection.
- Validação de criptografia de disco.
- Ataques baseados em rede.

A central de segurança usa o [RBAC (controle de acesso baseado em função)](/azure/role-based-access-control/role-assignments-portal). O RBAC fornece [funções internas](../../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. Na central de segurança, você vê informações relacionadas a um recurso somente quando recebe a função de proprietário, colaborador ou leitor para a assinatura ou grupo de recursos ao qual um recurso pertence.

>[!Note]
>Para saber mais sobre funções e ações permitidas na central de segurança, confira [permissões na central de segurança do Azure](/azure/security-center/security-center-permissions).

A central de segurança usa o Microsoft Monitoring Agent. Esse é o mesmo agente usado pelo serviço de Azure Monitor. Os dados coletados desse agente são armazenados em um espaço de [trabalho](/azure/log-analytics/log-analytics-manage-access) log Analytics existente associado à sua assinatura do Azure ou a um novo espaço de trabalho, levando em conta a localização geográfica da VM.

## <a name="azure-monitor"></a>Azure Monitor

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com vários componentes interconectados e versões frequentes, as degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, os usuários geralmente descobrirão problemas que você não encontrou durante o teste. Você deve saber sobre esses problemas imediatamente e deve ter ferramentas para diagnosticar e corrigir os problemas.

[Azure monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) é uma ferramenta básica para o monitoramento de serviços em execução no Azure. Ele fornece dados de nível de infraestrutura sobre a taxa de transferência de um serviço e o ambiente ao redor. Se você estiver gerenciando seus aplicativos no Azure e decidindo se deseja escalar ou reduzir verticalmente os recursos, Azure Monitor é o lugar para começar.

Você também pode usar dados de monitoramento para obter informações aprofundadas sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção do aplicativo, ou automatizar ações que, caso contrário, exigirão intervenção manual. 

Azure Monitor inclui os componentes a seguir.

### <a name="azure-activity-log"></a>Registo de atividades do Azure

O [log de atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornece informações sobre as operações que foram executadas em recursos em sua assinatura. Anteriormente, era conhecido como "log de auditoria" ou "log operacional", pois ele relata eventos de plano de controle para suas assinaturas.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

[Os logs de diagnóstico do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Os logs do sistema de eventos do Windows são uma categoria de logs de diagnóstico para VMs. Os logs de BLOB, tabela e fila são categorias de logs de diagnóstico para contas de armazenamento.

Os logs de diagnóstico são diferentes do [log de atividades](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O log de atividades fornece informações sobre as operações que foram executadas em recursos em sua assinatura. Os logs de diagnóstico fornecem informações sobre as operações que seu recurso realizou.

### <a name="metrics"></a>Métricas

Azure Monitor fornece telemetria que oferece visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do [](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) Azure são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Diagnóstico do Azure habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes. Atualmente, há suporte para [funções de serviço de nuvem do Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuais do Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service) executando o Microsoft Windows e [Service Fabric do Azure](/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Observador de rede do Azure

Os clientes criam uma rede de ponta a ponta no Azure ao orquestrar e compor recursos de rede individuais, como redes virtuais, Azure ExpressRoute, gateway de Aplicativo Azure e balanceadores de carga. O monitoramento está disponível em cada um dos recursos de rede.

A rede de ponta a ponta pode ter configurações e interações complexas entre os recursos. O resultado são cenários complexos que precisam de monitoramento baseado em cenário por meio [do observador de rede do Azure](/azure/network-watcher/network-watcher-monitoring-overview).

O observador de rede simplifica o monitoramento e o diagnóstico de sua rede do Azure. Você pode usar as ferramentas de diagnóstico e visualização no observador de rede para:

- Faça capturas de pacotes remotas em uma máquina virtual do Azure.
- Obter informações sobre o tráfego de rede usando logs de fluxo.
- Diagnostique o gateway e as conexões de VPN do Azure.

O observador de rede atualmente tem os seguintes recursos:

- [Topologia](/azure/network-watcher/network-watcher-topology-overview): Fornece uma exibição das várias interconexões e associações entre os recursos de rede em um grupo de recursos.
- [Captura de pacote variável](/azure/network-watcher/network-watcher-packet-capture-overview): Captura dados de pacote dentro e fora de uma máquina virtual. As opções de filtragem avançadas e os controlos otimizados, como a possibilidade de definir limites de tempo e tamanho, proporcionam versatilidade. Os dados do pacote podem ser armazenados em um repositório de BLOB ou no disco local no formato. Cap.
- [Verificação de fluxo de IP](/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base em parâmetros de pacote de 5 tuplas para informações de fluxo (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se um grupo de segurança negar o pacote, a regra e o grupo que negaram o pacote serão retornados.
- [Próximo salto](/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para os pacotes que estão sendo roteados na malha de rede do Azure, para que você possa diagnosticar quaisquer rotas definidas pelo usuário mal configuradas.
- [Exibição do grupo de segurança](/azure/network-watcher/network-watcher-security-group-view-overview): Obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM.
- [Logs de fluxo do NSG para grupos de segurança de rede](/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Permite que você capture logs relacionados ao tráfego permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por informações de 5 tuplas: IP de origem, IP de destino, porta de origem, porta de destino e protocolo.
- [Solução de problemas de gateway de rede virtual e conexão](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Fornece a capacidade de solucionar problemas de gateways e conexões de rede virtual.
- [Limites de assinatura de rede](/azure/network-watcher/network-watcher-monitoring-overview): Permite exibir o uso de recursos de rede em relação aos limites.
- [Logs de diagnóstico](/azure/network-watcher/network-watcher-monitoring-overview): Fornece um único painel para habilitar ou desabilitar os logs de diagnóstico para recursos de rede em um grupo de recursos.

Para obter mais informações, consulte Configurar o observador de [rede](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Transparência de acesso do provedor de serviço de nuvem

[Sistema de proteção de dados do cliente para Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) é um serviço integrado ao portal do Azure que lhe dá controle explícito na instância rara quando um engenheiro de suporte da Microsoft pode precisar de acesso aos seus dados para resolver um problema. Há poucas instâncias, como um problema de depuração de acesso remoto, em que um engenheiro de Suporte da Microsoft requer permissões elevadas para resolver esse problema. Nesses casos, os engenheiros da Microsoft usam o serviço de acesso just-in-time que fornece autorização limitada de limite de tempo com acesso limitado ao serviço.  
Embora a Microsoft sempre tenha obtido o consentimento do cliente para acesso, Sistema de Proteção de Dados do Cliente agora oferece a capacidade de revisar e aprovar ou negar essas solicitações no portal do Azure. Os engenheiros de suporte da Microsoft não receberão acesso até que você aprove a solicitação.

## <a name="standardized-and-compliant-deployments"></a>Implantações padronizadas e em conformidade

Os [planos gráficos do Azure](/azure/governance/blueprints/overview) permitem que os arquitetos de nuvem e os grupos de tecnologia da informação central definam um conjunto repetível de recursos do Azure que implementam e aderem aos padrões, padrões e requisitos de uma organização.  
Isso possibilita que as equipes do DevOps compilem e criem novos ambientes rapidamente e confiem que estão criando-os com a infraestrutura que mantém a conformidade organizacional. Os planos gráficos fornecem uma maneira declarativa de orquestrar a implantação de vários modelos de recursos e outros artefatos, como: 

- Atribuições de Função
- Atribuições de Política
- Modelos do Azure Resource Manager
- Grupos de Recursos

## <a name="devops"></a>DevOps

Antes do desenvolvimento de aplicativos [DevOps (operações de desenvolvedor), as](https://www.visualstudio.com/learn/what-is-devops/) equipes eram responsáveis pela coleta de requisitos de negócios para um programa de software e pela gravação de código. Em seguida, uma equipe de p e r separada testou o programa em um ambiente de desenvolvimento isolado. Se os requisitos foram atendidos, a equipe de p e r liberou o código para operações a serem implantadas. As equipes de implantação foram fragmentadas ainda mais em grupos como rede e banco de dados. Cada vez que um programa de software foi "lançado pela parede" para uma equipe independente, ele adicionou afunilamentos.

O DevOps permite que as equipes forneçam soluções mais seguras e de maior qualidade de forma mais rápida e barata. Os clientes esperam uma experiência dinâmica e confiável ao consumir software e serviços. As equipes devem iterar rapidamente nas atualizações de software e medir o impacto das atualizações. Eles devem responder rapidamente com novas iterações de desenvolvimento para resolver problemas ou fornecer mais valor.  

As plataformas de nuvem, como Microsoft Azure, removeram afunilamentos tradicionais e ajudaram a ter a infraestrutura de mercadorias. Software Reigns em cada empresa como o principal diferencial e fator nos resultados de negócios. Nenhuma organização, desenvolvedor ou trabalho de ti pode ou deve evitar o movimento de DevOps.

Os profissionais maduros de DevOps adotam várias das práticas a seguir. Essas práticas [envolvem as pessoas](https://www.visualstudio.com/learn/what-is-devops-culture/) para formar estratégias com base nos cenários de negócios. As ferramentas podem ajudar a automatizar as várias práticas.

- As técnicas de [planejamento e gerenciamento de projeto Agile](https://www.visualstudio.com/learn/what-is-agile/) são usadas para planejar e isolar o trabalho em sprints, gerenciar a capacidade da equipe e ajudar as equipes a adaptarem-se rapidamente às necessidades de negócios em constante mudança.
- O [controle de versão, geralmente com o Git](https://www.visualstudio.com/learn/what-is-git/), permite que as equipes localizadas em qualquer lugar do mundo compartilhem a origem e se integrem a ferramentas de desenvolvimento de software para automatizar o pipeline de lançamento.
- A [integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/) impulsiona a mesclagem e o teste contínuos de código, o que leva a encontrar defeitos antecipadamente.  Outros benefícios incluem menos tempo desperdiçado em combate a problemas de mesclagem e comentários rápidos para equipes de desenvolvimento.
- A [entrega contínua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) de soluções de software para ambientes de produção e teste ajuda as organizações a corrigir bugs rapidamente e responder a requisitos de negócios em constante mudança.
- [Monitoramento](https://www.visualstudio.com/learn/what-is-monitoring/) de aplicativos em execução – incluindo ambientes de produção para integridade do aplicativo, bem como o uso do cliente – ajuda as organizações a formar uma hipótese e a validar rapidamente ou refutar estratégias.  Os dados avançados são capturados e armazenados em vários formatos de log.
- A [IaC (infraestrutura como código)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) é uma prática que permite a automação e a validação da criação e da desmontagem de redes e máquinas virtuais para ajudar a fornecer plataformas de Hospedagem de aplicativos seguras e estáveis.
- [](https://www.visualstudio.com/learn/what-are-microservices/) A arquitetura de microserviços é usada para isolar casos de uso de negócios em serviços pequenos reutilizáveis.  Essa arquitetura permite escalabilidade e eficiência.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a solução Segurança e Auditoria, consulte os seguintes artigos:

- [Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Centro de Segurança do Azure](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)

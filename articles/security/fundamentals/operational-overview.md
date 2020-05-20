---
title: Visão geral da segurança operacional do Azure Microsoft Docs
description: Este artigo fornece uma visão geral da segurança operacional do Azure.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 00a71fec9c0bfc1db45eee7129b7c2a8adaef0fa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674238"
---
# <a name="azure-operational-security-overview"></a>Visão geral de segurança operacional do Azure

A [segurança operacional do Azure](/azure/security/fundamentals/operational-security) refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Microsoft Azure. É uma estrutura que incorpora o conhecimento adquirido através de uma variedade de capacidades que são únicas para a Microsoft. Estas capacidades incluem o Microsoft Security Development Lifecycle (SDL), o programa Do Microsoft Security Response Center e uma profunda consciência do panorama das ameaças à cibersegurança.

## <a name="azure-management-services"></a>Serviços de gestão Azure

Uma equipa de operações de TI é responsável pela gestão de infraestruturas, aplicações e dados de datacenter, incluindo a estabilidade e segurança destes sistemas. No entanto, obter informações de segurança através de ambientes de TI complexos crescentes muitas vezes requer que as organizações reúnam dados de múltiplos sistemas de segurança e gestão.

Os registos do [Microsoft Azure Monitor](/azure/operations-management-suite/operations-management-suite-overview) são uma solução de gestão de TI baseada na nuvem que o ajuda a gerir e proteger as suas infraestruturas no local e na nuvem. A sua funcionalidade principal é fornecida pelos seguintes serviços que funcionam no Azure. O Azure inclui vários serviços que o ajudam a gerir e proteger as suas infraestruturas no local e na nuvem. Cada serviço fornece uma função de gestão específica. Pode combinar serviços para alcançar diferentes cenários de gestão. 

### <a name="azure-monitor"></a>Azure Monitor

[O Azure Monitor](/azure/azure-monitor/overview) recolhe dados de fontes geridas em lojas de dados centrais. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos os dados, está disponível para alerta, análise e exportação.

Pode consolidar dados de várias fontes e combinar dados dos seus serviços Azure com o ambiente existente no local. Os registos do Monitor Azure também separam claramente a recolha dos dados das medidas tomadas sobre esses dados, de modo a que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automatização

[A Azure Automation](/azure/automation/automation-intro) fornece uma forma de automatizar as tarefas manuais, de longa duração, propensas a erros e frequentemente repetidas que são geralmente executadas num ambiente de nuvem e empresa. Poupa tempo e aumenta a fiabilidade das tarefas administrativas. Até programa estas tarefas para serem executadas automaticamente em intervalos regulares. Pode automatizar processos utilizando livros de execução ou automatizar a gestão da configuração utilizando a Configuração do Estado Desejado.

### <a name="backup"></a>Cópia de segurança

[O Azure Backup](/azure/backup/backup-introduction-to-azure-backup) é o serviço baseado no Azure que pode utilizar para fazer backup (ou proteger) e restaurar os seus dados no Microsoft Cloud. O Azure Backup substitui a sua solução de backup existente no local ou fora do local por uma solução baseada na nuvem que é fiável, segura e competitiva em termos de custos.

O Azure Backup oferece componentes que descarrega e implementa no computador ou servidor apropriado, ou na nuvem. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes de Backup Azure (quer esteja a proteger dados no local ou na nuvem) podem ser usados para fazer backup de dados para um cofre dos Serviços de Recuperação Azure em Azure.

Para mais informações, consulte a tabela de componentes de [backup Azure](/azure/backup/backup-overview#what-can-i-back-up).

### <a name="site-recovery"></a>Recuperação de sites

[A Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidade ao negócio orquestrando a replicação de máquinas virtuais e físicas no local para Azure, ou para um local secundário. Se o seu site principal não estiver disponível, falha a localização secundária para que os utilizadores possam continuar a trabalhar. Falha-se quando os sistemas voltam à ordem de funcionamento. Utilize o Azure Security Center para realizar uma deteção de ameaças mais inteligente e eficaz.

## <a name="azure-active-directory"></a>Azure Active Directory

[O Azure Ative Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) é um serviço de identidade abrangente que:

-   Permite a gestão de identidade e acesso (IAM) como um serviço na nuvem.
-   Fornece gestão central de acesso, inscrição única (SSO) e reporte.
-   Suporta a gestão integrada de acesso para [milhares de aplicações](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) no Mercado Azure, incluindo Salesforce, Google Apps, Box e Concur.

A Azure AD também inclui um conjunto completo de capacidades de gestão de [identidade,](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports)incluindo estas:

- [Autenticação de vários fatores](/azure/multi-factor-authentication/multi-factor-authentication)
- [Gestão de palavras-passe personalizada](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gestão de grupode self-service](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Gestão privilegiada de conta](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Controlo de acesso baseado em funções](/azure/role-based-access-control/overview)
- [Monitorização da utilização da aplicação](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Auditoria rica](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorização e alerta de segurança](/azure/operations-management-suite/oms-security-responding-alerts)

Com o Azure Ative Directory, todas as aplicações que publica para os seus parceiros e clientes (empresas ou consumidores) têm as mesmas capacidades de gestão de identidade e acesso. Isto permite-lhe reduzir significativamente os seus custos operacionais.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Azure Security Center](/azure/security-center/security-center-intro) ajuda-o a prevenir, detetar e responder a ameaças com maior visibilidade na (e controlo sobre) a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições. Ajuda a detetar ameaças que de outra forma podem passar despercebidas, e funciona com um vasto ecossistema de soluções de segurança.

[Proteja os dados da máquina virtual (VM)](/azure/security-center/security-center-linux-virtual-machine) em Azure, proporcionando visibilidade às definições de segurança da sua máquina virtual e monitorizando as ameaças. O Centro de Segurança pode monitorizar as máquinas virtuais relativamente a:

- Definições de segurança do sistema operativo com as regras de configuração recomendadas.
- Segurança do sistema e atualizações críticas que faltam.
- Recomendações de proteção do ponto final.
- Validação da encriptação do disco.
- Ataques baseados na rede.

O Centro de Segurança utiliza [o Controlo de Acesso baseado em funções (RBAC)](/azure/role-based-access-control/role-assignments-portal). O RBAC fornece [funções incorporadas](../../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

O Security Center avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Security Center, só se vê informações relacionadas com um recurso quando lhe é atribuído o papel de proprietário, colaborador ou leitor para a subscrição ou grupo de recursos a que um recurso pertence.

>[!Note]
>Para saber mais sobre papéis e ações permitidas no Centro de Segurança, consulte [Permissões no Centro de Segurança Azure.](/azure/security-center/security-center-permissions)

O Security Center utiliza o Agente de Monitorização da Microsoft. Este é o mesmo agente que o serviço Azure Monitor utiliza. Os dados recolhidos deste agente são armazenados num espaço de [trabalho](/azure/log-analytics/log-analytics-manage-access) existente no Log Analytics associado à sua subscrição Azure ou num novo espaço de trabalho, tendo em conta a geolocalização do VM.

## <a name="azure-monitor"></a>Azure Monitor

Problemas de desempenho na sua aplicação cloud podem afetar o seu negócio. Com vários componentes interligados e lançamentos frequentes, as degradações podem ocorrer a qualquer momento. E se estiver a desenvolver uma aplicação, os seus utilizadores geralmente descobrem problemas que não encontrou nos testes. Deve saber imediatamente sobre estas questões, e deve ter ferramentas para diagnosticar e corrigir os problemas.

[O Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) é uma ferramenta básica para a monitorização dos serviços em funcionamento no Azure. Fornece-lhe dados ao nível da infraestrutura sobre a entrada de um serviço e o ambiente circundante. Se está a gerir as suas aplicações em Azure e a decidir se deve aumentar ou descer recursos, o Azure Monitor é o local para começar.

Também pode utilizar dados de monitorização para obter informações profundas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção da aplicação, ou automatizar ações que de outra forma exigiriam intervenção manual.

O Monitor Azure inclui os seguintes componentes.

### <a name="azure-activity-log"></a>Registo de Atividades do Azure

O [Registo de Atividades do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. Anteriormente era conhecido como "Registo de Auditoria" ou "Registo Operacional", porque reporta eventos de control-plane para as suas assinaturas.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Os [registos de diagnóstico azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. O conteúdo destes registos varia por tipo de recurso.

Os registos do sistema de eventos windows são uma categoria de registos de diagnóstico para VMs. Os registos de blob, mesa e fila são categorias de registos de diagnóstico para contas de armazenamento.

Os registos de diagnóstico diferem do Registo de [Atividade](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O registo de Atividades fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. Os registos de diagnóstico fornecem informações sobre as operações que o seu recurso realizou por si mesmo.

### <a name="metrics"></a>Métricas

O Azure Monitor fornece telemetria que lhe dá visibilidade no desempenho e saúde das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria Azure são as [métricas](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (também chamadas contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias formas de configurar e consumir estas métricas para monitorização e resolução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O Azure Diagnostics permite a recolha de dados de diagnóstico numa aplicação implementada. Pode utilizar a extensão de Diagnóstico de várias fontes. Atualmente suportados estão as funções de [serviço em nuvem Azure,](/azure/vs-azure-tools-configure-roles-for-cloud-service) [máquinas virtuais Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service) que executam o Microsoft Windows e tecido de [serviço Azure.](/azure/monitoring-and-diagnostics/azure-diagnostics)

## <a name="azure-network-watcher"></a>Observador de Rede do Azure

Os clientes constroem uma rede de ponta a ponta em Azure orquestrando e compondo recursos individuais de rede, tais como redes virtuais, Azure ExpressRoute, Azure Application Gateway e equilibradores de carga. Está disponível o acompanhamento em cada um dos recursos da rede.

A rede de ponta a ponta pode ter configurações complexas e interações entre recursos. O resultado são cenários complexos que precisam de monitorização baseada em cenários através [do Observador da Rede Azure](/azure/network-watcher/network-watcher-monitoring-overview).

O Network Watcher simplifica a monitorização e diagnóstico da sua rede Azure. Pode utilizar as ferramentas de diagnóstico e visualização no Observador da Rede para:

- Leve capturas remotas de pacotes numa máquina virtual Azure.
- Obtenha informações sobre o tráfego da sua rede utilizando registos de fluxo.
- Diagnosticar Gateway VpN Azure e ligações.

O Network Watcher tem atualmente as seguintes capacidades:

- [Topologia](/azure/network-watcher/network-watcher-topology-overview): Proporciona uma visão das várias interligações e associações entre recursos de rede num grupo de recursos.
- [Captura de pacotes variáveis:](/azure/network-watcher/network-watcher-packet-capture-overview)Captura dados de pacotes dentro e fora de uma máquina virtual. As opções de filtragem avançadas e os controlos otimizados, como a possibilidade de definir limites de tempo e tamanho, proporcionam versatilidade. Os dados do pacote podem ser armazenados numa loja de bolhas ou no disco local em formato .cap.
- [Verificação do fluxo IP](/azure/network-watcher/network-watcher-ip-flow-verify-overview): Verifica se um pacote é permitido ou negado com base em parâmetros de pacote de 5 tuple para informações de fluxo (IP de destino, fonte IP, porta de destino, porta de origem e protocolo). Se um grupo de segurança negar o pacote, a regra e o grupo que negou o pacote são devolvidos.
- [Próximo salto](/azure/network-watcher/network-watcher-next-hop-overview): Determina o próximo salto para os pacotes que estão a ser encaminhados no tecido da rede Azure, para que possa diagnosticar quaisquer rotas mal configuradas pelo utilizador.
- [Visão de grupo](/azure/network-watcher/network-watcher-security-group-view-overview)de segurança : Obtém as regras de segurança eficazes e aplicadas que são aplicadas num VM.
- [Registos de fluxo NSG para grupos](/azure/network-watcher/network-watcher-nsg-flow-logging-overview)de segurança de rede : Permitir-lhe capturar registos relacionados com o tráfego que é permitido ou negado pelas regras de segurança do grupo. O fluxo é definido por informações de 5-tuple: FONTE IP, destino IP, porta de origem, porta de destino e protocolo.
- [Gateway de rede virtual e resolução](/azure/network-watcher/network-watcher-troubleshoot-manage-rest)de problemas de ligação : Fornece a capacidade de resolver problemas de gateways e ligações de rede virtual.
- [Limites](/azure/network-watcher/network-watcher-monitoring-overview)de subscrição da rede : Permite-lhe visualizar o uso de recursos de rede contra limites.
- [Registos de diagnóstico](/azure/network-watcher/network-watcher-monitoring-overview): Fornece um único painel para ativar ou desativar os registos de diagnóstico dos recursos de rede num grupo de recursos.

Para mais informações, consulte [Configure Network Watcher](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Transparência de acesso ao fornecedor de serviços de nuvem

[O Bloqueio de Clientes do Microsoft Azure](customer-lockbox-overview.md) é um serviço integrado no portal Azure que lhe dá controlo explícito na rara instância em que um Engenheiro de Suporte da Microsoft pode precisar de acesso aos seus dados para resolver um problema.
Existem muito poucos casos, como um problema de depuração de acesso remoto, em que um Engenheiro de Suporte da Microsoft requer permissões elevadas para resolver este problema. Nestes casos, os engenheiros da Microsoft utilizam um serviço de acesso just-in-time que fornece uma autorização limitada e com acesso limitado ao serviço.  
Embora a Microsoft sempre tenha obtido o consentimento do cliente para o acesso, o Customer Lockbox dá-lhe agora a capacidade de rever e aprovar ou negar tais pedidos do Portal Azure. Os engenheiros de suporte da Microsoft não terão acesso até que aprove o pedido.

## <a name="standardized-and-compliant-deployments"></a>Implementações padronizadas e conformes

Os [Projetos Azure](/azure/governance/blueprints/overview) permitem aos arquitetos da nuvem e aos grupos centrais de tecnologias da informação definir um conjunto repetível de recursos Azure que implementam e aderem aos padrões, padrões e requisitos de uma organização.  
Isto permite que as equipas da DevOps construam e defendam rapidamente novos ambientes e confiem que estão a construí-los com infraestruturas que mantêm o cumprimento organizacional.
As plantas fornecem uma forma declarativa de orquestrar a implantação de vários modelos de recursos e outros artefactos tais como:

- Atribuições de Funções
- Atribuições de Política
- Modelos do Azure Resource Manager
- Grupos de Recursos

## <a name="devops"></a>DevOps

Antes do desenvolvimento de aplicações de [Operações de Desenvolvimento (DevOps),](https://www.visualstudio.com/learn/what-is-devops/) as equipas estavam encarregues de recolher requisitos empresariais para um programa de software e código de escrita. Em seguida, uma equipa de QA separada testou o programa em um ambiente de desenvolvimento isolado. Se os requisitos fossem cumpridos, a equipa da QA divulgou o código para as operações a serem implantadas. As equipas de implantação foram ainda fragmentadas em grupos como networking e base de dados. De cada vez que um programa de software era "atirado por cima da parede" a uma equipa independente, adicionava estrangulamentos.

A DevOps permite que as equipas oferesiquem soluções mais seguras e de maior qualidade mais rápidas e baratas. Os clientes esperam uma experiência dinâmica e fiável ao consumir software e serviços. As equipas devem iterar rapidamente as atualizações de software e medir o impacto das atualizações. Devem responder rapidamente com novas iterações de desenvolvimento para resolver questões ou fornecer mais valor.  

Plataformas cloud como o Microsoft Azure removeram os estrangulamentos tradicionais e ajudaram a commoditizar a infraestrutura. O software reina em todos os negócios como o principal diferenciador e fator nos resultados do negócio. Nenhuma organização, desenvolvedor ou trabalhador de TI pode ou deve evitar o movimento DevOps.

Os praticantes de DevOps Maduros adotam várias das seguintes práticas. Estas práticas [envolvem as pessoas](https://www.visualstudio.com/learn/what-is-devops-culture/) a formar estratégias baseadas nos cenários de negócio. A ferramenta pode ajudar a automatizar as várias práticas.

- As técnicas [ágeis](https://www.visualstudio.com/learn/what-is-agile/) de planeamento e gestão de projetos são usadas para planear e isolar o trabalho em sprints, gerir a capacidade da equipa e ajudar as equipas a adaptarem-se rapidamente às necessidades de negócio em mudança.
- O controlo de [versão, geralmente com git,](https://www.visualstudio.com/learn/what-is-git/)permite que as equipas localizadas em qualquer parte do mundo partilhem fonte e integrem-se com ferramentas de desenvolvimento de software para automatizar o pipeline de lançamento.
- [A integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/) impulsiona a fusão e teste contínuo de código, o que leva a encontrar defeitos precocemente.  Outros benefícios incluem menos tempo desperdiçado na luta contra as questões de fusão e feedback rápido para as equipas de desenvolvimento.
- [A entrega contínua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) de soluções de software para ambientes de produção e teste ajuda as organizações a corrigir rapidamente bugs e a responder a requisitos de negócio em constante mudança.
- [O acompanhamento](https://www.visualstudio.com/learn/what-is-monitoring/) das aplicações em execução- incluindo ambientes de produção para a saúde da aplicação, bem como o uso do cliente -- ajuda as organizações a formar uma hipótese e a validar ou refutar rapidamente estratégias.  Os dados ricos são capturados e armazenados em vários formatos de exploração madeireira.
- [A infraestrutura como Código (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) é uma prática que permite a automatização e validação da criação e demolição de redes e máquinas virtuais para ajudar a fornecer plataformas de hospedagem de aplicações seguras e estáveis.
- A arquitetura [de microserviços](https://www.visualstudio.com/learn/what-are-microservices/) é usada para isolar casos de uso de negócios em pequenos serviços reutilizáveis.  Esta arquitetura permite escalabilidade e eficiência.

## <a name="next-steps"></a>Passos seguintes

Para conhecer a solução de Segurança e Auditoria, consulte os seguintes artigos:

- [Segurança e conformidade](https://azure.microsoft.com/overview/trusted-cloud/)
- [Centro de Segurança do Azure](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)

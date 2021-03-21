---
title: Visão geral da segurança operacional do Azure| Microsoft Docs
description: Conheça a segurança operacional da Azure nesta visão geral. Segurança operacional refere-se a serviços de proteção de bens, controlos e funcionalidades.
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
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595506"
---
# <a name="azure-operational-security-overview"></a>Visão geral da segurança operacional do Azure

[A segurança operacional da Azure](./operational-security.md) refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Microsoft Azure. É uma estrutura que incorpora o conhecimento adquirido através de uma variedade de capacidades que são únicas para a Microsoft. Estas capacidades incluem o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma profunda consciência do panorama da ameaça à cibersegurança.

## <a name="azure-management-services"></a>Serviços de gestão Azure

Uma equipa de operações de TI é responsável pela gestão de infraestruturas, aplicações e dados do datacenter, incluindo a estabilidade e segurança destes sistemas. No entanto, obter informações de segurança em ambientes de TI cada vez mais complexos exige que as organizações juntem dados de múltiplos sistemas de segurança e gestão.

[Os registos do Microsoft Azure Monitor](../../azure-monitor/overview.md) são uma solução de gestão de TI baseada na nuvem que o ajuda a gerir e proteger as suas infraestruturas de nuvem e instalações. A sua funcionalidade principal é fornecida pelos seguintes serviços que funcionam em Azure. O Azure inclui vários serviços que o ajudam a gerir e proteger as suas infraestruturas no local e na nuvem. Cada serviço fornece uma função de gestão específica. Pode combinar serviços para alcançar diferentes cenários de gestão. 

### <a name="azure-monitor"></a>Azure Monitor

[O Azure Monitor](../../azure-monitor/overview.md) recolhe dados de fontes geridas para lojas de dados centrais. Estes dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos através da API. Após a recolha dos dados, está disponível para alerta, análise e exportação.

Pode consolidar dados a partir de uma variedade de fontes e combinar dados dos seus serviços Azure com o ambiente existente no local. Os registos do Azure Monitor também separam claramente a recolha dos dados das medidas tomadas nesses dados, de modo a que todas as ações estejam disponíveis para todo o tipo de dados.

### <a name="automation"></a>Automatização

[A Azure Automation](../../automation/automation-intro.md) fornece uma forma de automatizar o manual, de longa duração, propenso a erros e tarefas frequentemente repetidas que são normalmente executadas num ambiente de nuvem e empresa. Poupa tempo e aumenta a fiabilidade das tarefas administrativas. Ele até agenda estas tarefas para serem executadas automaticamente em intervalos regulares. Pode automatizar processos utilizando runbooks ou automatizar a gestão da configuração utilizando a Configuração do Estado Desejada.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) é o serviço baseado no Azure que pode utilizar para fazer backup (ou proteger) e restaurar os seus dados na Cloud microsoft. O Azure Backup substitui a sua solução de backup existente no local ou fora do local por uma solução baseada na nuvem que seja fiável, segura e competitiva em termos de custos.

O Azure Backup oferece componentes que descarrega e implementa no computador ou servidor apropriado, ou na nuvem. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes Azure Backup (quer esteja a proteger dados no local ou na nuvem) podem ser utilizados para fazer backup de dados para um cofre dos Serviços de Recuperação do Azure em Azure.

Para obter mais informações, consulte a [tabela de componentes de backup Azure](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Recuperação de sites

[A Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidade de negócios orquestrando a replicação de máquinas virtuais e físicas no local para Azure, ou para um site secundário. Se o seu site principal não estiver disponível, falha na localização secundária para que os utilizadores possam continuar a trabalhar. Falhas quando os sistemas voltam a funcionar. Utilize o Centro de Segurança Azure para realizar uma deteção de ameaças mais inteligente e eficaz.

## <a name="azure-active-directory"></a>Azure Active Directory

[O Azure Ative Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) é um serviço de identidade abrangente que:

-   Permite a gestão de identidade e acesso (IAM) como um serviço de nuvem.
-   Fornece gestão central de acesso, sso único e reporte.
-   Suporta a gestão integrada de acessos para [milhares de aplicações](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) no Azure Marketplace, incluindo Salesforce, Google Apps, Box e Concur.

A Azure AD também inclui um conjunto completo de capacidades de gestão de [identidade,](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports)incluindo estas:

- [Autenticação multifator](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Gestão de palavras-passe personalizada](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gestão de grupos de self-service](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Gestão de conta privilegiada](../../active-directory/privileged-identity-management/pim-configure.md)
- [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)
- [Monitorização da utilização da aplicação](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Auditoria rica](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Monitorização e alerta de segurança](../../security-center/security-center-managing-and-responding-alerts.md)

Com o Azure Ative Directory, todas as aplicações que publica para os seus parceiros e clientes (negócios ou consumidores) têm as mesmas capacidades de gestão de identidade e acesso. Isto permite-lhe reduzir significativamente os seus custos operacionais.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Azure Security Center](../../security-center/security-center-introduction.md) ajuda a prevenir, detetar e responder a ameaças com maior visibilidade para (e controlar) a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições. Ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e funciona com um amplo ecossistema de soluções de segurança.

[Salvaguardar os dados da máquina virtual (VM)](../../security-center/security-center-introduction.md) em Azure, proporcionando visibilidade nas definições de segurança da sua máquina virtual e monitorizando ameaças. O Centro de Segurança pode monitorizar as máquinas virtuais relativamente a:

- Definições de segurança do sistema operativo com as regras de configuração recomendadas.
- Segurança do sistema e atualizações críticas que faltam.
- Recomendações de proteção de ponto final.
- Validação de encriptação de disco.
- Ataques baseados em rede.

O Security Center utiliza [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md). O Azure RBAC fornece [funções incorporadas](../../role-based-access-control/built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços em Azure.

O Centro de Segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Security Center, vê informações relacionadas com um recurso apenas quando lhe é atribuída a função de proprietário, colaborador ou leitor para a subscrição ou grupo de recursos a que pertence um recurso.

>[!Note]
>Para saber mais sobre funções e ações permitidas no Centro de Segurança, consulte [permissões no Centro de Segurança Azure.](../../security-center/security-center-permissions.md)

O Security Center utiliza o Agente de Monitorização da Microsoft. Este é o mesmo agente que o serviço Azure Monitor utiliza. Os dados recolhidos deste agente são armazenados num espaço de [trabalho](../../azure-monitor/logs/manage-access.md) existente do Log Analytics associado à sua subscrição Azure ou a um novo espaço de trabalho, tendo em conta a geolocalização do VM.

## <a name="azure-monitor"></a>Azure Monitor

Problemas de desempenho na sua aplicação cloud podem afetar o seu negócio. Com vários componentes interligados e lançamentos frequentes, as degradações podem acontecer a qualquer momento. E se está a desenvolver uma aplicação, os seus utilizadores geralmente descobrem problemas que não encontrou nos testes. Deve saber imediatamente sobre estas questões e deve dispor de ferramentas para diagnosticar e corrigir os problemas.

[O Azure Monitor](../../azure-monitor/overview.md) é uma ferramenta básica para monitorizar os serviços em execução no Azure. Dá-lhe dados de nível de infraestrutura sobre a produção de um serviço e o ambiente circundante. Se está a gerir as suas aplicações todas em Azure e a decidir se vai aumentar ou descer recursos, o Azure Monitor é o local certo para começar.

Também pode utilizar dados de monitorização para obter informações profundas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a manutenção, ou automatizar ações que de outra forma exigiriam uma intervenção manual.

O Monitor Azure inclui os seguintes componentes.

### <a name="azure-activity-log"></a>Registo de Atividades do Azure

O [Azure Activity Log](../../azure-monitor/essentials/platform-logs-overview.md) fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. Anteriormente era conhecido como "Registo de Auditoria" ou "Registo Operacional", porque reporta eventos de control-plane para as suas subscrições.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

[Os registos de diagnóstico Azure](../../azure-monitor/essentials/platform-logs-overview.md) são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. O conteúdo destes registos varia consoá-lo por tipo de recurso.

Os registos do sistema de eventos windows são uma categoria de registos de diagnóstico para VMs. Os registos de bolhas, mesas e filas são categorias de registos de diagnóstico para contas de armazenamento.

Os registos de diagnóstico diferem do Registo de [Atividade.](../../azure-monitor/essentials/platform-logs-overview.md) O registo de Atividades fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. Os registos de diagnóstico fornecem informações sobre as operações que o seu recurso executou por si próprio.

### <a name="metrics"></a>Métricas

O Azure Monitor fornece telemetria que lhe dá visibilidade ao desempenho e saúde das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria Azure são as [métricas](../../azure-monitor/data-platform.md) (também chamadas contadores de desempenho) emitidas pela maioria dos recursos Azure. O Azure Monitor fornece várias formas de configurar e consumir estas métricas para monitorização e resolução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O Azure Diagnostics permite a recolha de dados de diagnóstico numa aplicação implementada. Pode utilizar a extensão de Diagnóstico de várias fontes. Atualmente suportados são [funções de serviço em nuvem Azure,](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) [máquinas virtuais Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) que executam o Microsoft Windows e [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Observador de Rede do Azure

Os clientes constroem uma rede de ponta a ponta em Azure orquestrando e compondo recursos individuais de rede, tais como redes virtuais, Azure ExpressRoute, Azure Application Gateway e equilibristas de carga. A monitorização está disponível em cada um dos recursos da rede.

A rede de ponta a ponta pode ter configurações complexas e interações entre recursos. O resultado são cenários complexos que precisam de monitorização baseada em cenários através [do Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

O Network Watcher simplifica a monitorização e o diagnóstico da sua rede Azure. Pode utilizar as ferramentas de diagnóstico e visualização no Observador de Redes para:

- Faça capturas remotas de pacotes numa máquina virtual Azure.
- Obtenha informações sobre o tráfego da sua rede utilizando registos de fluxo.
- Diagnosticar gateway Azure VPN e conexões.

O Network Watcher tem atualmente as seguintes capacidades:

- [Topologia](../../network-watcher/view-network-topology.md): Proporciona uma visão das várias interconexões e associações entre recursos de rede num grupo de recursos.
- [Captura variável de pacotes](../../network-watcher/network-watcher-packet-capture-overview.md): Captura dados de pacotes dentro e fora de uma máquina virtual. As opções de filtragem avançadas e os controlos otimizados, como a possibilidade de definir limites de tempo e tamanho, proporcionam versatilidade. Os dados do pacote podem ser armazenados numa loja de bolhas ou no disco local em formato .cap.
- [Verificação do fluxo IP:](../../network-watcher/network-watcher-ip-flow-verify-overview.md)Verifique se um pacote é permitido ou negado com base em parâmetros de pacote de 5 tuple para informações de fluxo (DESTINO IP, fonte IP, porta de destino, porta de origem e protocolo). Se um grupo de segurança negar o pacote, a regra e o grupo que negou o pacote são devolvidos.
- [Próximo lúpulo](../../network-watcher/network-watcher-next-hop-overview.md): Determina o próximo salto para pacotes que estão a ser encaminhados no tecido da rede Azure, para que possa diagnosticar quaisquer rotas mal configuradas definidas pelo utilizador.
- [Visão do grupo de segurança](../../network-watcher/network-watcher-security-group-view-overview.md): Obtém as regras de segurança eficazes e aplicadas que são aplicadas num VM.
- [Registos de fluxo NSG para grupos de segurança](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)de rede : Permita-lhe capturar registos relacionados com o tráfego que é permitido ou negado pelas regras de segurança do grupo. O fluxo é definido por informações de 5 tuple: origem IP, DESTINATION IP, porta de origem, porta de destino e protocolo.
- [Gateway de rede virtual e resolução de problemas de conexão](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): Fornece a capacidade de resolver os gateways e ligações de rede virtuais.
- [Limites de subscrição de rede](../../network-watcher/network-watcher-monitoring-overview.md): Permite-lhe visualizar a utilização de recursos de rede contra limites.
- [Registos de diagnóstico](../../network-watcher/network-watcher-monitoring-overview.md): Fornece um único painel para ativar ou desativar registos de diagnóstico para recursos de rede num grupo de recursos.

Para mais informações, consulte [o Configure Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Transparência de acesso ao fornecedor de serviços na nuvem

[O Customer Lockbox para o Microsoft Azure](customer-lockbox-overview.md) é um serviço integrado no portal Azure que lhe dá controlo explícito nos raros casos em que um Engenheiro de Suporte da Microsoft pode precisar de acesso aos seus dados para resolver um problema.
Existem muito poucas instâncias, como um problema de depurar o acesso remoto, em que um Engenheiro de Suporte da Microsoft requer permissões elevadas para resolver este problema. Nesses casos, os engenheiros da Microsoft utilizam um serviço de acesso just-in-time que fornece uma autorização limitada e limitada no tempo com acesso limitado ao serviço.  
Embora a Microsoft sempre tenha obtido o consentimento do cliente para acesso, o Customer Lockbox dá-lhe agora a possibilidade de rever e aprovar ou negar tais pedidos a partir do Portal Azure. Os engenheiros de suporte da Microsoft não terão acesso até que aprove o pedido.

## <a name="standardized-and-compliant-deployments"></a>Implementações padronizadas e conformes

[As Plantas Azure](../../governance/blueprints/overview.md) permitem que arquitetos em nuvem e grupos centrais de tecnologias da informação definam um conjunto repetível de recursos Azure que implementam e aderem aos padrões, padrões e requisitos de uma organização.  
Isto permite que as equipas da DevOps construam rapidamente e suportem novos ambientes e confiem que estão a construí-los com infraestruturas que mantenham o cumprimento organizacional.
As plantas fornecem uma forma declarativa de orquestrar a implantação de vários modelos de recursos e outros artefactos tais como:

- Atribuições de Funções
- Atribuições de Política
- Modelos do Azure Resource Manager
- Grupos de Recursos

## <a name="devops"></a>DevOps

Antes do desenvolvimento [de aplicações de Developer Operations (DevOps),](https://azure.microsoft.com/overview/what-is-devops/) as equipas estavam encarregadas de recolher os requisitos de negócio para um programa de software e código de escrita. Em seguida, uma equipa de QA separada testou o programa em um ambiente de desenvolvimento isolado. Se os requisitos fossem cumpridos, a equipa da QA divulgou o código para as operações a implementar. As equipas de implantação foram ainda fragmentadas em grupos como networking e base de dados. De cada vez que um programa de software era "atirado por cima da parede" para uma equipa independente, acrescentava estrangulamentos.

O DevOps permite que as equipas proporcionem soluções mais seguras e de maior qualidade mais rápidas e baratas. Os clientes esperam uma experiência dinâmica e fiável ao consumir software e serviços. As equipas devem iterar rapidamente as atualizações de software e medir o impacto das atualizações. Devem responder rapidamente com novas iterações de desenvolvimento para resolver questões ou fornecer mais valor.  

Plataformas cloud como o Microsoft Azure removeram os estrangulamentos tradicionais e ajudaram a commoditizar a infraestrutura. O software reina em todos os negócios como o principal diferenciador e fator nos resultados do negócio. Nenhuma organização, desenvolvedor ou trabalhador de TI pode ou deve evitar o movimento DevOps.

Os praticantes de DevOps maduros adotam várias das seguintes práticas. Estas práticas [envolvem as pessoas](/azure/devops/learn/what-is-devops-culture) a formar estratégias baseadas nos cenários de negócio. A ferramenta pode ajudar a automatizar as várias práticas.

- As técnicas [ágeis de planeamento e gestão de projetos](https://www.visualstudio.com/learn/what-is-agile/) são usadas para planear e isolar o trabalho em sprints, gerir a capacidade da equipa e ajudar as equipas a adaptarem-se rapidamente às necessidades de negócio em mudança.
- [O controlo de versão, normalmente com](/azure/devops/learn/git/what-is-git)o Git, permite que as equipas localizadas em qualquer parte do mundo partilhem a origem e se integrem com ferramentas de desenvolvimento de software para automatizar o pipeline de lançamento.
- [A integração contínua](/azure/devops/learn/what-is-continuous-integration) impulsiona a fusão e teste de código em curso, o que leva a encontrar defeitos precocemente.  Outros benefícios incluem menos tempo desperdiçado na luta contra questões de fusão e feedback rápido para as equipas de desenvolvimento.
- [A entrega contínua](/azure/devops/learn/what-is-continuous-delivery) de soluções de software para ambientes de produção e teste ajuda as organizações a corrigir rapidamente bugs e a responder a requisitos de negócio em constante mudança.
- [Monitorização](/azure/devops/learn/what-is-monitoring) de aplicações em execução -- incluindo ambientes de produção para a saúde da aplicação, bem como o uso do cliente -- ajuda as organizações a formar uma hipótese e a validar ou refutar rapidamente estratégias.  Os dados ricos são capturados e armazenados em vários formatos de registo.
- [Infraestrutura como Código (IAC)](/azure/devops/learn/what-is-infrastructure-as-code) é uma prática que permite a automatização e validação da criação e demolição de redes e máquinas virtuais para ajudar a fornecer plataformas de hospedagem de aplicações seguras e estáveis.
- A arquitetura [microserviços](/azure/devops/learn/what-are-microservices) é usada para isolar casos de uso de negócios em pequenos serviços reutilizáveis.  Esta arquitetura permite escalabilidade e eficiência.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a solução segurança e auditoria, consulte os seguintes artigos:

- [Segurança e conformidade](https://azure.microsoft.com/overview/trusted-cloud/)
- [Centro de Segurança do Azure](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)
---
title: Linha de segurança Azure para o Centro de Segurança
description: A linha de base de segurança do Centro de Segurança fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e9e7586c29f3b75512a57b0f89885f13e58e2ba1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949263"
---
# <a name="azure-security-baseline-for-security-center"></a>Linha de segurança Azure para o Centro de Segurança

Esta linha de base de segurança aplica orientações do [Azure Security Benchmark](../security/benchmarks/overview.md) ao Azure Security Center. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Security Center. Foram excluídos **os controlos** não aplicáveis ao Centro de Segurança Azure. Para ver como o Azure Security Center mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base do Azure Security Center](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: O Centro de Segurança Azure é uma oferta central do Azure. Não é possível associar uma rede virtual, sub-rede ou grupo de segurança de rede diretamente ao Security Center. Se ativar a recolha de dados para os seus recursos computacional, então o Security Center armazena os dados que recolhe através de um espaço de trabalho do Log Analytics, pode configurar esse espaço de trabalho para utilizar o Private Link para aceder aos dados do seu espaço de trabalho através de um ponto final privado na sua rede virtual. Além disso, se utilizar a recolha de dados, o Security Center depende do agente Log Analytics ser implantado nos seus servidores para recolher dados de segurança e fornecer proteção a estes recursos de computação. O agente Log Analytics requer que portas e protocolos específicos sejam abertos para o funcionamento adequado com o Centro de Segurança. Bloqueie as suas redes apenas para permitir as portas e protocolos necessários e apenas adicionar regras adicionais que a sua aplicação exige operar através de grupos de segurança de rede.

- [Data collection in Azure Security Center](security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure)

- [Tráfego de rede de ficheiros com um grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

- [Requisitos de firewall para a utilização do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Compreenda a ligação privada Azure](../private-link/private-link-overview.md) 

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: A oferta do Azure Security Center não se integra diretamente com uma rede virtual, mas pode recolher dados de servidores configurados com o agente Log Analytics que são implantados nas suas redes. Os seus servidores configurados para enviar dados para o Centro de Segurança exigem que determinadas portas e protocolos possam comunicar corretamente. Definir e impor configurações de segurança padrão para estes recursos de rede com a Política Azure.

Também pode usar plantas Azure para simplificar as implementações de Azure em larga escala através de artefactos de ambiente chave de embalagem, tais como modelos de Gestor de Recursos Azure, atribuições de funções e atribuições de Política Azure, numa única definição de planta. Pode aplicar a planta a novas subscrições para implementar configurações do Centro de Segurança e recursos de rede relacionados de forma consistente e segura.

- [Data collection in Azure Security Center](security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure)

- [Requisitos de firewall para a utilização do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

- [Como criar uma Planta Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: A oferta do Azure Security Center não se integra diretamente com uma rede virtual, mas pode recolher dados de servidores configurados com o agente Log Analytics que são implantados nas suas redes. Os seus servidores configurados para enviar dados para o Centro de Segurança exigem que determinadas portas e protocolos possam comunicar corretamente. Definir e impor configurações de segurança padrão para estes recursos de rede com a Política Azure.

Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos, como servidores nas suas redes configurados para enviar registos de segurança para o Centro de Segurança Azure. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede. 

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas. 

- [Data collection in Azure Security Center](security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure)

- [Requisitos de firewall para a utilização do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma Rede Virtual Azure](../virtual-network/quick-create-portal.md) 

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de atividade do Azure para monitorizar as configurações dos recursos e detetar alterações nos recursos de rede relacionados com o Centro de Segurança Azure. Crie alertas no Azure Monitor para o notificar quando ocorrerem alterações nos recursos críticos.

- [Como visualizar e recuperar eventos de log de atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Dados de segurança agregados gerados pelo Azure Security Center e suas fontes conectadas utilizando um espaço de trabalho central do Log Analytics. 

Configure a recolha de dados do Security Center para enviar dados de segurança e eventos dos seus recursos de computação Azure conectados para um espaço de trabalho central do Log Analytics. Além da recolha de dados, utilize a funcionalidade de exportação contínua para transmitir alertas de segurança e recomendações geradas pelo Security Center para o seu espaço de trabalho central do Log Analytics. No Azure Monitor, pode consultar e realizar análises sobre os dados de segurança gerados pelo Security Center e os seus recursos Azure conectados. 

Em alternativa, pode enviar dados produzidos pelo Security Center para a Azure Sentinel ou para um SIEM de terceiros.

- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)

- [Data collection in Azure Security Center](security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de atividade do Monitor Azure estão automaticamente disponíveis, estes registos contêm todas as operações de escrita para o seu recurso, como o Azure Security Center, incluindo que operações foram feitas, quem iniciou a operação e quando ocorreram. Envie os seus registos de Atividade Azure para um espaço de trabalho Log Analytics para consolidação de registos e retenção acrescida.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Enviar registos de atividade para um espaço de trabalho Log Analytics](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, desa um ponto final no seu período de retenção do espaço de trabalho Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize as contas de armazenamento Azure para armazenamento a longo prazo e arquivo. 

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize os registos produzidos pelo Azure Security Center e as suas fontes ligadas para comportamentos anómalos e reveja regularmente os resultados. Utilize o Azure Monitor e um espaço de trabalho log Analytics para rever registos e realizar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Começando com consultas log analytics](../azure-monitor/log-query/get-started-portal.md) 

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Configurar alertas de registo do Monitor Azure para consulta de atividades indesejadas ou anómalas que são registadas pelo seu registo de atividade ou pelos dados produzidos pelo Azure Security Center. Crie Grupos de Ação para que a sua organização seja notificada e possa tomar medidas se for iniciado um alerta de registo para atividades anómalas. Utilize a funcionalidade de automatização do fluxo de trabalho do Security Center para desencadear aplicações lógicas em alertas de segurança e recomendações. Os fluxos de trabalho do Centro de Segurança podem ser usados para notificar os utilizadores para resposta a incidentes, ou tomar medidas para remediar recursos com base nas informações de alerta.

Em alternativa, pode ativar e a bordo dados relacionados e produzidos pelo Azure Security Center para Azure Sentinel. O Azure Sentinel suporta playbooks que permitem respostas automáticas de ameaças a questões relacionadas com a segurança.

- [Azure Security Center de automatização do fluxo de trabalho](workflow-automation.md)

- [Como gerir alertas no Centro de Segurança Azure](security-center-managing-and-responding-alerts.md) 

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/learn/tutorial-response.md)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

- [Alertas de registo no Monitor Azure](../azure-monitor/platform/alerts-unified-log.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso aos recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. O Azure Security Center tem funções incorporadas para 'Security Reader' ou 'Security Admin' que permite aos utilizadores ler ou atualizar políticas de segurança e rejeitar alertas e recomendações.

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas para a Plataforma Azure ou específicas da oferta do Centro de Segurança Azure. Utilize a Azure Security Center Identity and Access Management para monitorizar o número de contas administrativas no Diretório Ativo Azure. O Security Center também tem funções incorporadas para o 'Security Admin' que permite aos utilizadores atualizarem as políticas de segurança e descartarem alertas e recomendações, garantir que revejam e reconciliem todos os utilizadores que tenham esta atribuição de funções regularmente.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, poderá utilizar recomendações do Azure Security Center ou políticas de Azure incorporadas, tais como:

- Deve haver mais de um proprietário atribuído à sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)

- [Como utilizar o Centro de Segurança Azure para monitorizar a identidade e o acesso (Pré-visualização)](security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Sempre que possível, utilize o Azure Ative Directory SSO em vez de configurar credenciais individuais autónomas por serviço. Use recomendações de identidade e acesso do Azure Security Center.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Capacite o Azure Ative Directory MFA para aceder ao Centro de Segurança Azure e ao portal Azure, siga qualquer identidade do Centro de Segurança e recomendações de acesso. 

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Como ativar O Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory e monitoriem para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores de AD Azure sinalizados para atividade de risco](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Centro de Segurança Azure](security-center-identity-access.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Utilize localizações nomeadas a Azure AD para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. 

- [Como configurar a Azure AD nomeou localizações](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização quando utilizar o Centro de Segurança Azure. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores. O Azure Security Center tem funções incorporadas que são atribuíveis como 'Security Admin' que permite aos utilizadores atualizar políticas de segurança e rejeitar alertas e recomendações.

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a identidade AZure E access reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador relacionado com o Azure Security Center pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado. 

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml) 

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e fontes de registo de eventos de risco, que lhe permitem integrar-se com qualquer ferramenta SIEM/monitor. 

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho do Log Analytics.  

- [Como integrar registos de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta 

**Orientação**: Utilize funcionalidades de Proteção de Identidade Ad Azure para configurar respostas automatizadas para detetar ações suspeitas relacionadas com as identidades dos utilizadores. Você também pode ingerir dados em Azure Sentinel para mais investigação. 

- [Como ver a Azure AD a entrar em risco](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear recursos do Azure, como o espaço de trabalho Log Analytics, que armazena informações de segurança sensíveis do Azure Security Center.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados. Pode restringir o nível de acesso aos seus recursos Azure que as suas aplicações e ambientes empresariais exigem. Você pode controlar o acesso aos recursos Azure via Azure RBAC.

Por predefinição, os dados do Azure Security Center são armazenados no serviço de backend do Centro de Segurança. Se a sua organização tiver requisitos adicionais para armazenar estes dados nos seus próprios recursos, pode configurar um espaço de trabalho log Analytics para armazenar dados, alertas e recomendações do Centro de Segurança. Ao utilizar o seu próprio espaço de trabalho, pode adicionar uma maior separação configurando diferentes espaços de trabalho de acordo com o ambiente em que os dados se originaram.

- [Exportar continuamente dados do Centro de Segurança](continuous-export.md)

- [Data collection in Azure Security Center](security-center-enable-data-collection.md) (Recolha de dados no Centro de Segurança do Azure)

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure seja capaz de negociar TLS 1.2 ou superior. Quaisquer máquinas virtuais configuradas com o agente Log Analytics e para enviar dados para o Centro de Segurança Azure devem ser configuradas para utilizar o TLS 1.2.

Siga as recomendações do Azure Security Center para encriptação em repouso e encriptação em trânsito, sempre que aplicável. 

- [Envio de dados de forma segura para Registar Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Utilizar o Azure RBAC para controlar o acesso aos recursos 

**Orientação**: Use o controlo de acesso baseado em funções Azure (Azure RBAC) para gerir o acesso a dados e recursos relacionados com o Azure Security Center. O Azure Security Center tem funções incorporadas para 'Security Reader' ou 'Security Admin' que permite aos utilizadores ler ou atualizar políticas de segurança e rejeitar alertas e recomendações. O espaço de trabalho Log Analytics que armazena os dados recolhidos pelo Security Center também tem funções incorporadas que pode atribuir como 'Log Analytics Reader', 'Log Analytics Contributor', entre outros. Atribua o papel menos permissivo necessário para que os utilizadores completem as suas tarefas necessárias. Por exemplo, atribua o papel de Leitor a utilizadores que apenas precisam de visualizar informações sobre a saúde de segurança de um recurso, mas não tomem medidas, tais como a aplicação de recomendações ou políticas de edição.

- [Permissões para espaço de trabalho Azure Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Permissions in Azure Security Center](security-center-permissions.md) (Permissões no Centro de Segurança do Azure)

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: O Azure Security Center utiliza um espaço de trabalho configurado do Log Analytics para armazenar os dados, alertas e recomendações que gera. Configure uma chave gerida pelo cliente (CMK) para o espaço de trabalho que configuraste para a recolha de dados do Security Center. A CMK permite que todos os dados guardados ou enviados para o espaço de trabalho sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. 

- [Chave gerida pelo cliente do Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure para criar alertas quando ocorrerem alterações para recursos críticos do Azure relacionados com o Centro de Segurança Azure. Estas alterações podem incluir quaisquer ações que modifiquem configurações relacionadas com o centro de segurança, como a desativação de alertas ou recomendações, ou a atualização ou eliminação de lojas de dados.

- [Como criar alertas para eventos de registo de atividades do Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, Sistema Comum de Pontuação de Vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

- [NIST Publication--Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descubra todos os recursos relacionados com o Azure Security Center nas suas subscrições. Certifique-se de permissões (ler) adequadas no seu inquilino e enumera todas as subscrições do Azure para descobrir os recursos do Centro de Segurança. 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Utilize etiquetas para ajudar a rastrear recursos do Azure, como o espaço de trabalho Log Analytics, que armazena informações de segurança sensíveis do Azure Security Center.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize etiquetas, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear os recursos do Centro de Segurança Azure. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

Além disso, utilize a política da Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições. 

Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições.  Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados. 

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Remova os recursos do Azure relacionados com o Azure Security Center quando já não forem necessários como parte do processo de inventário e revisão da sua organização.

- [Grupo de recursos Azure e supressão de recursos](../azure-resource-manager/management/delete-resource-group.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Configurar acesso condicionado Azure para limitar a capacidade dos utilizadores de interagir com o Azure Resource Manager, configurando "Block access" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o Azure Security Center e o seu espaço de trabalho conectado através da Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.OperationalInsights" e "Microsoft.Security" para criar definições personalizadas de Política Azure para auditar ou impor a configuração do Security Center e do seu espaço de trabalho Log Analytics.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize os efeitos da Política Azure para "negar" e "implementar se não existir" para impor configurações seguras através dos seus recursos Azure. Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md) 

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md) 

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS. 

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Definir e implementar configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos recursos relacionados com o Centro de Segurança Azure. Além disso, pode utilizar a Azure Automation para implementar alterações de configuração. 

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como usar pseudónimos](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure nas "Microsoft.OperationalInsights" e "Microsoft. Espaçoes de nome de segurança para criar políticas personalizadas para alertar, auditar e impor configurações de recursos Azure. Utilize os efeitos da política Azure "auditoria", "negar" e "implementar se não existir" para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: O Azure Security Center utiliza um espaço de trabalho configurado do Log Analytics para armazenar os dados, alertas e recomendações que gera. Configure uma chave gerida pelo cliente (CMK) para o espaço de trabalho que configuraste para a recolha de dados do Security Center. A CMK permite que todos os dados guardados ou enviados para o espaço de trabalho sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. 

- [Chave gerida pelo cliente do Azure Monitor](../azure-monitor/platform/customer-managed-keys.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Centro de Segurança Azure não se destina a armazenar ou processar ficheiros. É da sua responsabilidade pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados, incluindo o espaço de trabalho log Analytics.

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [Azure Security Benchmark: Data Recovery](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares 

**Orientação**: Siga uma infraestrutura à medida que o código (IAC) se aproxime e utilize o Gestor de Recursos Azure para implementar os recursos relacionados com o Azure Security Center num modelo de Notação de Objetos JavaScript (JSON) que pode ser usado como cópia de segurança para configurações relacionadas com recursos.

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Modelos do Gestor de Recursos Azure para recursos de segurança](/azure/templates/microsoft.security/allversions)

- [Sobre a Azure Automation](../automation/automation-intro.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: O Azure Security Center utiliza um espaço de trabalho Log Analytics para armazenar os dados, alertas e recomendações que gera. Pode configurar o Azure Monitor e o espaço de trabalho que o Security Center utiliza para ativar uma chave gerida pelo cliente. Se estiver a utilizar um Cofre-Chave para armazenar as suas chaves geridas pelo cliente, certifique-se de cópias de segurança automáticas regulares das suas chaves.

- [Como apoiar chaves do cofre](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Certifique-se de que a restauração é executada periodicamente utilizando ficheiros de modelos apoiados pelo Azure Resource Manager. Teste de restauração de chaves geridas pelo cliente.

- [Gerir o espaço de trabalho do Log Analytics utilizando modelos de Gestor de Recursos Azure](../azure-monitor/samples/resource-manager-workspace.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure e modelos de Gestor de Recursos Azure. Para proteger os recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se estiverem integrados com Azure DevOps ou Ative Directory se integrados com TFS. Utilize o controlo de acesso baseado em funções Azure para proteger as chaves geridas pelo cliente.

Além disso, Ative Soft-Delete e purpurar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.  Se o Azure Storage for utilizado para armazenar cópias de segurança do modelo do Azure Resource Manager, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas. 

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Como permitir a proteção de Soft-Delete e purga no Cofre de Chaves](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Utilize o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde o incidente ocorreu. 

- [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário. 

- [Publicação do NIST--Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. A exportação contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho Azure Security Center para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure. 

- [Como configurar a automatização do fluxo de trabalho no Centro de Segurança](workflow-automation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft. 

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre [as linhas de base de segurança da Azure](../security/benchmarks/security-baselines-overview.md)
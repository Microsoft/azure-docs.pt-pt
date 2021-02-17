---
title: Linha de base de segurança Azure para recuperação do local
description: A linha de base de segurança da Recuperação do Local fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576730"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Linha de base de segurança Azure para recuperação do local

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) para a Recuperação do Local. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Recuperação do Site. Foram excluídos **os controlos** não aplicáveis à Recuperação do Local. 

Para ver como a Recuperação do Site mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança do local completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A Recuperação do Site microsoft Azure não suporta a implementação numa Rede Virtual Azure. Configure o serviço de recuperação do local com um Azure Private Endpoint para impor comunicações seguras sobre a sua rede.

- [Suporte de ligação privada de recuperação do site Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: O serviço de Recuperação de Sítios suporta etiquetas de serviço, que permitem aos clientes abrir o tráfego apenas a serviços e portos específicos. Os clientes têm de permitir a etiqueta de serviço "AzureSiteRecovery" na sua firewall ou grupo de segurança de rede para permitir o acesso de saída ao serviço de Recuperação de Sítios.

- [Conectividade de saída usando tags de serviço](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas de recursos para grupos de segurança de rede e outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para documentar as regras que permitem o tráfego de e para uma rede. 

Incorpore qualquer uma das definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e notificá-lo dos recursos existentes não marcados. 

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas. 

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md) 

- [Como criar uma Rede Virtual Azure](../virtual-network/quick-create-portal.md) 

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Monitorize quaisquer alterações às configurações de recursos de rede relacionadas com o serviço de Recuperação do Local utilizando Registos de Atividade Azure. Crie alertas no Azure Monitor para o notificar quando os recursos críticos da rede de recuperação do local forem alterados.

- [Ver e recuperar eventos de Registo de Atividades Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Criar, visualizar e gerir alertas de registo de atividades utilizando o Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure para a verificação de registos de auditoria e enviar os registos para um espaço de trabalho log Analytics, conta de Armazenamento Azure ou um Hub de Eventos Azure para arquivo.

Utilize dados do Registo de Atividades Azure para determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas nos seus recursos Azure.

Ingeste os registos de recuperação do site no Azure Monitor para agregar dados de segurança gerados. Dentro do Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento para armazenamento a longo prazo ou arquivo. Além disso, pode ativar e a bordo dados para Azure Sentinel ou uma solução de Segurança Incidente e Gestão de Eventos (SIEM) de terceiros.

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Monitorizar o Site Recovery com os Registos do Azure Monitor](monitor-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure para a verificação de registos de auditoria e enviar os registos para um espaço de trabalho log Analytics, conta de Armazenamento Azure ou para um Centro de Eventos Azure para arquivo. 

Utilize dados do Registo de Atividades Azure para determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) realizadas nos seus recursos Azure.

Ingeste os registos de recuperação do site com o Azure Monitor para agregar dados de segurança gerados. Dentro do Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento para armazenamento a longo prazo/arquivo. Ativar e a bordo dados para Azure Sentinel ou uma solução de Gestão de Incidentes e Eventos de Segurança de terceiros (SIEM).

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Monitorizar o Site Recovery com os Registos do Azure Monitor](monitor-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Desconfie do período de retenção de registos para os espaços de trabalho do Log Analytics associados aos cofres dos Serviços de Recuperação Azure utilizando o Azure Monitor de acordo com os regulamentos de conformidade da sua organização. 

- [Como definir parâmetros de retenção de registos](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. 

Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e insights sobre os dados do registo de atividade recolhidos a partir de Cofres de Serviços de Recuperação.

- [Monitorizar o Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/essentials/activity-log.md)

- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Monitores máquinas replicadas pela Recuperação do Local Azure utilizando registos do Monitor Azure e Análise de Registos. Utilize o Log Analytics dentro do Azure Monitor para escrever e testar consultas de registo e para analisar interativamente dados de registo. O Azure Monitor recolhe registos de atividade e recursos, juntamente com outros dados de monitorização. 

Visualizar e consultar os resultados dos registos e configurar alertas para tomar ações com base em dados monitorizados. Alertas de configuração num espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar soluções automatizadas, como playbooks e usadas para remediar problemas de segurança. Crie alertas de registo personalizados no seu espaço de trabalho Log Analytics utilizando o Azure Monitor. 

- [Monitorizar o Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Criar, visualizar e gerir alertas de registo usando o Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Não são atribuídas funções por defeito. Têm de ser explicitamente atribuídos com base nas necessidades empresariais. Quaisquer atribuições de funções podem ser verificadas com o PowerShell CLI ou com o Azure Ative Directory (Azure AD) para descobrir contas que sejam membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Utilize as funcionalidades de Gestão de Identidade e Acesso do Centro de Segurança para monitorizar o número de contas administrativas.

Além disso, para ajudá-lo a acompanhar contas administrativas dedicadas, utilize recomendações do Security Center ou políticas de Azure incorporadas, tais como: 

- Deve haver mais de um proprietário atribuído à sua subscrição 
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição 

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

Crie um processo para rastrear a identidade e o controlo de acesso para contas administrativas e revê-lo periodicamente.

- [Como usar o Centro de Segurança Azure para monitorizar a identidade e o acesso](../security-center/security-center-identity-access.md)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Utilize o registo de aplicações Azure com um Diretor de Serviços para recuperar um token para ser usado para interagir com os cofres dos Seus Serviços de Recuperação através de chamadas API.

- [Como chamar Azure REST APIs](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registar a sua aplicação ao cliente (principal serviço) com a Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informação da Azure Recovery Services API](/rest/api/recoveryservices)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a Azure AD, a autenticação multifactor e seguir as recomendações de Identidade e Acesso do Centro de Segurança. 
- [Planeie uma implementação de autenticação multi-factor Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorizar a identidade e o acesso](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Uma Estação de Trabalho de Acesso Privilegiado (PAW)) com autenticação multifactor Azure para tarefas administrativas e para executar ações privilegiadas nos recursos de Recuperação de Locais.

- [Estações de Trabalho de Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planear a implementação da Multi-Factor Authentication do Azure AD com base na cloud](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize a funcionalidade de Gestão de Identidade Privilegiada (PIM) da Azure AD para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.
Ver alertas e relatórios sobre comportamento de utilizador de risco com recurso de deteção de risco Azure AD.

- [Como implementar gestão de identidade privilegiada (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Compreender deteções de risco Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados de acesso condicional para permitir o acesso ao portal Azure a partir de agrupamentos lógicos específicos de intervalos de endereços IP, regiões ou países.
- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure AD como o sistema central de autenticação e autorização para recuperação do local. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso, em trânsito e também em sais, hashes e armazena de forma segura as credenciais dos utilizadores. 

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Utilize registos AD Azure para ajudar a descobrir contas velhas. 

Gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções com as análises de identidade e acesso da Azure AD. 

Crie um processo para rever o acesso do utilizador regularmente para garantir que apenas os utilizadores com revisões de acesso concluídas tenham acesso continuado. 

- [Compreenda a Azure AD reportando](../active-directory/reports-monitoring/index.yml)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Utilize o Azure AD como o sistema central de autenticação e autorização para recursos de recuperação do local. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores.

Tem acesso a fontes de login, auditoria e registo de eventos de risco da Azure, que lhe permitem integrá-las com o Azure Sentinel ou qualquer ferramenta de monitorização ou SIEM disponível no Azure Marketplace.

Agilizar ainda mais este processo, criando definições de diagnóstico para contas de utilizadores AZure E enviando os registos de auditoria e de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro de um espaço de trabalho log analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize o Azure AD como sistema central de autenticação e autorização para os cofres dos Serviços de Recuperação. 

Empregue as funcionalidades de Proteção de Identidade da Azure para deteção de comportamento de login de conta e para configurar respostas automatizadas para ações suspeitas detetadas, como relacionadas com as identidades dos utilizadores. Também ingere dados em Azure Sentinel para mais investigação.

- [Como ver a Azure AD a entrar em risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições ou grupos de gestão separados para desenvolvimento, teste e produção de Serviços de Recuperação Cofres. Separar recursos com uma rede virtual ou sub-rede, marcados adequadamente e protegidos por um grupo de segurança de rede ou Azure Firewall. 

Desligue as máquinas virtuais, que armazenam ou processam dados sensíveis, quando não estão a ser utilizadas. Implementar políticas e procedimentos para tornar este processo recorrente. 

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Visão geral da recuperação do local](site-recovery-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Utilize link privado ou ponto final privado, grupos de segurança de rede e tags de serviço para mitigar quaisquer oportunidades de exfiltração de dados a partir da Recuperação do Site habilitada a máquinas virtuais.

A Microsoft gere a plataforma subjacente utilizada pela Recuperação do Site e trata todos os conteúdos do cliente como sensíveis e protegidos contra a perda e exposição de dados do cliente. A Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados para garantir que os dados dos clientes dentro do Azure permanecem seguros. 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

- [Replicar máquinas virtuais com pontos finais privados Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replicar máquinas virtuais com etiquetas de serviço de recuperação do site Azure](azure-to-azure-about-networking.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: A Recuperação do Site utiliza um canal de https seguro, encriptado utilizando o Advanced Encryption Standard (AES 256), desde servidores de carga de trabalho Azure até serviços de Recuperação de Locais alojados atrás de um cofre dos Serviços de Recuperação.

As atuais versões TLS suportadas para recuperação de sítios são TLS 1.0, TLS 1.1, TLS 1.2 em regiões, que estavam em direto até ao final de 2019. TLS1.2 é a única versão TLS suportada para quaisquer novas regiões.

- [Compreender a encriptação em trânsito para a recuperação do site Azure](physical-azure-set-up-source.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para recuperação do local. 

Implementar uma solução de terceiros, se necessário, para efeitos de conformidade.

A Microsoft gere a plataforma subjacente utilizada pela Recuperação do Site e trata todos os conteúdos do cliente como sensíveis e guarda contra a perda e exposição de dados do cliente. Implementou e mantém um conjunto de robustos controlos e capacidades de proteção de dados para garantir que os dados dos clientes dentro do Azure permanecem seguros. 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso a dados e recursos relacionados com os recursos de Recuperação de Sítios. 

Tarefas de trabalho separadas com a Azure RBAC e concedem o acesso adequado necessário para eles. Utilize as funções de Recuperação do Local incorporadas para controlar as operações de gestão da Recuperação do Local.

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Utilize Role-Based Controlo de Acesso para gerir a recuperação do site Azure](site-recovery-role-based-linked-access-control.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Ative a dupla encriptação com as teclas geridas pelo cliente e plataforma. Esta capacidade está disponível na Recuperação do Local. 

A Recuperação do Site suporta a encriptação no descanso para obter dados. Para cargas de trabalho do Azure IaaS, os dados são encriptados em repouso utilizando encriptação do serviço de armazenamento (SSE). 

Apenas o cliente tem acesso à chave de encriptação enquanto utiliza um cofre dos Serviços de Recuperação encriptado com uma chave gerida pelo cliente. A Microsoft nunca mantém uma cópia, não tem acesso à chave e não desencripta os dados transferidos do local primário para a recuperação de desastres em nenhum ponto. 

- [Suporte de chaves geridas pelo cliente para recuperação do site Azure](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com registos de atividade do Azure para criar alertas quando ocorrerem alterações para recursos críticos,. Estes recursos podem incluir casos de produção de Cofres de Serviços de Recuperação, recursos do serviço de recuperação de locais e recursos conexos.
- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar ou descobrir todos os recursos, incluindo cofres de serviços de recuperação, dentro das suas subscrições. Certifique-se de permissões de leitura adequadas no seu inquilino e enumere todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas nos cofres dos Serviços de Recuperação e outros recursos relacionados, utilizados pela Recuperação do Site com metadados, para os organizar logicamente numa taxonomia.
- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e acompanhar a Recuperação do Local (cofres dos Serviços de Recuperação) e outros recursos relacionados. 

Além disso, utilize a Azure Policy para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada: 

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Criar um inventário de recursos Azure aprovados e software aprovado para recursos de computação com base nos requisitos organizacionais do cliente.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada: 

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos 
- Tipos de recursos permitidos

Compreender como criar e gerir políticas no Azure é importante para manter a conformidade com os seus padrões empresariais e contratos de nível de serviço.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações aos recursos num ambiente de alta segurança.

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o cofre dos Serviços de Recuperação com a Política Azure. 

Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.RecoveryServices" para criar políticas personalizadas para auditar ou impor a configuração dos recursos de cofre de serviços de recuperação do serviço de recuperação de locais.
- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos para impor configurações seguras em todos os seus recursos Azure.
- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Escolha O Azure Repos para armazenar e gerir o seu código de forma segura se estiver a utilizar definições personalizadas da Política Azure para os seus Cofres de Serviços de Recuperação e recursos relacionados.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.RecoveryServices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.RecoveryServices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Use a Política Azure [auditoria], [negar], e [implementar se não existir] efeitos para impor automaticamente configurações para os seus recursos Azure.
- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: O cliente deve gerir os segredos de recuperação do site integrados com o cofre da Chave Azure, permitindo simultanea a recuperação de desastres para máquinas virtuais ativadas por encriptação de discos Azure. 

- [Como criar um cofre chave](../key-vault/general/quick-create-portal.md)

- [Como autenticar para o cofre da Chave](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso a cofre chave](../key-vault/general/assign-access-policy-portal.md)

- [Como ativar o DR para máquinas virtuais ativadas por encriptação de discos Azure usando a Recuperação do Site](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A Recuperação do Local suporta a identidade gerida pelo sistema apenas quando um cliente pode permitir a identidade gerida pelo sistema no cofre dos Serviços de Recuperação. A mesma metodologia aplica-se aos recursos utilizados na oferta de Recuperação de Desastres para definir a fronteira de acesso. 

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. 

As identidades geridas permitem-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Como ativar a identidade gerida pelo sistema no cofre de serviços de recuperação](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Recuperação do Site), no entanto não funciona no seu conteúdo. Pré-digitalize quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage e Blob Storage.

Utilize a deteção de ameaças do Security Center para os serviços de dados detetarem malware enviado para contas de armazenamento.

- [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Compreenda a deteção de ameaças do Azure Security Center para serviços de dados](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A Recuperação do Local utiliza internamente uma conta de Armazenamento Azure para manter o estado da solução de Recuperação de Desastres, tal como configurado pelos clientes nas suas cargas de trabalho.

Todos os recursos de armazenamento utilizados pelos metadados dos serviços de recuperação do site com configuração do tipo: Leia o armazenamento geo-redundante de acesso (RA-GRS). As contas de armazenamento do tipo acima de GRS (Como RAGRS, RAG-ZRS) replicam os seus dados numa região secundária (a centenas de quilómetros da localização primária dos dados de origem) para continuar a servir a Recuperação de Desastres para os clientes durante as interrupções.

Isto está fora do alcance do cliente e a equipa de Recuperação do Site trata-o internamente. O cliente pode fazer backup das chaves do Cofre em Azure.

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Ensaio periodicamente restaura as teclas geridas pelo cliente.

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Os dados são encriptados em repouso utilizando encriptação do serviço de armazenamento (SSE) com a Infraestrutura do Azure como máquinas virtuais baseadas em serviço (IaaS). Ativar a eliminação suave no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir a eliminação suave no Cofre de Chaves](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. 

Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento ou gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: Priorize quais os alertas que devem ser investigados primeiro com base na gravidade de alerta atribuída pelo Centro de Segurança. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identificar pontos fracos e lacunas e rever o plano conforme necessário

- [Consulte a publicação do NIST - Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. 

Crie um processo para rever incidentes, pós-ocorrência, para garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. 

Utilize o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel, conforme necessário.
- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para ativar automaticamente respostas através de "Aplicações lógicas" em alertas e recomendações de segurança.
- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
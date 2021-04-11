---
title: Linha de segurança Azure para firewall de aplicação web Azure
description: A linha de base de segurança Azure Web Application Firewall fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 54582456e5c55f7cbe513f8ab83fbc87ef6a3931
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285416"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Linha de segurança Azure para firewall de aplicação web Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para a Azure Web Application Firewall. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Web Application Firewall. 

> [!NOTE]
> **Foram excluídos os controlos** não aplicáveis ao Azure Web Application Firewall, ou para os quais a responsabilidade é da Microsoft. Para ver como o Azure Web Application Firewall mapeia completamente para o Benchmark de Segurança Azure, consulte o ficheiro completo de **[mapeamento de base de base de segurança da Azure Web Application Firewall](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Utilize o Microsoft Azure Web Application Firewall (WAF) para proteção centralizada de aplicações web contra explorações e vulnerabilidades comuns, tais como injeção de SQL e scripts de sites cruzados. 

- Modo de deteção: Utilize este modo para aprender o tráfego da rede, compreender e rever falsos positivos. Monitoriza e regista todos os alertas de ameaça. Certifique-se de que os registos de diagnóstico e WAF estão selecionados e ligados. Note que o WAF não bloqueia os pedidos de entrada quando está a funcionar no modo deteção.
- Modo de prevenção: Bloqueia intrusões e ataques detetados pelas regras. Um intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

Baseie o tráfego da sua rede com o modo de deteção da WAF. Depois de determinar as suas necessidades de tráfego, configuure o MODO WAF no modo de Prevenção para fazer tráfego indesejado.

Acompanhe recomendações de alta gravidade do Security Center para quaisquer recursos ativados pela Web que não estejam protegidos pela WAF.  

- [Grupos e regras de regras de CRS de firewall de aplicação web](ag/application-gateway-crs-rulegroups-rules.md) 

- [Modos WAF no Gateway de Aplicação](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modos WAF na Porta frontal](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Utilize regras personalizadas com a Firewall de Aplicação Web Azure (WAF) para permitir e bloquear o tráfego. Por exemplo, todo o tráfego proveniente de uma série de endereços IP pode ser bloqueado. Configure a Azure WAF para funcionar no modo de prevenção que bloqueia intrusões e ataques detetados pelas regras. Um intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

- [Modos WAF no Gateway de Aplicação](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modos WAF na Porta frontal](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Azure Web Application Firewall (WAF) é um componente central das proteções de aplicações web da Azure. Utilize o Azure WAF para fornecer proteção centralizada para aplicações web de explorações e vulnerabilidades comuns com regras geridas pré-configuradas contra assinaturas de ataque conhecidas das categorias OWASP TOP 10.

Personalize o Azure WAF com regras e grupos de regras para atender aos requisitos de aplicação web e eliminar falsos positivos. Associar uma Política Azure WAF para cada site por trás de um WAF para permitir uma configuração específica do site. A Azure WAF suporta regras de definição de regras de definição de regras de geo-filtragem, limitação de taxas, geridas pelo Azure. e regras personalizadas podem ser criadas de acordo com as necessidades de uma aplicação. 

Configure o Azure WAF para funcionar no modo prevenção depois de basear o tráfego da rede no modo deteção por um determinado período de tempo. O Azure WAF bloqueia intrusões e ataques detetados pelas regras no modo prevenção. Um intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

- [Modos WAF no Gateway de Aplicação](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modos WAF na Porta frontal](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Grupos e regras de regras de firewall de aplicação web CRS](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Criar, associar e organizar logicamente recursos através de uma subscrição do Azure com tags para deteção de configurações comuns de aplicações (por exemplo, Apache e IIS). 

Aplicar regras e grupos de regras às políticas Azure Web Application Firewall (WAF) com base nos metadados de identificação aplicados.

- [Política da WAF no Gateway de Aplicações](/cli/azure/network/application-gateway/waf-policy)

- [Política da WAF na Porta da Frente](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de segurança de rede associados à Firewall de Aplicação Web Azure (WAF) na sua sub-rede Azure Application Gateway, bem como quaisquer outros recursos relacionados com a segurança da rede e o fluxo de tráfego. Para regras individuais do grupo de segurança da rede, utilize o campo "Descrição" para especificar a necessidade, duração e assim por diante do negócio, para quaisquer regras que permitam o tráfego de ou para uma rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Escolha Azure PowerShell ou Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com um Config de Segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividade do Azure para monitorizar as configurações de recursos de rede e detete alterações para as definições de rede e recursos relacionados com as suas implementações de Firewall de Aplicação Web Azure (WAF). Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nas definições ou recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Crie uma regra de rede para a Azure Web Application Firewall (WAF) para permitir o acesso a um servidor NTP com a porta e o protocolo apropriados, como a porta 123 sobre a UDP.

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Configurar os registos da Firewall de Aplicação Web (WAF) a enviar para uma solução central de gestão de registos de segurança, como o Azure Sentinel, ou um SIEM de terceiros. Estes registos incluem Azure Activity, Diagnostic e registos WAF em tempo real, estes registos podem ser vistos em diferentes ferramentas, tais como Azure Monitor, Excel e Power BI. Os registos Azure Web Application Firewall dão uma visão dos dados que o Azure WAF está a avaliar, combinar e bloquear.

Azure Sentinel tem um livro de Azure WAF incorporado, que fornece uma visão geral dos eventos de segurança no Azure WAF. Este livro inclui eventos, regras compatíveis e bloqueadas, e tudo o resto que é registado nos registos de firewall. Esta telemetria pode ser usada para iniciar a automatização de playbook para notificar ou tomar ações de remediação com base em eventos WAF recolhidos por Azure Sentinel.

- [Ver Registos de Atividades](../azure-resource-manager/management/view-activity-logs.md)

- [Registos de diagnóstico para Gateway de Aplicação](../application-gateway/application-gateway-diagnostics.md)

- [Conecte os dados da firewall da aplicação web da Microsoft ao Azure Sentinel](../sentinel/connect-azure-waf.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative o registo dos recursos do Azure Web Application Firewall (WAF) para acesso a registos de auditoria, segurança e diagnóstico. O Azure Web Application Firewall fornece relatórios detalhados sobre cada uma das suas ameaças detetadas que são disponibilizadas nos registos de diagnóstico configurados. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

- [Visão geral do registo](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Visão geral da consulta de registo do Azure Monitor](../azure-monitor/logs/log-query-overview.md)

- [Visão geral dos registos da Plataforma Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Enviar os registos Azure Web Application Firewall (WAF) para uma conta de armazenamento personalizado e definir a política de retenção. Utilize o Azure Monitor para definir o período de retenção do espaço de trabalho Do Log Analytics com base nos requisitos de conformidade da sua organização.
- [Configure a monitorização de uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Azure Web Application Firewall (WAF) fornece relatórios detalhados sobre cada ameaça detetada. O registo está integrado com registos Azure Diagnostics que fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. 

As instâncias Azure WAF são integradas com o Security Center para enviar alertas e informações de saúde para relatórios. Use as recomendações do Security Center para detetar aplicações web desprotegidas e proteger estes recursos vulneráveis. 

Azure Sentinel tem um livro de eventos de firewall incorporado, que fornece uma visão geral dos eventos de segurança na WAF. Estes incluem eventos, regras compatíveis e bloqueadas, e tudo o resto que é registado nos registos de firewall.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](/azure/azure-monitor/platform/activity-log)

- [Como ativar as definições de diagnóstico para O Gateway de Aplicação Azure](../application-gateway/application-gateway-diagnostics.md)

- [Métricas de monitorização e troncos na Porta frontal de Azure](../frontdoor/front-door-diagnostics.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Ativar as definições de diagnóstico do Registo de Atividades Azure, bem como as definições de diagnóstico para o seu Azure WAF, e enviar os registos para um espaço de trabalho do Log Analytics. Realize consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados recolhidos. Crie alertas para atividades anómalas com base em métricas WAF. Por exemplo, se o número bloqueado de pedidos superiores a 'X', faça 'Y'.

- [Como ativar as definições de diagnóstico para registo de atividades Azure](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Como criar alertas dentro do Azure](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Implementar firewall de aplicação web Azure (WAF) em frente a aplicações web críticas para inspeção adicional do tráfego de entrada. 

O Azure WAF fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns e protege as suas aplicações inspecionando o tráfego web de entrada para bloquear ataques como injeções DE SQL, Scripting Cross-Site, uploads de malware e ataques DDoS.

- [Como implantar a Azure WAF](ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: O Azure Ative Directory (Azure AD) tem funções incorporadas que são capazes de consultar e devem ser explicitamente atribuídos. Utilize o módulo Azure AD PowerShell para realizar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Não existem atribuições de administrador local disponíveis dentro da WAF. No entanto, poderia haver funções de administrador do Azure Ative Directory (Azure AD) no ambiente que permitissem o controlo da gestão sobre os recursos da Azure WAF.
É uma boa prática criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso a instâncias do Azure Web Application Firewall (WAF). Utilize as funcionalidades de Gestão de Identidade e Acesso do Centro de Segurança para monitorizar o número de contas administrativas.

- [Compreender a identidade e o acesso do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

- [Compreender como criar utilizadores administrativos na Base de Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [Como usar a Política Azure](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Permitir a autenticação multifactora Azure Ative (Azure AD) e seguir as recomendações de Gestão de Identidade e Acesso do Centro de Segurança.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize a Estação de Trabalho de Acesso Privilegiada (PAW) com autenticação multifactor configurada para iniciar sessão e configurar firewall de aplicação web Azure (WAF) e recursos conexos.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança do Azure Ative Directory (Azure AD) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie as suas localizações nomeadas.

Criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões com localizações nomeadas. Restringir o acesso aos seus recursos sensíveis, como os segredos do Azure Key Vault, aos seus locais configurados.

- [Qual é a condição de localização no Azure Ative Directy (Azure AD) Acesso Condicional](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. O Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito e também sais, hashes e armazena de forma segura as credenciais dos utilizadores.
- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Use a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. Reveja o acesso dos utilizadores regularmente para garantir que apenas os utilizadores ativos tenham acesso continuado.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring)

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Integrar o Azure Ative Directory (Azure AD) Fontes de registo de registo de eventos de auditoria e risco, com qualquer SIEM ou ferramenta de monitorização, como a Azure Sentinel.

Dinamize este processo criando Definições de Diagnóstico para contas de utilizadores Azure E envie os registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize as funcionalidades de Proteção de Risco e Identidade (Azure AD) do Azure AD para configurar respostas automatizadas a ações suspeitas detetadas relacionadas com identidades dos utilizadores. Ingerir dados no Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear o Firewall de Aplicação Web Azure (WAF) e recursos relacionados que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade de dados, por exemplo, ambientes de desenvolvimento, teste e produção. 

Controlar o acesso aos recursos Azure com o Azure Ative Directory (Azure AD) controlo de acesso baseado em funções (Azure RBAC).

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Certifique-se de que quaisquer clientes que se conectem à sua Firewall de Aplicação Web Azure (WAF) e recursos relacionados sejam capazes de negociar TLS 1.2 ou maior.

Siga as recomendações do Centro de Segurança para encriptação em repouso e encriptação em trânsito, quando aplicável.

- [Compreender a encriptação em trânsito com Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos 

**Orientação**: Controle o acesso aos seus recursos Azure como web Application Firewall com controlo de acesso baseado em funções Azure (Azure RBAC).

- [Como configurar o Azure RBAC em Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Utilize a encriptação em repouso para todos os recursos Azure, incluindo a Azure Web Application Firewall (WAF) e recursos relacionados. A Microsoft recomenda que o Azure gere as suas chaves de encriptação, no entanto existe a opção de gerir as suas próprias chaves em alguns casos.

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

- [Como configurar chaves de encriptação geridas pelo cliente](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Configurar a Firewall de Aplicação Web Azure (WAF) para funcionar no modo de Prevenção depois de basear o tráfego de rede no modo deteção durante um período de tempo pré-determinado. 

O Azure WAF, em modo prevenção, bloqueia intrusões e ataques que são detetados pelas regras. O intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

- [Visão geral da integração entre o Application Gateway e o Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [Modos WAF no Gateway de Aplicação](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [Modos WAF na Porta frontal](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar ou descobrir todos os recursos, tais como, cálculo, armazenamento, rede, portas e protocolos e assim por diante dentro das suas subscrições.

Certifique-se de permissões (leia) adequadas no seu inquilino e enumere todas as subscrições do Azure, bem como os recursos dentro das suas subscrições. Embora os recursos clássicos do Azure possam ser descobertos através do Gráfico de Recursos, é altamente recomendado criar e utilizar recursos do Azure Resource Manager.

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Utilize etiquetas nas políticas Azure Web Application Firewall (WAF). As etiquetas podem ser associadas a recursos e aplicadas logicamente para organizar o acesso a estes recursos numa subscrição de clientes. 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear a Azure WAF e recursos conexos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Criar um inventário de recursos aprovados, incluindo a configuração com base nas necessidades organizacionais.

Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Use a Política Azure para impor restrições ao tipo de recursos que podem ser criados nas suas subscrições.
Utilize o Gráfico de Recursos Azure para consultar ou descobrir os recursos do Azure Web Application Firewall (WAF) dentro das suas subscrições. Certifique-se de que todos os Azure WAF e recursos conexos presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Monitorize e remova os recursos Azure WAF não aprovados com a Azure Policy para negar a implantação de Azure WAF, ou um certo tipo de WAF, por exemplo, Azure WAF v1 vs V2.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para restringir os serviços que pode prestação no seu ambiente.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade de um utilizador interagir com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: O Azure Web Application Firewall (WAF) pode ser associado a diferentes ambientes através de redes, grupos de recursos ou subscrições para segregar aplicações de alto risco.

- [Visão geral da rede virtual Azure](../virtual-network/virtual-networks-overview.md)

- [Organize your resources with Azure management groups](../governance/management-groups/overview.md) (Organizar os recursos com os grupos de gestão do Azure)

- [Guia de decisão de subscrição](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para definições de rede relacionadas com as suas implementações de Firewall de Aplicação Web Azure (WAF).

Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus Gateways de Aplicações Azure, Redes Virtuais, grupos de segurança de rede e utilizar definições de políticas incorporadas.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] efeitos para impor definições seguras através do firewall de aplicação web Azure (WAF) e recursos relacionados. 

Utilize modelos do Gestor de Recursos Azure para manter a configuração de segurança do seu Azure WAF e recursos relacionados exigidos pela sua organização.

- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código, tais como políticas personalizadas de Azure e modelos de Gestor de Recursos Azure.

Conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD), se integrados com Azure DevOps, ou Azure AD, se integrado com o Team Foundation Server (TFS).

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolver um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](/azure/governance/policy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize definições de política de Azure incorporadas, bem como pseudónimos da Política Azure no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Use a Política Azure [auditoria], [negar], e [implementar se não existir] efeitos para impor automaticamente configurações para os seus recursos Azure.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Documentação do Azure Policy](/azure/governance/policy)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize o Cofre da Chave Azure para armazenar certificados de forma segura. O Azure Key Vault é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados SSL. 

O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. 

- [Como configurar a rescisão SSL com certificados Key Vault utilizando a Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código, o que também encorajará a movimentação de credenciais descobertas para locais mais seguros, como o Azure Key Vault.
- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada para o Cofre da Chave Azure. A eliminação suave permite a recuperação de cofres e objetos de abóbada apagados, tais como chaves, segredos e certificados.

- [Como usar o Soft Delete do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Desenvolva um guia de resposta a incidentes para a sua organização. Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções do pessoal, bem como as fases de tratamento e gestão de incidentes, desde a deteção até à revisão pós-incidente. 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Utilize o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na métrica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.
- [Consulte o Guia de Teste, Formação e Exercício da NIST para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.
- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Seu Centro de Segurança utilizando a função Exportação Contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Utilize o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel de acordo com os requisitos da sua organização.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança para ativar automaticamente respostas através de "Aplicações lógicas" em alertas e recomendações de segurança.
- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)

---
title: Linha de base de segurança Azure para Batch
description: A linha de base de segurança do Lote fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3678ead9f3e1ba2556fde3c2fbe30df4e7dc2225
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737240"
---
# <a name="azure-security-baseline-for-batch"></a>Linha de base de segurança Azure para Batch

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../security/benchmarks/overview-v1.md) ao Lote. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Batch. Foram excluídos **os controlos** não aplicáveis ao Lote.

 
Para ver como o Batch mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança do Lote completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Implementar o pool(s) Azure Batch dentro de uma rede virtual. Para permitir que os nós de cálculo de piscinas se comuniquem de forma segura com outras máquinas virtuais, ou com uma rede no local, pode providenciar a piscina numa sub-rede de uma rede virtual Azure. Além disso, a implementação do Pool dentro de uma rede virtual dá-lhe controlo sobre o grupo de segurança de rede (NSG) utilizado para proteger as interfaces de rede individuais dos nós (NIC), bem como a sub-rede. Configure o NSG para permitir o tráfego a partir de apenas IP(s)/locais fidedignos na Internet.

Quando aplicável, desative o acesso à rede pública utilizando o Azure Private Link para ligar à conta Azure Batch através de um ponto final privado. O serviço Azure Private Link está seguro e aceita ligações apenas a partir de pontos finais privados autenticados e autorizados. Pode ainda limitar a conectividade e a descoberta, desativando os pontos finais RDP/SSH expostos publicamente para os nós de computação numa piscina de Lote.

- [Como criar um Lote Azure Pool dentro de uma rede virtual](batch-virtual-network.md)

- [Como criar uma conta Azure Batch com acesso à rede desativado](private-connectivity.md)

- [Como criar um ponto final privado](../private-link/create-private-endpoint-portal.md)

- [Como negar o acesso ao tráfego RDP/SSH](pool-endpoint-configuration.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Azure Security Center e remedia as recomendações de proteção da rede relacionadas com o grupo de segurança de rede virtual /rede (NSG) associado ao seu pool de lote. Ativar os registos de fluxo no NSG que está a ser utilizado para proteger a sua piscina de Lote e enviar registos numa Conta de Armazenamento Azure para auditoria de tráfego. Também pode enviar registos de fluxo NSG para um espaço de trabalho Azure Log Analytics e utilizar o Azure Traffic Analytics para fornecer informações sobre o fluxo de tráfego na sua nuvem Azure. Algumas vantagens da Azure Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como Ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a Segurança da Rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar a proteção padrão do Azure DDoS (negação de serviço distribuído) na rede virtual que protege o seu pool Azure Batch para proteção contra ataques DDoS. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a Azure Security Center Integrada Desespionagem de Ameaças](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Ative os registos de fluxo no grupo de segurança da rede (NSG) que está a ser utilizado para proteger a sua piscina Azure Batch e envie registos para uma conta de armazenamento Azure para auditoria de tráfego.

- [Como ativar registos de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Se necessário para efeitos de conformidade, selecione um aparelho virtual de rede do Mercado Azure que suporte sistemas de deteção de intrusões (IDS) e sistemas de prevenção de intrusões (IPS) com capacidades de inspeção de carga útil.

Se a deteção e/ou prevenção de intrusões baseada na inspeção da carga útil não for um requisito, a Azure Firewall com inteligência de ameaça pode ser usada. A filtragem baseada em ameaças de Azure Firewall pode alertar e negar tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

Implemente o Azure Firewall com um endereço IP público na mesma rede virtual que os seus nós Azure Batch Pool. Configure as regras de tradução de endereços de rede (NAT) entre localizações fidedignas na Internet e os endereços IP privados dos seus nós de piscina individuais. No Azure Firewall, sob a Inteligência de Ameaça, configurar "Alert and deny" para bloquear para alertar e bloquear tráfego de/para/de endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence, e apenas os registos de maior confiança estão incluídos. 

- [Como criar um Lote Azure Pool dentro de uma rede virtual](batch-virtual-network.md)

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Utilize etiquetas de serviço de rede virtuais para definir controlos de acesso à rede em grupos de segurança de rede ou firewalls Azure associados à sua piscina Azure Batch. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e usar etiquetas de serviço](../virtual-network/service-tags-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para os recursos de rede associados ao seu(s) pool(s) Azure Batch com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.Batch" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus pools Azure Batch.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para grupos de serviço de rede (NSGs) e outros recursos relacionados com a segurança da rede e o fluxo de tráfego que estejam associados aos seus lotes Azure. Para regras individuais de NSG, utilize o campo "Descrição" para especificar a necessidade e/ou duração do negócio (etc.) para quaisquer regras que permitam o tráfego de/para uma rede.

Utilize qualquer uma das definições da Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo dos recursos existentes não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações nos recursos de rede relacionados com os seus pools Azure Batch. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Conta Azure Batch a bordo do Azure Monitor para agregar dados de segurança gerados pelos dispositivos de cluster. Aproveite as consultas personalizadas para detetar e responder a ameaças no ambiente.  Para a monitorização ao nível dos recursos do Azure Batch, utilize as APIs do lote para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

- [Como embarcar uma conta do Azure Batch para o Azure Monitor](batch-diagnostics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Para monitorização do nível de conta do Azure Batch, monitorize cada conta batch utilizando as características do Monitor Azure. O Azure Monitor recolhe métricas e registos de diagnóstico opcional para recursos abrangidos ao nível de uma conta Batch, tais como piscinas, empregos e tarefas. Recolher e consumir estes dados manualmente ou programáticamente para monitorizar as atividades na sua conta Batch e diagnosticar problemas.

Para monitorizar o nível de recursos do Azure Batch, utilize as APIs do Lote Azure para monitorizar ou consultar o estado dos seus recursos, incluindo empregos, tarefas, nós e piscinas.

- [Como configurar a monitorização e registo do nível de conta do Azure Batch](monitoring-overview.md)

- [Compreender a monitorização ao nível dos recursos do Batch](monitoring-overview.md#batch-resource-monitoring)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

#### <a name="azure-policy-built-in-definitions"></a>Definições incorporadas da Política Azure

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: O Azure Monitor recolhe métricas e registos de diagnóstico para recursos na sua conta Azure Batch. Recolher e consumir estes dados de várias formas de monitorizar a sua conta Azure Batch e diagnosticar problemas. Também pode configurar alertas métricos para que receba notificações quando uma métrica atinge um valor especificado.

Se necessário, talvez se conecte aos seus nós de piscina individuais através de Secured Shell (SSH) ou Remote Desktop Protocol (RDP) para aceder aos registos do sistema operativo local.

- [Como recolher registos de diagnóstico da sua conta Azure Batch](batch-diagnostics.md#batch-diagnostics)

- [Como ligar remotamente aos seus nosmos de piscina Azure Batch](./batch-service-workflow-features.md#basic-workflow)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Conta de lote Azure a bordo para o Monitor Azure. Certifique-se de que o espaço de trabalho Azure Log Analytics utilizado tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização

- [Como configurar a monitorização e a exploração madeireira do Azure Batch](monitoring-overview.md)

- [Como configurar o período de retenção do espaço de trabalho do Azure Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Crie alertas métricos do Lote Azure que desencadeiem quando o valor de uma métrica especificada cruza um determinado limiar.

- [Como configurar alertas métricos do Azure Batch](batch-diagnostics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Crie alertas métricos do Lote Azure que desencadeiem quando o valor de uma métrica especificada cruza um determinado limiar.

- [Como configurar alertas métricos do Azure Batch](batch-diagnostics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Utilize o Windows Defender nos seus nós individuais de lote no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Implementar solução de terceiros para a sessão de registo de DNS.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: Configurar manualmente a registo da consola e a transcrição powerShell numa base por nó.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha o registo da conta administrativa local que é criada durante o provisionamento do pool do Lote Azure, bem como quaisquer outras contas que crie. Além disso, se a integração do Azure Ative Directory for utilizada, a Azure AD tem funções incorporadas que devem ser explicitamente atribuídas e, portanto, são questionáveis. Utilize o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: Ao providenciar uma piscina Azure Batch, é-lhe dada a opção de criar contas de máquinas locais. Não existem senhas predefinidos para alterar, no entanto pode especificar diferentes palavras-passe para acesso secured Shell (SSH) e Remote Desktop Protocol (RDP). Depois de configurado o Azure Batch Pool, pode gerar um utilizador aleatório para nós individuais dentro do portal Azure ou através da AZure Resource Manager API.

- [Como adicionar um utilizador a um nó de computação específico](/rest/api/batchservice/computenode/adduser)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Integrar a autenticação para aplicações de lote Azure com diretório ativo Azure. Criar políticas e procedimentos em torno da utilização de contas administrativas dedicadas.

Além disso, pode utilizar recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure.

- [Como autenticar aplicações de Lote com Diretório Ativo Azure](batch-aad-auth.md)

- [Como monitorizar a identidade e o acesso com o Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Integrar a autenticação para aplicações de lote Azure com diretório ativo Azure. Ativar a autenticação multifactor Azure AD e seguir as recomendações do Azure Security Center Identity and Access Management.

 

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize PAWs (estações de trabalho privilegiadas de acesso) com autenticação multifactor configurada para iniciar sessão e configurar os seus recursos do Azure Batch.

- [Saiba mais sobre estações de trabalho de acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Se tiver autenticação integrada para aplicações de lote Azure com diretório Azure Ative, utilize relatórios de segurança do Azure Ative Directory para a geração de registos e alertas quando ocorrer atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar a identidade e a atividade de acesso dos utilizadores no Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Se tiver autenticação integrada para aplicações de lote Azure com diretório ativo Azure, pode utilizar locais com indicação de acesso condicional para permitir o acesso a partir de apenas agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas em Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory como sistema central de autenticação e autorização e integre a autenticação para aplicações de lote Azure com Azure AD. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.

- [Como criar e configurar instâncias do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Como autenticar aplicações de Lote com Azure AD](batch-aad-auth.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory fornece registos para ajudar a descobrir contas velhas. Além disso, pode utilizar a Azure Identity Access Reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso dos utilizadores pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

- [Como utilizar comentários sobre acesso à identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Criar Definições de diagnóstico para contas de utilizadores do Azure Ative Directory, enviar os registos de auditoria e registos de login para um espaço de trabalho Azure Log Analytics. Configure os alertas desejados dentro do espaço de trabalho Azure Log Analytics.

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize a funcionalidade de Deteção de Risco e Proteção de Identidade do Diretório Ativo Azure para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Além disso, você pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte  

**Orientação**: Não disponível; Lockbox do cliente ainda não suportado para Azure Batch.
 
- [Lista de serviços suportados pelo Bloqueio do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar subscrições separadas e/ou grupos de gestão para desenvolvimento, teste e produção. As piscinas de lote Azure devem ser separadas por rede/sub-rede virtuais, marcadas adequadamente e protegidas com um grupo de segurança de rede (NSG). Os dados do Azure Batch devem ser contidos numa conta de armazenamento Azure segura.

- [Como criar um Lote Azure Pool dentro de uma rede virtual](batch-virtual-network.md)

- [Como garantir contas de armazenamento Azure](../storage/blobs/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Para contas de armazenamento Azure associadas ao seu(s) Azure Batch Pool(s) que contenham informações sensíveis, marque-as como sensíveis usando Tags e as fixe com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

- [Como garantir contas de armazenamento Azure](../storage/blobs/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Criptografe todas as informações sensíveis em trânsito. Os recursos da Microsoft Azure negociarão tLS 1.2 por padrão. Certifique-se de que quaisquer clientes que se conectem aos seus Pools de Lote Azure ou lojas de dados (Contas de Armazenamento Azure) sejam capazes de negociar TLS 1.2 ou superior.

Certifique-se de que o HTTPS é necessário para aceder à Conta de Armazenamento que contém os seus dados do Azure Batch.

- [Compreender encriptação da conta de armazenamento Azure em trânsito](../storage/blobs/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Para contas de armazenamento Azure associadas ao seu(s) Azure Batch Pool(s) que contenham informações sensíveis, marque-as como sensíveis usando tags e prenda-as com as melhores práticas do Azure.

As funcionalidades de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

- [Como garantir contas de armazenamento Azure](../storage/blobs/security-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizar o controlo de acesso baseado em funções para controlar o acesso aos recursos

**Orientação**: Utilize o controlo de acesso baseado em funções (Azure RBAC) para controlar o acesso ao plano de gestão de recursos Azure, incluindo a Conta de Lote, Pool de Lotes e Contas de Armazenamento.

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para o Azure Storage ou para calcular recursos. Implementar solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: Para contas de armazenamento associadas à sua conta Azure Batch, é aconselhável permitir à Microsoft gerir as chaves de encriptação, no entanto, tem a opção de gerir as suas próprias chaves, se necessário.

A encriptação do disco Azure pode ser usada para ajudar a proteger e salvaguardar os seus dados para cumprir compromissos de segurança organizacional e conformidade. Todos os discos geridos, instantâneos, imagens e dados escritos para discos existentes são automaticamente encriptados em repouso com teclas geridas pela plataforma.

- [Como gerir chaves de encriptação para contas de armazenamento Azure](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Como configurar chaves de encriptação geridas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Como criar uma piscina com encriptação de disco ativada](disk-encryption.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o Registo de Atividades Azure para criar alertas para quando ocorrerem alterações para recursos críticos do Azure relacionados ou associados às suas contas/piscinas do Azure Batch.

Configure as definições de diagnóstico para contas de armazenamento associadas ao Azure Batch Pool para monitorizar e registar todas as operações CRUD contra dados do pool.

- [Como criar alertas para eventos de Registo de Atividades Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Como permitir a registo/auditoria adicional de uma Conta de Armazenamento Azure](../storage/common/storage-monitor-storage-account.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Para os nós do Azure Batch Pool, é responsável pela gestão da solução de gestão de vulnerabilidades.

Opcionalmente, se tiver uma subscrição de Rapid7, Qualys ou qualquer outra plataforma de gestão de vulnerabilidades, poderá instalar manualmente agentes de avaliação de vulnerabilidades nos nós da piscina Batch e gerir os nós através do respetivo portal.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Microsoft para manter e atualizar as imagens do nó do conjunto Azure Batch Pool. Certifique-se de que o sistema operativo dos nós do Azure Batch Pool permanece corrigido durante o período de vida útil do cluster, o que pode exigir atualizações automáticas, monitorização dos nós ou reinicialização periódica.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Certifique-se de que as aplicações de terceiros dos nós de Azure Batch Pool permanecem corrigidas durante o período de vida útil do cluster, o que pode exigir atualizações automáticas, monitorização dos nós ou reinicialização periódica.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: Se tiver uma subscrição de rapid7, Qualys ou qualquer outra subscrição da plataforma de gestão de vulnerabilidades, poderá utilizar o portal desse fornecedor para visualizar e comparar as verificações de vulnerabilidades de trás para a saturação.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize um programa comum de pontuação de risco (por exemplo, sistema comum de pontuação de vulnerabilidade) ou as classificações de risco padrão fornecidas pela sua ferramenta de digitalização de terceiros.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como cálculo, armazenamento, rede, etc.) dentro da sua subscrição(s). Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e que é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph Explorer, é altamente recomendado criar e utilizar os recursos do Azure Resource Manager.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas Subscrições Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos Azure, dando metadados para organizar logicamente numa taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter o inventário dos recursos aprovados da Azure

**Orientação**: Definir lista de recursos Azure aprovados e software aprovado para recursos computacional

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição.s. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para monitorizar os nós do cluster para aplicações de software não aprovadas.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para monitorizar os nós do cluster para aplicações de software não aprovadas.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Para os nós do Azure Batch Pool, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição(s) de subscrição de clientes, utilizando as seguintes definições de política incorporada: 
- Tipos de recursos não permitidos 
- Tipos de recursos permitidos 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Para os nós do Pool de Lote Azure, implemente uma solução de terceiros para evitar a execução de tipos de ficheiros não autorizados.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração das suas contas e piscinas do Azure Batch.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Estabeleça configurações seguras para o sistema operativo dos nós do Pool de Lote.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize a Política Azure [negar] e [implementar se não existir] para impor definições seguras para os recursos Azure relacionados com a sua conta Batch e piscinas (tais como redes virtuais, sub-redes, Firewalls Azure, Contas de Armazenamento Azure, etc.). Pode utilizar pseudónimos da Azure Policy a partir dos seguintes espaços de nome para criar políticas personalizadas:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender efeitos da política do Azure](../governance/policy/concepts/effects.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Imagens do sistema operativo Azure Batch Pool geridas e mantidas pela Microsoft. É responsável pela implementação da configuração do estado de nível oss.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para as suas contas, piscinas ou recursos relacionados do Azure Batch, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação de Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: Se utilizar imagens personalizadas para as suas piscinas Azure Batch, utilize o controlo de acesso baseado em funções (RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens.

- [Compreender o RBAC em Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Como configurar o RBAC no Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize definições de Política Azure incorporadas para alertar, auditar e impor configurações de recursos relacionadas com o Azure Batch.  Use pseudónimos da Azure Policy no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para as suas contas e piscinas do Azure Batch. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para manter o estado desejado para os sistemas operativos dos nós do Azure Batch Pool.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.Batch" para criar políticas personalizadas para auditar ou impor a configuração da sua instância do Azure Batch. Também pode utilizar quaisquer políticas incorporadas criadas especificamente para o Azure Batch ou os recursos utilizados pela Azure Batch, tais como:
- As subnetas devem ser associadas a um Grupo de Segurança de Rede -As contas de armazenamento devem utilizar um ponto final de serviço de rede virtual
- Os registos de diagnóstico nas contas do Lote devem ser ativados

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Implementar uma solução de terceiros para monitorizar o estado dos sistemas operativos dos seus nós Azure Batch Pool.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: O Cofre da Chave Azul pode ser utilizado com implementações do Azure Batch para gerir as chaves para armazenamento de piscinas dentro das contas de armazenamento do Azure.

- [Como integrar-se com identidades geridas aZure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre de Chaves Azure](../key-vault/general/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)
- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Utilizar software anti-malware gerido centralmente

**Orientação**: Utilize o Windows Defender nos seus nós individuais de piscina Azure Batch no caso dos sistemas operativos Windows ou forneça a sua própria solução anti-malware se estiver a utilizar o Linux.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Antimalware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Batch), no entanto não funciona com conteúdo do cliente.

Pré-digitalizar quaisquer ficheiros que sejam enviados para recursos Azure não computados, tais como App Service, Data Lake Storage, Blob Storage, etc. A Microsoft não pode aceder aos dados dos clientes nestes casos.

- [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software e assinaturas anti-malware são atualizados

**Orientação**: Utilize o Windows Defender nos seus nós individuais de piscina Azure Batch no caso dos sistemas operativos Windows e certifique-se de que a atualização automática está ativada. Forneça a sua própria solução anti-malware se estiver a utilizar o Linux.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS). 

- [Como configurar a redundância de armazenamento para contas de armazenamento Azure](../storage/common/storage-redundancy.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Quando utilizar uma conta de armazenamento Azure para a loja de dados Azure Batch Pool, escolha a opção de despedimento adequada (LRS, ZRS, GRS, RA-GRS). Se utilizar o Cofre da Chave Azure para qualquer parte da sua implantação do Azure Batch, certifique-se de que as suas chaves estão apoiadas.

- [Como configurar a redundância de armazenamento para contas de armazenamento Azure](../storage/common/storage-redundancy.md)

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Se estiver a gerir as suas próprias chaves para contas de armazenamento Azure ou qualquer outro recurso relacionado com a sua implementação do Azure Batch, teste periodicamente a restauração das teclas de apoio.

- [Como backup chaves chave cofre em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [Como restaurar uma chave gerida pelo cliente com o PowerShell](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Se o Cofre da Chave Azure estiver a ser utilizado para conter quaisquer teclas relacionadas com as contas de armazenamento de piscinas do Azure Batch, ative Soft-Delete no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.

- [Como permitir a eliminação suave no cofre da chave Azure](../key-vault/general/key-vault-recovery.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Certifique-se de que existem planos escritos de resposta a incidentes que definam funções de pessoal, bem como fases de tratamento/gestão de incidentes.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente ao mesmo. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: - [Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Pode encontrar mais informações sobre a estratégia e execução da Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridos pela Microsoft na nuvem, abaixo: 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)

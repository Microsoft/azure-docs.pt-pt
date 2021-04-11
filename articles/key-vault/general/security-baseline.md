---
title: Linha de segurança Azure para Key Vault
description: A base de segurança Key Vault fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 305fdd3a0b8e0c876924c6e1f090424e67571af0
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968710"
---
# <a name="azure-security-baseline-for-key-vault"></a>Linha de segurança Azure para Key Vault

Esta linha de base de segurança aplica orientações da [versão 1.0 do Benchmark de Segurança Azure](../../security/benchmarks/overview-v1.md) ao Key Vault. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Key Vault. **Foram excluídos os controlos** não aplicáveis ao Key Vault, ou para os quais a responsabilidade é da Microsoft.

Para ver como o Key Vault mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança key Vault completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Integre o cofre da chave Azure com a Ligação Privada Azure. O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault) e ao Azure hospedados serviços de cliente/parceiro sobre um Ponto Final Privado na sua rede virtual.

Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

- [Como integrar o Cofre de Chaves com Link Privado Azure](/azure/key-vault/private-link-service)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos configurados do Cofre chave em Azure. 

- [Para mais informações sobre a Segurança da Rede fornecida pelo Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ativar o Padrão de Proteção Azure DDoS nas Redes Virtuais Azure associadas aos seus casos de Cofre-Chave para proteção contra ataques de negação de serviço distribuídos. Use a Azure Security Center Integrated Threat Intelligence para negar comunicações com endereços IP de Internet maliciosos ou não utilizados.

 
- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](/azure/virtual-network/manage-ddos-protection)

- [Deteção de ameaças para a camada de serviço Azure no Centro de Segurança Azure](/azure/security-center/security-center-alerts-service-layer)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Este requisito pode ser cumprido configurando uma proteção avançada de ameaças (ATP) para o Cofre da Chave Azure. O ATP fornece uma camada adicional de inteligência de segurança. Esta ferramenta deteta tentativas potencialmente nocivas de aceder ou explorar contas do Azure Key Vault.

Quando o Azure Security Center deteta atividade anómala, apresenta alertas. Envia também um e-mail ao administrador de subscrição com detalhes da atividade suspeita e recomendações para como investigar e remediar as ameaças identificadas.

- [Configurar proteção avançada contra ameaças para o Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso às suas instâncias Azure Key Vault, utilize etiquetas de serviço Azure para o Cofre da Chave Azure para definir controlos de acesso à rede em grupos de segurança de rede ou firewall Azure. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, ApiManagement) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Visão geral das etiquetas de serviço Azure](../../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus casos de Azure Key Vault com a Política Azure. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.KeyVault" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas instâncias Azure Key Vault. Você também pode fazer uso de definições políticas incorporadas relacionadas com Azure Key Vault, tais como:
- O Key Vault deve usar um ponto final de serviço de rede virtual

Para obter mais informações, veja as seguintes referências:

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Exemplos do Azure Policy](/azure/governance/policy/samples)

- [Definir e atribuir uma planta no portal](../../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos relacionados com a segurança da rede e fluxo de tráfego para as suas instâncias Azure Key Vault para fornecer metadados e organização lógica.

Utilize as definições de Política Azure incorporadas relacionadas com a marcação, tais como "Exigir etiqueta e seu valor" para garantir que todos os recursos são criados com etiquetas e para notificá-lo de recursos não marcados.

Pode utilizar o Azure PowerShell ou o Azure CLI para procurar ou executar ações em recursos baseados nas suas etiquetas.

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com as suas instâncias do Azure Key Vault. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Ver e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log-view)

- [Criar, visualizar e gerir alertas de registo de atividades utilizando o Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pelo Azure Key Vault. Dentro do Azure Monitor, utilize o espaço de trabalho Azure Log Analytics para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Registo do Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Quickstart: Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Ative as definições de diagnóstico nos seus casos de Azure Key Vault para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

- [Registo do Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Âmbito do Monitor Azure, para o espaço de trabalho Log Analytics utilizado para manter os registos do Cofre da Chave Azure, desagure o período de retenção de acordo com os regulamentos de conformidade da sua organização. Utilize contas de armazenamento Azure para armazenamento a longo prazo/arquivo.

- [Change the data retention period](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) (Alterar o período de retenção de dados)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Registos de monitorização e revisão

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados dos seus recursos protegidos pelo Cofre da Chave Azure. Utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e efetuar consultas nos dados de registo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [A bordo Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Começa com o Log Analytics no Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Introdução às consultas de registos no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Centro de Segurança Azure, ative a proteção avançada de ameaças (ATP) para o Cofre de Chaves. Ativar as definições de diagnóstico no Cofre de Chaves Azure e enviar registos para um espaço de trabalho do Log Analytics. A bordo do seu espaço de trabalho Log Analytics para Azure Sentinel, uma vez que fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Isto permite criar playbooks (soluções automatizadas) e ser usados para remediar problemas de segurança.

- [Início Rápido: Integração do Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Responder a eventos com Alertas do Azure Monitor](/azure/azure-monitor/learn/tutorial-response)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Mantenha um inventário das suas aplicações registadas no Azure Ative Directory (Azure AD), bem como quaisquer contas de utilizador que tenham acesso às chaves, segredos e certificados do Azure Key Vault. Pode utilizar o portal Azure ou o PowerShell para consultar e conciliar o acesso ao Cofre de Chaves. Para visualizar o acesso no PowerShell, utilize o seguinte comando:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Propriedades.AccessPolicies

- [Registo de uma candidatura com Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Acesso seguro a um cofre de chaves](secure-your-key-vault.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Crie procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas que tenham acesso às suas instâncias Azure Key Vault. Utilize a Azure Security Center Identity and Access Management (atualmente em pré-visualização) para monitorizar o número de contas administrativas ativas.

- [Monitorizar identidade e acesso (pré-visualização)](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Utilize o Azure Ative Directory single sign-on (SSO)

**Orientação**: Utilize um principal de serviço Azure em conjunto com o AppId, TenantID e ClientSecret, para autenticar perfeitamente a sua aplicação e recuperar o token que será usado para aceder aos seus segredos do Cofre da Chave Azure.

- [Autenticação de serviço-a-serviço para Azure Key Vault usando .NET](/azure/key-vault/service-to-service-authentication)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilizar a autenticação de vários fatores para todos os acessos baseados no Diretório Ativo Azure

**Orientação**: Ativar a autenticação multifactora Azure Ative (Azure AD) e seguir recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure (atualmente em pré-visualização) para ajudar a proteger os recursos ativados pelo Centro de Eventos.

- [Planeie uma implementação de autenticação multi-factor Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorizar identidade e acesso (pré-visualização)](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Utilizar estações de trabalho seguras e geridas pelo Azure para tarefas administrativas

**Orientação**: Utilize uma estação de trabalho de acesso privilegiada (PAW) com autenticação multifactor AD Azure configurada para iniciar sessão e configurar recursos ativados do Key Vault.

- [Estações de Trabalho de Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Planejando uma implementação de autenticação multifactor Azure AD baseada na nuvem](../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize deteções de risco Azure AD para visualizar alertas e relatórios sobre o comportamento do utilizador de risco. Para obter registos adicionais, envie alertas de deteção de risco do Azure Security Center para o Azure Monitor e configuure alertas/notificações personalizados utilizando grupos de ação.

Ativar uma proteção avançada de ameaças (ATP) para o Azure Key Vault para gerar alertas para atividades suspeitas.

- [Implementar gestão de identidade privilegiada da Azure AD (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Confiem de proteção de ameaças avançadas para o Cofre da Chave Azure (pré-visualização)](/azure/security-center/advanced-threat-protection-key-vault)

- [Alertas para Azure Key Vault (Pré-visualização)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Deteções de risco Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Criar e gerir grupos de ações no portal do Azure](/azure/azure-monitor/platform/action-groups)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerir os recursos do Azure a partir de locais aprovados apenas

**Orientação**: Configure a condição de localização de uma política de acesso condicional e gerencie as suas localizações nomeadas. Com localizações nomeadas, pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões. Pode restringir o acesso a recursos sensíveis, como os seus segredos key vault, aos seus locais com nomes configurados.

- [Qual é a condição de localização no Azure Ative Directy (Azure AD) Acesso Condicional?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como o sistema central de autenticação e autorização para recursos Azure, como o Key Vault. Isto permite que o controlo de acesso baseado em funções (Azure RBAC) administrar recursos sensíveis.

- [Criar um novo inquilino em Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: Rever os registos do Azure Ative Directory (Azure AD) para ajudar a descobrir contas velhas com funções administrativas do Azure Key Vault. Além disso, utilize avaliações de acesso AZure AD para gerir eficientemente os membros do grupo, acesso a aplicações empresariais que possam ser usadas para aceder ao Azure Key Vault e atribuições de funções. O acesso ao utilizador deve ser revisto regularmente, como a cada 90 dias, para garantir que apenas os utilizadores certos têm acesso continuado.

- [Relatórios da AD AZure e documentação de monitorização](/azure/active-directory/reports-monitoring/)

- [O que são comentários de acesso a Ad AZure?](../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Ative as definições de diagnóstico para Azure Key Vault e Azure Ative Directory (Azure AD), enviando todos os registos para um espaço de trabalho Log Analytics. Configure os alertas desejados (tais como tentativas de acesso a segredos desativados) dentro do Log Analytics.

- [Integre registos AD AD com registos do Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrando da antiga solução Key Vault](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de inscrição na conta

**Orientação**: Utilize o Azure Ative Directory (Azure AD) da Proteção de Identidade e funcionalidades de deteção de riscos para configurar respostas automatizadas para detetar ações suspeitas relacionadas com os recursos protegidos do seu Cofre chave Azure. Deve permitir respostas automatizadas através do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Relatório de entradas de risco no portal AD AZure](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como: Configurar e permitir políticas de risco](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis sobre o Azure Key Vault habilitados recursos. 

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Pode garantir o acesso ao Cofre da Chave Azure utilizando pontos finais de serviço de rede virtuais configurados para restringir o acesso a sub-redes específicas.

Após as regras de firewall estarem em vigor, só pode efetuar operações de plano de dados Azure Key Vault quando o seu pedido tiver origem em sub-redes permitidas ou intervalos de endereços IP. Isto também se aplica ao acesso a Azure Key Vault no portal Azure. Embora possa navegar por um cofre chave a partir do portal Azure, poderá não conseguir listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Azure Key Vault Picker e outros serviços Azure. Pode ver listas de Cofres-Chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes de o fazer.

- [Configurar firewalls de cofre de chaves Azure e redes virtuais](/azure/key-vault/key-vault-network-security)

- [Pontos finais de serviço de rede virtual para o Azure Key Vault](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Todos os dados armazenados dentro do Cofre da Chave Azure são considerados sensíveis. Use os controlos de acesso do avião de acesso do Azure Key Vault para controlar o acesso aos segredos do Cofre da Chave Azure. Também pode utilizar a firewall incorporada do Key Vault para controlar o acesso na camada de rede. Para monitorizar o acesso ao Cofre da Chave Azure, ative as Definições de Diagnóstico do Cofre de Chaves e envie registos para uma conta de armazenamento Azure ou espaço de trabalho Log Analytics.

- [Acesso seguro a um cofre de chaves](secure-your-key-vault.md)

- [Configurar firewalls de cofre de chaves Azure e redes virtuais](/azure/key-vault/key-vault-network-security)

- [Registo do Azure Key Vault](/azure/key-vault/key-vault-logging)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Acesso seguro à gestão e plano de dados das suas instâncias Azure Key Vault.

- [Acesso seguro a um cofre de chaves](secure-your-key-vault.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize a solução Azure Key Vault Analytics no Azure Monitor para rever os registos de eventos de auditoria do Azure Key Vault.

- [Solução Azure Key Vault Analytics no Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Utilize as classificações de risco predefinidas (Pontuação Segura) fornecidas pelo Azure Security Center.

- [Melhore a sua pontuação segura no Centro de Segurança Azure](/azure/security-center/security-center-secure-score)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Gráfico de Recursos Azure para consultar e descubra todos os recursos (incluindo instâncias do Cofre chave Azure) dentro da sua subscrição. Certifique-se de que tem permissões (de leitura) adequadas no seu inquilino e é capaz de enumerar todas as subscrições da Azure, bem como recursos dentro das suas subscrições.

- [Execute a sua primeira consulta de Gráfico de Recursos usando o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Obtenha subscrições que a conta corrente pode aceder](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Controlo de acesso baseado em funções Azure (RBAC)](../../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos recursos do Azure Key Vault, dando metadados para organizá-los logicamente numa taxonomia.

- [Como criar e usar Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize etiquetas, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear instâncias e recursos relacionados do Azure Key Vault. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [Criar uma subscrição do Azure adicional](/azure/billing/billing-create-subscription)

- [Criar grupos de gestão para a organização e gestão de recursos](/azure/governance/management-groups/create)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize as políticas Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da(s) subscrição.

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md)

- [Quickstart: Executar a sua primeira consulta de gráfico de recurso usando O Explorador de Gráficos de Recursos Azure](../../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize as políticas Azure para impor restrições ao tipo de recursos que podem ser criados na subscrição de clientes, utilizando as seguintes definições de política incorporada:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Para obter mais informações, veja as seguintes referências:

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md)

- [Exemplos do Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resource Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management". Isto pode impedir a criação e alterações nos recursos dentro de um ambiente de alta segurança, como aqueles com configuração Key Vault.

- [Gerir o acesso à gestão do Azure com Acesso Condicional](../../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.KeyVault" para criar políticas personalizadas para auditar ou impor a configuração das suas instâncias do Azure Key Vault. Também pode utilizar definições de Política Azure incorporadas para a Azure Key Vault, tais como:

- Os objetos do Cofre chave devem ser recuperáveis

- Implementar definições de diagnóstico para cofre de chaves para registar espaço de trabalho

- Os registos de diagnóstico no Cofre de Chaves devem ser ativados

- O Key Vault deve usar um ponto final de serviço de rede virtual

- Implementar definições de diagnóstico para cofre de chave para o centro de eventos

- Utilize recomendações do Azure Security Center como base de configuração segura para as suas instâncias Azure Key Vault.

Para obter mais informações, veja as seguintes referências:

- [Como ver pseudónimos disponíveis da Política Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Utilize a Política Azure [negar] e [implementar se não existir] para impor definições seguras através dos recursos ativados pelo Cofre da Chave Azure. 

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

  
- [Compreender os efeitos da Política Azure](../../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para o seu Cofre de Chaves Azure, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação de Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.KeyVault" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus recursos protegidos pelo cofre da chave Azure.

- [Como remediar recomendações no Centro de Segurança Azure](../../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e proteger a gestão de segredos para as suas aplicações em nuvem. Certifique-se de que o cofre da chave Azure está ativado.

- [Integre com identidades geridas azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Criar um cofre de chaves](quick-create-portal.md)

- [Autenticar para o Key Vault](authentication.md)

- [Atribuir uma política de acesso ao Cofre de Chaves](assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: Utilize a Identidade de Serviço Gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

  

- [Como integrar-se com identidades geridas aZure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Como criar um Cofre-Chave](quick-create-portal.md)    

- [Como autenticar para o Cofre de Chaves](authentication.md)

- [Atribuir uma política de acesso ao Cofre de Chaves](assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.  
  
- [ Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Key Vault), no entanto, não funciona com conteúdo do cliente.

Pré-digitalize qualquer conteúdo que seja carregado ou enviado para recursos Azure não computados, como Azure Key Vault. A Microsoft não pode aceder aos seus dados nestes casos.

- [Compreenda o Antimalware da Microsoft para serviços em nuvem Azure e máquinas virtuais](../../security/fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir cópias de back-ups automáticas regulares

**Orientação**: Certifique-se de cópias de segurança automáticas regulares dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as cópias de segurança do Key Vault no suporte de backup Azure.

- [Como apoiar certificados de cofre de chaves](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Como apoiar chaves do cofre](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Como apoiar contas de armazenamento geridos do Cofre do Cofre](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Como apoiar segredos do Cofre de Chaves](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Como ativar o backup do Azure](/azure/backup)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: Execute cópias de segurança dos certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Opcionalmente, pode armazenar as cópias de segurança do Key Vault no suporte de backup Azure.

- [Como apoiar certificados de cofre de chaves](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Como apoiar chaves do cofre](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Como apoiar contas de armazenamento geridos do Cofre do Cofre](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Como apoiar segredos do Cofre de Chaves](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Como ativar o backup do Azure](/azure/backup)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: Execute periodicamente a restauração de dados dos seus certificados de cofre chave, chaves, contas de armazenamento gerido e segredos, com os seguintes comandos PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Para obter mais informações, veja as seguintes referências:

- [Como restaurar certificados de cofre chave](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Como restaurar chaves do cofre](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Como restaurar contas de armazenamento gerido de cofre chave](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar os segredos do cofre da chave](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Certifique-se de que a eliminação suave está ativada para o Cofre da Chave Azure. A eliminação suave permite a recuperação de cofres e objetos de abóbada apagados, tais como chaves, segredos e certificados. 

- [Como usar o soft-delete do Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: O [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa política padrão do Centro de Segurança e é a base para as [recomendações do Centro de Segurança.](/azure/security-center/security-center-recommendations) As definições da Política Azure relacionadas com este controlo são ativadas automaticamente pelo Centro de Segurança. Os alertas relacionados com este controlo podem requerer um plano [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições incorporadas da Azure Policy - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente. Estes processos devem ter como foco a proteção de sistemas sensíveis, como os que utilizam segredos do Key Vault.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../../security-center/security-center-planning-and-operations-guide.md)   

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança atribui uma gravidade a cada alerta para o ajudar a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na métrica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. Além disso, marque claramente as subscrições (para ex. produção, não-prod) e criar um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados sensíveis como os segredos do Azure Key Vault.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger as instâncias do Cofre da Chave Azure e recursos relacionados. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário.

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada.  Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de Exportação Contínua para ajudar a identificar riscos para os recursos ativados pelo Cofre da Chave Azure. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua.  Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel. 

 

- [Como configurar a exportação contínua](../../security-center/continuous-export.md) 

  

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho no Centro de Segurança Azure para ativar automaticamente respostas através de "Aplicações lógicas" em alertas de segurança e recomendações para proteger os recursos protegidos pelo cofre da chave Azure. 

 

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)

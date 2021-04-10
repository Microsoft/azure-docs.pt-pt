---
title: Linha de segurança Azure para Azure Machine Learning
description: A linha de base de segurança Azure Machine Learning fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f8bde45cfdf9cf1c9d50faee76161461d8b0aa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607834"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Linha de segurança Azure para Azure Machine Learning

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para o Microsoft Azure Machine Learning. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Azure Machine Learning. Foram excluídos **os controlos** não aplicáveis ao Azure Machine Learning.

 
Para ver como a Azure Machine Learning mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base de segurança Azure Machine Learning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: A Azure Machine Learning conta com outros serviços Azure para obter recursos compute. Os recursos de cálculo (metas de computação) são usados para treinar e implementar modelos. Pode criar estes alvos de computação numa rede virtual. Por exemplo, pode utilizar a azure virtual machine learning para treinar um modelo e, em seguida, implementar o modelo para o Serviço Azure Kubernetes (AKS). Você pode garantir seus ciclos de vida de aprendizagem automática isolando a Azure Machine Learning e inferências de trabalhos dentro de uma rede virtual Azure.

O Azure Firewall pode ser usado para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública.

- [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)

- [Use espaço de trabalho atrás do Azure Firewall para a aprendizagem de máquinas Azure](how-to-access-azureml-behind-firewall.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: A Azure Machine Learning conta com outros serviços Azure para obter recursos compute. Atribua grupos de segurança de rede às redes criadas como a sua implementação de Machine Learning. 

Ativar os registos de fluxo do grupo de segurança da rede e enviar os registos para uma conta de Armazenamento Azure para auditoria. Também pode enviar os registos de fluxo para um espaço de trabalho do Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações sobre os padrões de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: Pode permitir que o HTTPS garanta a comunicação com os serviços web implementados pela Azure Machine Learning. Os serviços web são implantados nos Serviços Azure Kubernetes (AKS) ou Azure Container Instances (ACI) e asseguram os dados enviados pelos clientes. Também pode utilizar IP privado com AKS para restringir a pontuação, de modo que apenas os clientes por trás de uma rede virtual podem aceder ao serviço web.

- [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)

- [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: Ative a Norma de Proteção DDoS nas redes virtuais associadas à sua instância de Machine Learning para se proteger contra ataques de negação de serviço distribuídos (DDoS). Utilize a deteção integrada de ameaças do Azure Security Center para detetar comunicações com endereços IP de Internet maliciosos ou não utilizados conhecidos.

Implemente o Azure Firewall em cada um dos limites da rede da organização com filtragem baseada em inteligência de ameaça, ativada e configurada para "alertar e negar" para tráfego de rede malicioso.

- [Como configurar a proteção DDoS](../ddos-protection/manage-ddos-protection.md)

- [Use espaço de trabalho atrás do Azure Firewall para a aprendizagem de máquinas Azure](how-to-access-azureml-behind-firewall.md)

- [Para mais informações sobre a deteção de ameaças do Centro de Segurança Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Para quaisquer VMs com a extensão adequada instalada nos seus serviços de Machine Learning Azure, pode permitir a captura de pacotes do Observador de Rede para investigar atividades anómalas. 

- [Como criar uma instância de Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: Implemente a solução de firewall à sua escolha em cada um dos limites de rede da sua organização para detetar e/ou bloquear tráfego malicioso.

Selecione uma oferta do Azure Marketplace que suporta a funcionalidade IDS/IPS com capacidades de inspeção de carga útil.  Quando a inspeção de carga útil não é um requisito, a inteligência de ameaça Azure Firewall pode ser usada. A filtragem baseada em ameaças de Azure Firewall é usada para alertar e/ou bloquear o tráfego de e/para e de endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft.

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com a Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa das regras de segurança da rede

**Orientação**: Para recursos que necessitem de acesso à sua conta Azure Machine Learning, utilize tags de serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (por exemplo, AzureMachineLearning) no campo de origem ou destino adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

O serviço Azure Machine Learning documenta uma lista de tags de serviço para os seus alvos de computação dentro de uma rede virtual que ajuda a minimizar a complexidade, podendo usá-la como diretrizes na gestão da sua rede.

- [Para mais informações sobre a utilização de tags de serviço](../virtual-network/service-tags-overview.md)

- [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para recursos de rede associados aos seus espaços de nome de Aprendizagem de Máquinas Azure com Azure Policy. Utilize pseudónimos da Azure Policy nos espaços de nome "Microsoft.MachineLearning" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus espaços de nome machine learning. 

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Utilize etiquetas para recursos de rede associados à sua implantação de Machine Learning Azure para organizá-las logicamente de acordo com uma taxonomia.

Para obter um recurso na sua rede virtual Azure Machine Learning que suporta o campo Descrição, utilize-a para documentar as regras que permitem o tráfego de/para uma rede.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o Registo de Atividades Azure para monitorizar as configurações de recursos de rede e detetar alterações para os recursos de rede relacionados com o Azure Machine Learning. Crie alertas dentro do Azure Monitor que irão desencadear quando ocorrerem alterações nos recursos críticos da rede.

- [Como visualizar e recuperar eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos via Azure Monitor para agregar dados de segurança gerados pela Azure Machine Learning. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo. Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um terceiro Security Incident and Event Management (SIEM).

- [Como configurar registos de diagnóstico para Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Permitir configurações de diagnóstico dos recursos Azure para acesso a registos de auditoria, segurança e diagnóstico. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis.

Também pode correlacionar os registos de funcionamento do serviço machine learning para fins de segurança e conformidade.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Compreenda o registo e diferentes tipos de registo em Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Ativar o registo em Azure Machine Learning](how-to-track-experiments.md)

- [Monitorização da aprendizagem da máquina de Azure](monitor-azure-machine-learning.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela recolha e monitorização. 

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para quaisquer recursos de computação que sejam propriedade da sua organização, utilize o Azure Security Center para monitorizar o sistema operativo. 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados aos seus casos de Aprendizagem automática Azure de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de registos](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Analise e monitorize registos para comportamento anómalo e reveja regularmente os resultados da sua Aprendizagem automática Azure. Utilize o Azure Monitor e um espaço de trabalho log Analytics para rever registos e realizar consultas nos dados de registo.

Em alternativa, pode ativar e a bordo dados para Azure Sentinel ou um SIEM de terceiros. 

- [Como realizar consultas para Azure Machine Learning em Log Analytics Workspaces](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Ativar o registo em Azure Machine Learning](how-to-track-experiments.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Começando com consultas log analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: No Monitor Azure, configurar registos relacionados com a aprendizagem automática Azure dentro do Registo de Atividades e configurações de diagnóstico de Machine Learning para enviar registos para um espaço de trabalho do Log Analytics para serem consultados ou para uma conta de armazenamento para armazenamento de arquivo a longo prazo. Use o espaço de trabalho do Log Analytics para criar alertas para a atividade anómala encontrada em registos de segurança e eventos.

Em alternativa, pode ativar e a bordo dados para a Azure Sentinel.

- [Para mais informações sobre alertas de Aprendizagem automática Azure](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Como alertar os dados do registo do espaço de trabalho do Log Analytics](/azure/azure-monitor/learn/tutorial-response)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela implementação do serviço de Aprendizagem automática Azure.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos compute que sejam propriedade da sua organização, permita a recolha de eventos antimalware para o Microsoft Antimalware para Azure Cloud Services e Virtual Machines.

- [Como configurar a extensão do Microsoft Antimalware para serviços na nuvem](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Compreenda o Antimalware da Microsoft](../security/fundamentals/antimalware.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS

**Orientação**: Não aplicável; A Azure Machine Learning não processa nem produz registos relacionados com DNS.

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos compute pertencentes à sua organização, utilize o Azure Security Center para permitir a monitorização de registo de eventos de segurança para máquinas virtuais Azure. O Azure Security Center fornece o agente Log Analytics em todos os VMs Azure suportados, e quaisquer novos que sejam criados se o fornecimento automático estiver ativado. Ou pode instalar o agente manualmente. O agente permite o evento de criação de processo 4688 e o campo de comando dentro do evento 4688. Os novos processos criados no VM são registados por registo de eventos e monitorizados pelos serviços de deteção do Security Center.

- [Data collection in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="identity-and-access-control"></a>Identidade e Controlo de Acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e Controlo de Acesso.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: Pode utilizar o separador Gestão de Identidade e Acesso para obter um recurso no portal Azure para configurar o controlo de acesso baseado em funções Azure (Azure RBAC) e manter o inventário nos recursos de Aprendizagem automática Azure. As funções são aplicadas aos utilizadores, grupos, diretores de serviços e identidades geridas no Ative Directory. Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos.

A Azure Machine Learning oferece papéis incorporados para cenários de gestão comuns em Azure Machine Learning. Um indivíduo que tenha um perfil no Azure Ative Directory (Azure AD) pode atribuir estas funções a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos de Aprendizagem automática Azure.

Também pode utilizar o módulo Azure AD PowerShell para realizar consultas de adhoc para descobrir contas que são membros de grupos administrativos.

- [Compreenda o controlo de acesso baseado em funções do Azure em Azure Machine Learning](how-to-assign-roles.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar palavras-passe padrão quando aplicável

**Orientação**: A gestão do acesso aos recursos de machine learning é controlada através do Azure Ative Directory (Azure AD). A Azure AD não tem o conceito de senhas padrão.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: A azure Machine Learning vem com três funções predefinidas quando um novo espaço de trabalho é criado, criando procedimentos operacionais padrão em torno da utilização de contas do proprietário.

Também pode permitir um acesso just-in-time às contas administrativas utilizando o Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada e Gestor de Recursos Azure.

- [Para aprender mais funções padrão de Aprendizagem automática](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Saiba mais sobre Gestão de Identidade Privilegiada](/azure/active-directory/privileged-identity-management/index)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Machine Learning é integrado com Azure Ative Directory (Azure AD), use Azure AD SSO em vez de configurar credenciais individuais autónomas por serviço. Use recomendações de identidade e acesso do Azure Security Center.

- [Compreender SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Utilize a autenticação multi-factor para todos os acessos baseados no Azure Ative Directory

**Orientação**: Ativar a autenticação multifactorA Azure Ative (Azure AD) e seguir as recomendações de identidade e acesso do Azure Security Center.

- [Como permitir a autenticação multifactor em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorizar a identidade e o acesso dentro do Centro de Segurança Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Utilize uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como permitir a autenticação multifactorA Azure Ative (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registar e alertar sobre atividades suspeitas a partir de contas administrativas

**Orientação**: Utilize relatórios de segurança e monitorização do Azure Ative Directory (Azure AD) para detetar quando ocorrem atividades suspeitas ou inseguras no ambiente. Utilize o Centro de Segurança Azure para monitorizar a atividade de identidade e acesso.

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerir os recursos do Azure apenas a partir de locais aprovados

**Orientação**: Utilize locais nomeados para a Azure Ative (Azure AD) para permitir o acesso apenas a agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar a Azure AD nomeou localizações](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Utilizar o Diretório Ativo Azure

**Orientação**: Utilize o Azure Ative Directory (Azure AD) como sistema central de autenticação e autorização. A Azure AD protege os dados utilizando uma encriptação forte para dados em repouso e em trânsito. A Azure AD também sai, hashes e armazena seguramente as credenciais dos utilizadores.
 
O acesso a funções pode ser alargado a vários níveis em Azure. Para machine learning, as funções podem ser geridas ao nível do espaço de trabalho, por exemplo, você tem acesso do proprietário a um espaço de trabalho pode não ter acesso do proprietário ao grupo de recursos que contém o espaço de trabalho. Isto fornece mais controlos de acesso granular a funções separadas dentro do mesmo grupo de recursos. 

- [Gerir o acesso a uma área de trabalho do Azure Machine Learning](how-to-assign-roles.md) 
 
- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Reveja e reconciliar regularmente o acesso dos utilizadores

**Orientação**: O Azure Ative Directory (Azure AD) fornece registos para ajudar a descobrir contas velhas. Além disso, utilize a identidade AZure E access reviews para gerir eficientemente os membros do grupo, o acesso a aplicações empresariais e atribuições de funções. O acesso ao utilizador pode ser revisto regularmente para garantir que apenas os utilizadores certos tenham acesso continuado.

Utilize a Azure AD Gestão de Identidade Privilegiada (PIM) para a geração de registos e alertas quando ocorrem atividades suspeitas ou inseguras no ambiente.

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Implementar gestão de identidade privilegiada da Azure AD (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorização tenta aceder a credenciais desativadas

**Orientação**: Tem acesso a atividade de login, auditoria e log de eventos de risco da Azure Ative, que lhe permitem integrar-se com qualquer ferramenta SIEM/monitor.

Pode agilizar este processo criando definições de diagnóstico para contas de utilizadores AZure E envio de registos de auditoria e registos de login para um espaço de trabalho do Log Analytics. Pode configurar os alertas desejados dentro do espaço de trabalho do Log Analytics.

- [Como integrar registos de atividades do Azure com o Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Alerta sobre desvio de comportamento de login de conta

**Orientação**: Utilize funcionalidades de Proteção de Identidade Azure Ative (Azure AD) para configurar respostas automatizadas para detetar ações suspeitas relacionadas com identidades do utilizador. Você também pode ingerir dados em Azure Sentinel para mais investigação.

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e permitir políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso aos dados relevantes dos clientes durante cenários de suporte

**Orientação**: Não aplicável; O serviço de Aprendizagem automática Azure não suporta o cofre do cliente.

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize etiquetas para ajudar a rastrear os recursos da Azure que armazenam ou processam informações sensíveis.
 
- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando assinaturas e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade aos dados. Pode restringir o nível de acesso aos seus recursos Azure que as suas aplicações e ambientes empresariais exigem. Você pode controlar o acesso aos recursos Azure via Azure RBAC.
 
- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

 
- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Utilize uma solução de terceiros do Azure Marketplace nos perímetros de rede para monitorizar a transferência não autorizada de informações sensíveis e bloquear tais transferências enquanto alerta os profissionais de segurança da informação. 

Quanto à plataforma subjacente, que é gerida pela Microsoft, a Microsoft encara todos os conteúdos dos clientes como confidenciais e protege-os da perda e exposição dos dados de cliente. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados. 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Os serviços web implementados através do Azure Machine Learning suportam apenas a versão 1.2 do TLS que impõe a encriptação de dados em trânsito.

- [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: As características de identificação, classificação e prevenção de perdas ainda não estão disponíveis para a Azure Machine Learning. Implementar uma solução de terceiros, se necessário para efeitos de conformidade.

Para a plataforma subjacente, que é gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes dentro do Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Utilizar o Azure RBAC para gerir o acesso aos recursos

**Orientação**: Azure Machine Learning suporta a utilização do Azure Ative Directory (Azure AD) para autorizar pedidos de machine learning. Com o Azure AD, pode utilizar o controlo de acesso baseado em funções (Azure RBAC) para conceder permissões a um principal de segurança, que pode ser um utilizador ou um diretor de serviço de aplicação.

- [Gerir o acesso a uma área de trabalho do Azure Machine Learning](how-to-assign-roles.md)

- [Use a Azure RBAC para a autorização de Kubernetes](../aks/manage-azure-rbac.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A azure Machine Learning armazena instantâneos, saídas e registos na conta de armazenamento Azure Blob que está ligada ao espaço de trabalho Azure Machine Learning e à sua subscrição. Todos os dados armazenados no armazenamento Azure Blob estão encriptados em repouso com as teclas geridas pela Microsoft. Também pode encriptar os dados armazenados no armazenamento do Azure Blob com as suas próprias chaves no serviço Machine Learning. 

- [Encriptação de dados de aprendizagem automática Azure em repouso](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

- [Como configurar chaves de encriptação geridas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Utilize o Monitor Azure com o registo de atividade azure para criar alertas para quando ocorrerem alterações em casos de produção de Azure Machine Learning e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos de Registo de Atividades Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="vulnerability-management"></a>Gestão de Vulnerabilidades

*Para mais informações, consulte o [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de digitalização de vulnerabilidades

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela gestão de vulnerabilidades do serviço Azure Machine Learning. 

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, siga as recomendações do Azure Security Center para a realização de avaliações de vulnerabilidade nas suas máquinas virtuais Azure, imagens de contentores e servidores SQL.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela gestão de patch do serviço Azure Machine Learning. 

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para quaisquer recursos compute que sejam propriedade da sua organização, utilize a Azure Automation Update Management para garantir que as atualizações de segurança mais recentes estão instaladas nos seus VMs Windows e Linux. Para os VMs do Windows, certifique-se de que o Windows Update foi ativado e definido para atualizar automaticamente.

- [Como configurar a Gestão de Atualização para máquinas virtuais em Azure](../automation/update-management/overview.md)

- [Compreenda as políticas de segurança do Azure monitorizadas pelo Security Center](../security-center/policy-reference.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar uma solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize uma solução de gestão de patch de terceiros. Os clientes que já utilizam o Gestor de Configuração no seu ambiente também podem utilizar o Editor de Atualizações do Sistema Center, permitindo-lhes publicar atualizações personalizadas no Serviço de Atualização do Servidor do Windows Server. Isto permite que a Gestão de Atualização remendo máquinas que usam o Gestor de Configuração como o seu repositório de atualização com software de terceiros.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar digitalizações de vulnerabilidades consecutivas

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, siga recomendações do Azure Security Center para a realização de avaliações de vulnerabilidade nas suas máquinas virtuais Azure, imagens de contentores e servidores SQL. A análise das exportações resulta em intervalos consistentes e compara os resultados com as análises anteriores para verificar se as vulnerabilidades foram remediadas. Ao utilizar recomendações de gestão de vulnerabilidades sugeridas pelo Azure Security Center, pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Não aplicável; esta orientação destina-se a recursos computacional.

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar e descobrir recursos (tais como computação, armazenamento, rede, portas e protocolos, etc.) nas suas subscrições. Certifique-se de permissões (leia) adequadas no seu inquilino e enumere todas as subscrições do Azure, bem como os recursos nas suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Azure Resource Graph Explorer, é altamente recomendado criar e utilizar os recursos do Azure Resource Manager.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como ver as suas subscrições Azure](/powershell/module/az.accounts/get-azsubscription)

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplicar etiquetas aos recursos do Azure, adicionando metadados para organizar logicamente de acordo com uma taxonomia.

- [Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Utilize a marcação, grupos de gestão e assinaturas separadas, se for caso disso, para organizar e rastrear ativos. Conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

- [ Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [ Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)
 
- [ Como criar e usar tags](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Crie um inventário de recursos Azure aprovados e software aprovado para recursos de computação de acordo com as suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

* Tipos de recursos não permitidos
* Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro das subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize o Azure Automation Change Tracking and Inventory para automatizar a recolha de informações de inventário dos seus Windows e Linux VMs. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para obter a data de instalação do software e outras informações, ative os diagnósticos ao nível do hóspede e direcione os Registos de Eventos do Windows para o espaço de trabalho do Log Analytics.

- [Como permitir a recolha de inventário da Azure Automation para um VM](../automation/automation-tutorial-installed-software.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize o Monitor de Integridade de Ficheiros (FIM) do Azure Security Center para identificar todo o software instalado em VMs. Outra opção que pode ser usada em vez de ou em conjunto com o FIM é o Azure Automation Change Tracking and Inventory para recolher o inventário dos seus VMs Linux e Windows. 

Pode implementar o seu próprio processo de remoção de software não autorizado. Também pode utilizar uma solução de terceiros para identificar software não aprovado.

Quando já não precisar dos recursos do Azure, remova-os.

- [Como utilizar o Monitor de Integridade do Ficheiro](../security-center/security-center-file-integrity-monitoring.md)

- [Compreender o rastreio e inventário de mudança de automatização da Azure](../automation/change-tracking/overview.md)

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

- [Grupo de recursos Azure e supressão de recursos](../azure-resource-manager/management/delete-resource-group.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos compute que sejam propriedade da sua organização, utilize os controlos de aplicação adaptativos do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de executar em Azure Virtual Machines.

- [Como utilizar controlos de aplicação adaptativos do Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados em subscrições de clientes utilizando as seguintes definições de política incorporada:

* Tipos de recursos não permitidos
* Tipos de recursos permitidos

Além disso, utilize o Gráfico de Recursos Azure para consultar e descobrir recursos nas subscrições.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para quaisquer recursos computacionais que sejam propriedade da sua organização, utilize os controlos de aplicação adaptativos do Azure Security Center para especificar quais os tipos de ficheiros a que uma regra pode ou não aplicar.

Implementar uma solução de terceiros se os controlos de aplicação adaptativos não cumprirem o requisito.

- [Como utilizar controlos de aplicação adaptativos do Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Azure Ative Directy (Azure AD) Acesso Condicional para limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para os recursos de cálculo que são propriedade da sua organização, dependendo do tipo de scripts, pode utilizar configurações específicas do sistema operativo ou recursos de terceiros para limitar a capacidade dos utilizadores de executar scripts em recursos de computação Azure. Também pode utilizar controlos de aplicação adaptativos do Azure Security Center para garantir que apenas o software autorizado executa e todo o software não autorizado está bloqueado de ser executado em Máquinas Virtuais Azure.

- [Como controlar a execução do script PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Como utilizar controlos de aplicação adaptativos do Azure Security Center](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Não aplicável; esta recomendação destina-se a aplicações web em execução no Azure App Service ou recursos de computação.

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Defina e implemente configurações de segurança padrão para o seu serviço de Aprendizagem automática Azure com a Política Azure. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.MachineLearning" para criar políticas personalizadas para auditar ou impor a configuração dos seus serviços de Aprendizagem automática Azure.

O Azure Resource Manager tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem os requisitos de segurança para a sua organização.

Também pode utilizar as recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

A Azure Machine Learning apoia plenamente os repositórios de Git para o trabalho de rastreio; pode clonar repositórios diretamente no seu sistema de ficheiros de espaço de trabalho partilhado, usar Git na sua estação de trabalho local e certificar-se de que as configurações seguras se aplicam aos recursos de código como parte do seu ambiente de Machine Learning.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pelas configurações seguras do sistema operativo do serviço Azure Machine Learning.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize recomendações do Azure Security Center para manter configurações de segurança em todos os recursos compute.  Além disso, pode utilizar imagens personalizadas do sistema operativo ou configuração do Estado da Automação Azure para estabelecer a configuração de segurança do sistema operativo exigido pela sua organização.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md)

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

- [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md)

- [Faça upload de um VHD e use-o para criar novos VMs windows em Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Criar um Linux VM a partir de um disco personalizado com o Azure CLI](../virtual-machines/linux/upload-vhd.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure. Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 
 
- [Compreender os efeitos da Política Azure](../governance/policy/concepts/effects.md)
 
- [Criar e gerir políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)
 
- [ Visão geral dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pelas configurações seguras do sistema operativo do serviço Azure Machine Learning.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus recursos de computação Azure.  Além disso, pode utilizar modelos de Gestor de Recursos Azure, imagens personalizadas do sistema operativo ou Configuração do Estado da Automação Azure para manter a configuração de segurança do sistema operativo exigido pela sua organização.   Os modelos de máquina virtual da Microsoft combinados com a Configuração do Estado da Automação Azure podem ajudar a cumprir e manter os requisitos de segurança. 

Note que a máquina virtual Azure Marketplace Imagens publicadas pela Microsoft são geridas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Como criar uma máquina virtual Azure a partir de um modelo ARM](../virtual-machines/windows/ps-template.md)

- [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md)

- [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

- [Informações sobre como descarregar o modelo VM](/previous-versions/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Se utilizar definições personalizadas da Política Azure para a sua Aprendizagem automática ou recursos relacionados, utilize o Azure Repos para armazenar e gerir o seu código de forma segura.

A Azure Machine Learning apoia plenamente os repositórios de Git para o trabalho de rastreio; pode clonar repositórios diretamente no seu sistema de ficheiros de espaço de trabalho partilhado, usar Git na sua estação de trabalho local e certificar-se de que as configurações seguras se aplicam aos recursos de código como parte do seu ambiente de Machine Learning.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação de Azure Repos](/azure/devops/repos/)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

**Orientação**: A Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos compute que sejam propriedade da sua organização, utilize o controlo de acesso baseado em funções (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às suas imagens personalizadas. Utilize uma Galeria de Imagens Partilhadas Azure, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviços ou grupos Azure Ative Directory (Azure AD) dentro da sua organização. Guarde as imagens do contentor no Registo do Contentor Azure e utilize o Azure RBAC para garantir que apenas os utilizadores autorizados tenham acesso.

- [Compreender Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Compreender O RBAC de Azure para o Registo de Contentores](../container-registry/container-registry-roles.md)

- [Como configurar o Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Visão geral da Galeria de Imagens Partilhada](../virtual-machines/shared-image-galleries.md)

- [Use a Azure RBAC para a autorização de Kubernetes](../aks/manage-azure-rbac.md)

**Responsabilidade**: Não aplicável

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Utilize pseudónimos da Política Azure no espaço de nomes "Microsoft.MachineLearning" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um oleoduto para gerir exceções políticas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela implementação segura da configuração do serviço Azure Machine Learning.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize a Configuração do Estado da Automação Azure para os nós de Configuração do Estado Desejado (DSC) em qualquer centro de dados em qualquer nuvem ou centro de dados no local. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado. 

- [Como ativar a configuração do Estado da Automação Azure](../automation/automation-dsc-onboarding.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus Recursos Azure. Além disso, utilize a Azure Policy para alertar e auditar as configurações de recursos do Azure.
 
 
 
- [ Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: Se o recurso computacional for propriedade da Microsoft, então a Microsoft é responsável pela monitorização automática de configuração segura do serviço Azure Machine Learning.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos de computação que sejam propriedade da sua organização, utilize aplicações Azure Security Center Compute &amp; Apps e siga as recomendações para VMs e servidores e contentores.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: Utilize identidades geridas em conjunto com o Azure Key Vault para simplificar a gestão secreta das suas aplicações em nuvem.

A Azure Machine Learning suporta a encriptação da loja de dados com chaves geridas pelo cliente, é necessário gerir a rotação da chave e revogar os requisitos de segurança e conformidade da organização. 

Use o Cofre de Chaves Azure para passar segredos para execuções remotas em vez de texto claro nos seus scripts de treino.

- [Chaves geridas pelo cliente Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Use segredos credenciais de autenticação em ações de formação de machine learning Azure](how-to-use-secrets-in-runs.md)

- [Como utilizar identidades geridas para recursos Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../key-vault/general/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

**Orientação**: A azure Machine Learning suporta papéis incorporados e a capacidade de criar funções personalizadas. Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). As identidades geridas permitem-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Gerir o acesso a uma área de trabalho do Azure Machine Learning](how-to-assign-roles.md)

- [Como configurar identidades geridas para os recursos da Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="malware-defense"></a>Defesa contra Software maligno

*Para mais informações, consulte a [Referência de Segurança Azure: Defesa contra malware.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: O anti-malware da Microsoft está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Machine Learning), no entanto, não funciona com conteúdo do cliente.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para obter recursos compute que sejam propriedade da sua organização, utilize o Microsoft Antimalware para a Azure monitorizar e defender continuamente os seus recursos. Para o Linux, utilize uma solução antimalware de terceiros. Além disso, utilize a deteção de ameaças do Azure Security Center para os serviços de dados para detetar malware enviado para contas de armazenamento.

- [Como configurar o Microsoft Antimalware para o Azure](../security/fundamentals/antimalware.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Ficheiros de pré-digitalização a serem enviados para recursos Azure não computados

**Orientação**: O Microsoft Anti-malware está ativado no anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Machine Learning), no entanto não funciona com conteúdo do cliente.

É da sua responsabilidade pré-digitalizar qualquer conteúdo que seja enviado para recursos Azure não computados. A Microsoft não pode aceder aos dados dos clientes e, portanto, não pode realizar análises anti-malware do conteúdo do cliente em seu nome.

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Assegurar que o software e assinaturas antimalware sejam atualizados

**Orientação**: O anti-malware da Microsoft está ativado e mantido para o anfitrião subjacente que suporta os serviços Azure (por exemplo, Azure Machine learning), no entanto, não funciona com conteúdo do cliente.

O Azure Machine Learning tem um suporte variado em diferentes recursos compute e até mesmo os seus próprios recursos de computação. Para quaisquer recursos compute que sejam propriedade da sua organização, siga as recomendações no Azure Security Center, Compute &amp; Apps para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. Para o Linux, utilize uma solução antimalware de terceiros.

- [Como implementar o Microsoft Antimalware para o Azure](../security/fundamentals/antimalware.md)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="data-recovery"></a>Recuperação de Dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Recuperação de Dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

**Orientação**: A gestão da recuperação de dados no serviço machine learning é através de gestões de dados em lojas de dados conectadas. Certifique-se de seguir as diretrizes de recuperação de dados para as lojas conectadas para fazer o back up dados por políticas de organização do cliente.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../backup/backup-azure-restore-files-from-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

**Orientação**: A cópia de segurança de dados no serviço Machine Learning é através de gestões de dados em lojas de dados conectadas. Ativar a cópia de segurança do Azure para os VMs e configurar os períodos de frequência e retenção pretendidos. Faça o reforço das chaves geridas pelo cliente no Cofre da Chave Azure.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves do Cofre chave em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

**Orientação**: A validação de backup de dados no serviço Machine Learning é através de gestões de dados em lojas de dados conectadas. Executar periodicamente a restauração de dados de conteúdos em Azure Backup. Certifique-se de que pode restaurar as chaves geridas pelo cliente.

- [Como recuperar ficheiros de uma cópia de segurança virtual da Azure](../backup/backup-azure-restore-files-from-vm.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

**Orientação**: Para a cópia de segurança no local, a encriptação em repouso é fornecida utilizando a palavra-passe que fornece quando se faz o backup para OZure. Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para proteger cópias de segurança e chaves geridas pelo cliente. 

Ativar a proteção de eliminação e purga suave no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa. Se o Azure Storage for utilizado para armazenar cópias de segurança, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas.

- [Compreender Azure RBAC](../role-based-access-control/overview.md)

- [Como ativar a eliminação recuperável e a proteção contra remoção no Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Excluir suave para armazenamento Azure Blob](../storage/blobs/soft-delete-blob-overview.md)

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

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque subscrições usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis. É sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde o incidente ocorreu.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular para ajudar a proteger os seus recursos Azure. Identifique pontos fracos e lacunas e, em seguida, reveja o seu plano de resposta conforme necessário.

- [Publicação do NIST--Guia para Testes, Formação e Programas de Exercício para Planos e Capacidades de TI](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas.

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação contínua para ajudar a identificar riscos para os recursos da Azure. A exportação contínua permite exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Azure Security Center para transmitir os alertas ao Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização de fluxos de trabalho Azure Security Center para acionar automaticamente respostas a alertas de segurança e recomendações para proteger os seus recursos Azure.

- [Como configurar a automatização do fluxo de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Partilhada

**Monitorização do Centro de Segurança Azure**: Nenhum

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](/azure/security/benchmarks/overview)
- Saiba mais sobre as [linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)

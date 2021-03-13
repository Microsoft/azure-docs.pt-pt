---
title: Linha de segurança Azure para AD de Rede Virtual
description: A linha de base de segurança NAT da rede virtual fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 501d5b17358501f17d17e8884ceec0bcbbb2ab1c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612410"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Linha de segurança Azure para AD de Rede Virtual

Esta linha de base de segurança aplica orientações da [versão 1.0](../security/benchmarks/overview-v1.md) do Benchmark de Segurança Azure para o Microsoft Virtual Network NAT. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Rede Virtual NAT. Foram excluídos **os controlos** não aplicáveis à Rede Virtual NAT.

 
Para ver como a Rede Virtual NAT mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de base de segurança da Rede Virtual NAT.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: A Rede Virtual NAT não suporta o controlo do tráfego de saída com grupos de segurança de rede (NSG).  O tráfego de entrada só é permitido em resposta a um fluxo de saída originado.

No entanto, os registos de fluxo do grupo de segurança da rede (NSG) podem ser utilizados com recursos de gateway NAT para monitorizar o tráfego originado pela saída.

Utilize o Centro de Segurança Azure e siga as recomendações de proteção da rede para ajudar a proteger os recursos da rede Azure. Ativar os registos de fluxo do grupo de segurança da rede e enviar os registos para uma conta de Armazenamento Azure para auditoria. Também pode enviar os registos de fluxo para um espaço de trabalho do Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações sobre os padrões de tráfego na sua nuvem Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede. 

- [Visão geral do NAT da rede virtual](./nat-overview.md)

- [Recurso do NAT gateway](./nat-gateway-resource.md)

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Permitir a captura de pacotes do Observador de Rede para investigar atividades anómalas. 

- [Como criar uma instância de Observador de Rede](../network-watcher/network-watcher-create.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Defina e implemente configurações de segurança padrão para sub-redes configuradas com recursos de gateway NAT utilizando definições e atribuições de Política Azure incorporadas ou personalizadas.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Amostras da Política Azure para networking](../governance/policy/samples/built-in-policies.md#network)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize o registo de atividade do Azure para monitorizar as configurações dos recursos e detetar alterações nos recursos de gateway DA NAT e recursos de rede virtual. Crie alertas no Azure Monitor para o notificar quando os recursos críticos forem alterados.

- [Como visualizar e recuperar eventos de log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e monitorização](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Ingerir registos relacionados com a Rede Virtual NAT via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Em alternativa, pode ativar e embarcar estes dados para a Azure Sentinel ou para um SIEM de terceiros.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como começar com o Azure Monitor e a integração do SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de gateway NAT, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md) 

Atualmente, a Rede Virtual NAT não produz quaisquer registos de diagnóstico adicionais que sejam configuráveis pelos clientes.

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Utilize o Centro de Segurança Azure com o log analytics espaço de trabalho para monitorizar e alertar sobre a atividade anómala encontrada em registos de segurança e eventos. Em vez disso, pode ativar e a bordo dados para Azure Sentinel.

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como gerir alertas no Centro de Segurança Azure](../security-center/security-center-managing-and-responding-alerts.md) 

- [Como alertar nos dados de registo de registo de registos de registos](../azure-monitor/alerts/tutorial-response.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de Recursos e Inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e Gestão de Ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Utilize o Azure Resource Graph para consultar/descobrir todos os recursos (tais como computação, armazenamento, rede, portas e protocolos e assim por diante) dentro das suas subscrições. 

Certifique-se de permissões (de leitura) adequadas no seu inquilino e enumera todas as subscrições e recursos da Azure dentro das suas subscrições.

Embora os recursos clássicos do Azure possam ser descobertos através do Resource Graph, é altamente recomendado criar e utilizar recursos baseados em Recursos Azure.

- [Consultas de gráfico de recursos Azure](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativos

**Orientação**: Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Aplicar etiquetas para gestão dos recursos da Azure.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gestão](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar Tags](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: Atualmente não disponível

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a Política Azure para impor restrições ao tipo de recursos que podem ser criados na sua(s) subscrição.
Utilize o Gráfico de Recursos Azure para consultar/descobrir recursos dentro da sua subscrição. Certifique-se de que todos os recursos Azure presentes no ambiente são aprovados.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 
- [Como criar consultas com Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Utilizar apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para restringir os serviços que pode prestação no seu ambiente.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração Segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração Segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Não aplicável; A Rede Virtual NAT não tem configurações de segurança.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: Utilize Azure DevOps para armazenar e gerir de forma segura o seu código como definições personalizadas de Política Azure, modelos de Gestor de Recursos Azure e scripts de configuração de estado desejados. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS. 

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow) 

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Não aplicável; A Rede Virtual NAT não tem configurações de segurança.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Deve ser criado um procedimento de resposta a incidentes para a oferta, a fim de garantir que possam ocorrer processos adequados de resposta a incidentes, de modo a que receba o nível adequado de priorização até à resolução dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: Deve ser criado um procedimento de pontuação e priorização de incidentes para a oferta que garanta a ocorrência de risco satisfatório ou pontuação de ameaças, de modo a que receba o nível adequado de priorização até à resolução dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Deve ser criado e testado um procedimento de resposta à segurança por incidentes para garantir uma adequada mitigação com o nível adequado de priorização até à resolução prevista dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: Deve ser criado e testado um procedimento de resposta à segurança por incidentes para garantir uma adequada mitigação com o nível adequado de priorização até à resolução prevista dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Deve ser criado e testado um procedimento de resposta à segurança por incidentes para garantir uma adequada mitigação com o nível adequado de priorização até à resolução prevista dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Deve ser criado e testado um procedimento de resposta à segurança por incidentes para garantir uma adequada mitigação com o nível adequado de priorização até à resolução prevista dos incidentes.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de Penetração e Exercícios de Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios da equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as regras de teste de penetração da Microsoft cloud para garantir que os seus testes de penetração não violam as políticas da Microsoft. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft. 

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
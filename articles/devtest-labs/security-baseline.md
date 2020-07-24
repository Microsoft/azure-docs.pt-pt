---
title: Linha de Base de Segurança Azure para Azure DevTest Labs
description: Linha de Base de Segurança Azure para Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098518"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Linha de Base de Segurança Azure para Azure DevTest Labs

A Linha de Base de Segurança Azure para a Azure DevTest Labs contém recomendações que o ajudarão a melhorar a postura de segurança da sua implantação.

A linha de base para este serviço é extraída da [versão 1.0 do Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview), que fornece recomendações sobre como pode garantir as suas soluções em nuvem no Azure.

Para obter mais informações, consulte [a visão geral da Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

*Para obter mais informações, consulte [Controlo de Segurança: Registo e monitorização](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas
**Orientação:** A Microsoft mantém fontes de tempo para os recursos da Azure. No entanto, pode gerir as definições de sincronização de tempo para os seus recursos de computação.

Consulte o seguinte artigo para saber sobre a sincronização de tempo configurada para os recursos computativos Azure: [Sincronização de tempo para VMs do Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividades Azure e enviar os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

Para obter mais informações, consulte [Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.](../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividades Azure e enviar os registos para um espaço de trabalho log Analytics, hub de eventos Azure ou conta de armazenamento Azure para o arquivo. Os registos de atividade fornecem informações sobre as operações que foram feitas nas suas instâncias Azure DevTest Labs ao nível do avião de gestão. Utilizando dados de Registo de Atividades Azure, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) feitas ao nível do plano de gestão para as suas instâncias DevTest Labs.

Para obter mais informações, consulte [Criar configurações de diagnóstico para enviar registos e métricas da plataforma para diferentes destinos.](../azure-monitor/platform/diagnostic-settings.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Recolher registos de segurança dos sistemas operativos
**Orientação:** As máquinas virtuais Azure DevTest Labs são criadas e propriedade do cliente. Então, é responsabilidade da organização monitorizá-lo. Pode utilizar o Azure Security Center para monitorizar o sistema operativo computacional. Os dados recolhidos pelo Security Center do sistema operativo incluem o tipo e versão de SISTEMA, OS (Registos de Eventos do Windows), processos de execução, nome da máquina, endereços IP e registados no utilizador. O Agente Descodudo de Log também recolhe ficheiros de despejo de acidentes.

Para obter mais informações, veja os seguintes artigos: 

- [Como recolher registos internos de anfitriões da Azure Virtual Machine com o Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança
***Orientação:** No Azure Monitor, detenva o período de retenção de registos para os espaços de trabalho do Log Analytics associados às instâncias da Azure DevTest Labs de acordo com os regulamentos de conformidade da sua organização.

Para obter mais informações, consulte o seguinte artigo: [Como definir parâmetros de retenção de registos](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos
**Orientação:** Ativar as definições de diagnóstico do Registo de Atividade do Azure e enviar os registos para um espaço de trabalho do Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados de registo de atividade que podem ter sido recolhidos para Azure DevTest Labs.

Para obter mais informações, veja os seguintes artigos:

- [Como ativar definições de diagnóstico para registo de atividades azure](../azure-monitor/platform/diagnostic-settings.md)
- [Como recolher e analisar registos de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Permitir alertas para atividades anómalas
**Orientação:** Utilize o espaço de trabalho Azure Log Analytics para monitorizar e alertar sobre atividades anómalas em registos de segurança e eventos relacionados com os seus Laboratórios Azure DevTest.

Para obter mais informações, consulte o seguinte artigo: [Como alertar nos dados de registo de registos de registos de registos](../azure-monitor/learn/tutorial-response.md)

**Monitorização do Centro de Segurança Azure:** Atualmente não disponível

**Responsabilidade:** Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware
**Orientação:** Não aplicável. A Azure DevTest Labs não processa ou produz registos relacionados com malware.

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: Ativar a sessão de consulta de DNS
**Orientação:** Não aplicável. A Azure DevTest Labs não processa nem produz registos relacionados com DNS.

**Monitorização do Centro de Segurança Azure:** Não aplicável

**Responsabilidade:** Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: Permitir a exploração de auditoria de linha de comando
**Orientação:** A Azure DevTest Labs cria máquinas Azure Compute que são propriedade e geridas pelo cliente. Utilize o Agente de Monitorização do Microsoft em todas as máquinas virtuais suportadas do Azure Windows para registar o evento de criação de processos e o campo CommandLine. Para máquinas virtuais suportadas Azure Linux, pode configurar manualmente o registo de consolas por nó e utilizar o Syslog para armazenar os dados. Além disso, utilize o espaço de trabalho do Log Analytics do Azure Monitor para rever registos e executar consultas em dados registados a partir de máquinas Virtuais Azure.

- [Data collection in Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier) (Recolha de dados no Centro de Segurança do Azure)
- [Como executar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog data sources in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md) (Origens de dados de Syslog no Azure Monitor)

**Monitorização do Centro de Segurança Azure:** Sim, é o seu

**Responsabilidade:** Cliente

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo:

- [Alertas de segurança para ambientes em Azure DevTest Labs](environment-security-alerts.md)
---
title: Azure Security Benchmark V2 - Registo e Deteção de Ameaças
description: Azure Security Benchmark V2 Logging e Deteção de Ameaças
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c5a855ffe06a17d8ec1bfe249dbe7bc41d1166af
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369077"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Controlo de Segurança V2: Registo e deteção de ameaças

A deteção de registos e deteção de ameaças abrange os controlos de deteção de ameaças no Azure e a capacitação, recolha e armazenamento de registos de auditoria para os serviços Azure. Isto inclui permitir processos de deteção, investigação e remediação com controlos para gerar alertas de alta qualidade com deteção de ameaças nativas nos serviços Azure; inclui também a recolha de registos com o Azure Monitor, a centralização da análise de segurança com o Azure Sentinel, a sincronização do tempo e a retenção de registos. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Certifique-se de que está a monitorizar diferentes tipos de ativos Azure para potenciais ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir falsos positivos para os analistas classificarem. Os alertas podem ser obtidos a partir de dados de registo, agentes ou outros dados.

Utilize a capacidade de deteção de ameaças incorporada do Azure Security Center, que se baseia na monitorização da telemetria de serviço Azure e na análise de registos de serviço. Os dados são recolhidos utilizando o agente Log Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos do sistema e copia os dados para o seu espaço de trabalho para análise. 

Além disso, use o Azure Sentinel para construir regras de análise, que caçam ameaças que correspondem a critérios específicos em todo o seu ambiente. As regras geram incidentes quando os critérios são combinados, para que possa investigar cada incidente. O Azure Sentinel também pode importar inteligência de ameaça de terceiros para aumentar a sua capacidade de deteção de ameaças. 

- [Proteção contra ameaças no Centro de Segurança do Azure](../../security-center/azure-defender.md)

- [Guia de referência do Centro de Segurança Azure alerta](../../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detetar ameaças](../../sentinel/tutorial-detect-threats-custom.md)

- [Inteligência de ameaça cibernética com Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

O Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização de análises mais sofisticados: 
-   Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

-   Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, como um número excessivo de tentativas de autenticação falhadas e contas precíídas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças do Azure Security Center também pode recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (como máquinas virtuais, contentores, serviço de aplicações), recursos de dados (como SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade permite-lhe ver anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria em Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Ativar o Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](../../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-3 | 9.3, 12.2, 12.5, 12.8 | AU-3, AU-6, AU-12, SI-4 |

Ativar e recolher registos de recursos do grupo de segurança da rede (NSG), registos de fluxo NSG, registos de firewall Azure e web application firewall (WAF) para análise de segurança para apoiar investigações de incidentes, caça de ameaças e geração de alerta de segurança. Pode enviar os registos de fluxo para um espaço de trabalho do Azure Monitor Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações. Certifique-se de que está a recolher registos de consultas DNS para ajudar a correlacionar outros dados de rede.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Registos e métricas do Azure Firewall](../../firewall/logs-and-metrics.md)

- [Como ativar e utilizar a Análise de Tráfego](../../network-watcher/traffic-analytics.md)

- [Monitorização com Monitor de Rede](../../network-watcher/network-watcher-monitoring-overview.md)

- [Soluções de monitorização da rede Azure no Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Recolha informações sobre a sua infraestrutura DNS com a solução DNS Analytics](../../azure-monitor/insights/dns-analytics.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-4 | 6.2, 6.3, 8.8 | AU-3, AU-12 |

Permitir a exploração madeireira dos recursos Azure para satisfazer os requisitos de conformidade, deteção de ameaças, caça e investigação de incidentes. 

Pode utilizar o Azure Security Center e a Azure Policy para permitir registos de recursos e registar a recolha de dados nos recursos da Azure para acesso a registos de auditoria, segurança e recursos. Os registos de atividade, que estão automaticamente disponíveis, incluem fonte de evento, data, utilizador, marcação de tempo, endereços de origem, endereços de destino e outros elementos úteis. 

- [Compreenda o registo e diferentes tipos de registo em Azure](../../azure-monitor/platform/platform-logs-overview.md)

- [Compreender a recolha de dados do Centro de Segurança Azure](../../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Segurança de infraestrutura e pontos finais 

- [Segurança de aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-5 | 6.5, 6.6 | AU-3, SI-4 |

Centralizar o armazenamento e análise de registo para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência. 

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Configure a sua retenção de registos de acordo com os seus requisitos de conformidade, regulação e negócio. 

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Alertas e recomendações do Centro de Segurança Azure](../../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: Utilizar fontes de sincronização de tempo aprovadas

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

A Microsoft mantém fontes de tempo para a maioria dos serviços Azure PaaS e SaaS. Para as suas máquinas virtuais, utilize o servidor NTP padrão da Microsoft para sincronização de tempo, a menos que tenha um requisito específico.  Se precisar de manter o seu próprio servidor de protocolo de tempo de rede (NTP), certifique-se de que protege a porta de serviço UDP 123.

Todos os registos gerados por recursos dentro do Azure fornecem carimbos de tempo com o fuso horário especificado por padrão.

- [Como configurar a sincronização do tempo para os recursos computativos do Azure Windows](../../virtual-machines/windows/time-sync.md)

- [Como configurar a sincronização temporal para os recursos computativos Azure Linux](../../virtual-machines/linux/time-sync.md)

- [Como desativar a UDP de entrada para serviços Azure](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Responsabilidade**: Partilhada

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Política e normas](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)
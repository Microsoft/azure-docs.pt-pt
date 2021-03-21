---
title: Azure Security Benchmark V2 - Resposta a incidentes
description: Resposta de incidente de referência de segurança Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b9295482c2464eb80bc49fa707744f49a2fbebfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035382"
---
# <a name="security-control-v2-incident-response"></a>Controlo de Segurança V2: Resposta a incidentes

Incident Response abrange controlos no ciclo de vida de resposta a incidentes - preparação, deteção e análise, contenção e atividades pós-incidente. Isto inclui a utilização de serviços Azure, como o Azure Security Center e o Sentinel, para automatizar o processo de resposta a incidentes.

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa Azure Security Benchmark Regulatory Compliance built-in: Incident Response](../../governance/policy/samples/azure-security-benchmark.md#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-2 | 19.5 | IR-4, IR-5, IR-6, IR-8 |

Confiem informações de contacto com incidentes de segurança no Centro de Segurança Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-3 | 19.6 | IR-4, IR-5 |

Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos. 

Os alertas de alta qualidade podem ser criados a partir da experiência com incidentes anteriores, de origens validadas pela comunidade e de ferramentas desenhadas para gerar e limpar alertas ao combinar e correlacionar diversas origens de sinais. 

O Centro de Segurança do Azure proporciona alertas de alta qualidade em muitos ativos do Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Certifique-se de que os analistas podem consultar e usar diversas fontes de dados à medida que investigam potenciais incidentes, para construir uma visão completa do que aconteceu. Para monitorizar as atividades de um potencial atacante em toda a rede do ataque e evitar ângulos mortos, devem ser recolhidos diversos registos. Também deve assegurar que são apreendidas informações e aprendizagens para outros analistas e para informação histórica futura.

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Utilize a capacidade de captura da memória nativa do sistema operativo para criar um instantâneo da memória do sistema operativo.

    - Utilize a funcionalidade de instantâneos dos serviços do Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e sensibilidade ao ativo. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança tem na descoberta ou na análise utilizada para emitir o alerta, bem como no nível de confiança em que havia uma intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)
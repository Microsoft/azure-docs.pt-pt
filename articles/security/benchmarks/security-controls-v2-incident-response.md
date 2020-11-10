---
title: Azure Security Benchmark V2 - Resposta a incidentes
description: Resposta de incidente de referência de segurança Azure V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ba7391b3b524f7532101083a444aab6059e8a10
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408760"
---
# <a name="security-control-v2-incident-response"></a>Controlo de Segurança V2: Resposta a incidentes

Incident Response abrange controlos no ciclo de vida de resposta a incidentes - preparação, deteção e análise, contenção e atividades pós-incidente. Isto inclui a utilização de serviços Azure, como o Azure Security Center e o Sentinel, para automatizar o processo de resposta a incidentes.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão.

- [Implementar segurança em todo o ambiente da empresa](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-2 | 19.5 | IR-4, IR-5, IR-6, IR-8 |

Confiem informações de contacto com incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-3 | 19.6 | IR-4, IR-5 |

Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos. 

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas ao Azure Sentinel. O Azure Sentinel permite criar regras avançadas de alerta para gerar incidentes automaticamente para uma investigação. 

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Alertas e recomendações de exportação, manualmente ou de forma contínua e contínua.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Certifique-se de que os analistas podem consultar e usar diversas fontes de dados à medida que investigam potenciais incidentes, para construir uma visão completa do que aconteceu. Devem ser recolhidos registos diversos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar pontos cegos.  Deve também garantir que os insights e aprendizagens são capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes centralizadas de registo que já estão a ser recolhidas dos serviços de âmbito e dos sistemas de funcionamento, mas também podem incluir:

- Dados da rede – utilize os registos de fluxo dos grupos de segurança da rede, o Azure Network Watcher e o Azure Monitor para capturar registos de fluxo de rede e outras informações analíticas. 

- Instantâneos dos sistemas de funcionamento: 

    - Utilize a capacidade de instantâneo da máquina virtual Azure para criar uma imagem do disco do sistema de funcionamento. 

    - Utilize a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas de execução.

O Azure Sentinel fornece uma análise extensiva de dados em praticamente qualquer fonte de registo e um portal de gestão de casos para gerir todo o ciclo de vida dos incidentes. Informações de inteligência durante uma investigação podem ser associadas a um incidente para rastrear e reportar propósitos. 

- [Snapshot um disco de máquina do Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot um disco de máquina Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Suporta informações de diagnóstico e recolha de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-5 | 19.8 | CA-2, IR-4 |

Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e sensibilidade ao ativo. 

O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque recursos usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais demoram mais tempo a ser executadas, abrandando cada incidente e reduzindo quantos incidentes um analista pode lidar. As tarefas manuais também aumentam a fadiga dos analistas, o que aumenta o risco de erro humano que causa atrasos, e degrada a capacidade dos analistas de se concentrarem eficazmente em tarefas complexas. Utilize funcionalidades de automatização de fluxos de trabalho no Azure Security Center e no Azure Sentinel para desencadear automaticamente ações ou executar um livro de jogadas para responder aos alertas de segurança que chegam. O livro de jogadas toma medidas, tais como o envio de notificações, a desativação de contas e a isolação de redes problemáticas. 

- [Configurar automatização de fluxos de trabalho no Centro de Segurança](../../security-center/workflow-automation.md)

- [Criar respostas automáticas de ameaças no Centro de Segurança Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Operações de segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)
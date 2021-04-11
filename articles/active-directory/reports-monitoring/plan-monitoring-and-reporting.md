---
title: Relatórios do plano & monitorização da implementação - Azure AD
description: Descreve como planear e executar a implementação de relatórios e monitorização.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37c672f8784052424e058837d31bc1d22fcfc57
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552619"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planear um Azure Ative Directory reportando e monitorizando a implementação

A sua solução de reporte e monitorização Azure Ative (Azure AD) depende dos seus requisitos legais, de segurança e operacionais e do ambiente e processos existentes. Este artigo apresenta as várias opções de design e guia-o para a estratégia de implementação certa.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Benefícios da comunicação e monitorização da AZure AD

O relatório AD AD da Azure fornece uma visão abrangente e registos da atividade da AD Azure no seu ambiente, incluindo sinal em eventos, eventos de auditoria e alterações no seu diretório.

Os dados fornecidos permite-lhe:

* determinar como as suas aplicações e serviços são usados.

* detetar riscos potenciais que afetam a saúde do seu ambiente.

* problemas de resolução de problemas que impedem os seus utilizadores de fazer o seu trabalho.

* obtenha insights vendo eventos de auditoria de alterações ao seu diretório AZure AD.

> [!IMPORTANT]
> A monitorização AD AZure permite-lhe direcionar os seus registos gerados pelo Azure AD reportando para diferentes sistemas-alvo. Em seguida, pode retê-los para utilização a longo prazo ou integrá-los com ferramentas de Gestão de Informações e Eventos de Segurança (SIEM) de terceiros para obter informações sobre o seu ambiente.

Com a monitorização AD AZure, pode encaminhar os registos para:

* uma conta de armazenamento Azure para fins de arquivo.
* Registos Azure Monitor, anteriormente conhecidos como espaço de trabalho Azure Log Analytics, onde pode analisar os dados, criar dashboards e alertar para eventos específicos.
* um centro de eventos Azure onde pode integrar-se com as suas ferramentas SIEM existentes, como Splunk, Sumologic ou QRadar.

> [!NOTE]
> Recentemente começamos a usar os registos do Azure Monitor em vez de Log Analytics. Os dados de registo ainda são armazenados num espaço de trabalho do Log Analytics e ainda são recolhidos e analisados pelo mesmo serviço Log Analytics. Estamos a atualizar a terminologia para melhor refletir o papel dos [registos no Azure Monitor.](../../azure-monitor/data-platform.md) Consulte [as alterações da terminologia do Azure Monitor](../../azure-monitor/terminology.md) para mais detalhes.

[Saiba mais sobre as políticas de retenção de relatórios.](./reference-reports-data-retention.md)

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenciamento e pré-requisitos para a informação e monitorização da Azure AD

Você precisará de uma licença AD AD AD para aceder ao sinal AZURE AD em registos.

Para informações detalhadas sobre funcionalidades e licenciamentos no [guia de preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para implementar a monitorização e reportagem da Azure, precisará de um utilizador que seja administrador global ou administrador de segurança para o inquilino da AD Azure.

Dependendo do destino final dos seus dados de registo, necessitará de um dos seguintes dados:

* Uma conta de armazenamento do Azure, para a qual tenha permissões ListKeys. Recomendamos que utilize uma conta de armazenamento para fins gerais e não uma conta de armazenamento de Blobs. Para obter informações sobre os preços de armazenamento, veja a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Um espaço de nome azure Event Hubs para integrar com soluções SIEM de terceiros.

* Um espaço de trabalho Azure Log Analytics para enviar registos para registos do Monitor Azure.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planear um projeto de implantação e monitorização do Azure

Neste projeto, você definirá o público que irá consumir e monitorizar relatórios, e definir a sua arquitetura de monitorização AZure AD.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas.](../fundamentals/active-directory-deployment-plans.md) Além disso, certifique-se de que as funções das partes interessadas no projeto são bem compreendidas documentando as partes interessadas e os seus contributos e contas do projeto.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando vai mudar, e como ganhar apoio se eles experimentarem problemas.

### <a name="document-your-current-infrastructure-and-policies"></a>Documente as suas atuais infraestruturas e políticas

A sua infraestrutura e políticas atuais irão impulsionar o seu design de relatórios e monitorização. Certifique-se de que sabe

* O que, se houver, ferramentas SIEM que está a usar.

* A sua infraestrutura Azure, incluindo as contas de armazenamento existentes e a monitorização que está a ser utilizada.

* As suas políticas de retenção organizacional para registos, incluindo quaisquer quadros de conformidade aplicáveis necessários. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planear uma implantação de relatórios e monitorização da AZure AD

O reporte e monitorização são usados para satisfazer os requisitos do seu negócio, obter insights sobre padrões de uso e aumentar a postura de segurança da sua organização.

### <a name="business-use-cases"></a>Casos de uso de negócios

* Necessário para uma solução para atender às necessidades do negócio
* É bom ter de atender às necessidades do negócio
* Não aplicável

|Área |Descrição |
|-|-|
|Retenção| **Retenção de registos superiores a 30 dias**. Devido a requisitos legais ou empresariais, é necessário armazenar registos de auditoria e assinar em registos de Azure AD por mais de 30 dias. |
|Análise| **Os registos têm de ser pesmáveis.** Os registos armazenados devem ser pescêjáveis com ferramentas analíticas. |
| Informações Operacionais| **Insights para várias equipas.** A necessidade de dar acesso a diferentes utilizadores para obter insights operacionais, tais como utilização de aplicações, assinar erros, uso de self-service, tendências, etc. |
| Insights de Segurança| **Insights para várias equipas.** A necessidade de dar acesso a diferentes utilizadores para obter insights operacionais, tais como utilização de aplicações, assinar erros, auto-atendimento, tendências, etc. |
| Integração nos sistemas SIEM      | **Integração SIEM.** A necessidade de integrar e transmitir o sinal AZure AD em registos e registos de auditoria aos sistemas SIEM existentes. |

### <a name="choose-a-monitoring-solution-architecture"></a>Escolha uma arquitetura de solução de monitorização

Com a monitorização AZure AD, pode encaminhar os seus registos de atividade azure AD para um sistema que melhor satisfaça as suas necessidades de negócio. Em seguida, pode retê-los para relatórios e análises a longo prazo para obter insights sobre o seu ambiente, e integrá-lo com ferramentas SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Gráfico de fluxo de decisão![Uma imagem mostrando o que é descrito em secções subsequentes](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Registos de arquivo numa conta de armazenamento

Ao encaminhar os registos para uma conta de armazenamento Azure, pode mantê-los por mais tempo do que o período de retenção predefinido descrito nas nossas [políticas de retenção.](./reference-reports-data-retention.md) Utilize este método se precisar de arquivar os seus registos, mas não precisa de os integrar com um sistema SIEM, e não precisa de consultas e análises em curso. Ainda pode fazer pesquisas a pedido.

Saiba como [encaminhar dados para a sua conta de armazenamento](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Enviar registos para registos do Monitor Azure

[Os registos do Azure Monitor](../../azure-monitor/logs/log-query-overview.md) consolidam dados de monitorização de diferentes fontes. Também fornece um motor de linguagem e análise de consulta que lhe dá informações sobre o funcionamento das suas aplicações e a utilização de recursos. Ao enviar registos de atividade azure AD para registos do Azure Monitor, pode rapidamente recuperar, monitorizar e alertar sobre os dados recolhidos. Utilize este método quando não tiver uma solução SIEM existente para a qual pretende enviar os seus dados diretamente, mas queira consultas e análises. Uma vez que os seus dados estão nos registos do Azure Monitor, pode enviá-lo para o centro de eventos e daí para um SIEM, se assim o desejar.

Saiba como [enviar dados para os registos do Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md).

Também pode instalar as vistas pré-construídas para registos de atividades Azure AD para monitorizar cenários comuns envolvendo eventos de login e auditoria.

Saiba como [instalar e utilizar vistas de análise de registos para registos de atividades Azure AD](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Faça logins de streaming para o seu centro de eventos Azure

Os registos de encaminhamento para um centro de eventos Azure permitem a integração com ferramentas SIEM de terceiros. Esta integração permite-lhe combinar dados de registo de atividade Azure AD com outros dados geridos pelo seu SIEM, para fornecer informações mais ricas sobre o seu ambiente. 

Saiba como [transmitir registos a um hub de eventos](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Plan Operations and Security for Azure AD reporting and monitoring

As partes interessadas precisam de aceder aos registos AZure AD para obter informações operacionais. Os utilizadores prováveis incluem membros da equipa de segurança, auditores internos ou externos, e a equipa de operações de gestão de identidade e acesso.

As funções AD do Azure permitem-lhe delegar a capacidade de configurar e visualizar relatórios AD Azure com base no seu papel. Identifique quem na sua organização precisa de permissão para ler relatórios AZure AD e qual o papel apropriado para eles. 

As seguintes funções podem ler relatórios Azure AD:

* Admin Global

* Administrador de Segurança

* Leitor de Segurança

* Leitor de Relatórios

Saiba mais sobre [as funções administrativas do Azure Ad](../roles/permissions-reference.md).

*Aplique sempre o conceito de privilégios mínimos para reduzir o risco de um compromisso de conta.* Considere implementar a [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) para garantir ainda mais a sua organização.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Implementar relatórios e monitorização da AD Azure

Dependendo das decisões que tomou anteriormente usando a orientação de design acima, esta secção irá guiá-lo para a documentação sobre as diferentes opções de implementação.

### <a name="consume-and-archive-azure-ad-logs"></a>Consumir e arquivar registos Azure AD

[Encontrar relatórios de atividade no portal do Azure](./howto-find-activity-reports.md)

[Arquivo Azure AD registra-se em uma conta de armazenamento Azure](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>Implementar monitorização e análise

[Enviar registos para O Monitor Azure](./howto-integrate-activity-logs-with-log-analytics.md)

[Instale e utilize as vistas de análise de registo para o Azure Ative Directory](./howto-install-use-log-analytics-views.md)

[Analise os registos de atividades da AZure com registos do Monitor Azure](./howto-analyze-activity-logs-log-analytics.md)

* [Interpretar esquema de registos de auditoria no Azure Monitor](./reference-azure-monitor-audit-log-schema.md)

* [Interpretar sinal no esquema de registos no Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Stream Azure AD registra para um centro de eventos Azure](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](./howto-integrate-activity-logs-with-splunk.md) (Utilizar o Azure Monitor para integrar registos do Azure AD no Splunk)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](./howto-integrate-activity-logs-with-sumologic.md) (Utilizar o Azure Monitor para integrar registos do Azure AD no SumoLogic)

 

 

## <a name="next-steps"></a>Passos seguintes

Considere implementar a [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) 

Considere implementar o [controlo de acesso baseado em funções do Azure (Azure RBAC)](../../role-based-access-control/overview.md)

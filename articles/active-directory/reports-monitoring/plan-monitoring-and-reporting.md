---
title: Relatórios de planos & monitorização - Azure AD
description: Descreve como planear e executar a implação de relatórios e monitorização.
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
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232112"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planeie um relatório e monitorização de diretórios ativos do Azure

A sua solução de reporte e monitorização do Seu Diretório Ativo Azure (Azure AD) depende dos seus requisitos legais, de segurança e operacionais e do seu ambiente e processos existentes. Este artigo apresenta as várias opções de design e guia-o para a estratégia de implementação certa.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Benefícios do relatório e monitorização da AD Azure

O relatório Azure AD fornece uma visão abrangente e registos da atividade da AD Azure no seu ambiente, incluindo assinar em eventos, eventos de auditoria e alterações no seu diretório.

Os dados fornecidos permite-lhe:

* determine como as suas aplicações e serviços são usados.

* detetar riscos potenciais que afetem a saúde do seu ambiente.

* problemas de resolução de problemas que impedem os seus utilizadores de fazer o seu trabalho.

* obtenha insights vendo eventos de auditoria de alterações ao seu diretório Azure AD.

> [!IMPORTANT]
> A monitorização da AD Azure permite-lhe encaminhar os seus registos gerados pela Azure AD reportando para diferentes sistemas-alvo. Em seguida, pode retê-los para utilização a longo prazo ou integrá-los com ferramentas de Gestão de Informações e Eventos de Segurança (SIEM) de terceiros para obter informações sobre o seu ambiente.

Com a monitorização da AD Azure, pode encaminhar registos para:

* uma conta de armazenamento Azure para fins de arquivo.
* Os registos do Azure Monitor, anteriormente conhecido como Espaço de trabalho Azure Log Analytics, onde pode analisar os dados, criar dashboards e alertar sobre eventos específicos.
* um hub de eventos Azure onde pode integrar-se com as suas ferramentas SIEM existentes, tais como Splunk, Sumologic ou QRadar.

> [!NOTE]
Recentemente começamos a usar os registos do Monitor Azure em vez de Log Analytics. Os dados de registo ainda são armazenados num espaço de trabalho do Log Analytics e ainda são recolhidos e analisados pelo mesmo serviço Log Analytics. Estamos a atualizar a terminologia para melhor refletir o papel dos [registos no Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection) Consulte [as alterações da terminologia do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) para obter detalhes.

[Saiba mais sobre as políticas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)de retenção de relatórios.

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenciamento e pré-requisitos para relatórios e monitorização da AD Azure

Você precisará de uma licença premium Azure AD para aceder ao sinal de anúncio azure em registos.

Para informações detalhadas sobre funcionalidades e licenciamentos no guia de preços do [Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

Para implementar a monitorização e reportagem da Azure AD, precisará de um utilizador que seja administrador global ou administrador de segurança do inquilino da AD Azure.

Dependendo do destino final dos seus dados de registo, necessitará de um dos seguintes dados:

* Uma conta de armazenamento do Azure, para a qual tenha permissões ListKeys. Recomendamos que utilize uma conta de armazenamento para fins gerais e não uma conta de armazenamento de Blobs. Para obter informações sobre os preços de armazenamento, veja a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Um espaço de nome Azure Event Hubs para integrar com soluções SIEM de terceiros.

* Um espaço de trabalho Azure Log Analytics para enviar registos para registos do Monitor Azure.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planeie um projeto de implantação de relatórios e monitorização do Azure

Neste projeto, irá definir o público que irá consumir e monitorizar relatórios, e definir a sua arquitetura de monitorização de Anúncios Azure.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas.](https://aka.ms/deploymentplans) Certifique-se também de que as funções das partes interessadas no projeto são bem compreendidas documentando as partes interessadas e as suas entradas e responsabilidades no projeto.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando irá mudar, e como obter suporte se experimentarem problemas.

### <a name="document-your-current-infrastructure-and-policies"></a>Documente as suas infraestruturas e políticas atuais

As suas atuais infraestruturas e políticas conduzirão o seu design de relatórios e monitorização. Certifique-se de que sabe

* O que, se houver, ferramentas SIEM que estás a usar.

* A sua infraestrutura Azure, incluindo as contas de armazenamento existentes e a monitorização em uso.

* As suas políticas de retenção organizacional para registos, incluindo quaisquer quadros de conformidade aplicáveis necessários. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planeie uma implantação de relatórios e monitorização da AD Azure

O reporte e monitorização são usados para satisfazer os seus requisitos de negócio, obter insights sobre padrões de uso e não aumentar a postura de segurança das suas organizações.

### <a name="business-use-cases"></a>Casos de uso de negócios

* Necessária para solução para atender às necessidades empresariais
* É bom ter de satisfazer as necessidades do negócio.
* Não aplicável

|Área |Descrição |
|-|-|
|Retenção| **Retenção de registos superiores a 30 dias.** Devido a requisitos legais ou empresariais, é necessário armazenar registos de auditoria e assinar em registos de AD Azure por mais de 30 dias. |
|Análise| **Os registos têm de ser pesquisáveis.** Os registos armazenados precisam de ser pesquisáveis com ferramentas analíticas. |
| Informações Operacionais| **Insights para várias equipas.** A necessidade de dar acesso a diferentes utilizadores para obter insights operacionais, tais como o uso da aplicação, assinar erros, uso de self-service, tendências, etc. |
| Insights de Segurança| **Insights para várias equipas.** A necessidade de dar acesso a diferentes utilizadores para obter insights operacionais, tais como o uso da aplicação, assinar erros, uso de self-service, tendências, etc. |
| Integração nos sistemas SIEM      | **Integração SIEM.** A necessidade de integrar e transmitir o sinal de AD Azure em registos e registos de auditoria aos sistemas SIEM existentes. |

### <a name="choose-a-monitoring-solution-architecture"></a>Escolha uma arquitetura de solução de monitorização

Com a monitorização da AD Azure, pode encaminhar os seus registos de atividade do Azure AD para um sistema que melhor responda às necessidades do seu negócio. Em seguida, pode retê-los para relatórios e análises a longo prazo para obter insights sobre o seu ambiente, e integrá-lo com ferramentas SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Gráfico de fluxo de decisão![Uma imagem mostrando o que é descrito em secções subsequentes](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Arquivo de registos numa conta de armazenamento

Ao encaminhar os registos para uma conta de armazenamento Azure, pode mantê-los por mais tempo do que o período de retenção padrão delineado nas nossas políticas de [retenção](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Utilize este método se precisar de arquivar os seus registos, mas não precisa integrá-los com um sistema SIEM, e não precisa de consultas e análises contínuas. Ainda pode fazer buscas a pedido.

Saiba como [encaminhar dados para a sua conta de armazenamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Envie registos para registos do Monitor Azure

Os [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consolidam os dados de monitorização de diferentes fontes. Também fornece um motor de linguagem e análise de consulta que lhe dá informações sobre o funcionamento das suas aplicações e utilização de recursos. Ao enviar registos de atividade da Azure AD para registos do Monitor Azure, pode recuperar, monitorizar e alertar rapidamente sobre os dados recolhidos. Utilize este método quando não tiver uma solução SIEM existente para a qual pretende enviar os seus dados diretamente, mas quer consultas e análises. Uma vez que os seus dados estão nos registos do Monitor Azure, pode enviá-los para o centro de eventos e daí para um SIEM, se quiser.

Saiba como [enviar dados para os registos do Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Também pode instalar as vistas pré-construídas para registos de atividade da Azure AD para monitorizar cenários comuns envolvendo eventos de inscrição e auditoria.

Aprenda a instalar e utilizar vistas de análise de [registos para registos de atividade da Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Registos de streaming para o seu centro de eventos Azure

Os registos de encaminhamento para um hub de eventos Azure permitem a integração com ferramentas SIEM de terceiros. Esta integração permite combinar dados de registo de atividade do Azure AD com outros dados geridos pelo seu SIEM, para fornecer informações mais ricas sobre o seu ambiente. 

Saiba como [transmitir registos a um hub de eventos](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Plano de Operações e Segurança para relatórios e monitorização da AD Azure

As partes interessadas precisam de aceder aos registos da AD Azure para obter informações operacionais. Os utilizadores prováveis incluem membros da equipa de segurança, auditores internos ou externos, e a equipa de operações de gestão de identidade e acesso.

As funções Azure AD permitem-lhe delegar a capacidade de configurar e ver relatórios de AD Azure com base no seu papel. Identifique quem na sua organização precisa de autorização para ler relatórios da AD Azure e qual o papel apropriado para eles. 

As seguintes funções podem ler relatórios da AD Azure:

* Admin Global

* Administrador de Segurança

* Leitor de Segurança

* Leitor de Relatórios

Saiba mais sobre [funções administrativas da AD Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

*Aplique sempre o conceito de menos privilégios para reduzir o risco de um compromisso de conta.* Considere implementar a [Gestão](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) de Identidade Privilegiada para garantir ainda mais a sua organização.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Implementar relatórios e monitorização da AD Azure

Dependendo das decisões que tomou anteriormente usando a orientação de design acima, esta secção irá guiá-lo para a documentação sobre as diferentes opções de implementação.

### <a name="consume-and-archive-azure-ad-logs"></a>Consumir e arquivar registos de Anúncios Azure

[Encontrar relatórios de atividade no portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Registos de Anúncios De Arquivo Azure para uma conta de Armazenamento Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Implementar monitorização e análise

[Enviar registos para o Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Instale e utilize as vistas de análise de registo para o Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analise registos de atividade da Azure AD com registos do Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretar esquemas de registos de auditoria no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretar o sinal em logs schema no Monitor Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Stream Azure AD regista um hub de eventos Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk) (Utilizar o Azure Monitor para integrar registos do Azure AD no Splunk)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic) (Utilizar o Azure Monitor para integrar registos do Azure AD no SumoLogic)

 

 

## <a name="next-steps"></a>Passos seguintes

Considere implementar [a Gestão](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) de Identidade Privilegiada 

Considere implementar [o controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 

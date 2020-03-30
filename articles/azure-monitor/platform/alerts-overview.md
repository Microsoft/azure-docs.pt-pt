---
title: Visão geral do alerta e monitorização da notificação em Azure
description: Visão geral do alerta em Azure. Alertas, alertas clássicos e a interface de alertas.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 7ca77531ed3e1fae8ec297e430597452c7512aea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274791"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Descrição geral dos alertas no Microsoft Azure 

Este artigo descreve quais são os alertas, os seus benefícios e como começar a usá-los.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Quais são os alertas no Microsoft Azure?
Os alertas notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. 

Este artigo discute a experiência de alerta unificada no Monitor Azure, que inclui alertas que foram anteriormente geridos pela Log Analytics e pela Application Insights. A [experiência de alerta anterior](alerts-classic.overview.md) e os tipos de alerta são *chamados de alertas clássicos.* Pode ver esta experiência mais antiga e o tipo de alerta mais antigo selecionando **ver alertas clássicos** no topo da página de alerta. 

## <a name="overview"></a>Descrição geral

O diagrama abaixo representa o fluxo de alertas. 

![Diagrama de fluxo de alerta](media/alerts-overview/Azure-Monitor-Alerts.svg)

As regras de alerta estão separadas dos alertas e das ações tomadas quando um alerta dispara. A regra do alerta captura o alvo e os critérios para alertar. A regra do alerta pode estar num estado ativado ou incapacitado. Alerta somente fogo quando ativado. 

Seguem-se os principais atributos de uma regra de alerta:

**Recurso-alvo**: Define o âmbito e os sinais disponíveis para alerta. Um alvo pode ser qualquer recurso Azure. Alvos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de escala de máquina virtual, um espaço de trabalho log Analytics ou um recurso Application Insights. Para certos recursos (como máquinas virtuais), pode especificar vários recursos como alvo da regra de alerta.

**Sinal**: Emitido pelo recurso-alvo. Os sinais podem ser dos seguintes tipos: métrica, registo de atividade, Insights de Aplicação e registo.

**Critérios**: Uma combinação de sinal e lógica aplicada num recurso-alvo. Exemplos: 

- Percentagem cpu > 70%
- Tempo de resposta do servidor > 4 ms 
- Contagem de resultados de uma consulta de registo > 100

**Nome do alerta**: Um nome específico para a regra de alerta configurado pelo utilizador.

**Descrição do alerta**: Uma descrição para a regra de alerta configurada pelo utilizador.

**Gravidade**: A gravidade do alerta após o acordo de conformidade com os critérios especificados na regra de alerta é cumprida. A gravidade pode variar de 0 a 4.

- Sev 0 = Crítico
- Sev 1 = Erro
- Sev 2 = Aviso
- Sev 3 = Informacional
- Sev 4 = Verbose 

**Ação**: Uma ação específica tomada quando o alerta é disparado. Para mais informações, consulte [Grupos de Ação](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>O que pode alertar

Pode alertar sobre métricas e registos, conforme descrito na monitorização de [fontes](../../azure-monitor/platform/data-sources.md)de dados . Estes incluem, mas não estão limitados a:

- Valores de métricas
- Consultas de pesquisa de registos
- Eventos de registo de atividades
- Estado de funcionamento da plataforma subjacente do Azure
- Testes de disponibilidade do site

Anteriormente, as métricas do Monitor Azure, Insights de Aplicação, Log Analytics e Service Health tinham capacidades de alerta separadas. Com o tempo, o Azure melhorou e combinou tanto a interface do utilizador como os diferentes métodos de alerta. Esta consolidação ainda está em curso. Como resultado, ainda existem algumas capacidades de alerta ainda não no novo sistema de alertas.  

| **Monitorizar fonte** | **Tipo de sinal**  | **Descrição** |
|-------------|----------------|-------------|
| Estado de funcionamento dos serviços | Registo de atividades  | Não suportado. Consulte criar alertas de registo de [atividade sonantes em notificações](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)de serviço .  |
| Application Insights | Testes de disponibilidade web | Não suportado. Consulte [os alertas](../../azure-monitor/app/monitor-web-app-availability.md)de teste da Web . Disponível em qualquer website que seja instrumentado para enviar dados para Application Insights. Receba uma notificação quando a disponibilidade ou capacidade de resposta de um website está abaixo das expectativas. |

## <a name="manage-alerts"></a>Gerir alertas
Pode definir o estado de um alerta para especificar onde está no processo de resolução. Quando os critérios especificados na regra de alerta são cumpridos, um alerta é criado ou disparado, e tem um estatuto de *Novo*. Pode alterar o estado quando reconhecer um alerta e quando o fechar. Todas as mudanças de Estado estão armazenadas na história do alerta.

Os seguintes estados de alerta são apoiados.

| Estado | Descrição |
|:---|:---|
| Novo | O assunto acabou de ser detetado e ainda não foi revisto. |
| Confirmado | Um administrador reviu o alerta e começou a trabalhar nele. |
| Fechado | A questão foi resolvida. Depois de um alerta ter sido encerrado, pode reabri-lo mudando-o para outro estado. |

*O estado* de alerta é diferente e independente da condição do *monitor.* O estado de alerta é definido pelo utilizador. A condição do monitor é definida pelo sistema. Quando um alerta dispara, o estado de monitorização do alerta está definido para *ser disparado*. Quando a condição subjacente que causou o alerta a disparar, a condição do monitor está definida para *ser resolvida*. O estado de alerta não é alterado até que o utilizador o altere. Aprenda [a mudar o estado dos seus alertas e grupos inteligentes.](https://aka.ms/managing-alert-smart-group-states)

## <a name="smart-groups"></a>Grupos inteligentes 

Grupos inteligentes são agregações de alertas baseados em algoritmos de aprendizagem automática, o que pode ajudar a reduzir o ruído de alerta e ajudar na resolução de problemas. [Saiba mais sobre grupos inteligentes](https://aka.ms/smart-groups) e [como gerir os seus grupos inteligentes.](https://aka.ms/managing-smart-groups)


## <a name="alerts-experience"></a>Experiência de alertas 
A página predefinida de Alertas fornece um resumo dos alertas que são criados dentro de um determinado intervalo de tempo. Exibe os alertas totais para cada gravidade, com colunas que identificam o número total de alertas em cada estado para cada gravidade. Selecione qualquer uma das severidades para abrir a página [All Alerts](#all-alerts-page) filtrada por essa gravidade.

Em alternativa, pode [enumerar programáticamente as instâncias de alerta geradas nas suas assinaturas utilizando APIs REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Só é possível aceder a alertas gerados nos últimos 30 dias.

Não mostra nem rastreia alertas clássicos. Pode alterar as subscrições ou os parâmetros de filtro para atualizar a página. 

![Screenshot da página alertas](media/alerts-overview/alerts-page.png)

Pode filtrar esta vista selecionando valores nos menus suspensos na parte superior da página.

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione as subscrições Azure para as quais pretende visualizar os alertas. Pode optar opcionalmente por selecionar todas as suas subscrições. Apenas os alertas a que tem acesso nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um único grupo de recursos. Apenas os alertas com alvos no grupo de recursos selecionados estão incluídos na vista. |
| Intervalo de tempo | Apenas os alertas disparados dentro do intervalo de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, as últimas 24 horas, os últimos 7 dias, e os últimos 30 dias. |

Selecione os seguintes valores na parte superior da página Alertas para abrir outra página:

| Valor | Descrição |
|:---|:---|
| Total de alertas | O número total de alertas que correspondem aos critérios selecionados. Selecione este valor para abrir a vista All Alerts sem filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criados a partir dos alertas que correspondem aos critérios selecionados. Selecione este valor para abrir a lista de grupos inteligentes na vista All Alerts.
| Regras totais de alerta | O número total de regras de alerta no grupo de subscrição e recursos selecionados. Selecione este valor para abrir a visão regras filtrada no grupo de subscrição e recursos selecionados.


## <a name="manage-alert-rules"></a>Gere regras do alerta
Para mostrar a página **Regras,** **selecione Gerir regras**de alerta . A página Regras é um único local para gerir todas as regras de alerta em todas as suas subscrições do Azure. Ele lista todas as regras de alerta e pode ser classificado com base em recursos-alvo, grupos de recursos, nome de regra ou estado. Também pode editar, ativar ou desativar as regras de alerta a partir desta página.  

 ![Screenshot da página Regras](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Pode autor de alertas de forma consistente, independentemente do serviço de monitorização ou do tipo de sinal. Todos os alertas disparados e detalhes relacionados estão disponíveis numa única página.
 
Eis como criar uma nova regra de alerta:
1. Escolha o _alvo_ para o alerta.
1. Selecione o _sinal_ a partir dos sinais disponíveis para o alvo.
1. Especifique a _lógica_ a aplicar aos dados a partir do sinal.
 
Este processo de autor simplificado já não requer que conheça a fonte de monitorização ou os sinais que são suportados antes de selecionar um recurso Azure. A lista de sinais disponíveis é automaticamente filtrada com base no recurso-alvo que seleciona. Também com base nesse alvo, é guiado através da definição automática da lógica da regra de alerta.  

Pode aprender mais sobre como criar regras de alerta na [Create, view e gerir alertas usando o Monitor Azure](../../azure-monitor/platform/alerts-metric.md).

Os alertas estão disponíveis em vários serviços de monitorização do Azure. Para obter informações sobre como e quando utilizar cada um destes serviços, consulte o [Monitoring Azure aplicações e recursos.](../../azure-monitor/overview.md) 


## <a name="all-alerts-page"></a>Página de Todos os Alertas 
Para ver a página **All Alerts,** selecione **Total Alerts**. Aqui pode ver uma lista de alertas criados dentro do tempo selecionado. Pode ver uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione o banner na parte superior da página para alternar entre as vistas.

![Screenshot da página de todos os alertas](media/alerts-overview/all-alerts-page.png)

Pode filtrar a vista selecionando os seguintes valores nos menus suspensos na parte superior da página:

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione as subscrições Azure para as quais pretende visualizar os alertas. Pode optar opcionalmente por selecionar todas as suas subscrições. Apenas os alertas a que tem acesso nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um único grupo de recursos. Apenas os alertas com alvos no grupo de recursos selecionados estão incluídos na vista. |
| Tipo de recurso | Selecione um ou mais tipos de recursos. Apenas os alertas com alvos do tipo selecionado estão incluídos na vista. Esta coluna só está disponível depois de um grupo de recursos ter sido especificado. |
| Recurso | Selecione um recurso. Apenas os alertas com esse recurso como alvo estão incluídos na vista. Esta coluna só está disponível depois de ser especificado um tipo de recurso. |
| Gravidade | Selecione uma gravidade de alerta ou selecione **Tudo** para incluir alertas de todas as gravidades. |
| Condição do monitor | Selecione uma condição de monitor, ou selecione **Todos** para incluir alertas de todas as condições. |
| Estado de alerta | Selecione um estado de alerta ou selecione **All** para incluir alertas de todos os estados. |
| Serviço de monitorização | Selecione um serviço ou selecione **Todos** os serviços. Apenas estão incluídos os alertas criados por regras que utilizam o serviço como alvo. |
| Intervalo de tempo | Apenas os alertas disparados dentro do intervalo de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, as últimas 24 horas, os últimos 7 dias, e os últimos 30 dias. |

Selecione **Colunas** na parte superior da página para selecionar quais colunas mostrar. 

## <a name="alert-details-page"></a>Página de detalhes de alerta
Ao selecionar um alerta, esta página fornece detalhes do alerta e permite-lhe alterar o seu estado.

![Screenshot da página de detalhes do Alerta](media/alerts-overview/alert-detail2.png)

A página de detalhes do Alerta inclui as seguintes secções:

| Section | Descrição |
|:---|:---|
| Resumo | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Enumera cada ação tomada pelo alerta e quaisquer alterações feitas ao alerta. Atualmente limitado a alterações estatais. |
| Diagnóstico | Informações sobre o grupo inteligente em que o alerta está incluído. A *contagem de alerta* refere-se ao número de alertas que estão incluídos no grupo inteligente. Inclui outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias, independentemente do filtro de tempo na página da lista de alertas. Selecione um alerta para ver os seus detalhes. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Controlo de acesso baseado em funções (RBAC) para as suas instâncias de alerta

O consumo e a gestão de casos de alerta exigem que o utilizador tenha as funções rBAC incorporadas quer do colaborador de [monitorização](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) quer do [leitor de monitorização.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) Estas funções são suportadas em qualquer âmbito do Gestor de Recursos Azure, desde o nível de subscrição até atribuições granulares a nível de recursos. Por exemplo, se um utilizador tiver apenas `ContosoVM1`monitorização do acesso dos contribuintes `ContosoVM1`à máquina virtual, esse utilizador só pode consumir e gerir apenas os alertas gerados em .

## <a name="manage-your-alert-instances-programmatically"></a>Gerencie os seus casos de alerta programáticamente

É melhor consultar programaticamente os alertas gerados contra a sua subscrição. Isto pode ser para criar vistas personalizadas fora do portal Azure, ou para analisar os seus alertas para identificar padrões e tendências.

Pode consultar alertas gerados contra as suas subscrições, quer utilizando a [API](https://aka.ms/alert-management-api) DE GESTÃO de Alerta ou utilizando o Gráfico de [Recursos Azure](../../governance/resource-graph/overview.md) e a [API REST para Recursos.](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)

O API de Recursos do Gráfico de Recursos permite-lhe consultar casos de alerta em escala. Isto é recomendado quando você tem que gerir alertas gerados em muitas subscrições. 

O seguinte pedido de amostra ao API do Gráfico de Recursos devolve a contagem de alertas dentro de uma subscrição:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Você também pode ver o resultado desta consulta de Gráfico de Recursos no portal com O Explorador de Gráficos de Recursos Azure: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Pode consultar os alertas para os seus campos [essenciais.](alerts-common-schema-definitions.md#essentials)

Utilize a API de Gestão de [Alerta sOSPara](https://aka.ms/alert-management-api) obter mais informações sobre alertas específicos, incluindo os seus campos de contexto de [alerta.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre grupos inteligentes](https://aka.ms/smart-groups)
- [Conheça grupos de ação](../../azure-monitor/platform/action-groups.md)
- [Gerir os seus casos de alerta em Azure](https://aka.ms/managing-alert-instances)
- [Gestão de Grupos Inteligentes](https://aka.ms/managing-smart-groups)
- [Saiba mais sobre os preços dos alertas do Azure](https://azure.microsoft.com/pricing/details/monitor/)







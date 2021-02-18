---
title: Visão geral do alerta e monitorização da notificação em Azure
description: Visão geral do alerta em Azure. Alertas, alertas clássicos e a interface de alertas.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 96e15c1e07d437855b6553757295800406a4cf4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614622"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Descrição geral dos alertas no Microsoft Azure 

Este artigo descreve quais são os alertas, os seus benefícios e como começar a usá-los.  

## <a name="what-are-alerts-in-microsoft-azure"></a>O que são alertas no Microsoft Azure?

Os alertas notificam-no proativamente quando forem encontrados problemas com a sua infraestrutura ou aplicação utilizando os seus dados de monitorização no Azure Monitor. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. 

## <a name="overview"></a>Descrição Geral

O diagrama abaixo representa o fluxo de alertas. 

![Diagrama de fluxo de alerta](media/alerts-overview/Azure-Monitor-Alerts.svg)

As regras de alerta são separadas dos alertas e das ações tomadas quando um alerta dispara. A regra de alerta captura o alvo e critérios para alertar. A regra de alerta pode estar num estado ativado ou deficiente. Alertas apenas para incêndios quando ativados. 

Seguem-se os principais atributos de uma regra de alerta:

**Recurso-alvo** - Define o âmbito e os sinais disponíveis para alerta. Um alvo pode ser qualquer recurso Azure. Alvos de exemplo:

- Máquinas virtuais.
- Contas de armazenamento.
- Log Analytics espaço de trabalho.
- Insights de Aplicação. 

Para certos recursos (como máquinas virtuais), pode especificar vários recursos como alvo da regra de alerta.

**Sinal** - Emitido pelo recurso alvo. Os sinais podem ser dos seguintes tipos: métrica, log de atividade, Insights de aplicação e log.

**Critérios** - Uma combinação de sinal e lógica aplicada num recurso-alvo. Exemplos: 

- Percentagem CPU > 70%
- Tempo de resposta do servidor > 4 ms 
- Contagem de resultados de uma consulta de registo > 100

**Nome de alerta** - Um nome específico para a regra de alerta configurada pelo utilizador.

**Descrição do alerta** - Descrição da regra de alerta configurada pelo utilizador.

**Gravidade** - A gravidade do alerta após os critérios especificados na regra de alerta é cumprida. A severidade pode variar de 0 a 4.

- Sev 0 = Crítico
- Sev 1 = Erro
- Sev 2 = Aviso
- Sev 3 = Informativo
- Sev 4 = Verbose 

**Ação** - Uma ação específica tomada quando o alerta é disparado. Para mais informações, consulte [Grupos de Ação.](../alerts/action-groups.md)

## <a name="what-you-can-alert-on"></a>O que pode alertar

Pode alertar em métricas e registos, conforme descrito na [monitorização de fontes de dados](./../agents/data-sources.md). Os sinais incluem, mas não se limitam a:

- Valores de métricas
- Consultas de pesquisa de registos
- Eventos de registo de atividades
- Estado de funcionamento da plataforma subjacente do Azure
- Testes de disponibilidade do site

## <a name="manage-alerts"></a>Gerir alertas

Pode definir o estado de alerta para especificar onde está no processo de resolução. Quando os critérios especificados na regra de alerta são cumpridos, um alerta é criado ou disparado, e tem um estado de *New*. Pode alterar o estado quando reconhecer um alerta e quando o fecha. Todas as alterações de estado são armazenadas na história do alerta.

Os seguintes estados de alerta são apoiados.

| Estado | Descrição |
|:---|:---|
| Novo | O problema foi detetado e ainda não foi revisto. |
| Confirmado | Um administrador reviu o alerta e começou a trabalhar nele. |
| Fechado | A questão foi resolvida. Depois de um alerta ter sido fechado, pode reabri-lo mudando-o para outro estado. |

*O estado de alerta* é diferente e independente da condição do *monitor.* O estado de alerta é definido pelo utilizador. A condição do monitor é definida pelo sistema. Quando um alerta dispara, o estado do monitor do alerta é definido para *'disparado',* e quando a condição subjacente que causou o alerta para o incêndio se apaga, a condição do monitor está definida para *"resolvida".* 

O estado de alerta não é alterado até que o utilizador o altere. Saiba [como alterar o estado dos seus alertas e grupos inteligentes.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="alerts-experience"></a>Experiência alerta 
A página de Alertas predefinidos fornece um resumo dos alertas que são criados dentro de um determinado intervalo de tempo. Apresenta os alertas totais para cada gravidade, com colunas que identificam o número total de alertas em cada estado para cada gravidade. Selecione qualquer uma das severidades para abrir a página [Todos os Alertas](#all-alerts-page) filtrado por essa gravidade.

Em vez disso, pode [enumerar programáticamente as instâncias de alerta geradas nas suas subscrições utilizando APIs REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Só é possível aceder aos alertas gerados nos últimos 30 dias.

Não mostra nem rastreia alertas clássicos. Pode alterar as subscrições ou filtrar parâmetros para atualizar a página. 

![Screenshot da página alertas](media/alerts-overview/alerts-page.png)

Pode filtrar esta vista selecionando valores nos menus suspensos no topo da página.

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione as subscrições Azure para as quais deseja visualizar os alertas. Pode optar opcionalmente por selecionar todas as suas subscrições. Apenas os alertas a que tem acesso nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um único grupo de recursos. Apenas os alertas com alvos no grupo de recursos selecionados estão incluídos na vista. |
| Intervalo de tempo | Apenas os alertas disparados dentro do intervalo de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, as últimas 24 horas, os últimos 7 dias, e os últimos 30 dias. |

Selecione os seguintes valores no topo da página Alertas para abrir outra página:

| Valor | Descrição |
|:---|:---|
| Total de alertas | O número total de alertas que correspondem aos critérios selecionados. Selecione este valor para abrir a vista Todos os Alertas sem filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criados a partir dos alertas que correspondem aos critérios selecionados. Selecione este valor para abrir a lista de grupos inteligentes na vista Todos os Alertas.
| Regras totais de alerta | O número total de regras de alerta no grupo de subscrição e recursos selecionados. Selecione este valor para abrir a vista Regras filtrada no grupo de subscrição e recursos selecionados.


## <a name="manage-alert-rules"></a>Gere regras do alerta
Para mostrar a página **'Regras',** **selecione Gerir as regras de alerta**. A página Regras é um único local para gerir todas as regras de alerta através das suas subscrições Azure. Ele lista todas as regras de alerta e pode ser classificado com base em recursos-alvo, grupos de recursos, nome de regra ou estado. Também pode editar, ativar ou desativar as regras de alerta desta página.  

 ![Screenshot da página Regras](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Pode autorizar regras de alerta de forma consistente, seja qual for o serviço de monitorização ou tipo de sinal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
Eis como criar uma nova regra de alerta:
1. Escolha o _alvo_ para o alerta.
1. Selecione o _sinal_ a partir dos sinais disponíveis para o alvo.
1. Especifique a _lógica_ a aplicar aos dados a partir do sinal.

Este processo de autoria simplificado já não requer que conheça a fonte de monitorização ou os sinais suportados antes de selecionar um recurso Azure. A lista de sinais disponíveis é automaticamente filtrada com base no recurso-alvo que seleciona. Também com base nesse alvo, é guiado pela definição automática da lógica da regra de alerta.  

Pode aprender mais sobre como criar regras de alerta em [Criar, ver e gerir alertas usando o Azure Monitor.](../alerts/alerts-metric.md)

Os alertas estão disponíveis em vários serviços de monitorização do Azure. Para obter informações sobre como e quando utilizar cada um destes serviços, consulte [aplicações e recursos do Monitor Azure.](../overview.md) 


## <a name="all-alerts-page"></a>Página de todos os alertas 
Para ver a página **Todos os Alertas,** selecione **Alertas Totais**. Aqui pode ver uma lista de alertas criados dentro do tempo selecionado. Pode ver uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione o banner no topo da página para alternar entre as vistas.

![Screenshot da página de todos os alertas](media/alerts-overview/all-alerts-page.png)

Pode filtrar a vista selecionando os seguintes valores nos menus suspensos no topo da página:

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione as subscrições Azure para as quais deseja visualizar os alertas. Pode optar opcionalmente por selecionar todas as suas subscrições. Apenas os alertas a que tem acesso nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um único grupo de recursos. Apenas os alertas com alvos no grupo de recursos selecionados estão incluídos na vista. |
| Tipo de recurso | Selecione um ou mais tipos de recursos. Apenas os alertas com alvos do tipo selecionado estão incluídos na vista. Esta coluna só está disponível depois de especificado um grupo de recursos. |
| Recurso | Selecione um recurso. Apenas os alertas com esse recurso como alvo estão incluídos na vista. Esta coluna só está disponível depois de especificado um tipo de recurso. |
| Gravidade | Selecione uma gravidade de alerta ou selecione **Todos** para incluir alertas de todas as severidades. |
| Condição do monitor | Selecione uma condição do monitor ou selecione **Todos** para incluir alertas de todas as condições. |
| Estado de alerta | Selecione um estado de alerta ou selecione **Todos** para incluir alertas de todos os estados. |
| Serviço de monitorização | Selecione um serviço ou selecione **Todos** para incluir todos os serviços. Apenas estão incluídos alertas criados pelas regras que utilizam o serviço como alvo. |
| Intervalo de tempo | Apenas os alertas disparados dentro do intervalo de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, as últimas 24 horas, os últimos 7 dias, e os últimos 30 dias. |

Selecione **Colunas** no topo da página para selecionar quais colunas mostrar. 

## <a name="alert-details-page"></a>Página de detalhes de alerta
Ao selecionar um alerta, esta página fornece detalhes do alerta e permite-lhe alterar o seu estado.

![Screenshot da página de detalhes do alerta](media/alerts-overview/alert-detail2.png)

A página de detalhes do alerta inclui as seguintes secções:

| Section | Description |
|:---|:---|
| Resumo | Exibe as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Enumera cada ação tomada pelo alerta e quaisquer alterações feitas ao alerta. Atualmente limitado a alterações de estado. |
| Diagnóstico | Informação sobre o grupo inteligente em que o alerta está incluído. A *contagem de alerta* refere-se ao número de alertas que estão incluídos no grupo inteligente. Inclui outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias, qualquer que seja o filtro de tempo na página da lista de alertas. Selecione um alerta para ver os seus detalhes. |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>Controlo de acesso baseado em funções Azure (Azure RBAC) para os seus casos de alerta

O consumo e a gestão de casos de alerta exigem que o utilizador tenha as funções de Azure incorporadas quer do [colaborador de monitorização](../../role-based-access-control/built-in-roles.md#monitoring-contributor) quer do [leitor de monitorização.](../../role-based-access-control/built-in-roles.md#monitoring-reader) Estas funções são suportadas em qualquer âmbito do Azure Resource Manager, desde o nível da subscrição até às atribuições granulares ao nível do recurso. Por exemplo, se um utilizador tiver apenas acesso de colaborador a máquina `ContosoVM1` virtual, esse utilizador pode consumir e gerir apenas os alertas gerados em `ContosoVM1` .

## <a name="manage-your-alert-instances-programmatically"></a>Gerencie os seus casos de alerta programáticamente

É melhor consultar programáticamente os alertas gerados contra a sua subscrição. As consultas podem ser para criar vistas personalizadas fora do portal Azure, ou para analisar os seus alertas para identificar padrões e tendências.

Pode consultar os alertas gerados contra as suas subscrições, utilizando a [API de Gestão de Alerta](/rest/api/monitor/alertsmanagement/alerts) ou utilizando o [Gráfico de Recursos Azure](../../governance/resource-graph/overview.md) e a [API de Recursos](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)REST.

O Recurso Graph REST API for Resources permite-lhe consultar casos de alerta em escala. O Gráfico de Recursos é recomendado quando tem de gerir os alertas gerados em muitas subscrições. 

O seguinte pedido de amostra para a API do Gráfico de Recurso retorna a contagem de alertas dentro de uma subscrição:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

Também pode ver o resultado desta consulta de Gráfico de Recurso no portal com O Azure Resource Graph Explorer: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

Pode consultar os alertas para os seus campos [essenciais.](../alerts/alerts-common-schema-definitions.md#essentials)

Utilize a [API de Gestão de Alertas](/rest/api/monitor/alertsmanagement/alerts) PARA obter mais informações sobre alertas específicos, incluindo os seus campos [de contexto de alerta.](../alerts/alerts-common-schema-definitions.md#alert-context)

## <a name="smart-groups"></a>Grupos inteligentes

Os grupos inteligentes são agregações de alertas baseados em algoritmos de aprendizagem automática, que podem ajudar a reduzir o ruído de alerta e a ajuda na resolução de problemas. [Saiba mais sobre os Grupos Inteligentes](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) e [como gerir os seus grupos inteligentes.](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre Grupos Inteligentes](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Conheça os grupos de ação](../alerts/action-groups.md)
- [Gerir as suas instâncias de alerta em Azure](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Gestão de Grupos Inteligentes](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Saiba mais sobre os preços dos alertas do Azure](https://azure.microsoft.com/pricing/details/monitor/)
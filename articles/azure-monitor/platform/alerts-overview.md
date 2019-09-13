---
title: Visão geral do monitoramento de alertas e notificações no Azure
description: Visão geral de alertas no Azure. Alertas, alertas clássicos e a interface de alertas.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: ae3e45ece1bd53846ab7728c29c0da1b709fe42c
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915953"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Visão geral de alertas no Microsoft Azure 

Este artigo descreve quais alertas são, seus benefícios e como começar a usá-los.  




## <a name="what-are-alerts-in-microsoft-azure"></a>O que são alertas no Microsoft Azure?
Os alertas o notificam proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas antes que os usuários do seu sistema os observem. 

Este artigo aborda a experiência de alerta unificada no Azure Monitor, que inclui alertas que foram gerenciados anteriormente pelo Log Analytics e Application Insights. A [experiência de alerta anterior](alerts-classic.overview.md) e os tipos de alerta são chamados de *alertas clássicos*. Você pode exibir essa experiência mais antiga e o tipo de alerta mais antigo selecionando **exibir alertas clássicos** na parte superior da página de alerta. 

## <a name="overview"></a>Descrição geral

O diagrama a seguir representa o fluxo de alertas. 

![Diagrama do fluxo de alerta](media/alerts-overview/Azure-Monitor-Alerts.svg)

As regras de alerta são separadas dos alertas e das ações tomadas quando um alerta é disparado. A regra de alerta captura o destino e os critérios para alertas. A regra de alerta pode estar em um estado habilitado ou desabilitado. Os alertas são acionados somente quando habilitados. 

Veja a seguir os principais atributos de uma regra de alerta:

**Recurso de destino**: Define o escopo e os sinais disponíveis para alertas. Um destino pode ser qualquer recurso do Azure. Destinos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de dimensionamento de máquinas virtuais, um espaço de trabalho Log Analytics ou um recurso de Application Insights. Para determinados recursos (como máquinas virtuais), você pode especificar vários recursos como o destino da regra de alerta.

**Sinal**: Emitido pelo recurso de destino. Os sinais podem ser dos seguintes tipos: métrica, log de atividades, Application Insights e log.

**Critérios**: Uma combinação de sinal e lógica aplicada em um recurso de destino. Exemplos: 
   - Percentual de CPU > 70%
   - Tempo de resposta do servidor > 4 MS 
   - Contagem de resultados de uma consulta de log > 100

**Nome do alerta**: Um nome específico para a regra de alerta configurada pelo usuário.

**Descrição do alerta**: Uma descrição para a regra de alerta configurada pelo usuário.

**Gravidade**: A severidade do alerta depois que os critérios especificados na regra de alerta são atendidos. A severidade pode variar de 0 a 4.
   - Sev 0 = crítico
   - Sev 1 = erro
   - Sev 2 = aviso
   - Sev 3 = informativo
   - Sev 4 = detalhado 

**Ação**: Uma ação específica executada quando o alerta é acionado. Para obter mais informações, consulte [grupos de ações](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Sobre o que você pode alertar

Você pode alertar sobre métricas e logs, conforme descrito em [monitorando fontes de dados](../../azure-monitor/platform/data-sources-reference.md). Eles incluem, mas não se limitam a:
- Valores de métrica
- Consultas de pesquisa de log
- Eventos do log de atividades
- Integridade da plataforma subjacente do Azure
- Testes de disponibilidade do site

Anteriormente, Azure Monitor métricas, Application Insights, Log Analytics e integridade do serviço tinham recursos de alerta separados. Ao longo do tempo, o Azure melhorou e combinou a interface do usuário e os diferentes métodos de alerta. Essa consolidação ainda está em andamento. Como resultado, ainda existem alguns recursos de alerta ainda não incluídos no novo sistema de alertas.  

| **Origem do monitor** | **Tipo de sinal**  | **Descrição** | 
|-------------|----------------|-------------|
| Service Health | Registo de atividades  | Não suportado. Consulte [criar alertas do log de atividades em notificações de serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testes de disponibilidade na Web | Não suportado. Consulte [alertas de teste na Web](../../azure-monitor/app/monitor-web-app-availability.md). Disponível para qualquer site que é instrumentado para enviar dados para Application Insights. Receba uma notificação quando a disponibilidade ou a capacidade de resposta de um site estiver abaixo das expectativas. |

## <a name="manage-alerts"></a>Gerir alertas
Você pode definir o estado de um alerta para especificar onde ele está no processo de resolução. Quando os critérios especificados na regra de alerta são atendidos, um alerta é criado ou acionado e tem um status *novo*. Você pode alterar o status ao reconhecer um alerta e quando fechá-lo. Todas as alterações de estado são armazenadas no histórico do alerta.

Há suporte para os seguintes Estados de alerta.

| State | Descrição |
|:---|:---|
| Novo | O problema acabou de ser detectado e ainda não foi revisado. |
| Reconhecido | Um administrador analisou o alerta e começou a trabalhar nele. |
| Fechado | O problema foi resolvido. Depois que um alerta for fechado, você poderá reabri-lo alterando-o para outro Estado. |

O *estado do alerta* é diferente e independente da condição do *Monitor*. O estado do alerta é definido pelo usuário. A condição do monitor é definida pelo sistema. Quando um alerta é *disparado, a condição*do monitor do alerta é definida como disparada. Quando a condição subjacente que fez com que o alerta fosse limpo, a condição do monitor é definida como *resolvida*. O estado do alerta não é alterado até que o usuário o altere. Saiba [como alterar o estado de seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Grupos inteligentes 

Os grupos inteligentes são agregações de alertas com base em algoritmos de aprendizado de máquina, o que pode ajudar a reduzir o ruído de alerta e auxiliar na solução de problemas. [Saiba mais sobre os grupos inteligentes](https://aka.ms/smart-groups) e [como gerenciar seus grupos inteligentes](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Experiência de alertas 
A página alertas padrão fornece um resumo dos alertas que são criados dentro de um intervalo de tempo específico. Ele exibe o total de alertas para cada severidade, com colunas que identificam o número total de alertas em cada Estado para cada severidade. Selecione qualquer uma das severidades para abrir a página [todos os alertas](#all-alerts-page) filtrada por essa gravidade.

Como alternativa, você pode [enumerar programaticamente as instâncias de alerta geradas em suas assinaturas usando APIs REST](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Você só pode acessar alertas gerados nos últimos 30 dias.

Ele não mostra nem rastreia alertas clássicos. Você pode alterar as assinaturas ou parâmetros de filtro para atualizar a página. 

![Captura de tela da página de alertas](media/alerts-overview/alerts-page.png)

Você pode filtrar essa exibição selecionando valores nos menus suspensos na parte superior da página.

| Coluna | Descrição |
|:---|:---|
| Subscription | Selecione as assinaturas do Azure para as quais você deseja exibir os alertas. Opcionalmente, você pode optar por selecionar todas as suas assinaturas. Somente os alertas aos quais você tem acesso nas assinaturas selecionadas são incluídos na exibição. |
| Resource group | Selecione um único grupo de recursos. Somente os alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Intervalo de tempo | Somente os alertas acionados no intervalo de tempo selecionado são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione os seguintes valores na parte superior da página alertas para abrir outra página:

| Value | Descrição |
|:---|:---|
| Alertas totais | O número total de alertas que correspondem aos critérios selecionados. Selecione este valor para abrir a exibição todos os alertas sem filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criados a partir dos alertas que correspondem aos critérios selecionados. Selecione esse valor para abrir a lista de grupos inteligentes na exibição todos os alertas.
| Regras de alertas totais | O número total de regras de alerta na assinatura e no grupo de recursos selecionados. Selecione este valor para abrir o modo de exibição de regras filtrado na assinatura e no grupo de recursos selecionados.


## <a name="manage-alert-rules"></a>Gere regras do alerta
Para mostrar a página **regras** , selecione **gerenciar regras de alerta**. A página de regras é um único local para gerenciar todas as regras de alerta em suas assinaturas do Azure. Ele lista todas as regras de alerta e pode ser classificado com base nos recursos de destino, grupos de recursos, nome da regra ou status. Você também pode editar, habilitar ou desabilitar regras de alerta nesta página.  

 ![Captura de tela da página regras](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Você pode criar alertas de maneira consistente, independentemente do serviço de monitoramento ou do tipo de sinal. Todos os alertas acionados e os detalhes relacionados estão disponíveis em uma única página.
 
Veja como criar uma nova regra de alerta:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ dos sinais disponíveis para o destino.
1. Especifique a _lógica_ a ser aplicada aos dados do sinal.
 
Esse processo de criação simplificado não exige mais que você conheça a fonte de monitoramento ou os sinais que têm suporte antes de selecionar um recurso do Azure. A lista de sinais disponíveis é automaticamente filtrada com base no recurso de destino selecionado. Também com base nesse destino, você é guiado por meio da definição da lógica da regra de alerta automaticamente.  

Você pode aprender mais sobre como criar regras de alerta em [criar, exibir e gerenciar alertas usando o Azure monitor](../../azure-monitor/platform/alerts-metric.md).

Os alertas estão disponíveis em vários serviços de monitoramento do Azure. Para obter informações sobre como e quando usar cada um desses serviços, consulte [monitorando aplicativos e recursos do Azure](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Página todos os alertas 
Para ver a página **todos os alertas** , selecione **total de alertas**. Aqui você pode exibir uma lista de alertas criados dentro da hora selecionada. Você pode exibir uma lista de alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione a faixa na parte superior da página para alternar entre exibições.

![Captura de tela de todas as páginas de alertas](media/alerts-overview/all-alerts-page.png)

Você pode filtrar a exibição selecionando os seguintes valores nos menus suspensos na parte superior da página:

| Coluna | Descrição |
|:---|:---|
| Subscription | Selecione as assinaturas do Azure para as quais você deseja exibir os alertas. Opcionalmente, você pode optar por selecionar todas as suas assinaturas. Somente os alertas aos quais você tem acesso nas assinaturas selecionadas são incluídos na exibição. |
| Resource group | Selecione um único grupo de recursos. Somente os alertas com destinos no grupo de recursos selecionado são incluídos na exibição. |
| Tipo de recurso | Selecione um ou mais tipos de recurso. Somente os alertas com destinos do tipo selecionado são incluídos na exibição. Esta coluna só estará disponível depois que um grupo de recursos tiver sido especificado. |
| Resource | Selecione um recurso. Somente os alertas com esse recurso como um destino são incluídos na exibição. Esta coluna só estará disponível depois que um tipo de recurso tiver sido especificado. |
| Severity | Selecione uma severidade de alerta ou selecione **tudo** para incluir alertas de todas as severidades. |
| Condição do monitor | Selecione uma condição de monitor ou selecione **tudo** para incluir alertas de todas as condições. |
| Estado de alerta | Selecione um estado de alerta ou selecione **todos** para incluir alertas de todos os Estados. |
| Monitorizar serviço | Selecione um serviço ou selecione **todos** para incluir todos os serviços. Somente os alertas criados por regras que usam o serviço como um destino são incluídos. |
| Intervalo de tempo | Somente os alertas acionados no intervalo de tempo selecionado são incluídos na exibição. Os valores com suporte são a última hora, as últimas 24 horas, os últimos 7 dias e os últimos 30 dias. |

Selecione **colunas** na parte superior da página para selecionar quais colunas mostrar. 

## <a name="alert-details-page"></a>Página detalhes do alerta
Quando você seleciona um alerta, essa página fornece detalhes do alerta e permite que você altere seu estado.

![Captura de tela da página de detalhes do alerta](media/alerts-overview/alert-detail2.png)

A página detalhes do alerta inclui as seguintes seções:

| Section | Descrição |
|:---|:---|
| Resumo | Exibe as propriedades e outras informações importantes sobre o alerta. |
| Histórico | Lista cada ação tomada pelo alerta e quaisquer alterações feitas no alerta. Atualmente limitado a alterações de estado. |
| Diagnóstico | Informações sobre o grupo inteligente no qual o alerta está incluído. A *contagem de alertas* refere-se ao número de alertas incluídos no grupo inteligente. Inclui outros alertas no mesmo grupo inteligente que foram criados nos últimos 30 dias, independentemente do filtro de tempo na página de lista de alertas. Selecione um alerta para exibir seus detalhes. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>RBAC (controle de acesso baseado em função) para suas instâncias de alerta

O consumo e o gerenciamento de instâncias de alerta exigem que o usuário tenha as funções RBAC internas de [monitorar o colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ou o [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Essas funções têm suporte em qualquer escopo de Azure Resource Manager, do nível de assinatura para atribuições granulares em um nível de recurso. Por exemplo, se um usuário só tiver acesso de colaborador de monitoramento para `ContosoVM1`a máquina virtual, esse usuário poderá consumir e gerenciar somente `ContosoVM1`alertas gerados no.

## <a name="manage-your-alert-instances-programmatically"></a>Gerencie suas instâncias de alerta programaticamente

Talvez você queira consultar programaticamente os alertas gerados em sua assinatura. Isso pode ser criar exibições personalizadas fora do portal do Azure ou analisar seus alertas para identificar padrões e tendências.

Você pode consultar alertas gerados em suas assinaturas usando a [API REST do gerenciamento de alertas](https://aka.ms/alert-management-api) ou usando a [API REST do grafo de recursos do Azure para alertas](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources).

A [API REST do grafo de recursos do Azure para alertas](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) permite que você consulte as instâncias de alerta em escala. Isso é recomendado quando você precisa gerenciar alertas gerados em várias assinaturas. 

A seguinte solicitação de exemplo para a API retorna a contagem de alertas em uma assinatura:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
Você pode consultar os alertas para seus campos [essenciais](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) .

Use a [API REST do gerenciamento de alertas](https://aka.ms/alert-management-api) para obter mais informações sobre alertas específicos, incluindo seus campos de [contexto de alerta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) .

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre os grupos inteligentes](https://aka.ms/smart-groups)
- [Saiba mais sobre grupos de ação](../../azure-monitor/platform/action-groups.md)
- [Gerenciando suas instâncias de alerta no Azure](https://aka.ms/managing-alert-instances)
- [Gerenciando grupos inteligentes](https://aka.ms/managing-smart-groups)







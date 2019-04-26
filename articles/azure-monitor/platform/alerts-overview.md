---
title: Descrição geral dos alertas e notificações de monitorização no Azure
description: Descrição geral dos alertas no Azure. Alertas, alertas clássicos, a interface de alertas.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 6fb49baf8ab58ae6cfe7639cedcc4466810c8b96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347489"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Descrição geral dos alertas no Microsoft Azure 

Este artigo descreve quais os alertas são, seus benefícios e como começar a usá-los.  




## <a name="what-are-alerts-in-microsoft-azure"></a>O que são alertas no Microsoft Azure?
Alertas de forma proativa notificá-lo quando importante condições forem encontradas nos seus dados de monitorização. Eles permitem que identificar e resolver problemas antes dos utilizadores do seu sistema tenha em atenção-los. 

Este artigo aborda a experiência unificada de alerta no Azure Monitor, que agora inclui alertas que foram geridas pelo Log Analytics e Application Insights. O [experiência anterior de alerta](alerts-classic.overview.md) e são chamados de tipos de alerta **alertas clássicos**. Pode ver esta experiência anterior e o tipo de alerta mais antigo, clicando em **ver alertas clássicos** na parte superior da página do alerta. 

## <a name="overview"></a>Descrição geral

O diagrama abaixo representa o fluxo de alertas. 

![Fluxo de alertas](media/alerts-overview/Azure-Monitor-Alerts.svg)

Regras de alerta são separadas dos alertas e a ação que são criados quando um alerta é acionado. 

**Regra de alerta** -a regra de alerta captura o destino e os critérios para alertas. A regra de alerta pode estar no estado desativado ou um ativada. Alertas acionam apenas quando ativado. 

Os principais atributos de uma regra de alerta são:

**Recurso de destino** – define o escopo e sinaliza disponível para alertas. Um destino pode ser qualquer recurso do Azure. Destinos de exemplo: uma máquina virtual, uma conta de armazenamento, um conjunto de dimensionamento de máquina virtual, uma área de trabalho do Log Analytics ou um recurso do Application Insights. Para determinados recursos (como máquinas virtuais), pode especificar vários recursos como o destino da regra de alerta.

**Sinal** - sinaliza são emitidos pelo recurso de destino e pode ser dos vários tipos. Métrica, registo de atividades, Application Insights e Log.

**Critérios** - critérios é a combinação de sinal e lógica aplicada num recurso de destino. Exemplos: 
   - Percentagem da CPU > 70%
   - Tempo de resposta do servidor > 4 ms 
   - Contagem de resultados de uma consulta de registo > 100

**Nome do alerta** – um nome específico para a regra de alerta configurado pelo utilizador

**Descrição do alerta** – uma descrição para a regra de alerta configurada pelo utilizador

**Gravidade** – a gravidade do alerta assim que os critérios especificados na regra de alerta são cumpridos. Gravidade pode variar entre 0 e 4.

**Ação** – uma ação específica tomada quando o alerta é acionado. Para obter mais informações, consulte [grupos de ação](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>O que pode alertar

Pode alertar relativamente métricas e registos, conforme descrito em [origens de dados de monitorização](../../azure-monitor/platform/data-sources-reference.md). Estes incluem, mas não sejam limitam a:
- Valores de métrica
- Consultas de pesquisa de registo
- Eventos de registo de atividade
- Estado de funcionamento da plataforma Azure subjacente
- Testes de disponibilidade do site

Anteriormente, métricas, Application Insights, do Log Analytics e o estado de funcionamento do serviço Azure Monitor tinham capacidades de alertas separadas. Ao longo do tempo, o Azure melhorada e combinados a interface do usuário e os métodos diferentes de alertas. Esta consolidação ainda está em processo. Como resultado, ainda existem algumas capacidades de alertas não ainda no novo sistema de alertas.  

| **Origem de monitor** | **Tipo de sinal**  | **Descrição** | 
|-------------|----------------|-------------|
| Estado de funcionamento do serviço | Registo de atividades  | Não suportado. Ver [criar alertas do registo de atividade nas notificações do serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Testes de disponibilidade da Web | Não suportado. Ver [alertas de teste Web](../../azure-monitor/app/monitor-web-app-availability.md). Disponível para qualquer Web site que foi instrumentada para enviar dados para o Application Insights. Receba uma notificação quando a disponibilidade ou a capacidade de resposta de um Web site é inferior expectativas. |

## <a name="manage-alerts"></a>Gerir alertas
Pode definir o estado de um alerta para especificar onde se encontra no processo de resolução. Quando os critérios especificados na regra de alerta são cumpridos, um alerta é criado ou disparado, ele tem o estado *New*. Pode alterar o estado quando reconhece um alerta e quando fechá-lo. Todas as alterações de estado são armazenadas no histórico do alerta.

São suportados os seguintes Estados de alerta.

| Estado | Descrição |
|:---|:---|
| Novo | O problema apenas foi detetado e ainda não foi revisto. |
| Confirmado | Um administrador tiver revisto o alerta e começou a trabalhar no mesmo. |
| Fechado | O problema foi resolvido. Após um alerta ter sido fechado, pode abri-lo novamente ao alterá-la para outro Estado. |

**Estado de alerta** é diferente e independente do **monitorizar condição**. Estado de alerta estiver definido pelo utilizador. Condição do monitor está definida pelo sistema. Quando um alerta é acionado, a condição do monitor do alerta estiver definida como *disparado*. Quando a condição subjacente que causou o alerta acionar limpa, a condição do monitor está definida como *resolvido*. O estado do alerta não é alterado até que o utilizador o altere. Saiba mais [como alterar o estado dos seus alertas e grupos inteligentes](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Grupos inteligentes 
Grupos inteligentes estão em pré-visualização. 

Os grupos de inteligentes são as agregações de alertas com base em algoritmos de machine learning, que podem ajudar a reduzem o ruído de alerta e ajudam a trouble-shooting. [Saiba mais sobre os grupos inteligente](https://aka.ms/smart-groups) e [como gerir os seus grupos inteligentes](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Experiência de alertas 
A página de alertas padrão fornece um resumo dos alertas que são criados dentro de uma janela de tempo específico. Apresenta o total de alertas para cada gravidade com as colunas que identificam o número total de alertas em cada Estado para cada gravidade. Selecione qualquer uma das gravidades para abrir o [todos os alertas](#all-alerts-page) página filtrada por esse gravidade.

Não mostrar ou controlar mais antiga [alertas clássicos](#classic-alerts). Pode alterar as subscrições ou filtrar parâmetros para atualizar a página. 

![Página de alertas](media/alerts-overview/alerts-page.png)

Pode filtrar esta vista ao selecionar os valores nos menus de lista pendente na parte superior da página.

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione até cinco subscrições do Azure. Apenas os alertas nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um grupo de recursos. Apenas os alertas com destinos no grupo de recursos selecionado estão incluídos na vista. |
| Intervalo de tempo | Apenas os alertas acionados dentro da janela de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, últimas 24 horas, últimos 7 dias e os últimos 30 dias. |

Selecione os seguintes valores na parte superior da página de alertas para abrir outra página.

| Value | Descrição |
|:---|:---|
| Alertas totais | O número total de alertas que correspondam aos critérios selecionados. Selecione este valor para abrir a vista de todos os alertas com nenhum filtro. |
| Grupos inteligentes | O número total de grupos inteligentes que foram criadas a partir os alertas que correspondam aos critérios selecionados. Selecione este valor para abrir a lista de grupos inteligente na vista de todos os alertas.
| Regras de alertas totais | O número total de regras de alerta do grupo de recursos e subscrição selecionados. Selecione este valor para abrir a vista de regras filtrado com base na subscrição selecionada e o grupo de recursos.


## <a name="manage-alert-rules"></a>Gere regras do alerta
Clique em **gerir regras de alerta** para mostrar o **regras** página. **Regras** é um único local para gerir todas as regras de alerta nas suas subscrições do Azure. Ele apresenta uma lista de todas as regras de alerta e pode ser ordenado com base nos recursos de destino, grupos de recursos, o nome da regra ou estado. Regras de alerta também podem ser editadas, ativadas ou desativadas a partir desta página.  

 ![regras de alertas](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Criar uma regra de alerta
Alertas podem ser criados de forma consistente, independentemente do serviço de monitoramento ou sinalizam tipo. Acionado todos os alertas e detalhes relacionados estão disponíveis na página única.
 
Criar uma nova regra de alerta com os seguintes três passos:
1. Escolha o _destino_ para o alerta.
1. Selecione o _sinal_ partir os sinais disponíveis para o destino.
1. Especifique a _lógica_ para ser aplicadas a dados a partir do sinal.
 
Este processo de criação simplificado já não necessita de saber a origem de monitorização ou sinais que são suportados antes de selecionar um recurso do Azure. A lista de sinais disponíveis automaticamente é filtrada com base no recurso de destino que selecionou. Também se baseia no que visam, é guiado pelos definir a lógica de regra de alerta automaticamente.  

Pode saber mais sobre como criar regras de alerta no [criar, ver e gerir alertas ao utilizar o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Alertas estão disponíveis em todo o Azure vários serviços de monitorização. Para obter informações sobre como e quando usar cada um desses serviços, consulte [aplicações de monitorização do Azure e recursos](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Página de todos os alertas 
Clique no Total de alertas para ver a página de todos os alertas. Aqui pode ver uma lista de alertas que foram criadas dentro da janela de tempo selecionado. Pode ver uma lista dos alertas individuais ou uma lista dos grupos inteligentes que contêm os alertas. Selecione a faixa na parte superior da página para alternar entre modos de exibição.

![Página de todos os alertas](media/alerts-overview/all-alerts-page.png)

Pode filtrar a vista ao selecionar os seguintes valores nos menus de lista pendente na parte superior da página.

| Coluna | Descrição |
|:---|:---|
| Subscrição | Selecione até cinco subscrições do Azure. Apenas os alertas nas subscrições selecionadas estão incluídos na vista. |
| Grupo de recursos | Selecione um grupo de recursos. Apenas os alertas com destinos no grupo de recursos selecionado estão incluídos na vista. |
| Tipo de recurso | Selecione um ou mais tipos de recursos. Apenas os alertas com destinos do tipo selecionado estão incluídos na vista. Esta coluna só está disponível depois de um grupo de recursos foi especificado. |
| Recurso | Selecione um recurso. Apenas alertas com esse recurso como um destino estão incluídas na vista. Esta coluna apenas está disponível após foi especificado um tipo de recurso. |
| Gravidade | Selecione uma gravidade de alerta ou selecione *todos os* para incluir alertas de todas as gravidades. |
| Condição do monitor | Selecione uma condição do monitor ou selecione *todos os* para incluir alertas de condições. |
| Estado de alerta | Selecione um Estado de alerta ou selecione *todos os* para incluir a alertas dos Estados. |
| Monitorizar serviço | Selecione um serviço ou selecione *todos os* para incluir todos os serviços. Apenas os alertas criados por regras que utilizam o serviço como um destino estão incluídos. |
| Intervalo de tempo | Apenas os alertas acionados dentro da janela de tempo selecionado estão incluídos na vista. Os valores suportados são a última hora, últimas 24 horas, últimos 7 dias e os últimos 30 dias. |

Selecione **colunas** na parte superior da página para selecionar as colunas a apresentar. 

## <a name="alert-detail-page"></a>Página de detalhes de alerta
A página de detalhes de alerta é apresentada quando seleciona um alerta. Ele fornece detalhes do alerta e permite-lhe alterar o seu estado.

![Detalhes do alerta](media/alerts-overview/alert-detail2.png)

A página de detalhes de alerta inclui as secções seguintes.

| Section | Descrição |
|:---|:---|
| Essentials | Apresenta as propriedades e outras informações significativas sobre o alerta. |
| Histórico | Apresenta uma lista de cada ação tomada pelo alerta e todas as alterações efetuadas ao alerta. Atualmente limitado a alterações de estado. |
| Grupo inteligente | Informações sobre o grupo inteligente o alerta estão incluídas no. O *contagem de alertas* refere-se ao número de alertas que estão incluídos no grupo de inteligente. Inclui outros alertas no mesmo grupo de inteligente que foram criados nos últimos 30 dias, independentemente do filtro de tempo na página de lista de alertas. Selecione um alerta para ver os seus detalhes. |
| Mais detalhes | Mostra informações contextuais para o alerta, o que é normalmente específico para o tipo de origem que criou o alerta de adicionais. |


## <a name="classic-alerts"></a>Alertas clássicos 

O registo de atividade e as métricas de Azure Monitor alertas capacidade antes de Junho de 2018 é chamado de "Alertas (clássico)". 

Para obter mais informações, consulte [alertas clássico](./../../azure-monitor/platform/alerts-classic.overview.md)


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre os grupos inteligente](https://aka.ms/smart-groups)
- [Saiba mais sobre grupos de ação](../../azure-monitor/platform/action-groups.md)
- [Gestão das suas instâncias do alerta no Azure](https://aka.ms/managing-alert-instances)
- [Gerir grupos inteligentes](https://aka.ms/managing-smart-groups)







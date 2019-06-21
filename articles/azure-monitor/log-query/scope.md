---
title: Inicie o âmbito de consulta do Log Analytics do Azure Monitor | Documentos da Microsoft
description: Descreve o intervalo de âmbito e tempo para uma consulta de registo no Log Analytics do Azure Monitor.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297282"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Inicie o intervalo de âmbito e tempo de consulta do Log Analytics do Azure Monitor
Quando executa uma [consulta de registo](log-query-overview.md) na [Log Analytics no portal do Azure](get-started-portal.md), o conjunto de dados avaliados pela consulta depende do âmbito e o intervalo de tempo que selecionar. Este artigo descreve o intervalo de âmbito e tempo e como pode definir cada consoante os seus requisitos. Também descreve o comportamento de diferentes tipos de âmbitos.


## <a name="query-scope"></a>Âmbito de consulta
O âmbito de consulta define os registos que são avaliados pela consulta. Normalmente, isto irá incluir todos os registos num único espaço de trabalho do Log Analytics ou aplicação do Application Insights. Também o log Analytics permite-lhe definir um âmbito para um determinado recurso do Azure monitorizado. Isso permite que um proprietário do recurso para se centrar apenas nos seus dados, mesmo que esse recurso grava em várias áreas de trabalho.

O âmbito é sempre apresentado na parte superior esquerda da janela do Log Analytics. Um ícone indica se o âmbito é uma área de trabalho do Log Analytics ou de um aplicativo do Application Insights. Nenhum ícone indica que outro recurso do Azure.

![Scope](media/scope/scope.png)

O escopo é determinado pelo método que utilizar para iniciar o Log Analytics e, em alguns casos, pode alterar o âmbito ao clicar no mesmo. A tabela seguinte lista os diferentes tipos de âmbito utilizado e detalhes diferentes para cada um.

| Âmbito de consulta | Registos no âmbito | Como selecionar | Alterar âmbito |
|:---|:---|:---|:---|
| Área de trabalho do log Analytics | Todos os registos na área de trabalho do Log Analytics. | Selecione **registos** partir a **Azure Monitor** menu ou o **áreas de trabalho do Log Analytics** menu.  | Pode alterar o âmbito para qualquer outro tipo de recurso. |
| Aplicação do Application Insights | Todos os registos da aplicação do Application Insights. | Selecione **Analytics** partir **descrição geral** página do Application Insights. | Só pode alterar o âmbito para outro aplicativo do Application Insights. |
| Grupo de recursos | Registos criados por todos os recursos no grupo de recursos. Pode incluir dados de várias áreas de trabalho do Log Analytics. | Selecione **registos** no menu de grupo de recursos. | Não é possível alterar o âmbito.|
| Subscrição | Registos criados por todos os recursos na subscrição. Pode incluir dados de várias áreas de trabalho do Log Analytics. | Selecione **registos** no menu da subscrição.   | Não é possível alterar o âmbito. |
| Outros recursos do Azure | Registos criados pelo recurso. Pode incluir dados de várias áreas de trabalho do Log Analytics.  | Selecione **registos** no menu de recursos.<br>OU<br>Selecione **registos** partir a **Azure Monitor** menu e, em seguida, selecione um novo âmbito. | Só pode alterar o âmbito para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações ao escopo de um recurso

Quando o âmbito de consulta é uma área de trabalho do Log Analytics ou um aplicativo do Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. Quando o escopo a um recurso no entanto, as seguintes opções no portal não está disponível porque eles são associados a um único espaço de trabalho ou aplicação:

- Guardar
- Explorador de consultas
- Nova regra de alerta

Não é possível utilizar os seguintes comandos numa consulta quando confinadas a um recurso, uma vez que o âmbito da consulta já incluirá todas as áreas de trabalho com dados para esse recurso ou o conjunto de recursos:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>Intervalo de tempo
O intervalo de tempo Especifica o conjunto de registos que são avaliadas para a consulta com base em quando o registo foi criado. Isso é definido por uma propriedade padrão em todos os registos na área de trabalho ou aplicativo conforme especificado na tabela seguinte.

| Location | Propriedade |
|:---|:---|
| Área de trabalho do log Analytics          | TimeGenerated |
| Aplicação do Application Insights | timestamp     |

Defina o intervalo de tempo ao selecioná-la a partir do Seletor de hora na parte superior da janela do Log Analytics.  Pode selecionar um período predefinido ou selecione **personalizado** para especificar um intervalo de tempo específico.

![Selecionador de hora](media/scope/time-picker.png)

Se definir um filtro na consulta que usa a propriedade de fuso horário, conforme mostrado na tabela acima, o Seletor de hora é alterado para **definir na consulta**, e o selecionador de hora está desabilitado. Neste caso, é mais eficiente para colocar o filtro na parte superior da consulta para que qualquer processamento subsequente só precisa trabalhar com os registos filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se utilizar o [área de trabalho](workspace-expression.md) ou [aplicação](app-expression.md) de comando para recuperar dados de outra área de trabalho ou aplicação, o Seletor de hora pode ter um comportamento diferente. Se o escopo é uma área de trabalho do Log Analytics e utilizar **aplicação**, ou se o escopo é um aplicativo do Application Insights e utilizar **área de trabalho**, em seguida, o Log Analytics talvez não compreendam que a propriedade usada no filtro deve determinar o filtro de tempo.

No exemplo a seguir, o âmbito será definido para uma área de trabalho do Log Analytics.  A consulta usa **área de trabalho** para recuperar dados de outra área de trabalho do Log Analytics. O selecionador de hora é alterado para **definir na consulta** uma vez que ele vê um filtro que utiliza a esperada **TimeGenerated** propriedade.

![Consultar com área de trabalho](media/scope/query-workspace.png)

Se a consulta usa **app** para obter dados a partir de um aplicativo do Application Insights no entanto, o Log Analytics não reconhece o **timestamp** propriedade no filtro e o selecionador de hora permanece inalterada. Neste caso, os dois filtros são aplicados. No exemplo, apenas os registos criados nas últimas 24 horas estão incluídos na consulta, mesmo que especifica sete dias na **onde** cláusula.

![Consultar com a aplicação](media/scope/query-app.png)

## <a name="next-steps"></a>Passos Seguintes

- Percorrer um [tutorial sobre como utilizar o Log Analytics no portal do Azure](get-started-portal.md).
- Percorrer um [tutorial sobre como escrever consultas](get-started-queries.md).
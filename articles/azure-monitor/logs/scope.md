---
title: Âmbito de consulta de registo no Azure Monitor Log Analytics
description: Descreve o alcance e o intervalo de tempo para uma consulta de registo no Azure Monitor Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 43e4e861905352c2818dfb08b8cb442bd70481c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047184"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Log consulta e intervalo de tempo em Azure Monitor Log Analytics
Quando executa uma [consulta de registo](../logs/log-query-overview.md) no Log Analytics no portal [Azure,](../logs/log-analytics-tutorial.md)o conjunto de dados avaliados pela consulta depende do âmbito e do intervalo de tempo que seleciona. Este artigo descreve o âmbito e o intervalo de tempo e como pode definir cada um dependendo dos seus requisitos. Também descreve o comportamento de diferentes tipos de âmbitos.


## <a name="query-scope"></a>Âmbito de consulta
O âmbito de consulta define os registos que são avaliados pela consulta. Isto geralmente incluirá todos os registos num único espaço de trabalho do Log Analytics ou na aplicação Application Insights. O Log Analytics também permite definir uma margem para um determinado recurso Azure monitorizado. Isto permite que um proprietário de recursos se concentre apenas nos seus dados, mesmo que esse recurso escreva para vários espaços de trabalho.

O âmbito é sempre apresentado na parte superior esquerda da janela Log Analytics. Um ícone indica se o âmbito é um espaço de trabalho Log Analytics ou uma aplicação Application Insights. Nenhum ícone indica outro recurso Azure.

![Âmbito exibido no portal](media/scope/scope.png)

O âmbito é determinado pelo método que utiliza para iniciar o Log Analytics e, em alguns casos, pode alterar o âmbito clicando nele. A tabela que se segue lista os diferentes tipos de âmbito utilizados e diferentes detalhes para cada um.

> [!IMPORTANT]
> Se estiver a utilizar uma aplicação baseada no espaço de trabalho no Application Insights, os seus dados são armazenados num espaço de trabalho do Log Analytics com todos os outros dados de registo. Para retrocompatibilidade, obtém-se a experiência clássica de Application Insights quando selecionar a aplicação como âmbito. Para ver estes dados no espaço de trabalho Log Analytics, desapasse o âmbito do espaço de trabalho.

| Âmbito de consulta | Registos no âmbito | Como selecionar | Mudança de Âmbito |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Todos os registos no espaço de trabalho do Log Analytics. | Selecione **Logs** do menu **Azure Monitor** ou do menu **de espaços de trabalho Log Analytics.**  | Pode alterar o âmbito para qualquer outro tipo de recurso. |
| Aplicação de Insights de Aplicação | Todos os registos da aplicação Application Insights. | Selecione **Registos** do menu **'Insights de Aplicação'** para a aplicação. | Só pode alterar o âmbito para outra aplicação Application Insights. |
| Grupo de recursos | Registos criados por todos os recursos do grupo de recursos. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Registos** do menu do grupo de recursos. | Não pode mudar de âmbito.|
| Subscrição | Registos criados por todos os recursos na subscrição. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Registos** do menu de subscrição.   | Não pode mudar de âmbito. |
| Outros recursos Azure | Registos criados pelo recurso. Pode incluir dados de vários espaços de trabalho do Log Analytics.  | Selecione **Registos** do menu de recursos.<br>OR<br>Selecione **Logs** do menu **Azure Monitor** e, em seguida, selecione um novo âmbito. | Só pode alterar o âmbito para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações quando limitadas a um recurso

Quando o âmbito de consulta é um espaço de trabalho log analytics ou uma aplicação Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. No entanto, quando examinados para um recurso, as seguintes opções no portal não estão disponíveis porque estão associadas a um único espaço de trabalho ou aplicação:

- Guardar
- Explorador de consultas
- Nova regra de alerta

Não é possível utilizar os seguintes comandos numa consulta quando se aplica a um recurso, uma vez que o âmbito de consulta já incluirá quaisquer espaços de trabalho com dados para esse recurso ou conjunto de recursos:

- [app](../logs/app-expression.md)
- [espaço de trabalho](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>Limites de âmbito de consulta
Definir o âmbito de um recurso ou conjunto de recursos é uma característica particularmente poderosa do Log Analytics, uma vez que permite consolidar automaticamente os dados distribuídos numa única consulta. Pode afetar significativamente o desempenho, se os dados precisarem de ser recuperados dos espaços de trabalho em várias regiões do Azure.

O Log Analytics ajuda a proteger contra sobrecargas excessivas de consultas que abrangem espaços de trabalho em várias regiões, emitindo um aviso ou erro quando um certo número de regiões estão sendo usadas. A sua consulta receberá um aviso se o âmbito inclui espaços de trabalho em 5 ou mais regiões. ainda vai funcionar, mas pode levar tempo excessivo para ser concluído.

![Aviso de consulta](media/scope/query-warning.png)

A sua consulta será impedida de funcionar se o âmbito incluir espaços de trabalho em 20 ou mais regiões. Neste caso, será solicitado para reduzir o número de regiões de espaço de trabalho e tentar executar a consulta novamente. O recuo mostrará todas as regiões no âmbito da consulta, e deverá reduzir o número de regiões antes de tentar executar a consulta novamente.

![Consulta falhou](media/scope/query-failed.png)


## <a name="time-range"></a>Intervalo de tempo
O intervalo de tempo especifica o conjunto de registos que são avaliados para a consulta com base na altura em que o registo foi criado. Isto é definido pela coluna **TimeGenerated** em cada registo no espaço de trabalho ou aplicação conforme especificado no quadro seguinte. Para uma aplicação clássica de Insights de Aplicação, a coluna **de relógios** é utilizada para o intervalo de tempo.


Desacione o intervalo de tempo selecionando-o a partir do seletor de tempos na parte superior da janela Log Analytics.  Pode selecionar um período predefinido ou selecionar **Custom** para especificar um intervalo de tempo específico.

![Apanhador de tempo](media/scope/time-picker.png)

Se definir um filtro na consulta que utiliza a coluna de tempo padrão, como indicado na tabela acima, o apanhador de tempo muda para **set in consulta**, e o selecionador de tempo está desativado. Neste caso, é mais eficiente colocar o filtro no topo da consulta para que qualquer processamento subsequente apenas precise de funcionar com os registos filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se utilizar o [espaço de trabalho](../logs/workspace-expression.md) ou o comando da [aplicação](../logs/app-expression.md) para recuperar dados de outro espaço de trabalho ou aplicação clássica, o selecionador de tempo pode comportar-se de forma diferente. Se o âmbito for um espaço de trabalho do Log Analytics e utilizar **uma aplicação**, ou se o âmbito for uma aplicação clássica de Insights de Aplicação e utilizar **espaço de trabalho,** então o Log Analytics pode não entender que a coluna utilizada no filtro deve determinar o filtro de tempo.

No exemplo seguinte, o âmbito é definido para um espaço de trabalho Log Analytics.  A consulta utiliza **espaço de trabalho** para obter dados de outro espaço de trabalho do Log Analytics. O apanhador de tempo muda para **Definir em consulta** porque vê um filtro que utiliza a coluna **TimeGenerated** esperada.

![Consulta com espaço de trabalho](media/scope/query-workspace.png)

Se a consulta utilizar a **aplicação** para obter dados de uma aplicação clássica de Insights de Aplicação, o Log Analytics não reconhece a coluna **de relógios** no filtro e o selecionador permanece inalterado. Neste caso, ambos os filtros são aplicados. No exemplo, apenas os registos criados nas últimas 24 horas estão incluídos na consulta, mesmo que especifique 7 dias na cláusula **onde.**

![Consulta com app](media/scope/query-app.png)

## <a name="next-steps"></a>Passos seguintes

- Caminhe através de um [tutorial sobre a utilização do Log Analytics no portal Azure](../logs/log-analytics-tutorial.md).
- Caminhe por um [tutorial sobre consultas de escrita.](../logs/get-started-queries.md)
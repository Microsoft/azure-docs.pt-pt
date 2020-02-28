---
title: Âmbito de consulta de log no Azure Monitor Log Analytics  Microsoft Docs
description: Descreve o intervalo de alcance e tempo para uma consulta de log no Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660314"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Âmbito de consulta de log e intervalo de tempo no Azure Monitor Log Analytics
Quando executa uma consulta de [log](log-query-overview.md) no [Log Analytics no portal Azure,](get-started-portal.md)o conjunto de dados avaliados pela consulta depende do âmbito e do intervalo de tempo que seleciona. Este artigo descreve o alcance e o intervalo de tempo e como pode definir cada um dependendo dos seus requisitos. Também descreve o comportamento de diferentes tipos de âmbitos.


## <a name="query-scope"></a>Âmbito de consulta
O âmbito de consulta define os registos que são avaliados pela consulta. Isto geralmente incluirá todos os registos de um único espaço de trabalho log analytics ou aplicação De Insights de Aplicação. O Log Analytics também permite definir uma margem para um recurso Azure monitorizado específico. Isto permite que um proprietário de recursos se concentre apenas nos seus dados, mesmo que esse recurso escreva para vários espaços de trabalho.

O âmbito é sempre exibido na parte superior esquerda da janela Log Analytics. Um ícone indica se o âmbito é um espaço de trabalho log Analytics ou uma aplicação De insights de aplicação. Nenhum ícone indica outro recurso Azure.

![Âmbito](media/scope/scope.png)

O âmbito é determinado pelo método que utiliza para iniciar o Log Analytics e, em alguns casos, pode alterar o âmbito clicando nele. A tabela seguinte enumera os diferentes tipos de âmbito utilizados e diferentes detalhes para cada um.

| Âmbito de consulta | Registos de âmbito | Como selecionar | Alteração do Âmbito |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Todos os registos no espaço de trabalho do Log Analytics. | Selecione **Registos** do menu **Do Monitor Azure** ou do menu de espaços de **trabalho Log Analytics.**  | Pode alterar o âmbito para qualquer outro tipo de recurso. |
| Aplicação de Insights de Aplicação | Todos os registos na aplicação Application Insights. | Selecione **Analytics** a partir da página **de visão geral** de Insights de Aplicação. | Só pode alterar o âmbito para outra aplicação de Insights de Aplicação. |
| Grupo de recursos | Registos criados por todos os recursos do grupo de recursos. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Registos** do menu do grupo de recursos. | Não pode mudar o âmbito.|
| Subscrição | Registos criados por todos os recursos na subscrição. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Registos** do menu de subscrição.   | Não pode mudar o âmbito. |
| Outros recursos Azure | Registos criados pelo recurso. Pode incluir dados de vários espaços de trabalho do Log Analytics.  | Selecione **Registos** do menu de recursos.<br>OU<br>Selecione **Registos** do menu **Do Monitor Azure** e, em seguida, selecione um novo âmbito. | Só pode mudar o âmbito para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações quando âmbitoado a um recurso

Quando o âmbito de consulta é um espaço de trabalho log Analytics ou uma aplicação Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. No entanto, quando se aplica misétes para um recurso, as seguintes opções no portal não estão disponíveis porque estão associadas a um único espaço de trabalho ou aplicação:

- Guardar
- Explorador de consulta
- Nova regra de alerta

Não é possível utilizar os seguintes comandos numa consulta quando ser reparado com um recurso, uma vez que o âmbito de consulta já incluirá quaisquer espaços de trabalho com dados para esse recurso ou conjunto de recursos:

- [app](app-expression.md)
- [espaço de trabalho](workspace-expression.md)
 

## <a name="query-limits"></a>Limites de consulta
Pode ter requisitos de negócio para um recurso Azure para escrever dados em vários espaços de trabalho do Log Analytics. O espaço de trabalho não precisa de estar na mesma região que o recurso, e um único espaço de trabalho pode recolher dados de recursos em várias regiões.  

Definir o âmbito para um recurso ou conjunto de recursos é uma característica particularmente poderosa do Log Analytics, uma vez que permite consolidar automaticamente os dados distribuídos numa única consulta. Pode afetar significativamente o desempenho se os dados precisarem de ser recuperados de espaços de trabalho em várias regiões do Azure.

O Log Analytics ajuda a proteger contra sobrecargas excessivas de consultas que abrangem espaços de trabalho em várias regiões, emitindo um aviso ou erro quando um certo número de regiões está sendo usado. A sua consulta receberá um aviso se o âmbito incluir espaços de trabalho em 5 ou mais regiões. ainda vai funcionar, mas pode levar tempo excessivo para completar.

![Aviso de consulta](media/scope/query-warning.png)

A sua consulta será bloqueada se o âmbito incluir espaços de trabalho em 20 ou mais regiões. Neste caso, será solicitado que reduza o número de regiões do espaço de trabalho e tente executar novamente a consulta. O dropdown mostrará todas as regiões no âmbito da consulta, e você deve reduzir o número de regiões antes de tentar executar a consulta novamente.

![A consulta falhou](media/scope/query-failed.png)


## <a name="time-range"></a>Intervalo de tempo
O intervalo de tempo especifica o conjunto de registos que são avaliados para a consulta com base no momento em que o registo foi criado. Isto é definido por uma propriedade padrão em cada registo no espaço de trabalho ou aplicação, conforme especificado na tabela seguinte.

| Localização | Propriedade |
|:---|:---|
| Área de trabalho do Log Analytics          | TimeGenerated |
| Aplicação de Insights de Aplicação | carimbo de data/hora     |

Desmarca o intervalo de tempo selecionando-o a partir do marcador de tempo na parte superior da janela Log Analytics.  Pode selecionar um período predefinido ou selecionar **o Costume** para especificar um intervalo de tempo específico.

![Selecionador de tempo](media/scope/time-picker.png)

Se definir um filtro na consulta que utiliza a propriedade de tempo padrão, como mostrado na tabela acima, o marcador de tempo muda para **set em consulta**, e o marcador de tempo está desativado. Neste caso, é mais eficiente colocar o filtro no topo da consulta para que qualquer processamento subsequente só precise de funcionar com os registos filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se utilizar o espaço de [trabalho](workspace-expression.md) ou o comando da [aplicação](app-expression.md) para recuperar dados de outro espaço de trabalho ou aplicação, o selecionador pode comportar-se de forma diferente. Se o âmbito for um espaço de trabalho do Log Analytics e utilizar a **aplicação**, ou se o âmbito for uma aplicação Deinsights de Aplicação e utilizar o espaço de **trabalho,** então o Log Analytics pode não entender que a propriedade utilizada no filtro deve determinar o filtro de tempo.

No exemplo seguinte, o âmbito é definido para um espaço de trabalho Log Analytics.  A consulta utiliza **espaço de trabalho** para recuperar dados de outro espaço de trabalho do Log Analytics. O apanhador de tempo muda para **Definir em consulta** porque vê um filtro que usa a propriedade **timeGenerated** esperada.

![Consulta com espaço de trabalho](media/scope/query-workspace.png)

Se a consulta utilizar a **app** para recuperar dados de uma aplicação De insights de aplicação, no entanto, o Log Analytics não reconhece a propriedade da **timestamp** no filtro, e o apanhador de tempo permanece inalterado. Neste caso, aplicam-se ambos os filtros. No exemplo, apenas os registos criados nas últimas 24 horas estão incluídos na consulta, mesmo que especifique 7 dias na cláusula **onde.**

![Consulta com app](media/scope/query-app.png)

## <a name="next-steps"></a>Passos seguintes

- Caminhe por um [tutorial sobre a utilização de Log Analytics no portal Azure](get-started-portal.md).
- Caminhe por um [tutorial sobre perguntas de escrita.](get-started-queries.md)

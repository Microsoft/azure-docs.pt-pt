---
title: Escopo de consulta de log em Azure Monitor Log Analytics | Microsoft Docs
description: Descreve o escopo e o intervalo de tempo para uma consulta de log no Azure Monitor Log Analytics.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: bwren
ms.openlocfilehash: e67dcb1236fd5ef113835dfe99de444fc2594481
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405729"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Escopo de consulta de log e intervalo de tempo em Azure Monitor Log Analytics
Quando você executa uma [consulta de log](log-query-overview.md) em [log Analytics no portal do Azure](get-started-portal.md), o conjunto de dados avaliado pela consulta depende do escopo e do intervalo de tempo que você selecionar. Este artigo descreve o escopo e o intervalo de tempo e como você pode definir cada um dependendo de seus requisitos. Ele também descreve o comportamento de diferentes tipos de escopos.


## <a name="query-scope"></a>Âmbito de consulta
O escopo da consulta define os registros que são avaliados pela consulta. Normalmente, isso incluirá todos os registros em um único espaço de trabalho Log Analytics ou Application Insights aplicativo. Log Analytics também permite que você defina um escopo para um recurso do Azure monitorado específico. Isso permite que um proprietário de recurso se concentre apenas em seus dados, mesmo se esse recurso gravar em vários espaços de trabalho.

O escopo é sempre exibido na parte superior esquerda da janela de Log Analytics. Um ícone indica se o escopo é um espaço de trabalho Log Analytics ou um aplicativo Application Insights. Nenhum ícone indica outro recurso do Azure.

![Scope](media/scope/scope.png)

O escopo é determinado pelo método usado para iniciar Log Analytics e, em alguns casos, você pode alterar o escopo clicando nele. A tabela a seguir lista os diferentes tipos de escopo usados e detalhes diferentes para cada um.

| Âmbito de consulta | Registros no escopo | Como selecionar | Alterando o escopo |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Todos os registros no espaço de trabalho Log Analytics. | Selecione **logs** no menu **Azure monitor** ou no menu **log Analytics espaços de trabalho** .  | Pode alterar o escopo para qualquer outro tipo de recurso. |
| Application Insights aplicativo | Todos os registros no aplicativo Application Insights. | Selecione **análise** na página **visão geral** do Application insights. | Só é possível alterar o escopo para outro aplicativo Application Insights. |
| Resource group | Registros criados por todos os recursos no grupo de recursos. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **logs** no menu grupo de recursos. | Não é possível alterar o escopo.|
| Subscription | Registros criados por todos os recursos na assinatura. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **logs** no menu assinatura.   | Não é possível alterar o escopo. |
| Outros recursos do Azure | Registros criados pelo recurso. Pode incluir dados de vários espaços de trabalho do Log Analytics.  | Selecione **logs** no menu de recursos.<br>OU<br>Selecione **logs** no menu **Azure monitor** e, em seguida, selecione um novo escopo. | Só é possível alterar o escopo para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações no escopo de um recurso

Quando o escopo da consulta é um espaço de trabalho Log Analytics ou um aplicativo Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. Quando o escopo é um recurso, as seguintes opções no portal não estão disponíveis porque estão associadas a um único espaço de trabalho ou aplicativo:

- Guardar
- Explorador de consultas
- Nova regra de alerta

Você não pode usar os comandos a seguir em uma consulta quando definido como escopo para um recurso, pois o escopo da consulta já incluirá quaisquer espaços de trabalho com dados para esse recurso ou conjunto de recursos:

- [app](app-expression.md)
- [espaço](workspace-expression.md)
 

## <a name="query-limits"></a>Limites de consulta
Você pode ter requisitos de negócios para que um recurso do Azure grave dados em vários espaços de trabalho do Log Analytics. O espaço de trabalho não precisa estar na mesma região que o recurso, e um único espaço de trabalho pode coletar dados de recursos em uma variedade de regiões.  

Definir o escopo para um recurso ou conjunto de recursos é um recurso particularmente poderoso de Log Analytics, pois ele permite que você consolide automaticamente os dados distribuídos em uma única consulta. Isso pode afetar significativamente o desempenho, embora os dados precisem ser recuperados de espaços de trabalho em várias regiões do Azure.

Log Analytics ajuda a proteger contra sobrecarga excessiva de consultas que abrangem espaços de trabalho em várias regiões emitindo um aviso ou erro quando um determinado número de regiões está sendo usado. Sua consulta receberá um aviso se o escopo incluir espaços de trabalho em 5 ou mais regiões. Ele ainda será executado, mas pode levar muito tempo para ser concluído.

![Aviso de consulta](media/scope/query-warning.png)

Sua consulta será impedida de ser executada se o escopo incluir espaços de trabalho em 20 ou mais regiões. Nesse caso, será solicitado que você reduza o número de regiões do espaço de trabalho e tente executar a consulta novamente. A lista suspensa exibirá todas as regiões no escopo da consulta e você deverá reduzir o número de regiões antes de tentar executar a consulta novamente.

![A consulta falhou](media/scope/query-failed.png)


## <a name="time-range"></a>Intervalo de tempo
O intervalo de tempo especifica o conjunto de registros que são avaliados para a consulta com base em quando o registro foi criado. Isso é definido por uma propriedade padrão em cada registro no espaço de trabalho ou aplicativo, conforme especificado na tabela a seguir.

| Location | Propriedade |
|:---|:---|
| Área de trabalho do Log Analytics          | TimeGenerated |
| Application Insights aplicativo | timestamp     |

Defina o intervalo de tempo selecionando-o no seletor de tempo na parte superior da janela de Log Analytics.  Você pode selecionar um período predefinido ou selecionar **personalizado** para especificar um intervalo de tempo específico.

![Seletor de hora](media/scope/time-picker.png)

Se você definir um filtro na consulta que usa a propriedade hora padrão, conforme mostrado na tabela acima, o seletor de tempo será alterado para **definido em consulta**e o seletor de hora será desabilitado. Nesse caso, é mais eficiente colocar o filtro na parte superior da consulta para que qualquer processamento subsequente só precise trabalhar com os registros filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se você usar o comando de [aplicativo](app-expression.md) ou [espaço de trabalho](workspace-expression.md) para recuperar dados de outro espaço de trabalho ou aplicativo, o seletor de tempo poderá se comportar de forma diferente. Se o escopo for um espaço de trabalho Log Analytics e você usar o **aplicativo**, ou se o escopo for um aplicativo Application insights e você usar o **espaço de trabalho**, log Analytics poderá não entender que a propriedade usada no filtro deve determinar o filtro de tempo.

No exemplo a seguir, o escopo é definido como um espaço de trabalho Log Analytics.  A consulta usa o **espaço de trabalho** para recuperar dados de outro espaço de trabalho log Analytics. O seletor de tempo muda para **definido na consulta** porque vê um filtro que usa a  Propriedade TimeGenerated esperada.

![Consulta com espaço de trabalho](media/scope/query-workspace.png)

Se a consulta usar o **aplicativo** para recuperar dados de um aplicativo Application insights, o log Analytics não reconhecerá a propriedade **timestamp** no filtro e o seletor de tempo permanecerá inalterado. Nesse caso, ambos os filtros são aplicados. No exemplo, somente os registros criados nas últimas 24 horas são incluídos na consulta, mesmo que especifique 7 dias na cláusula **Where** .

![Consultar com o aplicativo](media/scope/query-app.png)

## <a name="next-steps"></a>Passos Seguintes

- Percorra um [tutorial sobre como usar log Analytics no portal do Azure](get-started-portal.md).
- Percorra um [tutorial sobre como escrever consultas](get-started-queries.md).
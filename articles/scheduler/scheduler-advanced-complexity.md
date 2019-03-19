---
title: Criar agendas de tarefas avançadas e recorrências - Azure Scheduler
description: Saiba como criar agendas avançadas e recorrências para as tarefas no agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a413261d251c8dfc1de9209168ee8137b85009f1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860623"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Criar agendas avançadas e recorrências para as tarefas no agendador do Azure

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Dentro de um [agendador do Azure](../scheduler/scheduler-intro.md) tarefa, a agenda é o núcleo que determina quando e como o serviço de Scheduler executa a tarefa. Pode configurar várias agendas recorrentes e de uso individual para uma tarefa com o Scheduler. Agendas de uma única vez execute apenas uma vez numa hora específica e basicamente são recorrente agendas que são executados apenas uma vez. Executam agendas periódicas numa frequência especificada. Com essa flexibilidade, pode utilizar o programador para vários cenários de negócios, por exemplo:

* **Limpar dados regularmente**: Crie uma tarefa diária que elimina todos os tweets com mais de três meses.

* **Arquivar dados**: Crie uma tarefa mensal que pushes de nota fiscal histórico para um serviço de cópia de segurança.

* **Solicitar dados externos**: Crie uma tarefa que é executado a cada 15 minutos e obtém um novo relatório de meteorologia da NOAA.

* **Processar imagens**: Crie uma tarefa de dia da semana que é executado durante o horário de pico e utiliza a informática na cloud para a compressão de imagens carregadas durante o dia.

Este artigo descreve as tarefas de exemplo que pode criar ao utilizar o Scheduler e o [API de REST do agendador do Azure](/rest/api/scheduler)e inclui a definição de JavaScript Object Notation (JSON) para cada agenda. 

## <a name="supported-scenarios"></a>Cenários suportados

Estes exemplos mostram a gama de cenários que suporte o Azure Scheduler e como criar agendas para vários padrões de comportamento, por exemplo:

* Execute uma vez numa data e hora específicas.
* Execute e recorrer um número específico de vezes.
* Executar imediatamente e repetir.
* Executar e repetir cada *n* minutos, horas, dias, semanas ou meses, a partir de um momento específico.
* Execute e recorrer semanalmente ou mensalmente, mas apenas em dias específicos da semana ou em dias específicos do mês.
* Execute e repetir mais de uma vez por um período específico. Por exemplo, todos os meses na última sexta-feira e segunda-feira, ou diariamente às 5:15 e às 17:00: 15.

Este artigo descreve mais tarde estes cenários mais detalhadamente.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Criar agendamento com a REST API

Para criar um agendamento básico com o [API de REST do agendador do Azure](/rest/api/scheduler), siga estes passos:

1. Registar a sua subscrição do Azure com um fornecedor de recursos utilizando o [Registre-se a operação - API de REST do Resource Manager](https://docs.microsoft.com/rest/api/resources/providers). É o nome do fornecedor para o serviço Azure Scheduler **Microsoft.Scheduler**. 

1. Criar uma coleção de tarefas utilizando o [operação criar ou atualizar para coleções de tarefas](https://docs.microsoft.com/rest/api/scheduler/jobcollections) na API de REST do Scheduler. 

1. Criar uma tarefa com o [operação criar ou atualizar para as tarefas](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementos de esquema da tarefa

Esta tabela fornece uma visão geral para os principais elementos JSON que pode utilizar durante a configuração recorrências e agendas de tarefas. 

| Elemento | Necessário | Descrição | 
|---------|----------|-------------|
| **startTime** | Não | Um valor de cadeia em DateTime [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando a tarefa é iniciada pela primeira vez numa programação básica. <p>Para agendamentos complexos, a tarefa não é iniciado antes que **startTime**. | 
| **recurrence** | Não | As regras de periodicidade para quando a tarefa é executada. O **periodicidade** objeto oferece suporte esses elementos: **frequência**, **intervalo**, **agenda**, **contagem**, e **endTime**. <p>Se utilizar o **periodicidade** elemento, também tem de utilizar o **frequência** elemento, enquanto outros **periodicidade** elementos são opcionais. |
| **frequency** | Sim, quando utiliza **periodicidade** | A unidade de tempo entre ocorrências e suporta estes valores: "Minute", "Hour", "Day", "Week", "Mês" e "Ano" | 
| **interval** | Não | Um número inteiro positivo que determina o número de unidades de tempo entre ocorrências com base na **frequência**. <p>Por exemplo, se **intervalo** é 10 e **frequência** for "Week", a tarefa de voltar a ocorrer a cada 10 semanas. <p>Este é o maior número de intervalos para a frequência de cada: <p>-18 meses <br>-semanas 78 <br>-dias 548 <br>-Para horas e minutos, o intervalo é 1 < = <*intervalo*>< = 1000. | 
| **schedule** | Não | Define as alterações para a periodicidade com base no especificado minuto-marcas de, marcas de horas, dias da semana e dias do mês | 
| **count** | Não | Um número inteiro positivo que especifica o número de vezes que a tarefa é executada antes de terminar. <p>Por exemplo, quando uma tarefa diária possui **contagem** defini para o 7 e a data de início é segunda-feira, a tarefa é concluída em execução no Domingo. Se já tiver passado a data de início, a primeira execução é calculada a partir da hora de criação. <p>Sem **endTime** ou **contagem**, o trabalho seja executado infinitamente. Não é possível utilizar ambos **contagem** e **endTime** na mesma tarefa, mas a regra em primeiro lugar é honrada de conclusão. | 
| **endTime** | Não | Um valor de cadeia de caracteres data ou DateTime na [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando deixa da tarefa em execução. Pode definir um valor para **endTime** que está no passado. <p>Sem **endTime** ou **contagem**, o trabalho seja executado infinitamente. Não é possível utilizar ambos **contagem** e **endTime** na mesma tarefa, mas a regra em primeiro lugar é honrada de conclusão. |
|||| 

Por exemplo, este esquema JSON descreve uma programação básica e a periodicidade para uma tarefa: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*As datas e valores de DateTime*

* Incluir apenas a data de datas em tarefas do Scheduler e siga os [especificação ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Datas-horas nas tarefas do Scheduler incluem a data e hora, siga os [especificação ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)e devem para ser UTC de quando não é especificada nenhuma diferença para UTC. 

Para obter mais informações, consulte [conceitos, terminologia e entidades](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Detalhes: startTime

A tabela seguinte descreve como **startTime** controla a forma como uma tarefa é executada:

| startTime | Sem periodicidade | Periodicidade, nenhuma agenda | Periodicidade com agenda |
|-----------|---------------|-------------------------|--------------------------|
| **Sem hora de início** | Execute uma vez imediatamente. | Execute uma vez imediatamente. Executa as execuções posteriores calculadas a partir do último tempo de execução. | Execute uma vez imediatamente. Executa as execuções posteriores com base numa agenda de periodicidade. | 
| **Hora de início no passado** | Execute uma vez imediatamente. | Calcule o futuro primeiro tempo após a hora de início de execução e execute nesse momento. <p>Executa as execuções posteriores calculadas a partir do último tempo de execução. <p>Veja o exemplo abaixo desta tabela. | Iniciar tarefa *não imediatamente a seguir* hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executa as execuções posteriores com base numa agenda de periodicidade. | 
| **Hora de início no futuro ou a hora atual** | Execute uma vez na hora de início especificada. | Execute uma vez na hora de início especificada. <p>Executa as execuções posteriores calculadas a partir do último tempo de execução. | Iniciar tarefa *não imediatamente a seguir* hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executa as execuções posteriores com base numa agenda de periodicidade. |
||||| 

Suponha que neste exemplo, com essas condições: uma hora de início no passado, com periodicidade, mas sem agenda.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* A data e hora atuais é 08 de Abril de 2015 em 1 21 horas.

* A data de início e a hora é 07 de Abril de 2015, às 14:00, que é antes da data e hora atuais.

* A periodicidade é de dois em dois dias.

1. Nestas condições, a primeira execução é 09 de Abril de 2015, às 14:00. 

   Scheduler calcula as ocorrências de execução com base na hora de início, elimina quaisquer instâncias no passado e utiliza a instância seguinte no futuro. 
   Neste caso, **startTime** tenha 07 de Abril de 2015 às 14:00, portanto, a instância seguinte é dois dias a contar dessa hora, que é 09 de Abril de 2015, às 14:00.

   A primeira execução é o mesmo se **startTime** é 2015-04-05 14:00 ou 2015-04-01 14:00. Após a primeira execução, as execuções posteriores são calculadas com base na agenda. 
   
1. Em seguida, siga as execuções por esta ordem: 
   
   1. 2015-04-11 às 14:00
   1. 2015-04-13 às 14:00 
   1. 2015-04-15 às 14:00
   1. e assim por diante...

1. Por fim, quando um trabalho tiver uma agenda, mas não especificadas de horas e minutos, valores padrão, elas as horas e minutos na primeira execução, respectivamente.

<a name="schedule"></a>

## <a name="details-schedule"></a>Detalhes: agenda

Pode usar **agenda** ao *limite* o número de execuções de tarefas. Por exemplo, se uma tarefa com uma **frequência** "Month" tem uma agenda que é executado apenas no dia 31, a tarefa é executada apenas nos meses que têm 31 dias.

Também pode utilizar **agenda** ao *expanda* o número de execuções de tarefas. Por exemplo, se uma tarefa com uma **frequência** "Month" tem uma agenda que é executada nos dias 1 e 2, a tarefa é executada no primeiros e segundo dias do mês em vez de apenas uma vez por mês.

Se especificar mais de um elemento de agendamento, a ordem de avaliação é do maior à mais pequena: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela seguinte descreve os elementos de agenda detalhadamente:

| Nome JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **minutes** |Minutos da hora em que a tarefa é executada. |Uma matriz de inteiros. |
| **hours** |Horas do dia em que a tarefa é executada. |Uma matriz de inteiros. |
| **weekDays** |Dias da semana a tarefa é executada. Pode ser especificado apenas com uma frequência semanal. |Uma matriz de qualquer um dos seguintes valores (tamanho máximo da matriz é 7):<br />-"Segunda-feira"<br />-"Terça-feira"<br />-"Quarta-feira"<br />-"Quinta-feira"<br />-"Sexta-feira"<br />-"Sábado"<br />-"Domingo"<br /><br />Não diferencia maiúsculas de minúsculas. |
| **monthlyOccurrences** |Determina quais os dias do mês a tarefa é executada. Pode ser especificado apenas com uma frequência mensal. |Uma matriz de **monthlyOccurrences** objetos:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dia** é o dia da semana a tarefa é executada. Por exemplo, *{Domingo}* é cada Domingo do mês. Necessário.<br /><br />**ocorrência** é a ocorrência do dia durante o mês. Por exemplo, *{Domingo, -1}* é o último domingo do mês. Opcional. |
| **monthDays** |Dia do mês que a tarefa é executada. Pode ser especificado apenas com uma frequência mensal. |Uma matriz dos seguintes valores:<br />- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Exemplos: Agendas de periodicidade

Os exemplos seguintes mostram as várias agendas de periodicidade. Os exemplos nos concentrar no objeto da agenda e nos subelementos.

Estas agendas partem do princípio de que **intervalo** está definido como 1\. Os exemplos também presumem a correto **frequência** valores para os valores na **agenda**. Por exemplo, é possível utilizar um **frequência** "Day" e têm um **monthDays** modificação na **agenda**. Descrevemos essas restrições anteriormente neste artigo.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` |Execute às 05:00 todos os dias.<br /><br />Scheduler corresponde a cada valor em "horas", com cada valor em "minutos", um por um, para criar uma lista de todas as vezes em que a tarefa é executada. |
| `{"minutes":[15], "hours":[5]}` |Executar todos os dias às 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Executar todos os dias às 5:15 e 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Executar todos os dias às 5:15, 5:45, 17:15 e 17:45. |
| `{"minutes":[0,15,30,45]}` |Executar de 15 em 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Executar hora a hora.<br /><br />Esta tarefa é executada a cada hora. O minuto é controlado pelo valor para **startTime**, se for especificado. Se nenhum **startTime** valor for especificado, o minuto é controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (que se aplicar) for 12:25, a tarefa é executada em 00:25, 01:25, 02:25,..., 23:25.<br /><br />A agenda é o mesmo que uma tarefa com uma **frequência** "Hour", uma **intervalo** 1 e zero **agenda** valor. A diferença é que pode utilizar este agendamento com diferentes **frequência** e **intervalo** valores para criar outras tarefas. Por exemplo, se **frequência** é a "month", a agenda é executada apenas uma vez por mês em vez de todos os dias (se **frequência** é igual a "day"). |
| `{minutes:[0]}` |Executar de hora a hora, à hora certa.<br /><br />Esta tarefa também é executada a cada hora, mas à hora certa (12 AM, AM de 1, 2 AM e assim por diante). Esta agenda é o mesmo que uma tarefa com uma **frequência** "Hour", uma **startTime** valor de zero minutos e nenhuma **agenda**, se a frequência é igual a "day". No entanto, se o **frequência** é "igual a week" ou "month", a agenda é executada apenas um dia, semana ou um dia por mês, respectivamente. |
| `{"minutes":[15]}` |Execute de 15 minutos após a hora a cada hora.<br /><br />É executada a cada hora, começando 00:15 AM, 1:15 AM, 2:15 AM, e assim por diante. Ele termina às 23:00: 15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Execute às 17:00 à Sábado todas as semanas. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Execute às 17:00 à segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:15 e 17:45 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Execute às 5 AM e as 17:00 à segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 5:15, 5:45 AM, 5:15 PM e às 17:45 à segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar de 15 em 15 minutos nos dias de semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Execute a cada 15 minutos nos dias de semana, entre as 09:00 e as 4 às 17:45. |
| `{"weekDays":["sunday"]}` |Execute Domingos à hora de início. |
| `{"weekDays":["tuesday", "thursday"]}` |Execute às Terças e Quintas-à hora de início. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Executar às 06:00 do dia 28 de cada mês (pressupondo um **frequência** "Month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Execute às 06:00 no último dia do mês.<br /><br />Se quiser executar uma tarefa no último dia do mês, utilize -1 em vez de dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Execute às 06:00 no primeiro e último dia de cada mês. |
| `{monthDays":[1,-1]}` |Execute no primeiro e último dia de cada mês à hora de início. |
| `{monthDays":[1,14]}` |Execute no primeiro e 14 dias de cada mês à hora de início. |
| `{monthDays":[2]}` |Execute no segundo dia do mês à hora de início. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Execute na primeira sexta-feira de cada mês às 05:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Execute na primeira sexta-feira de cada mês à hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Execute na terceira sexta-feira do fim do mês, todos os meses, à hora de início. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sextas-feiras de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Execute na primeira e última sexta-feira de cada mês à hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Execute na quinta sexta-feira de cada mês à hora de início.<br /><br />Se não houver nenhum quinta sexta-feira num mês, a tarefa não é executado. Considere o uso de -1 em vez de 5 na ocorrência se quiser executar a tarefa ocorrendo última sexta-feira do mês. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Executar de 15 em 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês. |

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

---
title: Criar agendas e recorrências de trabalhos avançadas-Agendador do Azure
description: Saiba como criar agendas e recorrências avançadas para trabalhos no Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 386284543cd8fb00cc49fea9a29d9eaee4ca4963
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300973"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Criar agendas e recorrências avançadas para trabalhos no Agendador do Azure

> [!IMPORTANT]
> O [aplicativo lógico do Azure](../logic-apps/logic-apps-overview.md) está substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível.

Dentro de um trabalho [do Agendador do Azure](../scheduler/scheduler-intro.md) , o agendamento é o núcleo que determina quando e como o serviço de Agendador executa o trabalho. Você pode configurar várias agendas de uso único e recorrente para um trabalho com o Agendador. Os agendamentos únicos são executados apenas uma vez em um horário especificado e são basicamente agendamentos recorrentes que são executados apenas uma vez. Agendamentos recorrentes são executados em uma frequência especificada. Com essa flexibilidade, você pode usar o Agendador para vários cenários de negócios, por exemplo:

* **Limpar os dados regularmente**: Crie um trabalho diário que exclua todos os tweets com mais de três meses.

* **Dados de arquivamento**: Crie um trabalho mensal que envia o histórico de faturas para um serviço de backup.

* **Solicitar dados externos**: Crie um trabalho que seja executado a cada 15 minutos e receba um novo relatório meteorológico de NOAA.

* **Processar imagens**: Crie um trabalho de dia da semana que seja executado fora do horário de pico e use a computação em nuvem para compactar imagens carregadas durante o dia.

Este artigo descreve os trabalhos de exemplo que você pode criar usando o Agendador e a [API REST do Agendador do Azure](/rest/api/scheduler)e inclui a definição de JavaScript Object Notation (JSON) para cada agenda. 

## <a name="supported-scenarios"></a>Cenários suportados

Esses exemplos mostram o intervalo de cenários que o Agendador do Azure dá suporte e como criar agendas para vários padrões de comportamento, por exemplo:

* Executar uma vez em uma data e hora específicas.
* Executar e repetir um número específico de vezes.
* Executar imediatamente e repetir.
* Executar e repetir a cada *n* minutos, horas, dias, semanas ou meses, começando em um horário específico.
* Executar e repetir semanalmente ou mensalmente, mas somente em dias específicos da semana ou em dias específicos do mês.
* Executar e repetir mais de uma vez por um período específico. Por exemplo, todos os meses na última sexta-feira e segunda-feira, ou diariamente às 5:15 AM e às 5:15 PM.

Este artigo descreve posteriormente esses cenários mais detalhadamente.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Criar agenda com a API REST

Para criar um agendamento básico com a [API REST do Agendador do Azure](/rest/api/scheduler), siga estas etapas:

1. Registre sua assinatura do Azure com um provedor de recursos usando a [API REST de operação de registro-Gerenciador de recursos](https://docs.microsoft.com/rest/api/resources/providers). O nome do provedor do serviço Agendador do Azure é **Microsoft. Scheduler**. 

1. Crie uma coleção de trabalhos usando a [operação de criação ou atualização para coleções de trabalhos](https://docs.microsoft.com/rest/api/scheduler/jobcollections) na API REST do Agendador. 

1. Crie um trabalho usando a [operação criar ou atualizar para trabalhos](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementos do esquema de trabalho

Esta tabela fornece uma visão geral de alto nível para os principais elementos JSON que você pode usar ao configurar as recorrências e agendas para trabalhos. 

| Elemento | Requerido | Descrição | 
|---------|----------|-------------|
| **startTime** | Não | Um valor de cadeia de caracteres DateTime no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho é iniciado pela primeira vez em um agendamento básico. <p>Para agendamentos complexos, o trabalho não é iniciado antes de **StartTime**. | 
| **recurrence** | Não | As regras de recorrência para quando o trabalho é executado. O objeto de **recorrência** dá suporte a esses elementos: **frequência**, **intervalo**, **agenda**, **contagem**e **EndTime**. <p>Se você usar o elemento **Recurrence** , também deverá usar o elemento **Frequency** , enquanto outros elementos de **recorrência** são opcionais. |
| **frequency** | Sim, quando você usa a **recorrência** | A unidade de tempo entre ocorrências e dá suporte a esses valores: "Minuto", "hora", "dia", "semana", "mês" e "ano" | 
| **interval** | Não | Um inteiro positivo que determina o número de unidades de tempo entre ocorrências com base na **frequência**. <p>Por exemplo, se **Interval** é 10 e **Frequency** é "Week", o trabalho se repete a cada 10 semanas. <p>Aqui está a maior quantidade de intervalos para cada frequência: <p>-18 meses <br>-78 semanas <br>-548 dias <br>-Para horas e minutos, o intervalo é 1 < =*intervalo*de < > < = 1000. | 
| **schedule** | Não | Define as alterações na recorrência com base nas marcas de minuto, hora, dias da semana e dias do mês especificados | 
| **count** | Não | Um inteiro positivo que especifica o número de vezes que o trabalho é executado antes de concluir. <p>Por exemplo, quando um trabalho diário tem **contagem** definida como 7 e a data de início é segunda-feira, o trabalho é concluído em execução no domingo. Se a data de início já tiver passado, a primeira execução será calculada a partir da hora de criação. <p>Sem **EndTime** ou **Count**, o trabalho é executado infinitamente. Você não pode usar **Count** e **EndTime** no mesmo trabalho, mas a regra que termina primeiro é respeitada. | 
| **endTime** | Não | Um valor de cadeia de caracteres de data ou DateTime no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho para a execução. Você pode definir um valor para **EndTime** que está no passado. <p>Sem **EndTime** ou **Count**, o trabalho é executado infinitamente. Você não pode usar **Count** e **EndTime** no mesmo trabalho, mas a regra que termina primeiro é respeitada. |
|||| 

Por exemplo, esse esquema JSON descreve uma agenda básica e uma recorrência para um trabalho: 

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

*Valores de datas e DateTime*

* As datas nos trabalhos do Agendador incluem apenas a data e seguem a [Especificação ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Os horários de datas nos trabalhos do Agendador incluem data e hora, seguem a [Especificação ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)e são considerados UTC quando nenhum deslocamento UTC é especificado. 

Para obter mais informações, consulte [conceitos, terminologia e entidades](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Detalhes: StartTime

Esta tabela descreve como **StartTime** controla a maneira como um trabalho é executado:

| startTime | Sem recorrência | Recorrência, sem agendamento | Periodicidade com agenda |
|-----------|---------------|-------------------------|--------------------------|
| **Nenhuma hora de início** | Execute uma vez imediatamente. | Execute uma vez imediatamente. Executar execuções posteriores calculadas a partir da última hora de execução. | Execute uma vez imediatamente. Executar execuções posteriores com base em uma agenda de recorrência. | 
| **Hora de início no passado** | Execute uma vez imediatamente. | Calcule o primeiro tempo de execução futuro após a hora de início e execute nesse momento. <p>Executar execuções posteriores calculadas a partir da última hora de execução. <p>Consulte o exemplo após esta tabela. | Iniciar o trabalho *não antes* da hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executar execuções posteriores com base em uma agenda de recorrência. | 
| **Hora de início no futuro ou a hora atual** | Executar uma vez na hora de início especificada. | Executar uma vez na hora de início especificada. <p>Executar execuções posteriores calculadas a partir da última hora de execução. | Iniciar o trabalho *não antes* da hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executar execuções posteriores com base em uma agenda de recorrência. |
||||| 

Suponha que você tenha este exemplo com estas condições: uma hora de início no passado com uma recorrência, mas sem agendamento.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* A data e a hora atuais são 08 de abril de 2015 às 1:00 PM.

* A data e a hora de início são 07 de abril de 2015 às 2:00 PM, que é anterior à data e hora atuais.

* A recorrência é a cada dois dias.

1. Sob essas condições, a primeira execução é em 09 de abril de 2015 às 2:00. 

   O Agendador calcula as ocorrências de execução com base na hora de início, descarta todas as instâncias no passado e usa a próxima instância no futuro. 
   Nesse caso, **StartTime** está em 07 de abril de 2015 às 2:00 PM, portanto, a próxima instância é de dois dias a partir desse horário, que é 09 de abril de 2015 às 2:00 PM.

   A primeira execução é a mesma se **StartTime** for 2015-04-05 14:00 ou 2015-04-01 14:00. Após a primeira execução, as execuções posteriores são calculadas com base na agenda. 
   
1. As execuções são seguidas nesta ordem: 
   
   1. 2015-04-11 às 2:00
   1. 2015-04-13 às 2:00 
   1. 2015-04-15 às 2:00
   1. e assim por diante...

1. Por fim, quando um trabalho tem um agendamento, mas não há horas e minutos especificados, esses valores assumem como padrão as horas e os minutos na primeira execução, respectivamente.

<a name="schedule"></a>

## <a name="details-schedule"></a>Detalhes: Agenda

Você pode usar **agendamento** para *limitar* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda que é executada somente no dia 31, o trabalho será executado somente em meses que tenham um dia 31.

Você também pode usar **agendamento** para *expandir* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda que é executada nos dias 1 e 2 do mês, o trabalho será executado no primeiro e no segundo dias do mês, em vez de apenas uma vez por mês.

Se você especificar mais de um elemento Schedule, a ordem de avaliação será do maior para o menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela seguinte descreve os elementos de agenda detalhadamente:

| Nome JSON | Descrição | Valores válidos |
|:--- |:--- |:--- |
| **minutes** |Minutos da hora em que o trabalho é executado. |Uma matriz de inteiros. |
| **hours** |Horas do dia em que o trabalho é executado. |Uma matriz de inteiros. |
| **weekDays** |Dias da semana em que o trabalho é executado. Pode ser especificado somente com uma frequência semanal. |Uma matriz de qualquer um dos seguintes valores (o tamanho máximo da matriz é 7):<br />-"Segunda-feira"<br />-"Terça-feira"<br />-"Quarta-feira"<br />-"Quinta-feira"<br />-"Sexta-feira"<br />-"Sábado"<br />-"Domingo"<br /><br />Não diferencia maiúsculas de minúsculas. |
| **monthlyOccurrences** |Determina em quais dias do mês o trabalho é executado. Pode ser especificado somente com uma frequência mensal. |Uma matriz de objetos **monthlyOccurrences** :<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **Day** é o dia da semana em que o trabalho é executado. Por exemplo, *{domingo}* é todo domingo do mês. Necessário.<br /><br />**ocorrência** é a ocorrência do dia durante o mês. Por exemplo, *{domingo,-1}* é o último domingo do mês. Opcional. |
| **monthDays** |Dia do mês em que o trabalho é executado. Pode ser especificado somente com uma frequência mensal. |Uma matriz dos seguintes valores:<br />- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Exemplos: Agendamentos de recorrência

Os exemplos a seguir mostram vários agendamentos de recorrência. Os exemplos se concentram no objeto Schedule e em seus subelementos.

Esses agendamentos pressupõem que o **intervalo** está definido como 1\. Os exemplos também assumem os valores de **frequência** corretos para os valores na **agenda**. Por exemplo, você não pode usar uma **frequência** de "Day" e ter uma modificação **monthDays** na **agenda**. Descrevemos essas restrições anteriormente neste artigo.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` |Executar às 17:00 todos os dias.<br /><br />O Agendador corresponde a cada valor em "horas" com cada valor em "minutos", um a um, para criar uma lista de todos os horários em que o trabalho é executado. |
| `{"minutes":[15], "hours":[5]}` |Executar todos os dias às 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Executar todos os dias às 5:15 e 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Executar todos os dias às 5:15, 5:45, 17:15 e 17:45. |
| `{"minutes":[0,15,30,45]}` |Executar de 15 em 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Executar hora a hora.<br /><br />Esse trabalho é executado a cada hora. O minuto é controlado pelo valor de **StartTime**, se for especificado. Se nenhum valor de **StartTime** for especificado, o minuto será controlado pela hora de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 12:25 PM, o trabalho será executado às 00:25, 01:25, 02:25,..., 23:25.<br /><br />O agendamento é o mesmo que um trabalho com uma **frequência** de "hora", um **intervalo** de 1 e nenhum valor de **agendamento** . A diferença é que você pode usar essa agenda com diferentes valores de **frequência** e **intervalo** para criar outros trabalhos. Por exemplo, se **Frequency** é "month", a agenda é executada somente uma vez por mês, em vez de todos os dias (se **Frequency** for "Day"). |
| `{minutes:[0]}` |Executar de hora a hora, à hora certa.<br /><br />Esse trabalho também é executado a cada hora, mas na hora (12:00, 9h, 2 AM e assim por diante). Esse agendamento é o mesmo que um trabalho com uma **frequência** de "hora", um valor de **StartTime** de zero minutos e nenhum **agendamento**, se a frequência for "Day". No entanto, se a **frequência** for "Week" ou "month", a agenda executará somente um dia por semana ou um dia por mês, respectivamente. |
| `{"minutes":[15]}` |Execute 15 minutos após a hora a cada hora.<br /><br />Executa a cada hora, começando às 00:15 A.M., 1:15 AM, 2:15 AM e assim por diante. Termina às 11:15 PM. |
| `{"hours":[17], "weekDays":["saturday"]}` |Execute às 17:00 em sábado a cada semana. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Execute às 17:00 na segunda-feira, na quarta-feira e na sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:15 e 17:45 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Execute às 17:00 e às 17:00 na segunda-feira, na quarta-feira e na sexta-feira todas as semanas. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Execute às 5:15, 5:45 AM, 5:15 PM e 5:45 PM na segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar de 15 em 15 minutos nos dias de semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar a cada 15 minutos em dias da semana, entre 9h e 4:45 PM. |
| `{"weekDays":["sunday"]}` |Executar nos domingos na hora de início. |
| `{"weekDays":["tuesday", "thursday"]}` |Execute nas terças e quintas na hora de início. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Execute às 6h no dia 28 de cada mês (supondo uma **frequência** de "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Executar às 6h no último dia do mês.<br /><br />Se você quiser executar um trabalho no último dia de um mês, use-1 em vez do dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Executar às 6h no primeiro e no último dia de cada mês. |
| `{monthDays":[1,-1]}` |Executar no primeiro e no último dia de cada mês na hora de início. |
| `{monthDays":[1,14]}` |Executar no primeiro e no dia 14 de cada mês na hora de início. |
| `{monthDays":[2]}` |Executar no segundo dia do mês na hora de início. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês às 17:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Execute na terceira sexta-feira do final do mês, todos os meses, na hora de início. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sextas-feiras de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e na última sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Executar na quinta sexta-feira de cada mês na hora de início.<br /><br />Se não houver a quinta sexta-feira em um mês, o trabalho não será executado. Você pode considerar o uso de-1 em vez de 5 para a ocorrência se quiser executar o trabalho na última sexta-feira do mês. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Executar de 15 em 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês. |

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

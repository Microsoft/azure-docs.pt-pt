---
title: Construir horários de trabalho avançados e recorrências
description: Saiba como criar horários avançados e recorrências para empregos no Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368184"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Construir horários avançados e recorrências para empregos no Azure Scheduler

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Azure Scheduler, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que estabeleceu no Scheduler, [por favor, migra para a Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rápido possível. 
>
> O programador já não está disponível no portal Azure, mas os cmdlets [REST API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e coleções de emprego.

Dentro de um trabalho [de Azure Scheduler,](../scheduler/scheduler-intro.md) o horário é o núcleo que determina quando e como o serviço Scheduler gere o trabalho. Pode configurar vários horários únicos e recorrentes para um trabalho com o Scheduler. Os horários únicos são executados apenas uma vez por uma hora e são basicamente horários recorrentes que funcionam apenas uma vez. Os horários recorrentes funcionam numa frequência especificada. Com esta flexibilidade, pode utilizar o Scheduler para vários cenários de negócio, por exemplo:

* **Limpar os dados regularmente**: Crie um trabalho diário que elimine todos os tweets com mais de três meses.

* **Dados do** arquivo : Crie um trabalho mensal que empurre o histórico da fatura para um serviço de backup.

* **Solicitar dados externos**: Crie um trabalho que decorda a cada 15 minutos e retire um novo boletim meteorológico da NOAA.

* **Imagens de processo**: Crie um trabalho de dia de semana que decorda durante horas fora do pico e utilize computação em nuvem para comprimir imagens carregadas durante o dia.

Este artigo descreve os trabalhos de exemplo que pode criar utilizando o Scheduler e o API do [Azure Scheduler REST](/rest/api/scheduler), e inclui a definição de Notação de Objetos JavaScript (JSON) para cada programação. 

## <a name="supported-scenarios"></a>Cenários suportados

Estes exemplos mostram a gama de cenários que o Azure Scheduler suporta e como criar horários para vários padrões de comportamento, por exemplo:

* Corra uma vez numa data e hora específicas.
* Corra e repita um número específico de vezes.
* Corra imediatamente e recorda.
* Corra e repita a cada *minuto,* horas, dias, semanas ou meses, começando numa hora específica.
* Corra e repete-se semanalmente ou mensalmente, mas apenas em dias específicos da semana ou em dias específicos do mês.
* Corra e repita mais de uma vez por um período específico. Por exemplo, todos os meses na última sexta-feira e segunda-feira, ou diariamente às 5:15 e às 17:15.

Este artigo descreve mais tarde estes cenários com mais detalhes.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Criar horário com REST API

Para criar um calendário básico com a API do [Azure Scheduler REST,](/rest/api/scheduler)siga estes passos:

1. Registe a sua subscrição Azure com um fornecedor de recursos utilizando a [operação Register - Resource Manager REST API](/rest/api/resources/providers). O nome do fornecedor do serviço Azure Scheduler é **Microsoft.Scheduler**. 

1. Crie uma recolha de emprego utilizando a [operação Criar ou Atualizar para cobranças de emprego](/rest/api/scheduler/jobcollections) na API do Agendador REST. 

1. Crie um trabalho utilizando a [operação Criar ou Atualizar para trabalhos](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementos de esquema de trabalho

Esta tabela fornece uma visão geral de alto nível para os principais elementos JSON que você pode usar ao configurar recorrências e horários para empregos. 

| Elemento | Obrigatório | Descrição | 
|---------|----------|-------------|
| **horário de início** | No | Um valor de cadeia DateTime no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho começa pela primeira vez num horário básico. <p>Para horários complexos, o trabalho começa o mais cedo que **o startTime**. | 
| **recorrência** | No | As regras de recorrência para quando o trabalho funciona. O objeto **de recorrência** suporta estes elementos: **frequência,** **intervalo,** **programação,** **contagem** e fim **de hora**. <p>Se utilizar o elemento **de recorrência,** também deve utilizar o elemento **de frequência,** enquanto outros elementos **de recorrência** são opcionais. |
| **frequência** | Sim, quando se usa **a recorrência** | A unidade de tempo entre ocorrências e apoios a estes valores: "Minuto", "Hora", "Dia", "Semana", "Mês" e "Ano" | 
| **intervalo** | No | Um número inteiro positivo que determina o número de unidades de tempo entre ocorrências com base na **frequência**. <p>Por exemplo, se **o intervalo** for de 10 e **a frequência** for "Semana", o trabalho repete-se a cada 10 semanas. <p>Aqui está o maior número de intervalos para cada frequência: <p>- 18 meses <br>- 78 semanas <br>- 548 dias <br>- Durante horas e minutos, o intervalo é de 1 <= intervalo <*> <* = 1000. | 
| **agendar** | No | Define alterações à recorrência com base nas notas de minutos especificadas, marcas de horas, dias da semana e dias do mês | 
| **count** | No | Um número inteiro positivo que especifica o número de vezes que o trabalho funciona antes de terminar. <p>Por exemplo, quando um trabalho diário tem **a contagem** definida para 7, e a data de início é segunda-feira, o trabalho termina no domingo. Se a data de início já tiver passado, a primeira execução é calculada a partir do tempo de criação. <p>Sem **fim tempo** ou **contagem,** o trabalho funciona infinitamente. Não podes usar a **contagem** e o fim do **Tempo** no mesmo trabalho, mas a regra que termina primeiro é honrada. | 
| **endTime** | No | Um valor de cadeia Data ou DateTime no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica quando o trabalho deixa de funcionar. Pode definir um valor para **o fim do Tempo** que está no passado. <p>Sem **fim tempo** ou **contagem,** o trabalho funciona infinitamente. Não podes usar a **contagem** e o fim do **Tempo** no mesmo trabalho, mas a regra que termina primeiro é honrada. |
|||| 

Por exemplo, este esquema JSON descreve um horário básico e recorrência para um trabalho: 

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

*Datas e valores do Tempo de Data*

* As datas nos trabalhos do Programador incluem apenas a data e seguem a [especificação ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601)

* Os horários nos trabalhos do Programador incluem data e hora, seguir a [especificação ISO 8601,](https://en.wikipedia.org/wiki/ISO_8601)e é assumido como UTC quando não é especificada nenhuma compensação UTC. 

Para mais informações, consulte [Conceitos, terminologia e entidades.](../scheduler/scheduler-concepts-terms.md)

<a name="start-time"></a>

## <a name="details-starttime"></a>Detalhes: startTime

Esta tabela descreve como **o startTime** controla a forma como um trabalho funciona:

| startTime | Sem recorrência | Recorrência, sem horário. | Periodicidade com agenda |
|-----------|---------------|-------------------------|--------------------------|
| **Sem hora de início** | Corra uma vez imediatamente. | Corra uma vez imediatamente. Executar execuções posteriores calculadas a partir do último tempo de execução. | Corra uma vez imediatamente. Executar execuções posteriores com base num calendário de recorrência. | 
| **Hora de início no passado** | Corra uma vez imediatamente. | Calcular o primeiro tempo de execução futuro após a hora de início, e executar nessa altura. <p>Executar execuções posteriores calculadas a partir do último tempo de execução. <p>Veja o exemplo depois desta tabela. | Iniciar o trabalho *o mais cedo que* a hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executar execuções posteriores com base num calendário de recorrência. | 
| **Hora de início no futuro ou na hora atual** | Corra uma vez na hora de início especificada. | Corra uma vez na hora de início especificada. <p>Executar execuções posteriores calculadas a partir do último tempo de execução. | Iniciar o trabalho *o mais cedo que* a hora de início especificada. A primeira ocorrência é baseada na agenda calculada a partir da hora de início. <p>Executar execuções posteriores com base num calendário de recorrência. |
||||| 

Suponha que seja este exemplo com estas condições: uma hora de início no passado com uma recorrência, mas sem horário.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* A data e hora atuais é 08 de abril de 2015 às 13:00.

* A data e hora de início é 07 de abril de 2015 às 14:00, que é antes da data e hora atuais.

* A recorrência é de dois em dois dias.

1. Nestas condições, a primeira execução é no dia 09 de abril de 2015 às 14:00. 

   O programador calcula as ocorrências de execução com base na hora de início, descarta quaisquer instâncias no passado, e usa a próxima instância no futuro. 
   Neste caso, **o startTime** é no dia 07 de abril de 2015 às 14:00, pelo que a próxima instância é a dois dias dessa altura, que é 09 de abril de 2015 às 14:00.

   A primeira execução é a mesma se **startTime** é 2015-04-05 14:00 ou 2015-04-01 14:00. Após a primeira execução, execuções posteriores são calculadas com base no calendário. 
   
1. As execuções seguem-se então por esta ordem: 
   
   1. 2015-04-11 às 14:00
   1. 2015-04-13 às 14:00 
   1. 2015-04-15 às 14:00
   1. E assim por diante...

1. Finalmente, quando um trabalho tem um horário mas não tem horas e minutos especificados, estes valores padrão às horas e minutos da primeira execução, respectivamente.

<a name="schedule"></a>

## <a name="details-schedule"></a>Detalhes: horário

Pode usar **o horário** para *limitar* o número de execuções de empregos. Por exemplo, se um trabalho com uma **frequência** de "mês" tem um horário que funciona apenas no dia 31, o trabalho funciona apenas em meses que têm um 31º dia.

Também pode usar **o horário** para *expandir* o número de execuções de empregos. Por exemplo, se um trabalho com uma **frequência** de "mês" tiver um horário que funciona nos dias 1 e 2 do mês, o trabalho funciona no primeiro e segundo dias do mês em vez de apenas uma vez por mês.

Se especificar mais de um elemento de agenda, a ordem de avaliação é do maior ao menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela seguinte descreve os elementos de agenda detalhadamente:

| Nome JSON | Description | Valores válidos |
|:--- |:--- |:--- |
| **minutos** |Minutos da hora em que o trabalho funciona. |Uma série de inteiros. |
| **horas** |Horas do dia em que o trabalho funciona. |Uma série de inteiros. |
| **weekDays** |Dias da semana o trabalho funciona. Só pode ser especificado com uma frequência semanal. |Uma matriz de qualquer um dos seguintes valores (tamanho máximo de matriz é 7):<br />- "Segunda-feira".<br />- "Terça-feira"<br />- "Quarta-feira"<br />- "Quinta-feira"<br />- "Sexta-feira".<br />- "Sábado"<br />- "Domingo"<br /><br />Não sensível a casos. |
| **monthlyOccurrences** |Determina em que dias do mês o trabalho funciona. Só pode ser especificado com uma frequência mensal. |Uma variedade de **objetos mensais Desocupações:**<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dia** é o dia da semana o trabalho corre. Por exemplo, *{Sunday}* é todos os domingos do mês. Obrigatório.<br /><br />**ocorrência** é a ocorrência do dia durante o mês. Por exemplo,  *{Domingo, -1}* é o último domingo do mês. Opcional. |
| **monthDays** |Dia do mês em que o trabalho funciona. Só pode ser especificado com uma frequência mensal. |Uma matriz dos seguintes valores:<br />- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Exemplos: Horários de recorrência

Os exemplos a seguir mostram vários horários de recorrência. Os exemplos focam-se no objeto do programa e nos seus sub-ements.

Estes horários assumem que **o intervalo** está definido para 1\. Os exemplos assumem também os valores de **frequência** corretos para os valores **previstos.** Por exemplo, não é possível utilizar uma **frequência** de "dia" e ter uma modificação **mensal** no **horário**. Descrevemos estas restrições anteriormente no artigo.

| Exemplo | Descrição |
|:--- |:--- |
| `{"hours":[5]}` |Corra às 5 da manhã todos os dias.<br /><br />O programador corresponde a cada valor em "horas" com cada valor em "minutos", um a um, para criar uma lista de todas as horas em que o trabalho funciona. |
| `{"minutes":[15], "hours":[5]}` |Executar todos os dias às 5:15. |
| `{"minutes":[15], "hours":[5,17]}` |Executar todos os dias às 5:15 e 17:15. |
| `{"minutes":[15,45], "hours":[5,17]}` |Executar todos os dias às 5:15, 5:45, 17:15 e 17:45. |
| `{"minutes":[0,15,30,45]}` |Executar de 15 em 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Executar hora a hora.<br /><br />Este trabalho funciona a cada hora. O minuto é controlado pelo valor para **startTime**, se for especificado. Se não for especificado qualquer valor **startTime,** o minuto é controlado pelo tempo de criação. Por exemplo, se a hora de início ou criação (o que se aplicar) for às 12:25, o trabalho funciona às 00:25, 01:25, 02:25, ..., 23:25.<br /><br />O horário é o mesmo que um trabalho com **frequência** de "hora", um **intervalo** de 1, e sem valor **de agenda.** A diferença é que você pode usar este horário com diferentes valores **de frequência** e **intervalo** para criar outros empregos. Por exemplo, se **a frequência** for "mês", o horário funciona apenas uma vez por mês em vez de cada dia (se **a frequência** for "dia"). |
| `{minutes:[0]}` |Executar de hora a hora, à hora certa.<br /><br />Este trabalho também funciona a cada hora, mas à hora (12:00, 1:00, 2:00, e assim por diante). Este horário é o mesmo que um trabalho com **frequência** de "hora", um valor de início de **tempo** de zero minutos, e sem **horário,** se a frequência for "dia". No entanto, se a **frequência** for "semana" ou "mês", o horário executa apenas um dia por semana ou um dia por mês, respectivamente. |
| `{"minutes":[15]}` |Corra a 15 minutos depois da hora a cada hora.<br /><br />Funciona a cada hora, a partir das 00:15, 1:15 da manhã, 2:15 da manhã, e assim por diante. Termina às 23h15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Corra às 17:00 de sábado todas as semanas. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Corra às 17:00 de segunda, quarta e sexta todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:15 e 17:45 de segunda-feira, quarta-feira e sexta-feira todas as semanas. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Corra às 5:00 e 17:00 às segundas, quartas e sextas todas as semanas. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Corra às 5:15 da manhã, 5:45 da manhã, 17:15, e 17:45 na segunda, quarta e sexta-feira todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar de 15 em 15 minutos nos dias de semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Corra a cada 15 minutos nos dias úteis, entre as 9:00 e as 16:45. |
| `{"weekDays":["sunday"]}` |Corra aos domingos na hora de partida. |
| `{"weekDays":["tuesday", "thursday"]}` |Corra às terças e quintas na hora de início. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Corra às 6 da manhã no 28º dia de cada mês (assumindo uma **frequência** de "mês"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Corra às 6 da manhã do último dia do mês.<br /><br />Se quiser trabalhar no último dia de um mês, use -1 em vez do dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Corra às 6 da manhã no primeiro e último dia de cada mês. |
| `{monthDays":[1,-1]}` |Corra no primeiro e último dia de cada mês na hora de início. |
| `{monthDays":[1,14]}` |Corra no primeiro e 14º dia de cada mês na hora de início. |
| `{monthDays":[2]}` |Corra no segundo dia do mês na hora de início. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Corra na primeira sexta-feira de cada mês às 5 da manhã. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Corra na primeira sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Corra na terceira sexta-feira a partir do final do mês, todos os meses, na hora de início. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sextas-feiras de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Corra na primeira e última sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Corra na quinta sexta-feira de cada mês na hora de início.<br /><br />Se não houver quinta sexta-feira num mês, o trabalho não funciona. Pode considerar usar -1 em vez de 5 para a ocorrência se quiser executar o trabalho na última sexta-feira do mês. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Executar de 15 em 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês. |

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)
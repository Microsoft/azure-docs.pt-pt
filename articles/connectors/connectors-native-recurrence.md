---
title: Agendar tarefas e fluxos de trabalho recorrentes
description: Agendar e executar tarefas automáticas recorrentes e fluxos de trabalho com o gatilho de Recorrência em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3749a7080bf17c020b48ae3ebc3cff3aa998eeef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382298"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência em Azure Logic Apps

Para executar regularmente tarefas, processos ou empregos em horários específicos, pode iniciar o seu fluxo de trabalho de aplicações lógicas com o gatilho de **Recorrência** incorporado, que funciona de forma nativa em Azure Logic Apps. Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir o fluxo de trabalho. Se o gatilho falhar as recorrências por qualquer motivo, por exemplo, devido a perturbações ou fluxos de trabalho desativados, este gatilho não processa as recorrências perdidas, mas reinicia as recorrências no intervalo programado seguinte. Para obter mais informações sobre os gatilhos e ações da Agenda incorporada, consulte [Agenda e execute fluxos automáticos, tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que este gatilho suporta juntamente com recorrências mais avançadas e horários complexos:

* Corra imediatamente e repita cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Comece numa data e hora específicas, depois corra e repita cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Corra e repita uma ou mais vezes por dia, por exemplo, às 8:00 e às 17:00.

* Corra e repita todas as semanas, mas apenas para dias específicos, como sábado e domingo.

* Corra e repita todas as semanas, mas apenas para dias e horários específicos, como de segunda a sexta-feira às 8:00 e 17:00.

Para diferenças entre este gatilho e o gatilho da Janela Deslizante ou para obter mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [Agendar e executar tarefas, processos e fluxos de trabalho automáticos recorrentes com Apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser ativar a sua aplicação lógica e executar apenas uma vez no futuro, consulte [apenas uma vez os trabalhos run .](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Se é novo em aplicações lógicas, aprenda [a criar a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-the-recurrence-trigger"></a>Adicione o gatilho de recorrência

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de o Designer de Aplicações Lógica aparecer, na caixa de pesquisa, insira `recurrence` como filtro. A partir da lista de gatilhos, selecione este gatilho como o primeiro passo no seu fluxo de trabalho de aplicações lógicas: **Recorrência**

   ![Selecione o gatilho "Recorrência"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, desaça estas propriedades para executar o seu fluxo de trabalho todas as semanas.

   ![Definir intervalo e frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Description |
   |----------|-----------|----------|------|-------------|
   | **Intervalo** | `interval` | Yes | Número inteiro | Um inteiro positivo que descreve com que frequência o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses <br>- Semana: 1-71 semanas <br>- Dia: 1-500 dias <br>- Hora: 1-12.000 horas <br>- Minuto: 1-72.000 minutos <br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", então a recorrência é a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana** ou **Mês** |
   ||||||

   > [!IMPORTANT]
   > Se uma recorrência não especificar uma [data e hora](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)de início específicas, a primeira recorrência corre imediatamente quando guarda ou implementa a aplicação lógica, apesar da configuração de recorrência do gatilho. Para evitar este comportamento, forneça uma data de início e hora para quando quiser que a primeira recorrência seja executada.
   >
   > Se uma recorrência não especificar quaisquer outras opções avançadas de agendamento, tais como tempos específicos para executar recorrências futuras, essas recorrências são baseadas no último tempo de execução. Como resultado, os tempos de início para essas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. 
   > Para garantir que a sua aplicação lógica não perca uma recorrência, especialmente quando a frequência é em dias ou mais, experimente estas opções:
   > 
   > * Forneça uma data e hora de início para a recorrência mais os horários específicos para executar recorrências subsequentes utilizando as propriedades denominadas **Nestas horas** e **nestas minutos**, que estão disponíveis apenas para as frequências **dia** e **semana.**
   > 
   > * Utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

1. Para definir opções avançadas de agendamento, abra a nova lista **de parâmetros Adicionar.** Quaisquer opções que selecionar aparecem no gatilho após a seleção.

   ![Opções avançadas de agendamento](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Description |
   |----------|-----------|----------|------|-------------|
   | **Fuso horário** | `timeZone` | Não | String | Aplica-se apenas quando especifica uma hora de início porque este gatilho não aceita [a compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Hora de início** | `startTime` | Não | String | Forneça uma data e hora de início, que tenha um máximo de 49 anos no futuro e deve seguir a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [compensação UTC:](https://en.wikipedia.org/wiki/UTC_offset) <p><p>YYYY-MM-DDThh:mm:mm se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Assim, por exemplo, se você quiser 18 de setembro de 2020 às 14:00, então especifique "2020-09-18T14:00:00" e selecione um fuso horário como o Horário Padrão do Pacífico. Ou, especificar "2020-09-18T14:00:00Z" sem um fuso horário. <p><p>**Importante:** Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente. Se selecionar um valor de fuso horário, não precisa de adicionar um "Z" ao fim do seu valor **de tempo inicial.** Se o fizer, a Logic Apps ignora o valor do fuso horário porque o "Z" significa um formato de tempo UTC. <p><p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. [*Quais são as maneiras que posso usar a data de início e a hora?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **On these days** (Nestes dias) | `weekDays` | No | Matriz de cordas ou cordas | Se selecionar "Week", pode selecionar um ou mais dias quando quiser executar o fluxo de trabalho: **Segunda,** **Terça,** **Quarta,** **Quinta,** **Sexta,** **Sábado,** Sábado e **Domingo** |
   | **At these hours** (A estas horas) | `hours` | No | Conjunto inteiro ou inteiro | Se selecionar "Day" ou "Week", pode selecionar um ou mais inteiros de 0 a 23 como as horas do dia para quando pretender executar o fluxo de trabalho. <p><p>Por exemplo, se especificar "10", "12" e "14", obtém 10:00, 12:00 e 14:00 para as horas do dia, mas os minutos do dia são calculados com base no momento em que a recorrência começa. Para definir minutos específicos do dia, por exemplo, 10:00 AM, 12:00 pm e 14:00, especifique esses valores usando a propriedade chamada **Nestas atas**. |
   | **At these minutes** (A estes minutos) | `minutes` | No | Conjunto inteiro ou inteiro | Se selecionar "Day" ou "Week", pode selecionar um ou mais inteiros de 0 a 59 como minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minutos e usando o exemplo anterior para horas do dia, obtém 10:30 AM, 12:30 pm e 14:30. <p>**Nota:** Por vezes, a marcação de tempo para a execução desencadeada pode variar até 1 minuto a partir da hora programada. Se precisar de passar o relógio exatamente como programado para as ações subsequentes, pode utilizar expressões de modelo para alterar o relógio em conformidade. Para obter mais informações, consulte [funções de data e hora para expressões](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Por exemplo, suponha que hoje é sexta-feira, 4 de setembro de 2020. O seguinte gatilho de Recorrência não dispara mais *cedo* do que a data e hora de início, que é sexta-feira, 18 de setembro de 2020 às 8:00 PST. No entanto, o horário de recorrência está marcado para as 10:30, 12:30 e 14:30 apenas às segundas-feiras. Assim, a primeira vez que o gatilho dispara e cria uma instância lógica de fluxo de aplicações é na segunda-feira às 10:30. Para saber mais sobre como os horários de início funcionam, consulte estes [exemplos de horário de início](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   As próximas escoram às 12:30 e às 14:30 do mesmo dia. Cada recorrência cria a sua própria instância de fluxo de trabalho. Depois disso, toda a agenda repete-se novamente na próxima segunda-feira. [*Quais são outras ocorrências exemplo?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemplo avançado de agendamento](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > O gatilho mostra uma pré-visualização da sua recorrência especificada apenas quando seleciona "Day" ou "Week" como frequência.

1. Agora construa o seu fluxo de trabalho restante com outras ações. Para mais ações que pode adicionar, consulte [Conectores para Aplicações Lógicas Azure.](../connectors/apis-list.md)

## <a name="workflow-definition---recurrence"></a>Definição de fluxo de trabalho - Recorrência

Na definição subjacente do fluxo de trabalho da sua aplicação lógica, que utiliza o JSON, pode ver a definição de [gatilho de Recorrência](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) com as opções que escolheu. Para ver esta definição, na barra de ferramentas do designer, escolha **a vista código**. Para voltar ao designer, escolha na barra de ferramentas do designer, **Designer.**

Este exemplo mostra como uma definição de gatilho de recorrência pode parecer numa definição subjacente de fluxo de trabalho:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Mudança de recorrência do gatilho entre o horário de verão e o horário normal

Os gatilhos incorporados recorrentes honram o horário que definiu, incluindo qualquer fuso horário que especifique. Se não selecionar um fuso horário, o horário de verão (DST) pode afetar quando os gatilhos funcionam, por exemplo, deslocando a hora de início uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina.

Para evitar este turno de modo a que a sua aplicação lógica seja executado na hora de início especificada, certifique-se de que seleciona um fuso horário. Desta forma, o tempo utc para a sua aplicação lógica também muda para contrariar a mudança de tempo sazonal. No entanto, algumas janelas de tempo podem causar problemas quando a hora muda. Para obter mais informações e exemplos, consulte [Recorrência para horário de verão e horário normal](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Passos seguintes

* [Pausa nos fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
* [Conectores para as Logic Apps](../connectors/apis-list.md)

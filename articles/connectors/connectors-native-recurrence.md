---
title: Agendar tarefas e fluxos de trabalho recorrentes
description: Agendar e executar tarefas e fluxos de trabalho automatizados recorrentes com o gatilho de Recorrência em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445865"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência em Aplicações Lógicas Azure

Para executar regularmente tarefas, processos ou empregos em horários específicos, pode iniciar o seu fluxo de trabalho de aplicações lógicas com o gatilho de **recorrência incorporada - Agendar.** Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por qualquer motivo, este gatilho continua a repetir-se no intervalo programado seguinte. Para obter mais informações sobre os gatilhos e ações da Agenda incorporada, consulte [Agendar e executar fluxos automáticos, e de trabalho recorrentes com apps lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que este gatilho suporta juntamente com recorrências mais avançadas e horários complexos:

* Corra imediatamente e repita cada *número n* de segundos, minutos, horas, dias, semanas ou meses.

* Comece numa data e hora específicas, depois corra e repita cada *número n* de segundos, minutos, horas, dias, semanas ou meses.

* Corra e repita uma ou mais vezes por dia, por exemplo, às 8:00 e 17:00.

* Corra e repita todas as semanas, mas apenas para dias específicos, como sábado e domingo.

* Corra e repita todas as semanas, mas apenas para dias e horários específicos, como de segunda a sexta-feira às 8:00 e 17:00.

Para diferenças entre este gatilho e o gatilho da Janela Deslizante ou para mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [Agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicações lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser ativar a sua aplicação lógica e executar apenas uma vez no futuro, consulte o [Run Jobs apenas uma vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Se você é novo em aplicativos lógicos, aprenda [a criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Adicionar gatilho de recorrência

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de a Logic App Designer aparecer, na caixa de pesquisa, introduzir `recurrence` como filtro. A partir da lista de gatilhos, selecione este gatilho como o primeiro passo no fluxo de trabalho da sua aplicação lógica: **Recurrence**

   ![Selecione o gatilho "Recurrence"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, detete estas propriedades para executar o seu fluxo de trabalho todas as semanas.

   ![Intervalo e frequência de conjunto](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Número inteiro | Um inteiro positivo que descreve a frequência com que o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses </br>- Dia: 1-500 dias </br>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", então a recorrência é a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana,** ou **Mês** |
   ||||||

   > [!IMPORTANT]
   > Quando as recorrências não especificam opções avançadas de agendamento, as recorrências futuras são baseadas no último tempo de execução.
   > Os tempos de início destas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. Para garantir que a sua aplicação lógica não perca uma recorrência, especialmente quando a frequência é em dias ou mais, use uma destas opções:
   > 
   > * Dê um tempo de início para a recorrência.
   > 
   > * Especifique as horas e minutos para quando executar a recorrência utilizando as propriedades **At these hours** e At These **Minutes.**
   > 
   > * Utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

1. Para definir opções avançadas de agendamento, abra a **lista adicionar novo parâmetro.** Quaisquer opções que selecionar aparecem no gatilho após a seleção.

   ![Opções avançadas de agendamento](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Time zone** (Fuso horário) | `timeZone` | Não | String | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Start time** (Hora de início) | `startTime` | Não | String | Forneça uma data e hora de início neste formato: <p>YYYY-MM-DDThh:mm:ss se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Tempo Padrão do Pacífico. Ou, especificar "2017-09-18T14:00:00Z" sem fuso horário. <p>**Nota:** Esta hora de início tem um máximo de 49 anos no futuro e deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. [*Quais são as formas de usar a data e a hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **On these days** (Nestes dias) | `weekDays` | Não | String ou string array | Se selecionar "Week", pode selecionar um ou mais dias quando quiser executar o fluxo de trabalho: **segunda,** **terça,** **quarta,** **quinta,** **sexta,** **sábado**e **domingo** |
   | **At these hours** (A estas horas) | `hours` | Não | Matriz inteiro ou inteiro | Se selecionar "Day" ou "Week", pode selecionar um ou mais inteiros de 0 a 23 horas como as horas do dia para quando quiser executar o fluxo de trabalho. <p><p>Por exemplo, se especificar "10", "12" e "14", recebe 10:00, 12:00 e 14 horas para as horas do dia, mas as atas do dia são calculadas com base no momento em que a recorrência começa. Para definir as atas do dia, especifique o valor da propriedade **At these minutes.** |
   | **At these minutes** (A estes minutos) | `minutes` | Não | Matriz inteiro ou inteiro | Se selecionar "Day" ou "Week", pode selecionar um ou mais inteiros de 0 a 59 como os minutos da hora em que pretende executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minutos e usando o exemplo anterior para horas do dia, você recebe 10:30 am, 12:30 pm e 14:30. |
   |||||

   Por exemplo, suponha que hoje é segunda-feira, 4 de setembro de 2017. O seguinte gatilho recorrência não dispara *mais cedo* do que a data e hora de início, que é segunda-feira, 18 de setembro de 2017 às 8:00 PST. No entanto, o horário de recorrência está marcado para as 10h30, 12h30 e apenas às 14h30 às segundas-feiras. Assim, a primeira vez que o gatilho dispara e cria um caso de fluxo de aplicações lógica é às 10:30 da manhã. Para saber mais sobre como funcionam os tempos de início, veja estes exemplos de início de [hora.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)

   As corridas futuras acontecem às 12:30 e às 14:30 no mesmo dia. Cada recorrência cria o seu próprio caso de fluxo de trabalho. Depois disso, toda a agenda repete-se na próxima segunda-feira. [*Quais são outros exemplos de ocorrências?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemplo de agendamento avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O gatilho mostra uma pré-visualização da sua recorrência especificada apenas quando selecionar "Day" ou "Week" como frequência.

1. Agora construa o seu fluxo de trabalho restante com outras ações. Para mais ações que possa adicionar, consulte [Conectores para Aplicações Lógicas Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definição de fluxo de trabalho - Recorrência

Na definição subjacente de fluxo de trabalho da sua aplicação lógica, que utiliza a JSON, pode ver a definição de [gatilho recurrence](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) com as opções que escolheu. Para ver esta definição, na barra de ferramentas de design, escolha a **vista código**. Para voltar ao designer, escolha na barra de ferramentas de design, **Designer.**

Este exemplo mostra como uma definição de gatilho de Recorrência pode parecer numa definição subjacente ao fluxo de trabalho:

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
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Pausa de fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
* [Conectores para as Logic Apps](../connectors/apis-list.md)

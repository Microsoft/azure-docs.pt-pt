---
title: Agendar tarefas periódicas com o acionador de periodicidade - Azure Logic Apps
description: Agendar e executar tarefas automatizadas recorrentes e fluxos de trabalho com o acionador de periodicidade no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297561"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o acionador de periodicidade no Azure Logic Apps

Para executar tarefas, processos ou tarefas regularmente na agenda específica, pode iniciar o fluxo de trabalho de aplicação lógica com o incorporado **periodicidade - agenda** acionador. Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se recorrências seja perdidas por qualquer motivo, este acionador continua recorrentes no próximo intervalo agendado. Para obter mais informações sobre os acionadores de agenda internos e as ações, consulte [agendamento e execução recorrente, automatizado, tarefas e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que suporta, juntamente com recorrências mais avançadas e agendas complexas:

* Executar imediatamente e repetir cada *n* o número de segundos, minutos, horas, dias, semanas ou meses.

* Iniciar uma data e hora específicas, em seguida, executar e repetir cada *n* o número de segundos, minutos, horas, dias, semanas ou meses.

* Execute e repetida numa ou mais vezes por dia, por exemplo, às 8:00 e as 17:00.

* Execute e repetir todas as semanas, mas apenas para dias específicos, como Sábado e Domingo.

* Execute e repetir todas as semanas, mas apenas para dias específicos e horas, por exemplo, de segunda a sexta, às 8:00 e as 17:00.

Para as diferenças entre este acionador e o acionador de janela deslizante ou para obter mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [agendamento e execução recorrente, automatizado tarefas, processos e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser acionar a sua aplicação lógica e executar apenas uma vez no futuro, veja [executar tarefas de uma única vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicações lógicas](../logic-apps/logic-apps-overview.md). Se estiver familiarizado com aplicações lógicas, saiba [como criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Adicionar o acionador de periodicidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de aparecer Estruturador da aplicação lógica, na caixa de pesquisa, introduza "recurrence" como o filtro. Na lista de disparadores, selecione este acionador como a primeira etapa no seu fluxo de trabalho de aplicação lógica: **Periodicidade**

   ![Selecione o acionador "Recurrence"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina estas propriedades para executar o fluxo de trabalho todas as semanas.

   ![Definir intervalo e a frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriedade | Necessário | Nome JSON | Tipo | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | Sim | interval | Integer | Um número inteiro que descreve a frequência com que o fluxo de trabalho é executada com base na frequência. Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", em seguida, a periodicidade é a cada 6 meses. |
   | **Frequência** | Sim | frequency | String | A unidade de tempo para a periodicidade: **Segunda**, **minuto**, **hora**, **dia**, **semana**, ou **mês** |
   ||||||

   Para obter mais opções de agendamento, abra a **adicione o novo parâmetro** lista. 
   As opções que selecionou aparecem no acionador após a seleção.

   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriedade | Necessário | Nome JSON | Tipo | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Fuso horário** | Não | timeZone | String | Aplica-se apenas quando especificar uma hora de início porque este acionador não aceita [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Start time** (Hora de início) | Não | startTime | String | Forneça uma data de início e a hora neste formato: <p>AAAA-MM-ddTHH se selecionar um fuso horário <p>-ou- <p>AAAA-MM-: ssZ se não selecionar um fuso horário <p>Por exemplo, se quiser 18 de Setembro de 2017, às 14:00, em seguida, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como hora padrão do Pacífico. Em alternativa, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** A hora de início tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) na [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, tem de adicionar a letra "Z" no final, sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o acionador não dispara qualquer mais cedo do que a hora de início. [*Quais são as formas que posso usar a data de início e hora?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **On these days** (Nestes dias) | Não | weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se selecionar "Week", pode selecionar um ou mais dias, quando quiser executar o fluxo de trabalho: **Segunda-feira**, **Terça-feira**, **quarta-feira**, **Quinta-feira**, **sexta-feira**, **Sábado**, e **Domingo** |
   | **At these hours** (A estas horas) | Não | hours | Número inteiro ou matriz de números inteiros | Se selecionar "Dia" ou "Week", pode selecionar números inteiros de um ou mais de 0 e 23 horas do dia para que deseja executar o fluxo de trabalho. <p><p>Por exemplo, se especificar "10", "12" e "14", obtém 10 AM e PM de 2 para as horas do dia 12 PM, mas os minutos do dia são calculados com base em quando a periodicidade é iniciado. Para definir os minutos do dia, especifique o valor para o **a estes minutos** propriedade. |
   | **At these minutes** (A estes minutos) | Não | minutes | Número inteiro ou matriz de números inteiros | Se selecionar "Dia" ou "Week", pode selecionar números inteiros de um ou mais de 0 e 59 minutos da hora quando quiser executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minuto e usando o exemplo anterior de horas do dia, obtém 10:30, 12 17:30 e 2 17:30. |
   |||||

   Por exemplo, suponha que hoje é segunda-feira, 4 de Setembro de 2017. O acionador de periodicidade seguinte não dispara *antes* que a data de início e a hora, que é a segunda-feira, 18 de Setembro de 2017 em 8 4:00 PST. No entanto, o agendamento de periodicidade é definido para 10:30, 12 17:30 e 2 17:30, apenas às segundas-feiras. Então, o é a primeira vez que o acionador é acionado e cria uma instância de fluxo de trabalho de aplicação lógica é parte 10:30. Para saber mais sobre como o trabalho de tempos de início, vê-los [iniciar exemplos de tempo](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Execuções futuras acontecem em 12 17:30 e 2 17:30, no mesmo dia. Cada periodicidade cria sua própria instância do fluxo de trabalho. Depois disso, a agenda toda repete tudo sobre novamente próxima segunda-feira. [*Quais são algumas outras ocorrências de exemplo?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemplo de programação avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O acionador mostra uma pré-visualização para a periodicidade especificada só quando selecionar "Dia" ou "Week" como a frequência.

1. Agora crie o seu fluxo de trabalho restante com outras ações. Para obter mais ações que pode adicionar, ver [conectores para o Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definição de fluxo de trabalho - periodicidade

Na definição de fluxo de trabalho subjacentes da sua aplicação lógica, que usa JSON, pode ver o [definição de Acionador de periodicidade](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) com as opções que escolheu. Para ver esta definição, na barra de ferramentas da estruturador, escolha **exibição de código**. Para voltar ao estruturador, escolha na barra de ferramentas designer **Designer**.

Este exemplo mostra a aparência de uma definição de Acionador de periodicidade numa definição de fluxo de trabalho subjacente:

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

## <a name="next-steps"></a>Passos Seguintes

* [Colocar em pausa fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
* [Conectores para o Logic Apps](../connectors/apis-list.md)

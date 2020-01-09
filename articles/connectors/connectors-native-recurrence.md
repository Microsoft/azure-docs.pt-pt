---
title: Agendar tarefas e fluxos de trabalho recorrentes
description: Agendar e executar tarefas e fluxos de trabalho automatizados recorrentes com o gatilho de recorrência nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445865"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência nos aplicativos lógicos do Azure

Para executar tarefas, processos ou trabalhos regularmente em um agendamento específico, você pode iniciar o fluxo de trabalho do aplicativo lógico com o gatilho de **agenda de recorrência** interno. Você pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por algum motivo, esse gatilho continuará recorrente no próximo intervalo agendado. Para obter mais informações sobre os gatilhos e ações de agendamento internos, consulte [agendar e executar tarefas automatizadas e automáticas e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões aos quais esse gatilho dá suporte, juntamente com recorrências mais avançadas e agendas complexas:

* Executar imediatamente e repetir a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Inicie em uma data e hora específicas, em seguida, execute e repita a cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Execute e repita em uma ou mais vezes por dia, por exemplo, às 8:00 AM e 5:00 PM.

* Executar e repetir a cada semana, mas somente para dias específicos, como sábado e domingo.

* Executar e repetir a cada semana, mas apenas para dias e horários específicos, como segunda-feira a sexta-feira às 8:00 e 5:00 PM.

Para obter as diferenças entre esse gatilho e o gatilho de janela deslizante ou para obter mais informações sobre como agendar fluxos de trabalho recorrentes, consulte [agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se você quiser disparar seu aplicativo lógico e executar apenas uma vez no futuro, consulte [executar trabalhos apenas uma vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se você for novo em aplicativos lógicos, saiba [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Adicionar gatilho de recorrência

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois que o designer de aplicativo lógico aparecer, na caixa de pesquisa, insira `recurrence` como filtro. Na lista de gatilhos, selecione este gatilho como a primeira etapa no fluxo de trabalho do aplicativo lógico: **recorrência**

   ![Selecione o gatilho "recorrência"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina essas propriedades para executar o fluxo de trabalho a cada semana.

   ![Definir intervalo e frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propriedade | Nome JSON | Obrigatório | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Número inteiro | Um inteiro positivo que descreve com que frequência o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimo e máximo: <p>-Mês: 1-16 meses </br>-Dia: 1-500 dias </br>-Hora: 1 a 12000 horas </br>-Minuto: 1 a 72000 minutos </br>-Segundo: 1 a 9999999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. |
   | **Frequência** | `frequency` | Sim | Cadeia | A unidade de tempo para a recorrência: **segundo**, **minuto**, **hora**, **dia**, **semana**ou **mês** |
   ||||||

   > [!IMPORTANT]
   > Quando as recorrências não especificam opções de agendamento avançadas, as recorrências futuras são baseadas na última hora de execução.
   > As horas de início para essas recorrências podem ser desligadas devido a fatores como latência durante chamadas de armazenamento. Para certificar-se de que seu aplicativo lógico não perca uma recorrência, especialmente quando a frequência estiver em dias ou mais, use uma destas opções:
   > 
   > * Forneça uma hora de início para a recorrência.
   > 
   > * Especifique as horas e os minutos para quando executar a recorrência usando as propriedades a **seguir horas** e **em minutos** .
   > 
   > * Use o [gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

1. Para definir opções avançadas de agendamento, abra a lista **Adicionar novo parâmetro** . As opções que você selecionar aparecerão no gatilho após a seleção.

   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propriedade | Nome JSON | Obrigatório | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | **Time zone** (Fuso horário) | `timeZone` | Não | Cadeia | Aplica-se somente quando você especifica uma hora de início porque esse gatilho não aceita o [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Start time** (Hora de início) | `startTime` | Não | Cadeia | Forneça uma data e hora de início neste formato: <p>AAAA-MM-DDThh: mm: SS se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh: mm: ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e selecione um fuso horário como hora padrão do Pacífico. Ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, deverá adicionar a letra "Z" no final sem espaços. Esse "Z" refere-se ao [tempo náuticas](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o gatilho não é acionado antes da hora de início. [*Quais são as maneiras como posso usar a data e a hora de início?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **On these days** (Nestes dias) | `weekDays` | Não | Matriz de cadeia de caracteres ou cadeia de caracteres | Se você selecionar "semana", poderá selecionar um ou mais dias quando desejar executar o fluxo de trabalho: **segunda**, **terça**, **quarta**, **quinta**, **sexta**, **sábado**e **domingo** |
   | **At these hours** (A estas horas) | `hours` | Não | Matriz de inteiro ou de inteiro | Se você selecionar "dia" ou "semana", poderá selecionar um ou mais números inteiros de 0 a 23 como as horas do dia para quando você deseja executar o fluxo de trabalho. <p><p>Por exemplo, se você especificar "10", "12" e "14", obterá 10 AM, 12 PM e 2 PM para as horas do dia, mas os minutos do dia serão calculados com base em quando a recorrência for iniciada. Para definir os minutos do dia, especifique o valor para a propriedade a **seguir minutos** . |
   | **At these minutes** (A estes minutos) | `minutes` | Não | Matriz de inteiro ou de inteiro | Se você selecionar "dia" ou "semana", poderá selecionar um ou mais números inteiros de 0 a 59 como os minutos da hora em que deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minuto e usar o exemplo anterior para horas do dia, você recebe 10:30, 12:30 PM e 2:30 PM. |
   |||||

   Por exemplo, suponha que hoje seja segunda-feira, 4 de setembro de 2017. O gatilho de recorrência a seguir não é acionado *antes* da data e hora de início, que é segunda-feira, 18 de setembro de 2017 às 8:00 am PST. No entanto, o agendamento de recorrência é definido para 10:30 A.M., 12:30 PM e 2:30 PM apenas nas segundas-feiras. Então, na primeira vez que o gatilho é acionado e cria uma instância de fluxo de trabalho do aplicativo lógico, às 10:30 AM. Para saber mais sobre como as horas de início funcionam, consulte estes [exemplos de hora de início](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Execuções futuras acontecem às 12:30 PM e 2:30 no mesmo dia. Cada recorrência cria sua própria instância de fluxo de trabalho. Depois disso, toda a agenda se repetirá novamente na próxima segunda-feira. [*Quais são algumas outras ocorrências de exemplo?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exemplo de agendamento avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O gatilho mostra uma visualização para a recorrência especificada somente quando você seleciona "dia" ou "semana" como a frequência.

1. Agora, crie seu fluxo de trabalho restante com outras ações. Para obter mais ações que você pode adicionar, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definição de fluxo de trabalho-recorrência

Na definição de fluxo de trabalho subjacente do aplicativo lógico, que usa JSON, você pode exibir a [definição do gatilho de recorrência](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) com as opções escolhidas. Para exibir essa definição, na barra de ferramentas do designer, escolha **exibição de código**. Para retornar ao designer, escolha na barra de ferramentas do designer, **Designer**.

Este exemplo mostra como uma definição de gatilho de recorrência pode parecer em uma definição de fluxo de trabalho subjacente:

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

* [Pausar fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
* [Conectores para aplicativos lógicos](../connectors/apis-list.md)

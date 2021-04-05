---
title: Agendar tarefas para lidar com dados contíguos
description: Criar e executar tarefas recorrentes que lidam com dados contíguos utilizando janelas deslizantes em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 103805fbf395dc120acc96fbcee273abcf14939d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010423"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Agendar e executar tarefas para dados contíguos utilizando o gatilho da janela deslizante em Azure Logic Apps

Para executar regularmente tarefas, processos ou trabalhos que devem lidar com dados em pedaços contíguos, pode iniciar o seu fluxo de trabalho de aplicação lógica com o gatilho da **Janela Deslizante.** Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir o fluxo de trabalho. Se as recorrências forem perdidas por qualquer motivo, por exemplo, devido a perturbações ou fluxos de trabalho desativados, este gatilho processa as recorrências perdidas. Por exemplo, ao sincronizar dados entre a sua base de dados e o armazenamento de backup, utilize o gatilho da Janela Deslizante para que os dados se sincronizem sem incorrerem em lacunas. Para obter mais informações sobre os gatilhos e ações da Agenda incorporada, consulte [Agenda e execute fluxos automáticos, tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que este gatilho suporta:

* Corra imediatamente e repita cada *n* número de segundos, minutos, horas, dias, semanas ou meses.

* Comece numa data e hora específicas, depois corra e repita cada *n* número de segundos, minutos, horas, dias, semanas ou meses. Com este gatilho, pode especificar uma hora de início no passado, que passa todas as recorrências passadas.

* Atrase cada recorrência por uma duração específica antes de correr.

Para diferenças entre este gatilho e o gatilho de Recorrência ou para mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [Agendar e executar tarefas, processos e fluxos de trabalho automáticos recorrentes com Apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser ativar a sua aplicação lógica e executar apenas uma vez no futuro, consulte [apenas uma vez os trabalhos run .](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Se é novo em aplicações lógicas, aprenda [a criar a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-sliding-window-trigger"></a>Adicione o gatilho da janela deslizante

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de aparecer o Designer de Aplicações Lógicas, na caixa de pesquisa, `sliding window` introduza-se como filtro. A partir da lista de gatilhos, selecione o gatilho **da janela deslizante** como o primeiro passo no fluxo de trabalho da aplicação lógica.

   ![Selecione o gatilho "Janela Deslizante"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, desaça estas propriedades para executar o seu fluxo de trabalho todas as semanas.

   ![Definir intervalo e frequência](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Description |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | `interval` | Yes | Número inteiro | Um inteiro positivo que descreve com que frequência o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>- Mês: 1-16 meses <br>- Semana: 1-71 semanas <br>- Dia: 1-500 dias <br>- Hora: 1-12.000 horas <br>- Minuto: 1-72.000 minutos <br>- Segundo: 1-9.999,999 segundos <p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", então a recorrência é a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana** ou **Mês** |
   ||||||

   ![Opções avançadas de recorrência](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Para obter mais opções de recorrência, abra a nova lista **de parâmetros Adicionar.** Quaisquer opções que selecionar aparecem no gatilho após a seleção.

   | Propriedade | Necessário | Nome JSON | Tipo | Description |
   |----------|----------|-----------|------|-------------|
   | **Atraso** | No | atraso | String | A duração para atrasar cada recorrência utilizando a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso horário** | No | timeZone | String | Aplica-se apenas quando especifica uma hora de início porque este gatilho não aceita [a compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Hora de início** | No | startTime | String | Fornecer uma data e hora de início neste formato: <p>YYYY-MM-DDThh:mm:mm se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Assim, por exemplo, se você quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Horário Padrão do Pacífico. Ou, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** Esta hora de início deve seguir a [especificação de hora de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de hora de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem uma [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para recorrências avançadas, o gatilho não dispara mais cedo do que a hora de início. [*Quais são as maneiras que posso usar a data de início e a hora?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Agora construa o seu fluxo de trabalho restante com outras ações. Para mais ações que pode adicionar, consulte [Conectores para Aplicações Lógicas Azure.](../connectors/apis-list.md)

## <a name="workflow-definition---sliding-window"></a>Definição de fluxo de trabalho - Janela deslizante

Na definição subjacente do fluxo de trabalho da sua aplicação lógica, que utiliza o JSON, pode ver a definição de gatilho da Janela Deslizante com as opções que escolheu. Para ver esta definição, na barra de ferramentas do designer, escolha **a vista código**. Para voltar ao designer, escolha na barra de ferramentas do designer, **Designer.**

Este exemplo mostra como uma definição de gatilho da janela deslizante pode parecer numa definição de fluxo de trabalho subjacente, onde o atraso para cada recorrência é de cinco segundos para uma recorrência de hora em hora:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Atrasar a próxima ação nos fluxos de trabalho](../connectors/connectors-native-delay.md)
* [Conectores para as Logic Apps](../connectors/apis-list.md)

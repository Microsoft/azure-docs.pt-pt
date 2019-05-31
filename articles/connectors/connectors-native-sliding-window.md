---
title: Agendar tarefas periódicas com o acionador de janela deslizante - Azure Logic Apps
description: Agendar e executar tarefas automatizadas recorrentes e fluxos de trabalho com o acionador de janela deslizante no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299506"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o acionador de janela deslizante no Azure Logic Apps

Para executar regularmente tarefas, processos ou tarefas que tem de processar dados em segmentos contínuos, pode iniciar o fluxo de trabalho de aplicação lógica com o **janela deslizante - agenda** acionador. Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se recorrências seja perdidas por algum motivo, este acionador processa esses recorrências em falta. Por exemplo, durante a sincronização de dados entre a base de dados e armazenamento de cópia de segurança, utilize o acionador de janela deslizante, para que os dados obtém sincronizados sem incorrer em lacunas. Para obter mais informações sobre os acionadores de agenda internos e as ações, consulte [agendamento e execução recorrente, automatizado, tarefas e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que oferece suporte a este acionador:

* Executar imediatamente e repetir cada *n* o número de segundos, minutos ou horas.

* Iniciar uma data e hora específicas, em seguida, executar e repetir cada *n* o número de segundos, minutos ou horas. Com este acionador, pode especificar uma hora de início no passado, que é executado todos os anteriores recorrências.

* Atraso cada periodicidade durante um período específico antes de executar.

Para as diferenças entre este acionador e o acionador de periodicidade ou para obter mais informações sobre o agendamento de fluxos de trabalho recorrentes, consulte [agendamento e execução recorrente, automatizado tarefas, processos e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser acionar a sua aplicação lógica e executar apenas uma vez no futuro, veja [executar tarefas de uma única vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicações lógicas](../logic-apps/logic-apps-overview.md). Se estiver familiarizado com aplicações lógicas, saiba [como criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Adicionar o acionador de janela deslizante

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de aparecer Estruturador da aplicação lógica, na caixa de pesquisa, introduza "janela deslizante" como o filtro. Na lista de disparadores, selecione este acionador como a primeira etapa no seu fluxo de trabalho de aplicação lógica: **Janela deslizante**

   ![Selecione o acionador de "Janela deslizante"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina estas propriedades para executar o fluxo de trabalho todas as semanas.

   ![Definir intervalo e a frequência](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriedade | Necessário | Nome JSON | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | Sim | interval | Integer | Um número inteiro que descreve a frequência com que o fluxo de trabalho é executada com base na frequência. Seguem-se os intervalos mínimos e máximo: <p>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Hour", em seguida, a periodicidade é a cada 6 horas. |
   | **Frequência** | Sim | frequency | String | A unidade de tempo para a periodicidade: **Segunda**, **minuto**, ou **hora** |
   ||||||

   ![Opções avançadas de periodicidade](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Para obter mais opções de periodicidade, abra a **adicione o novo parâmetro** lista. 
   As opções que selecionou aparecem no acionador após a seleção.

   | Propriedade | Necessário | Nome JSON | Type | Descrição |
   |----------|----------|-----------|------|-------------|
   | **atraso** | Não | delay | String | A duração para atrasar a cada periodicidade, usando o [especificação de hora ISO 8601 data](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Fuso horário** | Não | timeZone | String | Aplica-se apenas quando especificar uma hora de início porque este acionador não aceita [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Start time** (Hora de início) | Não | startTime | String | Forneça uma data de início e a hora neste formato: <p>AAAA-MM-ddTHH se selecionar um fuso horário <p>-ou- <p>AAAA-MM-: ssZ se não selecionar um fuso horário <p>Por exemplo, se quiser 18 de Setembro de 2017, às 14:00, em seguida, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como hora padrão do Pacífico. Em alternativa, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** A hora de início tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) na [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, tem de adicionar a letra "Z" no final, sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para recorrências avançada, o acionador não dispara qualquer mais cedo do que a hora de início. [*Quais são as formas que posso usar a data de início e hora?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Agora crie o seu fluxo de trabalho restante com outras ações. Para obter mais ações que pode adicionar, ver [conectores para o Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definição de fluxo de trabalho - janela deslizante

Na definição de fluxo de trabalho subjacentes da sua aplicação lógica, que usa JSON, pode ver a definição de Acionador de janela deslizante com as opções que escolheu. Para ver esta definição, na barra de ferramentas da estruturador, escolha **exibição de código**. Para voltar ao estruturador, escolha na barra de ferramentas designer **Designer**.

Este exemplo mostra como pode parecer uma definição de Acionador de janela deslizante numa definição de fluxo de trabalho subjacentes em que o atraso para cada periodicidade é de cinco segundos para uma periodicidade por hora:

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

## <a name="next-steps"></a>Passos Seguintes

* [Atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md)
* [Conectores para o Logic Apps](../connectors/apis-list.md)
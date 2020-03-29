---
title: Agendar tarefas para lidar com dados contíguos
description: Criar e executar tarefas recorrentes que lidam com dados contíguos utilizando janelas deslizantes em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786916"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Agendar e executar tarefas para dados contíguos utilizando o gatilho da janela deslizante em Aplicações lógicas azure

Para executar regularmente tarefas, processos ou trabalhos que devem lidar com dados em pedaços contíguos, pode iniciar o fluxo de trabalho da sua aplicação lógica com o gatilho **da Janela Deslizante.** Pode definir uma data e hora, bem como um fuso horário para iniciar o fluxo de trabalho e uma recorrência para repetir esse fluxo de trabalho. Se as recorrências forem perdidas por qualquer razão, este gatilho processa as recorrências perdidas. Por exemplo, ao sincronizar os dados entre a sua base de dados e o armazenamento de cópia de segurança, utilize o gatilho da Janela Deslizante para que os dados sejam sincronizados sem incorrer em lacunas. Para obter mais informações sobre os gatilhos e ações da Agenda incorporada, consulte [Agendar e executar fluxos automáticos, e de trabalho recorrentes com apps lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns padrões que este gatilho suporta:

* Corra imediatamente e repita cada *número n* de segundos, minutos ou horas.

* Comece numa data e hora específicas, em seguida, corra e repita cada *n* número de segundos, minutos ou horas. Com este gatilho, pode especificar uma hora de início no passado, que corre todas as recorrências passadas.

* Adie cada recorrência por uma duração específica antes de correr.

Para diferenças entre este gatilho e o gatilho de Recorrência ou para mais informações sobre agendamento de fluxos de trabalho recorrentes, consulte [Agendar e executar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicações lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Se quiser ativar a sua aplicação lógica e executar apenas uma vez no futuro, consulte o [Run Jobs apenas uma vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Se você é novo em aplicativos lógicos, aprenda [a criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-sliding-window-trigger"></a>Adicionar gatilho de janela deslizante

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois de a Logic App Designer aparecer, na caixa de pesquisa, introduzir a "janela deslizante" como filtro. A partir da lista de gatilhos, selecione este gatilho como o primeiro passo no fluxo de trabalho da sua aplicação lógica: **Janela deslizante**

   ![Selecione o gatilho "Janela deslizante"](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Defina o intervalo e a frequência da periodicidade. Neste exemplo, detete estas propriedades para executar o seu fluxo de trabalho todas as semanas.

   ![Intervalo e frequência de conjunto](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Propriedade | Necessário | Nome JSON | Tipo | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | Sim | intervalo | Número inteiro | Um inteiro positivo que descreve a frequência com que o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p>- Hora: 1-12.000 horas </br>- Minuto: 1-72.000 minutos </br>- Segundo: 1-9.999,999 segundos<p>Por exemplo, se o intervalo for 6, e a frequência for "Hora", então a recorrência é a cada 6 horas. |
   | **Frequência** | Sim | frequência | Cadeia | A unidade de tempo para a recorrência: **Segundo,** **Minuto**ou **Hora** |
   ||||||

   ![Opções avançadas de recorrência](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Para mais opções de recorrência, abra a **lista adicionar novo parâmetro.** 
   Quaisquer opções que selecionar aparecem no gatilho após a seleção.

   | Propriedade | Necessário | Nome JSON | Tipo | Descrição |
   |----------|----------|-----------|------|-------------|
   | **Atraso** | Não | atraso | Cadeia | A duração para atrasar cada recorrência utilizando a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Time zone** (Fuso horário) | Não | timeZone | Cadeia | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Start time** (Hora de início) | Não | startTime | Cadeia | Forneça uma data e hora de início neste formato: <p>YYYY-MM-DDThh:mm:ss se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Tempo Padrão do Pacífico. Ou, especificar "2017-09-18T14:00:00Z" sem fuso horário. <p>**Nota:** Esta hora de início deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para recorrências avançadas, o gatilho não dispara tão cedo quanto a hora de início. [*Quais são as formas de usar a data e a hora de início?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Agora construa o seu fluxo de trabalho restante com outras ações. Para mais ações que possa adicionar, consulte [Conectores para Aplicações Lógicas Azure](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Definição de fluxo de trabalho - Janela deslizante

Na definição subjacente de fluxo de trabalho da sua aplicação lógica, que utiliza a JSON, pode ver a definição de gatilho da Janela Deslizante com as opções que escolheu. Para ver esta definição, na barra de ferramentas de design, escolha a **vista código**. Para voltar ao designer, escolha na barra de ferramentas de design, **Designer.**

Este exemplo mostra como uma definição de gatilho de janela deslizante pode parecer numa definição subjacente de fluxo de trabalho onde o atraso para cada recorrência é de cinco segundos para uma recorrência horária:

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
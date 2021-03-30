---
title: Atrasar a próxima ação nos fluxos de trabalho
description: Aguarde para executar a próxima ação em fluxos de trabalho de aplicações lógicas usando o atraso ou atraso até ações em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74787341"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Atrasar a execução da próxima ação em Azure Logic Apps

Para que a sua aplicação lógica aguarde um tempo antes de executar a próxima ação, pode adicionar a ação incorporada **Delay - Agendar** antes de uma ação no fluxo de trabalho da sua aplicação lógica. Ou, pode adicionar o atraso incorporado **até - Agendar** a ação para aguardar até uma data e hora específicas antes de executar a próxima ação. Para obter mais informações sobre as ações e gatilhos de Agenda incorporadas, consulte [Agendar e executar fluxos automáticos, tarefas e fluxos de trabalho recorrentes com apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Atraso**: Aguarde o número especificado de unidades de tempo, tais como segundos, minutos, horas, dias, semanas ou meses, antes da próxima ação.

* **Atrase até:** Aguarde até à data e hora especificadas antes da próxima ação.

Aqui estão algumas formas de usar estas ações:

* Aguarde até um dia de semana para enviar uma atualização de estado por e-mail.

* Atrase o seu fluxo de trabalho até que uma chamada HTTP termine antes de retomar e recuperar dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se numa conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Antes de poder utilizar uma ação, a sua aplicação lógica tem primeiro de começar com um gatilho. Pode utilizar o gatilho que quiser e adicionar outras ações antes de adicionar uma ação de atraso. Este tópico usa um gatilho do Office 365 Outlook. Se é novo em aplicações lógicas, aprenda [a criar a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adicione a ação de atraso

1. No Logic App Designer, sob o passo em que pretende adicionar a ação de atraso, escolha **Novo passo**.

   Para adicionar a ação de atraso entre os passos, mova o ponteiro sobre a seta que liga os degraus. Escolha o sinal de mais (+) que aparece e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza "delay" como filtro. Na lista de ações, selecione esta ação: **Atraso**

   ![Adicionar ação de "atraso"](./media/connectors-native-delay/add-delay-action.png)

1. Especifique a quantidade de tempo para esperar antes da próxima ação.

   ![Definir o tempo para o atraso](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Description |
   |----------|-----------|----------|------|-------------|
   | de palavras | count | Yes | Número inteiro | O número de unidades de tempo para atrasar |
   | Unidade | unit | Sim | String | A unidade de tempo, por exemplo: `Second` , , , , `Minute` `Hour` `Day` `Week` ou `Month` |
   ||||||

1. Adicione quaisquer outras ações que queira executar no seu fluxo de trabalho.

1. Quando terminar, guarde a sua aplicação lógica.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Adicione o atraso até a ação

1. No Logic App Designer, sob o passo em que pretende adicionar a ação de atraso, escolha **Novo passo**.

   Para adicionar a ação de atraso entre os passos, mova o ponteiro sobre a seta que liga os degraus. Escolha o sinal de mais (+) que aparece e, em seguida, **selecione Adicione uma ação**.

1. Na caixa de pesquisa, introduza "delay" como filtro. A partir da lista de ações, selecione esta ação: **Atrase até**

   ![Adicione ação "Adiar até"](./media/connectors-native-delay/add-delay-until-action.png)

1. Forneça a data e a hora do fim para quando pretender retomar o fluxo de trabalho.

   ![Especificar o tempo de tempo para quando terminar o atraso](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Description |
   |----------|-----------|----------|------|-------------|
   | CarimboDeDataEHora | carimbo de data/hora | Sim | String | A data e hora de fim para retomar o fluxo de trabalho utilizando este formato: <p>YYYY-MM-DDThh:mm:ssZ <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, especifique "2017-09-18T14:00:00Z". <p>**Nota:** Este formato de tempo deve seguir a [especificação de hora de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de hora de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem uma [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Sem fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente. |
   ||||||

1. Adicione quaisquer outras ações que queira executar no seu fluxo de trabalho.

1. Quando terminar, guarde a sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

* [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md)
* [Conectores para as Logic Apps](../connectors/apis-list.md)
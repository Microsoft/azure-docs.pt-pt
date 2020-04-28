---
title: Atrasar a próxima ação nos fluxos de trabalho
description: Aguarde para executar a próxima ação em fluxos de aplicativos lógicos usando o Atraso ou Atraso até ações em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74787341"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Atraso na próxima ação nas Aplicações Lógicas Do Azure

Para que a sua aplicação lógica aguarde uma quantidade de tempo antes de executar a próxima ação, pode adicionar a ação de Atraso incorporado **- Agendar** antes de uma ação no fluxo de trabalho da sua aplicação lógica. Ou, pode adicionar o Atraso incorporado **até - Agendar** a ação para esperar até uma data e hora específicas antes de executar a próxima ação. Para obter mais informações sobre as ações e gatilhos incorporados, consulte [Agendar e executar fluxos automáticos, e de trabalho recorrentes com apps lógicas azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Atraso**: Aguarde o número especificado de unidades de tempo, tais como segundos, minutos, horas, dias, semanas ou meses, antes da próxima ação.

* **Adie até**: Aguarde até que a data e a hora especificadas se prolonguem.

Aqui estão algumas formas exemplos de usar estas ações:

* Aguarde até um dia de semana para enviar uma atualização de estado por e-mail.

* Atrase o seu fluxo de trabalho até que uma chamada http termina antes de retomar e recuperar dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [inscrever-se para uma conta Azure gratuita.](https://azure.microsoft.com/free/)

* Conhecimento básico sobre [aplicações lógicas.](../logic-apps/logic-apps-overview.md) Antes de poder utilizar uma ação, a sua aplicação lógica deve começar primeiro com um gatilho. Pode utilizar qualquer gatilho que quiser e adicionar outras ações antes de adicionar uma ação de atraso. Este tópico usa um gatilho do Office 365 Outlook. Se você é novo em aplicativos lógicos, aprenda [a criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adicione a ação de atraso

1. No Logic App Designer, sob o passo em que pretende adicionar a ação de atraso, escolha **novo passo**.

   Para adicionar a ação de atraso entre passos, mova o ponteiro sobre a seta que liga os passos. Escolha o sinal de mais (+) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "delay" como filtro. Da lista de ações, selecione esta ação: **Atraso**

   ![Adicionar ação "Atrasar"](./media/connectors-native-delay/add-delay-action.png)

1. Especifique o tempo de espera antes da próxima ação.

   ![Definir o tempo para o atraso](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Contagem | count | Sim | Número inteiro | O número de unidades de tempo para atrasar |
   | Unidade | unit | Sim | String | A unidade de tempo, `Second` `Minute`por `Hour` `Day`exemplo: , , , `Week``Month` |
   ||||||

1. Adicione quaisquer outras ações que queira executar no seu fluxo de trabalho.

1. Quando terminar, guarde a sua aplicação lógica.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Adicione a ação de atraso até

1. No Logic App Designer, sob o passo em que pretende adicionar a ação de atraso, escolha **novo passo**.

   Para adicionar a ação de atraso entre passos, mova o ponteiro sobre a seta que liga os passos. Escolha o sinal de mais (+) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "delay" como filtro. Da lista de ações, selecione esta ação: **Delay até**

   ![Adicione a ação "Delay until"](./media/connectors-native-delay/add-delay-until-action.png)

1. Forneça a data e a hora finais para quando pretender retomar o fluxo de trabalho.

   ![Especificar a marca de tempo para quando terminar o atraso](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Carimbo de data/hora | carimbo de data/hora | Sim | String | Data e hora finais para retomar o fluxo de trabalho utilizando este formato: <p>YYYY-MM-DDThh:mm:ssZ <p>Assim, por exemplo, se quiser 18 de setembro de 2017 às 14:00, especifique "2017-09-18T14:00:00:00Z". <p>**Nota:** Este formato de tempo deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Sem um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . |
   ||||||

1. Adicione quaisquer outras ações que queira executar no seu fluxo de trabalho.

1. Quando terminar, guarde a sua aplicação lógica.

## <a name="next-steps"></a>Passos seguintes

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md)
* [Conectores para as Logic Apps](../connectors/apis-list.md)
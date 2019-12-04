---
title: Atrasar a próxima ação em fluxos de trabalho
description: Aguarde para executar a próxima ação nos fluxos de trabalho do aplicativo lógico usando o atraso ou atraso até as ações nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787341"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Atraso ao executar a próxima ação nos aplicativos lógicos do Azure

Para que seu aplicativo lógico Aguarde um período de tempo antes de executar a próxima ação, você pode adicionar a ação interna de **agendamento de atraso** antes de uma ação no fluxo de trabalho do aplicativo lógico. Ou então, você pode adicionar a ação interna **atraso até-agenda** para aguardar até uma data e hora específicas antes de executar a próxima ação. Para obter mais informações sobre as ações e os gatilhos de agendamento internos, consulte [agendar e executar tarefas automatizadas e automáticas e fluxos de trabalho com aplicativos lógicos do Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Atraso**: Aguarde o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses, antes da execução da próxima ação.

* **Atraso até**: aguardar até a data e a hora especificadas antes da execução da próxima ação.

Aqui estão alguns exemplos de maneiras de usar essas ações:

* Aguarde até que um dia da semana envie uma atualização de status por email.

* Atrase o fluxo de trabalho até que uma chamada HTTP seja concluída antes de retomar e recuperar dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura, poderá se [inscrever para uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Antes de você poder usar uma ação, seu aplicativo lógico deve começar primeiro com um gatilho. Você pode usar qualquer gatilho que desejar e adicionar outras ações antes de adicionar uma ação de atraso. Este tópico usa um gatilho do Outlook do Office 365. Se você for novo em aplicativos lógicos, saiba [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adicionar a ação de atraso

1. No designer de aplicativo lógico, na etapa em que você deseja adicionar a ação de atraso, escolha **nova etapa**.

   Para adicionar a ação de atraso entre as etapas, mova o ponteiro sobre a seta que conecta as etapas. Escolha o sinal de adição (+) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "Delay" como filtro. Na lista ações, selecione esta ação: **atraso**

   ![Adicionar ação de "atraso"](./media/connectors-native-delay/add-delay-action.png)

1. Especifique a quantidade de tempo de espera antes da execução da próxima ação.

   ![Definir a quantidade de tempo para o atraso](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriedade | Nome JSON | Obrigatório | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Contagem | count | Sim | Número inteiro | O número de unidades de tempo a serem atrasadas |
   | Unidade | unidade | Sim | String | A unidade de tempo, por exemplo: `Second`, `Minute`, `Hour`, `Day`, `Week`ou `Month` |
   ||||||

1. Adicione outras ações que você deseja executar em seu fluxo de trabalho.

1. Quando tiver terminado, salve seu aplicativo lógico.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Adicionar a ação atraso-até

1. No designer de aplicativo lógico, na etapa em que você deseja adicionar a ação de atraso, escolha **nova etapa**.

   Para adicionar a ação de atraso entre as etapas, mova o ponteiro sobre a seta que conecta as etapas. Escolha o sinal de adição (+) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "Delay" como filtro. Na lista ações, selecione esta ação: **atrasar até**

   ![Adicionar a ação "atrasar até"](./media/connectors-native-delay/add-delay-until-action.png)

1. Forneça a data e a hora de término para quando você quiser retomar o fluxo de trabalho.

   ![Especifique o carimbo de data/hora para quando terminar o atraso](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriedade | Nome JSON | Obrigatório | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Carimbo de data/hora | carimbo de data/hora | Sim | String | A data e a hora de término para retomar o fluxo de trabalho usando este formato: <p>AAAA-MM-DDThh: mm: ssZ <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00Z". <p>**Observação:** Esse formato de hora deve seguir a especificação de data e hora [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Sem um fuso horário, você deve adicionar a letra "Z" no final sem espaços. Esse "Z" refere-se ao [tempo náuticas](https://en.wikipedia.org/wiki/Nautical_time)equivalente. |
   ||||||

1. Adicione outras ações que você deseja executar em seu fluxo de trabalho.

1. Quando tiver terminado, salve seu aplicativo lógico.

## <a name="next-steps"></a>Passos seguintes

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md)
* [Conectores para aplicativos lógicos](../connectors/apis-list.md)
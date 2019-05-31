---
title: Atrasar a próxima ação em fluxos de trabalho - Azure Logic Apps
description: Aguardar a próxima ação em fluxos de trabalho de aplicação de lógica para utilizar para executar as ações de atraso ou atraso até no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
tags: connectors
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 27475fb3f086dbc5166a473e9d657d2dab723938
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297645"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Atraso a próxima ação a executar no Azure Logic Apps

Para ter a aplicação de lógica aguardar um período de tempo antes de executar a ação seguinte, é possível adicionar o incorporado **atraso – agendar** ação antes de uma ação no fluxo de trabalho da sua aplicação lógica. Em alternativa, pode adicionar o incorporado **atraso até – agendar** ação esperar até uma data e hora específicas antes de executar a ação seguinte. Para obter mais informações sobre as ações de agendamento incorporadas e os disparadores, consulte [agendamento e execução recorrente, automatizado, tarefas e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **atraso**: Aguarde que o número de unidades de tempo, por exemplo, segundos, minutos, horas, dias, semanas ou meses, antes das execuções de ação seguintes especificado.

* **Atraso até**: Aguarde até a data e hora especificadas antes das execuções de ação seguintes.

Aqui estão algumas formas de exemplo para utilizar estas ações:

* Aguarde até um dia da semana para enviar uma atualização de estado por e-mail.

* Atrasar o seu fluxo de trabalho até que uma chamada HTTP é concluída antes de a retomar e recuperar dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [Inscreva-se uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicações lógicas](../logic-apps/logic-apps-overview.md). Antes de poder utilizar uma ação, a aplicação lógica em primeiro lugar tem de começar com um acionador. Pode usar qualquer acionador que pretende e adicionar outras ações antes de adicionar uma ação de atraso. Este tópico utiliza um acionador do Outlook do Office 365. Se estiver familiarizado com aplicações lógicas, saiba [como criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Adicione a ação de atraso

1. No Estruturador da aplicação lógica, no passo em que pretende adicionar a ação de atraso, escolha **novo passo**.

   Para adicionar a ação de atraso entre etapas, mova o ponteiro sobre a seta que liga-se os passos. Selecione o sinal de adição (+) que é apresentado e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "atraso" como o filtro. Na lista de ações, selecione a ação: **atraso**

   ![Adicionar ação de "Atraso"](./media/connectors-native-delay/add-delay-action.png)

1. Especifique a quantidade de tempo de espera antes das execuções de ação seguintes.

   ![Definir período de tempo para o atraso](./media/connectors-native-delay/delay-time-intervals.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Count | count | Sim | Integer | O número de unidades de tempo de atraso |
   | Unidade | Unidade | Sim | String | A unidade de tempo, por exemplo: `Second`, `Minute`, `Hour`, `Day`, `Week`, ou `Month` |
   ||||||

1. Adicione outras ações que pretende executar no seu fluxo de trabalho.

1. Quando tiver terminado, guarde a aplicação lógica.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Adicionar o atraso-até a ação

1. No Estruturador da aplicação lógica, no passo em que pretende adicionar a ação de atraso, escolha **novo passo**.

   Para adicionar a ação de atraso entre etapas, mova o ponteiro sobre a seta que liga-se os passos. Selecione o sinal de adição (+) que é apresentado e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "atraso" como o filtro. Na lista de ações, selecione a ação: **Atraso até**

   ![Adicionar ação "Atraso até"](./media/connectors-native-delay/add-delay-until-action.png)

1. Forneça a data de fim e a hora para que deseja retomar o fluxo de trabalho.

   ![Especificar timestamp para quando o atraso de fim](./media/connectors-native-delay/delay-until-timestamp.png)

   | Propriedade | Nome JSON | Necessário | Tipo | Descrição |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | Sim | String | A data de fim e a hora de retomar o fluxo de trabalho com este formato: <p>YYYY-MM-DDThh:mm:ssZ <p>Por exemplo, se quiser 18 de Setembro de 2017, às 14:00, especificar "2017-09-18T14:00:00Z". <p>**Nota:** Este formato de hora tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) na [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Sem um fuso horário, tem de adicionar a letra "Z" no final, sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Adicione outras ações que pretende executar no seu fluxo de trabalho.

1. Quando tiver terminado, guarde a aplicação lógica.

## <a name="next-steps"></a>Passos Seguintes

* [Criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o acionador de periodicidade](../connectors/connectors-native-recurrence.md)
* [Conectores para o Logic Apps](../connectors/apis-list.md)
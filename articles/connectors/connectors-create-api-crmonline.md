---
title: Ligue-se à Dinâmica 365
description: Crie e gerea registos com Dynamics 365 (online) REST APIs e Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789882"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gerir registos do Dynamics 365 com Azure Logic Apps

Com as Azure Logic Apps e o conector do Dynamics 365, pode criar tarefas e fluxos de trabalho automatizados com base nos seus registos no Dynamics 365. Os seus fluxos de trabalho podem criar registos, atualizar itens, devolver registos e muito mais na sua conta do Dynamics 365. Pode incluir ações nas suas aplicações lógicas que obtêm respostas da Dynamics 365 e disponibilizam a saída para outras ações. Por exemplo, quando um item é atualizado na Dynamics 365, pode enviar um e-mail usando o Office 365.

Este artigo mostra como se pode construir uma aplicação lógica que cria uma tarefa na Dynamics 365 sempre que um novo recorde de chumbo é criado na Dynamics 365.
Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conta [Dynamics 365](https://dynamics.microsoft.com)

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta Dynamics 365. Para iniciar a sua aplicação lógica com um gatilho Dynamics 365, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-dynamics-365-trigger"></a>Adicionar gatilho Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Em primeiro lugar, adicione um gatilho Dynamics 365 que dispara quando um novo recorde de chumbo aparece na Dynamics 365.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer, se não estiver aberta já.

1. Na caixa de pesquisa, introduza "Dynamics 365" como filtro. Para este exemplo, sob a lista de gatilhos, selecione este gatilho: **Quando um disco é criado**

   ![Selecione gatilho](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Se for solicitado que inscreva-se na Dynamics 365, inscreva-se agora.

1. Forneça estes detalhes do gatilho:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da Organização** | Sim | O nome da sua organização Dynamics 365 exemplo para monitorizar, por exemplo, "Contoso" |
   | **Nome da entidade** | Sim | O nome para a entidade monitorizar, por exemplo, "Leads" | 
   | **Frequência** | Sim | A unidade de tempo a utilizar com intervalos ao verificar se há atualizações relacionadas com o gatilho |
   | **Intervalo** | Sim | O número de segundos, minutos, horas, dias, semanas ou meses que passam antes do próximo cheque |
   ||| 

   ![Detalhes do gatilho](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Adicionar ação Dynamics 365

Adicione agora a ação Dynamics 365 que cria um recorde de tarefas para o novo recorde de chumbo.

1. Sob o seu gatilho, escolha **novo passo.**

1. Na caixa de pesquisa, introduza "Dynamics 365" como filtro. Da lista de ações, selecione esta ação: **Criar um novo recorde**

   ![Selecione ação](./media/connectors-create-api-crmonline/select-action.png)

1. Forneça estes detalhes de ação:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da Organização** | Sim | A Dinâmica 365 em que quer criar o disco, que não tem de ser o mesmo no seu gatilho, mas é "Contoso" neste exemplo |
   | **Nome da entidade** | Sim | A entidade onde pretende criar o disco, por exemplo, "Tarefas" |
   | | |

   ![Detalhes de ação](./media/connectors-create-api-crmonline/action-details.png)

1. Quando a caixa **'Assunto'** aparecer na sua ação, clique no interior da caixa **'Assunto'** para que a lista de conteúdos dinâmicos apareça. A partir desta lista, selecione os valores de campo a incluir no registo de tarefas associado ao novo registo de chumbo:

   | Campo | Descrição |
   |-------|-------------|
   | **Apelido** | O último nome do chumbo como o contacto primário no registo |
   | **Tópico** | O nome descritivo para o chumbo no registo |
   | | |

   ![Detalhes do registo de tarefas](./media/connectors-create-api-crmonline/create-record-details.png)

1. Na barra de ferramentas de design, escolha **Guardar** para a sua aplicação lógica. 

1. Para iniciar manualmente a aplicação lógica, na barra de ferramentas de design, escolha **Run**.

   ![Executar a aplicação lógica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Agora crie um registo de chumbo na Dynamics 365 para que possa desencadear o fluxo de trabalho da sua aplicação lógica.

## <a name="add-filter-or-query"></a>Adicionar filtro ou consulta

Para especificar como filtrar os dados numa ação da Dynamics 365, escolha **opções avançadas** do Show nessa ação. Em seguida, pode adicionar um filtro ou encomenda por consulta.
Por exemplo, pode utilizar uma consulta de filtro para obter apenas as contas ativas e encomendar esses registos pelo nome da conta. Para esta tarefa, siga estes passos:

1. Em **consulta de filtro,** introduza esta consulta de filtro OData:`statuscode eq 1`

2. Sob **encomenda Por**, quando a lista de conteúdos dinâmicos aparecer, selecione Nome de **Conta**. 

   ![Especificar filtro e encomenda](./media/connectors-create-api-crmonline/advanced-options.png)

Para mais informações, consulte estas opções de consulta do sistema Web API de envolvimento com o cliente Dynamics 365:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Melhores práticas para as opções avançadas

Quando especifica um valor para um campo numa ação ou gatilho, o tipo de dados do valor deve corresponder ao tipo de campo, quer introduza manualmente o valor ou se selecione o valor da lista de conteúdos dinâmicos.

Esta tabela descreve alguns tipos de campo e os tipos de dados necessários para os seus valores.

| Tipo de campo | Tipo de dados necessário | Descrição | 
|------------|--------------------|-------------|
| Campos de texto | Uma linha de texto | Estes campos requerem uma única linha de texto ou conteúdo dinâmico que tenha o tipo de texto. <p><p>*Campos de exemplo*: **Descrição** e **categoria** | 
| Campos de número inteiro | Número inteiro | Alguns campos requerem conteúdo inteiro ou dinâmico que tenha o tipo inteiro. <p><p>*Campos de exemplo*: **Percent completo** e **duração** | 
| Campos de data | Data e Hora | Alguns campos requerem uma data com formato mm/dd/yyyy ou conteúdo dinâmico que tem o tipo de data. <p><p>*Campos de exemplo*: **Criado,** Data de **Início,** **Início Real,** **Fim Real**e Data **de Vencimento** | 
| Campos que requerem um disco de identificação e tipo de procura | Chave primária | Alguns campos que referenciam outro registo de entidade requerem tanto um disco de identificação como um tipo de procuração. | 
||||

Expandindo-se nestes tipos de campo, aqui estão campos de exemplo em gatilhos e ações da Dynamics 365 que requerem tanto um ID de registo como o tipo de procura. Esta exigência significa que os valores que seleciona da lista dinâmica não funcionarão.

| Campo | Descrição |
|-------|-------------|
| **Proprietário** | Deve ser um ID de utilizador válido ou identificação de registo da equipa. |
| **Tipo proprietário** | Deve ser `systemusers` `teams`ou. |
| **Relativo A** | Deve ser um id de registo válido, como um ID de conta ou identificação de registo de contato. |
| **No que diz respeito ao Tipo** | Deve ser um tipo de `accounts` `contacts`procura, como ou . |
| **Cliente** | Deve ser um id de registo válido, como um ID de conta ou identificação de registo de contato. |
| **Tipo de cliente** | Deve ser do tipo de `accounts` `contacts`procura, como ou . |
|||

Neste exemplo, a ação denominada **Criar um novo recorde** cria um novo registo de tarefas:

![Criar registo de tarefas com iDs de gravação e tipos de procura](./media/connectors-create-api-crmonline/create-record-advanced.png)

Esta ação atribui o registo de tarefa a um ID específico do utilizador ou ao id de registo da equipa, com base no ID de registo no campo **Proprietário** e no tipo de pesquisa no campo **Do tipo proprietário:**

![Identificação de registo do proprietário e tipo de procura](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Esta ação também adiciona um registo de conta que está associado ao ID de registo adicionado no campo **Regarding** e ao tipo de procura no campo **Regarding Type:**

![No que diz respeito ao tipo de identificação de registo e de procura](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Encontre identificação de registo

Para encontrar uma identificação recorde, siga estes passos:

1. Na Dynamics 365, abra um recorde, como um recorde de conta.

2. Na barra de ferramentas de ações, escolha um destes passos:

   * Escolha **pop out**. ![popout disco](./media/connectors-create-api-crmonline/popout-record.png) 
   * Escolha **e-mail Um LINK** para que possa copiar o URL completo no seu programa de e-mail predefinido.

   O ID de registo aparece `%7b` no `%7d` URL entre os caracteres de codificação e codificação:

   ![Encontre identificação de registo](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Resolver problemas de execuções com falhas

Para encontrar e rever passos falhados na sua aplicação lógica, pode ver o histórico, estado, inputs, saídas da sua aplicação lógica, e assim por diante.

1. No portal Azure, no menu principal da sua aplicação lógica, selecione **Visão Geral**. Na secção **de história runs,** que mostra todos os status de execução para a sua aplicação lógica, selecione uma execução falhada para mais informações.

   ![Estado de execução de aplicações lógicas](./media/connectors-create-api-crmonline/run-history.png)

1. Expanda um passo falhado para que possa ver mais detalhes.

   ![Expandir passo falhado](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Reveja os detalhes do passo, tais como as inputs e saídas, que podem ajudá-lo a encontrar a causa por detrás da falha.

   ![Passo falhado - inputs e saídas](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Para obter mais informações sobre aplicações lógicas de resolução de problemas, consulte falhas de [aplicações lógicas de diagnóstico.](../logic-apps/logic-apps-diagnosing-failures.md)

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro OpenAPI (ex-Swagger) do conector, consulte a página de [referência do conector](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)

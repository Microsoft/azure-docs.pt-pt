---
title: Ligar ao Serviço Comum de Dados
description: Crie e gere ncisões do Serviço De Dados Comuns utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 98da7e959e4b59ad2d0f3f3f79364391b4ceddbd
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997103"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Criar e gerir registos no Serviço Comum de Dados utilizando aplicações da Lógica Azure

Com [aplicativos azure logic](../logic-apps/logic-apps-overview.md) e o [conector do Serviço Comum](https://docs.microsoft.com/connectors/commondataservice/)de Dados, pode construir fluxos de trabalho automatizados que gerem registos na sua base de dados do Serviço de Dados [Comuns.](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro) Estes fluxos de trabalho podem criar registos, atualizar registos e realizar outras operações. Também pode obter informações na sua base de dados do Serviço De Dados Comuns e disponibilizar a saída para outras ações a utilizar na sua aplicação lógica. Por exemplo, quando um registo é atualizado na sua base de dados do Serviço de Dados Comuns, pode enviar um e-mail utilizando o conector Do Outlook do Office 365.

Este artigo mostra como se pode construir uma aplicação lógica que cria um registo de tarefas sempre que um novo disco de chumbo é criado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um ambiente de [Serviço Comum](https://docs.microsoft.com/power-platform/admin/environments-overview)de Dados, que é um espaço onde a sua organização armazena, gere e partilha dados de negócios e uma base de dados do Serviço De Dados Comuns. Para mais informações, consulte estes recursos:<p>

  * [Saiba: Começar com o Serviço Comum de Dados](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power Platform - Visão geral dos ambientes](https://docs.microsoft.com/power-platform/admin/environments-overview)

* Conhecimentos básicos sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) e a aplicação lógica de onde pretende aceder aos registos na sua base de dados do Serviço de Dados Comuns. Para iniciar a sua aplicação lógica com um gatilho do Common Data Service, precisa de uma aplicação lógica em branco. Se é novo em Aplicações Lógicas Azure, reveja [Quickstart: Crie o seu primeiro fluxo de trabalho utilizando aplicações da Azure Logic](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Adicionar gatilho do Serviço de Dados Comuns

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Para este exemplo, adicione o gatilho do Serviço Comum de Dados que dispara quando um novo recorde é criado.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer, se não estiver aberta já.

1. Na caixa de pesquisa, introduza `common data service`. Para este exemplo, sob a lista de gatilhos, selecione este gatilho: **Quando um disco é criado**

   ![Selecione o gatilho "Quando um disco é criado"](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Se solicitado, inscreva-se no Serviço Comum de Dados.

1. No gatilho, forneça informações sobre o ambiente onde pretende monitorizar novos registos "Leads", por exemplo:

   ![Desencadear informações para o ambiente monitorizar](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Ambiente** | Sim | O ambiente para monitorizar, por exemplo, "Fabrikam Sales Production". Para mais informações, consulte [power platform - Environments overview](https://docs.microsoft.com/power-platform/admin/environments-overview). |
   | **Nome da entidade** | Sim | A entidade para monitorizar, por exemplo, "Leads" |
   | **Âmbito** | Sim | A fonte que criou o novo registo, por exemplo, um utilizador na sua unidade de negócio ou qualquer utilizador da sua organização. Este exemplo utiliza "Unidade de Negócios". |
   ||||

## <a name="add-common-data-service-action"></a>Adicionar ação do Serviço Comum de Dados

Adicione agora uma ação do Serviço de Dados Comuns que cria um registo de tarefas para um novo disco "Leads".

1. Sob o **gatilho Quando um disco é criado,** selecione Novo **passo**.

1. Na caixa de pesquisa, introduza `common data service`. Da lista de ações, selecione esta ação: **Criar um novo recorde**

   ![Selecione ação "Criar um novo recorde"](./media/connect-common-data-service/select-create-new-record-action.png)

1. Na ação, forneça a informação sobre o ambiente onde pretende criar o novo registo de tarefas. Se disponível, outras propriedades também aparecem com base na entidade que selecionou para esta ação, por exemplo:

   ![Informação de ação para o ambiente onde criar o recorde](./media/connect-common-data-service/create-new-record-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da Organização** | Sim | O ambiente onde se quer criar o disco, que não tem de ser o mesmo ambiente no seu gatilho, mas é "Fabrikam Sales Production" neste exemplo |
   | **Nome da entidade** | Sim | A entidade onde pretende criar o disco, por exemplo, "Tarefas" |
   | **Assunto** | Sim, com base na entidade selecionada neste exemplo | Uma breve descrição sobre o objetivo para esta tarefa |
   ||||

   1. Para a propriedade **Do Assunto,** insira este texto com um espaço de trailing:

      `Follow up with new lead:`

   1. Mantenha o ponteiro dentro da caixa **de Assuntos** para que a lista de conteúdos dinâmicos permaneça visível.
   
   1. Na lista, a partir da secção **Quando um disco é criado,** selecione as saídas do gatilho que pretende incluir no registo de tarefas, por exemplo:

      ![Selecione saídas de gatilho para usar no registo de tarefas](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Saída de gatilho | Descrição |
      |----------------|-------------|
      | **Nome próprio** | O primeiro nome do registo principal a usar como o contacto primário no registo de tarefas |
      | **Apelido** | O último nome do registo principal a usar como o contacto primário no registo de tarefas |
      | **Descrição** | Outras saídas a incluir no registo de tarefas, tais como endereço de e-mail e número de telefone de negócios |
      |||

   Quando terminar, a ação pode parecer este exemplo:

   ![Terminou a ação "Criar um novo recorde"](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

1. Para iniciar manualmente a aplicação lógica, na barra de ferramentas de design, selecione **Executar**. Para testar a sua aplicação lógica, crie um novo disco "Leads".

## <a name="trigger-only-on-updated-attributes"></a>Desencadear apenas em atributos atualizados

Para os gatilhos que são executados quando os registos são atualizados, como o **When a record is updateed** action, you can use filter atributos de modo que a sua aplicação lógica seja executada apenas quando os atributos especificados são atualizados. Esta capacidade ajuda-o a prevenir execuções de aplicações lógicas desnecessárias.

1. No gatilho, a partir da **lista adicionar novo parâmetro,** selecione **Filtros de Atributo**.

   ![Adicione a propriedade "Atributo filters"](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Para cada item de **filtros de atributo,** selecione o atributo que pretende monitorizar para atualizações, por exemplo:

   ![Adicione a propriedade "Atributo filters"](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Lista rumina registos com base num filtro

Para ações que devolvem registos, como a ação **de registos da Lista,** pode utilizar uma consulta ODATA que devolve registos com base no filtro especificado. Por exemplo, tem a lista de ação apenas os registos de contas ativas.

1. Na ação, abra a **lista de novos parâmetros e** selecione a propriedade Filter **Query.**

   ![Adicione a propriedade "Filter Query"](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Na propriedade **Filter Query** que agora aparece na ação, introduza esta consulta de filtro ODATA:`statuscode eq 1`

   ![Introduza a consulta do filtro ODATA para filtrar registos](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Para obter `$filter` mais informações sobre as opções de consulta do sistema, consulte O [Serviço Comum de Dados - Filtrar resultados](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Registos de listas com base numa encomenda

Para ações que devolvem registos, como a ação de **registos da Lista,** pode utilizar uma consulta ODATA que devolve registos numa ordem especificada, que varia com base nos registos que a ação devolve. Por exemplo, pode ter a lista de ação dos registos com base no nome da conta.

1. Na ação, abra a **lista de novos parâmetros e** selecione a encomenda **por** propriedade.

   ![Adicionar propriedade "Encomendar por"](./media/connect-common-data-service/list-records-action-order-by.png)

1. Na **Ordem Por** imóvel que agora aparece na ação, insira esta consulta de filtro ODATA:`name`

   ![Insira a consulta de filtro ODATA para encomendar registos](./media/connect-common-data-service/list-records-action-order-by-value.png)

Para obter `$orderby` mais informações sobre as opções de consulta do sistema, consulte O [Serviço Comum de Dados - Resultados](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)da Encomenda .

## <a name="field-data-types"></a>Tipos de dados de campo

Independentemente de introduzir manualmente um valor ou selecionar um valor da lista de conteúdos dinâmicos para um campo num gatilho ou ação, o tipo de dados do valor deve corresponder ao tipo de dados exigido pelo campo.

Esta tabela descreve alguns tipos de campo e os tipos de dados que esses campos exigem para os seus valores.

| Campo | Tipo de dados | Descrição |
|-------|-----------|-------------|
| Campo de texto | Uma linha de texto | Requer uma única linha de texto ou conteúdo dinâmico que tenha o tipo de dados de texto, por exemplo, estas propriedades: <p><p>- **Descrição** <br>- **Categoria** |
| Campo inteiro | Número inteiro | Requer um conteúdo inteiro ou dinâmico que tenha o tipo de dados inteiros, por exemplo, estas propriedades: <p><p>- **Por cento completo** <br>- **Duração** |
| Campo de data | Data e Hora | Requer uma data em formato MM/DD/YYY ou conteúdo dinâmico que tenha o tipo de dados da data, por exemplo, estas propriedades: <p><p>- **Criado em** <br>- **Data de início** <br>- **Início Real** <br>- **Fim Real** <br>- **Data de Vencimento** |
| Campo que faz referência a outro registo de entidade | Chave primária | Requer tanto um ID de registo, como um GUID, e um tipo de procuração, o que significa que os valores da lista de conteúdos dinâmicos não funcionarão, por exemplo, estas propriedades: <p><p>- **Proprietário**: Deve ser um ID de utilizador válido ou um ID de registo de equipa. <br>- **Tipo proprietário**: Deve ser um `systemusers` `teams`tipo de procura, como ou, respectivamente. <p><p>- **Em relação**: Deve ser um ID de registo válido, como um ID de conta ou um ID de registo de contato. <br>- **No que diz respeito ao Tipo:** Deve ser um tipo de procuração, respectivamente `accounts` `contacts`ou, respectivamente. <p><p>- **Cliente**: Deve ser um ID de registo válido, como um ID de conta ou identificação de registo de contato. <br>- **Tipo de cliente**: Deve ser `accounts` o `contacts`tipo de procura, como ou, respectivamente. |
||||

Este exemplo mostra como a **Create a new record** action cria um novo registo "Tasks" que está associado a outros registos de entidades, especificamente um registo de utilizador e um registo de conta. A ação especifica os tipos de IDs e de procura para esses registos de entidades utilizando valores que correspondam aos tipos de dados esperados para as propriedades relevantes.

* Com base na propriedade **Proprietário,** que especifica um ID do utilizador, `systemusers` e na propriedade Do Tipo **Proprietário,** que especifica o tipo de pesquisa, a ação associa o novo registo "Tarefas" a um utilizador específico.

* Com base na propriedade **Relativa,** que especifica um ID de registo, e `accounts` na propriedade Relativa ao **Tipo,** que especifica o tipo de pesquisa, a ação associa o novo registo "Tarefas" a uma conta específica.

![Criar registo de "Tarefas" associado a IDs e tipos de procura](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Referência do conector

Para obter informações técnicas baseadas na descrição swagger do conector, tais como gatilhos, ações, limites e outros detalhes, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/commondataservice/).

## <a name="next-steps"></a>Passos seguintes

* Conheça outros [conectores para aplicações lógicas azure](../connectors/apis-list.md)
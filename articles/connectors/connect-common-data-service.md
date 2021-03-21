---
title: Conecte-se ao Serviço Comum de Dados (Microsoft Dataverse)
description: Criar e gerir registos do Common DataVerse (Microsoft Dataverse) utilizando aplicações lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382361"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Criar e gerir registos no Common DataVerse (Microsoft Dataverse) utilizando aplicações Azure Logic

> [!NOTE]
> Em novembro de 2020, o Common Data Service foi renomeado para Microsoft Dataverse.

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o [conector Common Data Service,](/connectors/commondataservice/)pode construir fluxos de trabalho automatizados que gerem registos no seu [Serviço Comum de Dados, agora base de dados do Microsoft Dataverse.](/powerapps/maker/common-data-service/data-platform-intro) Estes fluxos de trabalho podem criar registos, atualizar registos e realizar outras operações. Também pode obter informações a partir da sua base de dados Dataverse e disponibilizar a saída para outras ações a utilizar na sua aplicação lógica. Por exemplo, quando um registo é atualizado na sua base de dados Dataverse, pode enviar um e-mail utilizando o conector Office 365 Outlook.

Este artigo mostra como pode construir uma aplicação lógica que cria um registo de tarefas sempre que um novo registo de chumbo é criado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um [ambiente comum de Serviço de Dados,](/power-platform/admin/environments-overview)que é um espaço onde a sua organização armazena, gere e partilha dados de negócios e uma base de dados do Common Data Service. Para mais informações, consulte estes recursos:<p>

  * [Saiba: Começar com o Serviço Comum de Dados](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Plataforma de Energia - Visão geral dos ambientes](/power-platform/admin/environments-overview)

* Conhecimentos [básicos sobre como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) e a aplicação lógica de onde pretende aceder aos registos na sua base de dados Dataverse. Para iniciar a sua aplicação lógica com um gatilho do Serviço de Dados Comum, precisa de uma aplicação lógica em branco. Se é novo em Azure Logic Apps, reveja [Quickstart: Crie o seu primeiro fluxo de trabalho utilizando apps Azure Logic](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Adicionar gatilho de serviço de dados comum

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Para este exemplo, adicione o gatilho do Serviço Comum de Dados que dispara quando um novo registo é criado.

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica em branco no Logic App Designer, se não abrir já.

1. Na caixa de pesquisa, introduza `common data service`. Para este exemplo, na lista de gatilhos, selecione este gatilho: **Quando um registo é criado**

   ![Selecione o gatilho "Quando um registo é criado"](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Se solicitado, inscreva-se no Serviço Comum de Dados.

1. No gatilho, forneça informações sobre o ambiente onde pretende monitorizar novos registos "Leads", por exemplo:

   ![Detonar informações para o ambiente monitorizar](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Environment** | Yes | O ambiente para monitorizar, por exemplo, "Fabrikam Sales Production". Para mais informações, consulte [a Plataforma de Energia - Visão geral dos Ambientes.](/power-platform/admin/environments-overview) |
   | **Nome da entidade** | Yes | A entidade para monitorizar, por exemplo, "Leads" |
   | **Âmbito** | Yes | A fonte que criou o novo registo, por exemplo, um utilizador na sua unidade de negócio ou qualquer utilizador da sua organização. Este exemplo utiliza "Unidade de Negócio". |
   ||||

## <a name="add-common-data-service-action"></a>Adicionar ação comum de serviço de dados

Adicione agora uma ação do Serviço de Dados Comum que cria um registo de tarefas para um novo registo "Leads".

1. Sob o **gatilho Quando um disco é criado,** selecione Novo **passo**.

1. Na caixa de pesquisa, introduza `common data service`. A partir da lista de ações, selecione esta ação: **Criar um novo recorde**

   ![Selecione ação "Criar um novo recorde"](./media/connect-common-data-service/select-create-new-record-action.png)

1. Na ação, forneça a informação sobre o ambiente onde pretende criar o novo registo de tarefas. Se disponível, outras propriedades também aparecem com base na entidade que selecionou para esta ação, por exemplo:

   ![Informação de ação para o ambiente onde criar o registo](./media/connect-common-data-service/create-new-record-action-details.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da Organização** | Yes | O ambiente onde se quer criar o disco, que não tem de ser o mesmo ambiente no seu gatilho, mas é a "Fabrikam Sales Production" neste exemplo. |
   | **Nome da entidade** | Yes | A entidade onde pretende criar o registo, por exemplo, "Tarefas" |
   | **Assunto** | Sim, com base na entidade selecionada neste exemplo | Uma breve descrição sobre o objetivo para esta tarefa |
   ||||

   1. Para a propriedade **Assunto,** insira este texto com um espaço de fuga:

      `Follow up with new lead:`

   1. Mantenha o ponteiro dentro da caixa **Assunto** para que a lista de conteúdos dinâmicos permaneça visível.
   
   1. Na lista, a partir da secção **Quando é criado um registo,** selecione as saídas de gatilho que pretende incluir no registo de tarefas, por exemplo:

      ![Selecione saídas de gatilho para usar no registo de tarefas](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Saída do gatilho | Description |
      |----------------|-------------|
      | **Primeiro nome** | O primeiro nome do registo de chumbo a ser usado como o contacto principal no registo de tarefas |
      | **Último Nome** | O último nome do registo principal a ser usado como o contacto principal no registo de tarefas |
      | **Descrição** | Outras saídas para incluir no registo de tarefas, tais como endereço de e-mail e número de telefone comercial |
      |||

   Quando terminar, a ação pode parecer este exemplo:

   ![Termine a ação "Criar um novo recorde"](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

1. Para iniciar manualmente a aplicação lógica, na barra de ferramentas do designer, selecione **Run**. Para testar a sua aplicação lógica, crie um novo registo "Leads".

## <a name="trigger-only-on-updated-attributes"></a>Gatilho apenas em atributos atualizados

Para os gatilhos que são executados quando os registos são atualizados, como o **Quando um registo é atualizado,** pode utilizar atributos de filtro para que a sua aplicação lógica seja executada apenas quando os atributos especificados forem atualizados. Esta capacidade ajuda-o a prevenir aplicações lógicas desnecessárias.

1. No gatilho, a partir da nova lista **de parâmetros** adicionar, selecione **Filters de Atributos**.

   ![Screenshot que mostra a ação "Quando um registo é atualizado" e a lista "Adicionar novo parâmetro" com a propriedade "Atributo Filtros" selecionada.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Para cada **Item de Filtros de Atributos**, selecione o atributo que pretende monitorizar para atualizações, por exemplo:

   ![Adicionar propriedade "Atributo Filtros"](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Listar registos com base num filtro

Para ações que devolvam registos, como a ação **de registos da Lista,** pode utilizar uma consulta ODATA que retorna os registos com base no filtro especificado. Por exemplo, tem a lista de ação apenas os registos de contas ativas.

1. Na ação, abra a nova lista **de parâmetros adicionar** e selecione a propriedade **'Consulta de Filtro'.**

   ![Adicionar propriedade "Consulta de Filtro"](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Na propriedade **'Saber filtro'** que agora aparece na ação, insira esta consulta de filtro ODATA: `statuscode eq 1`

   ![Introduza a consulta do filtro ODATA para registos de filtragem](./media/connect-common-data-service/list-records-action-filter-query-value.png)

Para obter mais informações sobre `$filter` as opções de consulta do sistema, consulte [o Common Data Service - Filter results](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Listar registos com base numa encomenda

Para ações que devolvam registos, como a ação **de registos da Lista,** pode utilizar uma consulta ODATA que devolve registos numa ordem especificada, que varia em com base nos registos que a ação devolve. Por exemplo, pode ter a lista de ação os registos com base no nome da conta.

1. Na ação, abra a nova lista **de parâmetros adicionar** e selecione a **Ordem Por** propriedade.

   ![Adicionar propriedade "Encomendar por"](./media/connect-common-data-service/list-records-action-order-by.png)

1. Na **Ordem Por** propriedade que agora aparece na ação, insira esta consulta de filtro ODATA: `name`

   ![Introduza a consulta do filtro ODATA para encomendar registos](./media/connect-common-data-service/list-records-action-order-by-value.png)

Para obter mais informações sobre `$orderby` as opções de consulta do sistema, consulte [o Serviço Comum de Dados - Resultados da encomenda](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Tipos de dados de campo

Independentemente de introduzir manualmente um valor ou selecionar um valor da lista de conteúdos dinâmicos para um campo num gatilho ou ação, o tipo de dados do valor deve corresponder ao tipo de dados exigido pelo campo.

Esta tabela descreve alguns tipos de campo e os tipos de dados que esses campos exigem para os seus valores.

| Campo | Tipo de dados | Descrição |
|-------|-----------|-------------|
| Campo de texto | Uma linha de texto | Requer uma única linha de texto ou conteúdo dinâmico que tenha o tipo de dados de texto, por exemplo, estas propriedades: <p><p>- **Descrição** <br>- **Categoria** |
| Campo inteiro | Número inteiro | Requer um conteúdo inteiro ou dinâmico que tenha o tipo de dados inteiro, por exemplo, estas propriedades: <p><p>- **Por cento Completo** <br>- **Duração** |
| Campo de data | Data e Hora | Requer uma data no formato MM/DD/YYY ou conteúdo dinâmico que tenha o tipo de dados de data, por exemplo, estas propriedades: <p><p>- **Criado em** <br>- **Data de Início** <br>- **Início Real** <br>- **Fim Real** <br>- **Data de Vencimento** |
| Campo que faz referência a outro registo de entidade | Chave primária | Requer tanto um ID de registo, como um GUID, e um tipo de procura, o que significa que os valores da lista de conteúdos dinâmicos não funcionam, por exemplo, estas propriedades: <p><p>- **Proprietário**: Deve ser um ID de utilizador válido ou um ID de registo de equipa. <br>- **Tipo proprietário**: Deve ser um tipo de procura como `systemusers` `teams` ou, respectivamente. <p><p>- **No que diz respeito a**: Deve ser um ID de registo válido, como um ID de conta ou um ID de registo de contacto. <br>- **No que diz respeito ao tipo**: Deve ser um tipo de procura, como `accounts` `contacts` ou, respectivamente. <p><p>- **Cliente**: Deve ser um ID de registo válido, como um ID de conta ou iD de registo de contacto. <br>- **Tipo de Cliente**: Deve ser o tipo de procura, como `accounts` `contacts` ou, respectivamente. |
||||

Este exemplo mostra como a **criação de uma nova** ação de gravação cria um novo registo de "Tasks" que está associado a outros registos de entidades, especificamente um registo de utilizador e um registo de conta. A ação especifica os tipos de IDs e procura para esses registos de entidades utilizando valores que correspondam aos tipos de dados esperados para as propriedades relevantes.

* Com base na propriedade **Do Proprietário,** que especifica um ID do utilizador, e a propriedade **Owner Type,** que especifica o `systemusers` tipo de pesquisa, a ação associa o novo registo de "Tarefas" a um utilizador específico.

* Com base no **imóvel Relativo,** que especifica um ID de registo, e a propriedade **Relativa Tipo,** que especifica o `accounts` tipo de pesquisa, a ação associa o novo registo de "Tarefas" a uma conta específica.

![Criar registo de "Tarefas" associado a IDs e tipos de procura](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Referência do conector

Para obter informações técnicas com base na descrição do Swagger do conector, tais como gatilhos, ações, limites e outros detalhes, consulte a [página de referência do conector](/connectors/commondataservice/).

## <a name="troubleshooting-problems"></a>Problemas de resolução de problemas

### <a name="calls-from-multiple-environments"></a>Chamadas de vários ambientes

Ambos os conectores, Common Data Service e Common Data Service (ambiente atual), armazenam informações sobre os fluxos de trabalho de aplicações lógicas que precisam e recebem notificações sobre alterações de entidades utilizando a `callbackregistrations` entidade no seu Microsoft Dataverse. Se copiar uma organização Dataverse, quaisquer webhooks também são copiados. Se copiar a sua organização antes de desativar os fluxos de trabalho que estão mapeados para a sua organização, quaisquer webhooks copiados também apontam para as mesmas aplicações lógicas, que depois recebem notificações de várias organizações.

Para parar as notificações indesejadas, elimine o registo de retorno da organização que envia essas notificações seguindo estas etapas:

1. Identifique a organização Dataverse de onde pretende remover notificações e inscreva-se nessa organização.

1. No navegador Chrome, encontre o registo de retorno que pretende eliminar seguindo estes passos:

   1. Reveja a lista genérica de todos os registos de chamadas no seguinte OData URI para que possa ver os dados dentro da `callbackregistrations` entidade:

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > Se não forem devolvidos valores, poderá não ter permissões para visualizar este tipo de entidade, ou poderá não ser inscrito na organização correta.

   1. Filtrar o nome lógico da entidade de desencadeamento e o evento de `entityname` notificação que corresponde ao fluxo de trabalho da aplicação lógica (mensagem). Cada tipo de evento é mapeado para o inteiro de mensagem da seguinte forma:

      | Tipo de evento | Inteiro de mensagem |
      |------------|-----------------|
      | Criar | 1 |
      | Eliminar | 2 |
      | Atualizar | 3 |
      | CreateOrUpdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      Este exemplo mostra como pode filtrar `Create` notificações numa entidade nomeada `nov_validation` utilizando o seguinte OData URI para uma organização de amostras:

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![Screenshot que mostra a janela do navegador e OData URI na barra de endereços.](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > Se existirem vários gatilhos para a mesma entidade ou evento, pode filtrar a lista utilizando filtros adicionais, como os `createdon` `_owninguser_value` atributos e atributos. O nome do utilizador do proprietário aparece sob `/api/data/v9.0/systemusers({id})` .

   1. Depois de encontrar o ID para o registo de retorno que pretende apagar, siga estes passos:
   
      1. No seu navegador Chrome, abra as Ferramentas de Desenvolvimento Do Chrome (Teclado: F12).

      1. Na janela, na parte superior, selecione o **separador Consola.**

      1. No pedido de linha de comando, insira este comando, que envia um pedido para apagar o registo de retorno especificado:

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > Certifique-se de que faz o pedido a partir de uma página de Interface de Cliente não Unificada (UCI), por exemplo, a partir da própria página de resposta OData ou API. Caso contrário, a lógica no ficheiro app.js pode interferir com esta operação.

   1. Para confirmar que o registo de retorno já não existe, consulte a lista de registos de chamadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores para Apps Azure Logic](../connectors/apis-list.md)

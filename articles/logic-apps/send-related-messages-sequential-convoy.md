---
title: Enviar mensagens correlacionadas em ordem usando um comboio sequencial
description: Envie mensagens relacionadas por ordem usando o padrão de comboio sequencial em Azure Logic Apps com Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048666"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Envie mensagens relacionadas por ordem usando um comboio sequencial em Azure Logic Apps com Azure Service Bus

Quando precisa de enviar mensagens correlacionadas numa ordem específica, pode seguir o padrão do [ *comboio sequencial*](/azure/architecture/patterns/sequential-convoy) ao utilizar [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) utilizando o [conector Azure Service Bus](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como o ID para a [sessão](../service-bus-messaging/message-sessions.md) em Service Bus.

Por exemplo, suponha que tem 10 mensagens para uma sessão chamada "Sessão 1", e tem 5 mensagens para uma sessão chamada "Session 2" que são todas enviadas para a mesma [fila de autocarros](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)de serviço. Pode criar uma aplicação lógica que processa mensagens a partir da fila para que todas as mensagens de "Sessão 1" sejam manuseadas por um único gatilho e todas as mensagens de "Sessão 2" sejam tratadas pelo próximo gatilho.

![Padrão geral do comboio sequencial](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Este artigo mostra como criar uma aplicação lógica que implementa este padrão usando a entrega de **encomenda correlacionada usando** o modelo de sessões de autocarro de serviço. Este modelo define um fluxo de trabalho de aplicação lógica que começa com o conector do Service Bus quando uma mensagem é recebida num gatilho **de fila (peek-lock),** que recebe mensagens de uma fila de [autocarros](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)de serviço . Aqui estão os passos de alto nível que esta aplicação lógica executa:

* Inicialize uma sessão com base numa mensagem que o gatilho lê da fila do Service Bus.

* Leia e processe todas as mensagens da mesma sessão na fila durante a corrente de fluxo de trabalho.

Para rever o ficheiro JSON deste modelo, consulte [GitHub: service-bus-sessions.jsem](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Para mais informações, consulte [o padrão do comboio sequencial - Azure Architecture Cloud Design Patterns](/azure/architecture/patterns/sequential-convoy).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nomes de Service Bus e uma [fila de ônibus de serviço,](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)que é uma entidade de mensagens que você usará na sua aplicação lógica. Estes itens e a sua aplicação lógica precisam de utilizar a mesma subscrição do Azure. Certifique-se de que seleciona **As sessões de ativação** quando criar a sua fila. Se não tiver estes itens, aprenda [a criar o seu espaço de nomes de Service Bus e uma fila.](../service-bus-messaging/service-bus-create-namespace-portal.md)

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Conhecimento básico sobre como criar aplicações lógicas. Se é novo em Azure Logic Apps, experimente o arranque rápido, [Crie o seu primeiro fluxo de trabalho automatizado](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Verifique o acesso ao espaço de nomes do Service Bus

Se não tem a certeza se a sua aplicação lógica tem permissões para aceder ao seu espaço de nomes de Service Bus, confirme essas permissões.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Encontre e selecione o seu *espaço de nomes* de Service Bus .

1. No menu namespace, em **Definições,** selecione **Políticas de acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de Gestão** para esse espaço de nome.

   ![Gerir permissões para o espaço de nomes do Service Bus](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Agora obtenha a cadeia de ligação para o seu espaço de nomes de Service Bus. Pode utilizar esta cadeia mais tarde quando criar uma ligação ao espaço de nome a partir da sua aplicação lógica.

   1. No painel **de políticas de acesso partilhado,** no âmbito da **Política,** selecione **RootManageSharedAccessKey**.
   
   1. Ao lado da sua cadeia de ligação primária, selecione o botão de cópia. Guarde o fio de ligação para utilização posterior.

      ![Cadeia de conexão de espaço de nome do serviço de cópia do serviço de serviço](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a sua cadeia de ligação está associada ao seu espaço de nomes de Service Bus ou a uma entidade de mensagens, como uma fila, procure a cadeia de ligação para o `EntityPath` parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para uma entidade específica, e não é a cadeia correta para usar com a sua aplicação lógica.

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Nesta secção, você cria uma aplicação lógica usando a entrega de ordem correlacionada usando o modelo **de sessões de ônibus de serviço,** que inclui o gatilho e ações para implementar este padrão de fluxo de trabalho. Também cria uma ligação ao seu espaço de nomes de Service Bus e especifica o nome da fila de ônibus de serviço que pretende utilizar.

1. No [portal Azure,](https://portal.azure.com)crie uma aplicação lógica em branco. A partir da página inicial do Azure, **selecione Criar uma** App lógica de  >  **integração**  >  **de** recursos.

1. Depois da galeria do modelo aparecer, percorra o vídeo e as secções de gatilho comum. A partir da secção **Modelos,** selecione o modelo, **correlacionada entrega por encomenda usando sessões de ônibus de serviço**.

   ![Selecione o modelo "Entrega correlacionada por encomenda utilizando sessões de autocarro de serviço"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Quando a caixa de confirmação aparecer, selecione **Utilize este modelo**.

1. No Logic App Designer, na forma **de Service Bus,** selecione **Continue** e, em seguida, selecione o sinal de mais ( ) que aparece **+** na forma.

   ![Selecione "Continue" para ligar ao ônibus da Azure Service](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Agora crie uma ligação service bus escolhendo qualquer uma das opções:

   * Para utilizar a cadeia de ligação que copiou anteriormente do seu espaço de nomes de Service Bus, siga estes passos:

     1. Selecione **Informação de ligação manual**.

     1. Para **obter o nome de ligação,** forneça um nome para a sua ligação. Para **a cadeia de ligação,** cole a cadeia de ligação do seu espaço de nome e selecione **Criar,** por exemplo:

        ![Introduza o nome de ligação e a cadeia de conexão Service Bus](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Se não tiver esta cadeia de ligação, aprenda a [encontrar e copiar a cadeia de conexão de nomes do Service Bus](#permissions-connection-string).

   * Para selecionar um espaço de nomes de Service Bus a partir da subscrição atual do Azure, siga estes passos:

     1. Para **obter o nome de ligação,** forneça um nome para a sua ligação. Para **Service Bus Namespace**, selecione o seu espaço de nomes de Service Bus, por exemplo:

        ![Insira o nome da ligação e selecione o espaço de nomes do Service Bus](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Quando aparecer o próximo painel, selecione a sua política de ônibus de serviço e selecione **Criar**.

        ![Selecione a política do ônibus de serviço e, em seguida, "Criar"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Quando terminar, **selecione Continue**.

   O Logic App Designer mostra agora a entrega correlacionada por encomenda utilizando o modelo **de sessões de autocarro de serviço,** que contém um fluxo de trabalho pré-povoado com um gatilho e ações, incluindo dois âmbitos que implementam o manuseamento de erros que seguem o `Try-Catch` padrão.

Agora você pode aprender mais sobre o gatilho e as ações no modelo, ou saltar à frente para [fornecer os valores para o modelo de aplicação lógica](#complete-template).

<a name="template-summary"></a>

## <a name="template-summary"></a>Resumo do modelo

Aqui está o fluxo de trabalho de alto nível na entrega de encomenda correlacionada usando o modelo **de sessões de ônibus de serviço** quando os detalhes são colapsados:

![Fluxo de trabalho de alto nível do modelo](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Nome | Descrição |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Com base na recorrência especificada, este gatilho do Service Bus verifica a fila de serviços especificado para qualquer mensagem. Se existe uma mensagem na fila, o gatilho dispara, o que cria e executa uma instância de fluxo de trabalho. <p><p>O termo *"peek-lock"* significa que o gatilho envia um pedido para recuperar uma mensagem da fila. Se existir uma mensagem, o gatilho recupera e bloqueia a mensagem de modo a que nenhum outro tratamento aconteça nessa mensagem até que o período de bloqueio expire. Para mais detalhes, [inicialize a sessão](#initialize-session). |
| **`Init isDone`** | Esta [ **ação variável inicializa**](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) uma variável Booleana que está definida `false` e indica quando as seguintes condições são verdadeiras: <p><p>- Não há mais mensagens disponíveis para ler. <br>- O bloqueio da sessão já não precisa de ser renovado para que a atual instância de fluxo de trabalho possa terminar. <p><p>Para mais detalhes, consulte [Initialize the session](#initialize-session). |
| **`Try`** | Esta ação [ **Scope**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contém as ações que correm para processar uma mensagem. Se um problema acontecer no `Try` âmbito, a ação scope subsequente `Catch`  trata desse problema. Para mais informações, consulte [o âmbito "Tente".](#try-scope) |
| **`Catch`**| Esta ação [ **Scope**](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contém as ações que são executadas se um problema acontecer no `Try` âmbito anterior. Para mais informações, consulte [o âmbito "Catch".](#catch-scope) |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Âmbito "Tentar"

Aqui está o fluxo de alto nível na `Try` [ação](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) de âmbito quando os detalhes são colapsados:

![Fluxo de trabalho de ação de âmbito "Tente"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Nome | Descrição |
|------|-------------|
| **`Send initial message to topic`** | Pode substituir esta ação por qualquer ação que queira lidar com a primeira mensagem da sessão na fila. O ID da sessão especifica a sessão. <p><p>Para este modelo, uma ação de Service Bus envia a primeira mensagem para um tópico de ônibus de serviço. Para mais informações, consulte [Handle the initial message](#handle-initial-message). |
| (ramo paralelo) | Esta [ação paralela do ramo](../logic-apps/logic-apps-control-flow-branches.md) cria dois caminhos: <p><p>- #1 de ramificação: Continue a processar a mensagem. Para obter mais informações, consulte [Branch #1: Complete a mensagem inicial na fila](#complete-initial-message). <p><p>- Filial #2: Abandone a mensagem se algo correr mal e liberte-a para ser recolhida por outro gatilho. Para mais informações, consulte [Branch #2: Abandone a mensagem inicial da fila](#abandon-initial-message). <p><p>Ambos os caminhos se juntam mais tarde na **sessão Close numa fila e sucedem** a ação, descrita na linha seguinte. |
| **`Close a session in a queue and succeed`** | Esta ação de Service Bus junta-se aos ramos previamente descritos e fecha a sessão na fila após qualquer um dos seguintes eventos acontecerem: <p><p>- O fluxo de trabalho termina o processamento de mensagens disponíveis na fila. <br>- O fluxo de trabalho abandona a mensagem inicial porque algo correu mal. <p><p>Para mais detalhes, consulte [Fechar uma sessão em fila e ter sucesso](#close-session-succeed). |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>#1 de filial: Complete a mensagem inicial na fila

| Nome | Descrição |
|------|-------------|
| `Complete initial message in queue` | Esta ação do Service Bus marca uma mensagem recuperada com sucesso como completa e remove a mensagem da fila para evitar o reprocessamento. Para mais informações, consulte [Handle the initial message](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Este loop [ **Até**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) que o loop continue a receber mensagens enquanto as mensagens existem ou até que uma hora passe. Para obter mais informações sobre as ações neste ciclo, consulte [Enquanto houver mais mensagens para a sessão na fila.](#while-more-messages-for-session) |
| **`Set isDone = true`** | Quando não existem mais mensagens, esta [ **ação variável definida**](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) `isDone` define-se para `true` . |
| **`Renew session lock until cancelled`** | Este loop [ **Até**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) que o loop certifique-se de que o bloqueio de sessão é mantido por esta aplicação lógica enquanto as mensagens existem ou até que uma hora passe. Para obter mais informações sobre as ações neste ciclo, consulte [renovar o bloqueio da sessão até ser cancelado](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>#2 de filial: Abandone a mensagem inicial da fila

Se a ação que trata da primeira mensagem falhar, a ação do Service Bus, **abandone a mensagem inicial da fila,** lança a mensagem para outra instância de fluxo de trabalho executada para recolher e processar. Para mais informações, consulte [Handle the initial message](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Âmbito "Catch"

Se as ações no `Try` âmbito falharem, a aplicação lógica ainda deve fechar a sessão. A `Catch` [ação de âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) é de âmbito quando a ação de `Try` âmbito resulta no estado, ou `Failed` `Skipped` `TimedOut` . O âmbito devolve uma mensagem de erro que inclui o ID da sessão onde o problema aconteceu e termina a aplicação lógica.

Aqui está o fluxo de alto nível na ação de `Catch` âmbito quando os detalhes são colapsados:

![Fluxo de trabalho de ação de âmbito "Catch"](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Nome | Descrição |
|------|-------------|
| **`Close a session in a queue and fail`** | Esta ação de Service Bus encerra a sessão na fila para que o bloqueio da sessão não fique aberto. Para mais detalhes, consulte [Fechar uma sessão numa fila e falhar](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Esta ação [ **Filter Array**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria um conjunto a partir das entradas e saídas de todas as ações dentro do `Try` âmbito com base nos critérios especificados. Neste caso, esta ação devolve os resultados das ações que resultaram em `Failed` estatuto. Para mais detalhes, consulte [Find Fail MSG do bloco 'Try'.](#find-failure-message) |
| **`Select error details`** | Esta ação [ **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON com base nos critérios especificados. Estes objetos JSON são construídos a partir dos valores da matriz criada pela ação anterior, `Find failure msg from 'Try' block` . Neste caso, esta ação devolve um conjunto que contém um objeto JSON criado a partir dos detalhes de erro devolvidos da ação anterior. Para mais detalhes, consulte [Selecionar detalhes de erro .](#select-error-details) |
| **`Terminate`** | Esta ação [ **terminada**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) interrompe a execução do fluxo de trabalho, cancela quaisquer ações em curso, ignora quaisquer ações restantes e devolve o estado especificado, o ID da sessão e o resultado do erro da `Select error details` ação. Para mais detalhes, consulte [a aplicação lógica Terminate](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Complete o modelo

Para fornecer os valores para o gatilho e ações na entrega correlacionada de encomenda usando o modelo **de sessões de ônibus de serviço,** siga estes passos. Tem de fornecer todos os valores necessários, que são marcados por um asterisco antes de **\*** poder guardar a sua aplicação lógica.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inicializar a sessão

* Para o quando uma mensagem é recebida num gatilho **de fila (peek-lock),** forneça estas informações para que o modelo possa inicializar uma sessão utilizando a propriedade **de id Session,** por exemplo:

  ![Service Bus despoletou detalhes para "Quando uma mensagem é recebida em uma fila (peek-lock)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Inicialmente, o intervalo de votação é definido para três minutos para que a aplicação lógica não corra com mais frequência do que se espera e resulte em taxas de faturação imprevistas. Idealmente, desaprote o intervalo e a frequência para 30 segundos para que a aplicação lógica dispare imediatamente quando uma mensagem chegar.

  | Propriedade | Necessário para este cenário | Valor | Descrição |
  |----------|----------------------------|-------|-------------|
  | **Nome da fila** | Yes | <*nome de fila*> | O nome da sua fila de ônibus de serviço anteriormente criada. Este exemplo utiliza "Fabrikam-Service-Bus-Queue". |
  | **Tipo de fila** | Yes | **Principal** | Sua fila de ônibus de serviço primário |
  | **Id de sessão** | Yes | **Próximo disponível** | Esta opção obtém uma sessão para cada gatilho, com base no ID da sessão a partir da mensagem na fila do Service Bus. A sessão também está bloqueada para que nenhuma outra aplicação lógica ou outro cliente possa processar mensagens relacionadas com esta sessão. As ações subsequentes do fluxo de trabalho processam todas as mensagens que estão associadas a essa sessão, como descrito mais tarde neste artigo. <p><p>Aqui está mais informações sobre as outras opções **de id session:** <p>- **Nenhuma**: A opção padrão, que não resulta em sessões e não pode ser usada para implementar o padrão do comboio sequencial. <p>- **Insira o valor personalizado**: Utilize esta opção quando souber o ID da sessão que pretende utilizar e sempre pretenda executar o gatilho para esse ID de sessão. <p>**Nota:** O conector Service Bus pode guardar um número limitado de sessões únicas de cada vez desde a Azure Service Bus até à cache do conector. Se a contagem de sessão exceder este limite, as sessões antigas são removidas da cache. Para obter mais informações, consulte [mensagens exchange na nuvem com Azure Logic Apps e Azure Service Bus](../connectors/connectors-create-api-servicebus.md#connector-reference). |
  | **Intervalo** | Yes | <*número de intervalos*> | O número de unidades de tempo entre recorrências antes de verificar uma mensagem. |
  | **Frequência** | Yes | **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana** ou **Mês** | A unidade de tempo para a recorrência usar ao verificar uma mensagem. <p>**Sugestão**: Para adicionar um **fuso horário** ou **hora de início,** selecione estas propriedades da nova lista **de parâmetros** adicionar. |
  |||||

  Para obter mais informações sobre o gatilho, consulte [o Service Bus - Quando uma mensagem é recebida numa fila (peek-lock)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). O gatilho produz um [ServiceBusMessage](/connectors/servicebus/#servicebusmessage).

Após a inicialização da sessão, o fluxo de trabalho utiliza a ação **variável Initialize** para criar uma variável Booleana que inicialmente se definiu `false` e indica quando as seguintes condições são verdadeiras: 

* Não há mais mensagens disponíveis para ler.

* O bloqueio da sessão já não precisa de ser renovado para que a atual instância de fluxo de trabalho possa terminar.

![Detalhes de ação "Initialize Variable" para "Init isDone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Em seguida, no bloco **Try,** o fluxo de trabalho executa ações na primeira mensagem que é lida.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Lidar com a mensagem inicial

A primeira ação é uma ação de espaço reservado Service Bus, **Envie a mensagem inicial para o tópico**, que pode substituir por qualquer outra ação que pretenda lidar com a primeira mensagem da sessão na fila. O ID da sessão especifica a sessão de onde a mensagem teve origem.

A ação "Service Bus" de espaço reservado envia a primeira mensagem para um tópico de Service Bus especificado pela propriedade **Session Id.** Desta forma, todas as mensagens associadas a uma sessão específica vão para o mesmo tema. Todas as propriedades **de ID de Sessão** para ações subsequentes neste modelo usam o mesmo valor de ID de sessão.

![Detalhes de ação do Service Bus para "Enviar mensagem inicial para o tópico"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Na ação Service Bus, Complete a **mensagem inicial em fila,** forneça o nome da sua fila de Service Bus e mantenha todos os outros valores de propriedade predefinidos na ação.

   ![Detalhes de ação do Service Bus para "Complete a mensagem inicial na fila"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Na ação Service Bus, **abandone a mensagem inicial da fila,** forneça o nome da sua fila de Service Bus e mantenha todos os outros valores de propriedade predefinidos na ação.

   ![Detalhes da ação do Service Bus para "Abandonar a mensagem inicial da fila"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Em seguida, irá fornecer as informações necessárias para as ações que seguem a **mensagem inicial Completa em** ação de fila. Começará com as ações no **While there's more messages for the session in the queue** loop.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Enquanto há mais mensagens para a sessão na fila

Isto [ **Até** que o loop](../logic-apps/logic-apps-control-flow-loops.md#until-loop) executa estas ações enquanto as mensagens existem na fila ou até que uma hora passe. Para alterar o prazo do loop, edite o valor da propriedade **timeout** do loop.

* Receba mensagens adicionais da fila enquanto existem mensagens.

* Verifique o número de mensagens restantes. Se ainda existirem mensagens, continue a processar mensagens. Se não existirem mais mensagens, o fluxo de trabalho define `isDone` a variável para `true` , e sai do loop.

![Até loop - Processar mensagens durante a fila](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Na ação Service Bus, **obtenha mensagens adicionais da sessão,** forneça o nome da sua fila de Service Bus. Caso contrário, mantenha todos os outros valores de propriedade padrão na ação.

   > [!NOTE]
   > Por predefinição, o número máximo de mensagens é definido para `175` , mas este limite é afetado pelo tamanho da mensagem e propriedade de tamanho máximo de mensagem em Service Bus. Para obter mais informações, consulte [o tamanho da mensagem para uma fila](../service-bus-messaging/service-bus-quotas.md).

   ![Service Bus action - "Obter mensagens adicionais da sessão"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Em seguida, o fluxo de trabalho divide-se nestes ramos paralelos:

   * Se um erro ou falha acontecer durante a verificação de mensagens adicionais, desa estale a `isDone` variável para `true` .

   * O **Processo de mensagens se obtivermos alguma** condição verifica se o número de mensagens restantes é zero. Se existirem mensagens falsas e mais, continue a processar. Se não existirem mensagens verdadeiras e não existirem mais mensagens, o fluxo de trabalho define `isDone` a variável para `true` .

   ![Condição - Processar mensagens se houver](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Na secção **Se for falsa,** a **Para cada** loop processa cada mensagem na primeira ordem de primeira e primeira saída (FIFO). Nas **Definições** do loop, a definição de Controlo de **Concurrency** está definida para `1` , pelo que apenas uma única mensagem é processada de cada vez.

   ![Loop "Para cada" - Processe cada mensagem uma de cada vez](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Para as ações do Service Bus, **complete a mensagem em fila** e **abandone a mensagem em fila**, forneça o nome da sua fila de Autocarros de Serviço.

   ![Ações de ônibus de serviço - "Complete a mensagem em uma fila" e "Abandone a mensagem em uma fila"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Depois de **haver mais mensagens para a sessão na fila,** o fluxo de trabalho define a `isDone` variável para `true` .

Em seguida, irá fornecer as informações necessárias para as ações no bloqueio de **sessão Renovar até que** o loop cancelado.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Renovar o bloqueio da sessão até ser cancelado

Este loop [ **Até**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) que o loop certifique-se de que o bloqueio de sessão é mantido por esta aplicação lógica enquanto as mensagens existem na fila ou até que uma hora passe executando estas ações. Para alterar o prazo do loop, edite o valor da propriedade **timeout** do loop.

* Atrase por 25 segundos ou uma quantidade de tempo que seja inferior à duração do tempo de bloqueio para a fila que está a ser processada. A menor duração do bloqueio é de 30 segundos, pelo que o valor predefinido é suficiente. No entanto, pode otimizar o número de vezes que o loop funciona ajustando-se adequadamente.

* Verifique se a `isDone` variável está definida para `true` .

  * Se `isDone` não estiver definido, o fluxo de trabalho ainda está a `true` processar mensagens, pelo que o fluxo de trabalho renova o bloqueio da sessão na fila e verifica novamente o estado do loop.

    Você precisa fornecer o nome para sua fila de Service Bus na ação Service Bus, [**Renovar o bloqueio na sessão em uma fila**](#renew-lock-on-session).

  * Se `isDone` estiver `true` definido, o fluxo de trabalho não renova o bloqueio da sessão na fila e sai do circuito.

  ![Até loop - "Renovar o bloqueio da sessão até ser cancelado"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Renovar o bloqueio na sessão em uma fila

Esta ação service bus renova o bloqueio da sessão na fila enquanto o fluxo de trabalho ainda está a processar mensagens.

* Na ação Service Bus, **renove o bloqueio da sessão em fila,** forneça o nome da sua fila de Autocarros de Serviço.

  ![Service Bus action - "Renovar o bloqueio na sessão na fila"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Em seguida, você fornecerá as informações necessárias para a ação de Service Bus, **feche uma sessão em uma fila e tenha sucesso**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Feche uma sessão em uma fila e tenha sucesso

Esta ação de Service Bus fecha a sessão na fila depois de o fluxo de trabalho terminar de processar todas as mensagens disponíveis na fila, ou o fluxo de trabalho abandona a mensagem inicial.

* Na ação Service Bus, **feche uma sessão em fila e tenha sucesso,** forneça o nome da sua fila de Service Bus.

  ![Service Bus action - "Feche uma sessão em fila e tenha sucesso"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

As secções seguintes descrevem as ações na `Catch` secção, que lidam com erros e exceções que ocorrem no seu fluxo de trabalho.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Feche uma sessão em uma fila e falhe

Esta ação de Service Bus funciona sempre como a primeira ação no `Catch` âmbito e fecha a sessão na fila.

* Na ação Service Bus, **feche uma sessão em fila e falhe,** forneça o nome da sua fila de Autocarros de Serviço.

  ![Service Bus action - "Fechar uma sessão em fila e falhar"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Em seguida, o fluxo de trabalho cria um conjunto que tem as entradas e saídas de todas as ações no `Try` âmbito para que a aplicação lógica possa aceder a informações sobre o erro ou falha que aconteceu.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Encontre msg de falha do bloco 'Try'

Esta ação [ **Filter Array**](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria uma matriz que tem as entradas e saídas de todas as ações dentro do `Try` âmbito com base nos critérios especificados utilizando a [ `result()` função](../logic-apps/workflow-definition-language-functions-reference.md#result). Neste caso, esta ação devolve as saídas das ações que têm `Failed` estatuto utilizando a [ `equals()` função](../logic-apps/workflow-definition-language-functions-reference.md#equals) e [ `item()` a função.](../logic-apps/workflow-definition-language-functions-reference.md#item)

![Ação de matriz de filtro - "Encontre msg de falha do bloco 'Try' "](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Aqui está a definição JSON para esta ação:

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

Em seguida, o fluxo de trabalho cria uma matriz com um objeto JSON que contém as informações de erro na matriz devolvidas da `Find failure msg from 'Try' block` ação.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Selecione detalhes de erro

Esta ação [ **Select**](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON com base na matriz de entrada que é saída da ação anterior, `Find failure msg from 'Try' block` . Especificamente, esta ação devolve uma matriz que tem apenas as propriedades especificadas para cada objeto na matriz. Neste caso, a matriz contém o nome de ação e as propriedades de resultados de erro.

![Selecione ação - "Selecione detalhes de erro"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Aqui está a definição JSON para esta ação:

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

Em seguida, o fluxo de trabalho para a execução da aplicação lógica e devolve o estado de execução juntamente com mais informações sobre o erro ou falha que aconteceu.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>Executar aplicativos de lógica de fim

Esta [ação **Terminada**](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) para a execução da aplicação lógica e retorna `Failed` como o estado da aplicação lógica executado juntamente com o ID da sessão e o erro resulta da `Select error details` ação.

![Termine a ação para parar a execução de aplicações lógicas](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Aqui está a definição JSON para esta ação:

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>Guardar e executar aplicativo de lógica

Depois de completar o modelo, pode agora guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

Para testar a sua aplicação lógica, envie mensagens para a sua fila de Service Bus. 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [gatilhos e ações do conector do Service Bus](/connectors/servicebus/)

---
title: Envie mensagens correlacionadas em ordem usando um comboio sequencial
description: Envie mensagens relacionadas por ordem usando o padrão sequencial de comboio em Aplicativos Lógica Azure com ônibus de serviço Azure
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: f2a5ad78ecf4bf02e84b9bf2e37fea13c708e072
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143101"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Envie mensagens relacionadas por ordem usando um comboio sequencial em Aplicações Lógicas Azure com ônibus de serviço Azure

Quando precisa enviar mensagens correlacionadas numa ordem específica, pode seguir o padrão [ *sequencial* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy) de comboio ao utilizar [aplicações da Azure Logic](../logic-apps/logic-apps-overview.md) utilizando o [conector Azure Service Bus](../connectors/connectors-create-api-servicebus.md). As mensagens correlacionadas têm uma propriedade que define a relação entre essas mensagens, como o ID para a [sessão](../service-bus-messaging/message-sessions.md) no Service Bus.

Por exemplo, suponha que tem 10 mensagens para uma sessão chamada "Sessão 1", e tem 5 mensagens para uma sessão chamada "Session 2" que são todas enviadas para a mesma fila de [autocarros de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Pode criar uma aplicação lógica que processa mensagens a partir da fila para que todas as mensagens da "Sessão 1" sejam tratadas por um único gatilho e todas as mensagens da "Sessão 2" sejam tratadas pelo próximo gatilho.

![Padrão geral de comboio sequencial](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

Este artigo mostra como criar uma aplicação lógica que implementa este padrão usando a entrega em encomenda correlacionada usando o modelo **de sessões de ônibus** de serviço. Este modelo define um fluxo de trabalho de aplicação lógica que começa com o conector de ônibus de serviço Quando uma mensagem é recebida num gatilho de **fila (peek-lock),** que recebe mensagens de uma fila de [ônibus de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Aqui estão os passos de alto nível que esta aplicação lógica executa:

* Inicialize uma sessão com base numa mensagem que o gatilho lê da fila do Autocarro de Serviço.

* Leia e processe todas as mensagens da mesma sessão na fila durante a execução de fluxo de trabalho.

Para rever o ficheiro JSON deste modelo, consulte [gitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json).

Para mais informações, consulte o padrão do [comboio Sequencial - Padrões](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)de design de nuvem de arquitetura azure .

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Um espaço de nome de ônibus de serviço e uma [fila de ônibus de serviço](../service-bus-messaging/service-bus-queues-topics-subscriptions.md), que é uma entidade de mensagens que você vai usar na sua aplicação lógica. Estes itens e a sua aplicação lógica precisam de usar a mesma subscrição do Azure. Certifique-se de que seleciona **sessões de ativação** quando criar a sua fila. Se não tiver estes itens, aprenda [a criar o seu espaço](../service-bus-messaging/service-bus-create-namespace-portal.md)de nome service Bus e uma fila .

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* Conhecimento básico sobre como criar aplicações lógicas. Se é novo em Aplicações Lógicas Azure, experimente o quickstart, Crie o [seu primeiro fluxo de trabalho automatizado.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Verifique o acesso ao espaço de nome do ônibus de serviço

Se não tem a certeza se a sua aplicação lógica tem permissões para aceder ao seu espaço de nome saque de ônibus de serviço, confirme essas permissões.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Encontre e selecione o seu espaço de *nome*service Bus .

1. No menu espaço de nome, em **Definições,** selecione políticas de **acesso partilhado**. Em **Sinistros,** verifique se tem permissões **de gestão** para esse espaço de nome.

   ![Gerir permissões para o espaço de nome do ônibus de serviço](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Agora obtenha a corda de ligação para o seu espaço de nome service Bus. Pode usar esta corda mais tarde quando criar uma ligação ao espaço de nome a partir da sua aplicação lógica.

   1. No painel de políticas de **acesso partilhado,** em **Política,** selecione **RootManageSharedAccessKey**.
   
   1. Ao lado da corda de ligação primária, selecione o botão de cópia. Guarde a corda de ligação para posterior utilização.

      ![Linha de ligação nome do ônibus de serviço de cópia](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > Para confirmar se a sua cadeia de ligação está associada ao seu espaço de nome service Bus ou a uma entidade de mensagens, como uma fila, procure na cadeia de ligação o `EntityPath`   parâmetro. Se encontrar este parâmetro, a cadeia de ligação é para uma entidade específica, e não é a corda correta para usar com a sua aplicação lógica.

## <a name="create-logic-app"></a>Criar uma aplicação lógica

Nesta secção, você cria uma aplicação lógica usando a entrega em encomenda correlacionada usando o modelo de **sessões de ônibus** de serviço, que inclui o gatilho e ações para implementar este padrão de fluxo de trabalho. Também cria uma ligação ao seu espaço de nome do Ônibus de serviço e especifica o nome da fila service Bus que pretende utilizar.

1. No [portal Azure, crie](https://portal.azure.com)uma aplicação lógica em branco. A partir da página inicial do Azure, selecione **Criar uma**  >  **Integration**  >  **Aplicação Lógica**de Integração de Recursos .

1. Depois da galeria do modelo aparecer, percorra o vídeo e as secções comuns de gatilhos. Na secção **Modelos,** selecione o modelo, **entrega em encomenda correlacionada utilizando sessões de ônibus**de serviço .

   ![Selecione o modelo "Entrega correlacionada em encomenda utilizando sessões de autocarro de serviço"](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. Quando a caixa de confirmação aparecer, selecione **Utilize este modelo**.

1. No Logic App Designer, na forma do **Ônibus de serviço,** selecione **Continuar**, e depois selecione o sinal de mais ( **+** ) que aparece na forma.

   ![Selecione "Continue" para ligar ao Ônibus de Serviço Azure](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. Agora crie uma ligação de ônibus de serviço, escolhendo qualquer uma das opções:

   * Para utilizar a cadeia de ligação que copiou anteriormente do seu espaço de nome service Bus, siga estes passos:

     1. **Selecione introduza manualmente as informações**de ligação .

     1. Para **o Nome da Ligação,** forneça um nome para a sua ligação. Para **a Cadeia de Ligação,** reexa a sua cadeia de ligação espaço de nome e selecione **Criar,** por exemplo:

        ![Introduza o nome da ligação e a corda de ligação do ônibus de serviço](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > Se não tiver esta cadeia de ligação, aprenda a encontrar e copiar a cadeia de ligação nome [space do Ônibus de serviço](#permissions-connection-string).

   * Para selecionar um espaço de nome do Bus de Serviço da sua subscrição atual do Azure, siga estes passos:

     1. Para **o Nome da Ligação,** forneça um nome para a sua ligação. Para **service bus namespace,** selecione o seu espaço de nome do ônibus de serviço, por exemplo:

        ![Introduza o nome da ligação e selecione Service Bus namespace](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. Quando aparecer o próximo painel, selecione a sua política de ônibus de serviço e selecione **Criar**.

        ![Selecione a política de ônibus de serviço e, em seguida, "Criar"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. Quando terminar, selecione **Continue**.

   O Logic App Designer mostra agora a **entrega em encomenda correlacionada utilizando** o modelo de sessões de ônibus de serviço, que contém um fluxo de trabalho pré-povoado com um gatilho e ações, incluindo dois âmbitos que implementam o manuseamento de erros que seguem o `Try-Catch` padrão.

Agora você pode aprender mais sobre o gatilho e ações no modelo, ou saltar para a frente para [fornecer os valores para o modelo](#complete-template)de aplicação lógica .

<a name="template-summary"></a>

## <a name="template-summary"></a>Resumo do modelo

Aqui está o fluxo de trabalho de alto nível na entrega em encomenda correlacionada usando o modelo de **sessões de ônibus** de serviço quando os detalhes são colapsados:

![Fluxo de trabalho de alto nível do modelo](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| Name | Descrição |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | Com base na recorrência especificada, este gatilho de ônibus de serviço verifica a fila de ônibus de serviço especificada para quaisquer mensagens. Se existe uma mensagem na fila, o gatilho dispara, o que cria e executa uma instância de fluxo de trabalho. <p><p>O termo *"peek-lock"* significa que o gatilho envia um pedido para recuperar uma mensagem da fila. Se existir uma mensagem, o gatilho recupera e bloqueia a mensagem para que não aconteça outro processamento nessa mensagem até que o prazo de bloqueio expire. Para mais detalhes, [inicialize a sessão.](#initialize-session) |
| **`Init isDone`** | Esta ação [ **variável inicializar** ](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable) cria uma variável booleana que é definida `false` e indica quando as seguintes condições são verdadeiras: <p><p>- Não há mais mensagens na sessão disponíveis para ler. <br>- O bloqueio da sessão já não precisa de ser renovado para que a atual instância de fluxo de trabalho possa terminar. <p><p>Para mais detalhes, consulte [Inicializar a sessão](#initialize-session). |
| **`Try`** | Esta ação do [ **Scope** ](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) contém as ações que correm para processar uma mensagem. Se um problema ocorrer no `Try` âmbito, a ação scope subsequente `Catch` **Scope** trata desse problema. Para mais informações, consulte [o âmbito "Tente".](#try-scope) |
| **`Catch`**| Esta [ **Scope** ação](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) scope contém as ações que funcionam se um problema ocorrer no `Try` âmbito anterior. Para mais informações, consulte [o âmbito "Apanhar".](#catch-scope) |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>Âmbito "Tente"

Aqui está o fluxo de nível superior na ação de `Try` [âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) quando os detalhes são colapsados:

![Fluxo de trabalho de ação de âmbito "Tente"](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| Name | Descrição |
|------|-------------|
| **`Send initial message to topic`** | Pode substituir esta ação por qualquer ação que queira lidar com a primeira mensagem da sessão na fila. O ID da sessão especifica a sessão. <p><p>Para este modelo, uma ação de ônibus de serviço envia a primeira mensagem para um tópico de ônibus de serviço. Para mais detalhes, consulte [tratar da mensagem inicial](#handle-initial-message). |
| (ramo paralelo) | Esta [ação paralela](../logic-apps/logic-apps-control-flow-branches.md) cria dois caminhos: <p><p>- Sucursal #1: Continue a processar a mensagem. Para mais informações, consulte [Branch #1: Complete](#complete-initial-message)a mensagem inicial na fila . <p><p>- Ramo #2: Abandone a mensagem se algo correr mal e liberte para recolha por outra execução do gatilho. Para mais informações, consulte [Branch #2: Abandone](#abandon-initial-message)a mensagem inicial da fila . <p><p>Ambos os caminhos juntam-se mais tarde na **sessão close em uma fila e sucesso** ação, descrito na próxima fila. |
| **`Close a session in a queue and succeed`** | Esta ação do Service Bus junta-se aos balcões previamente descritos e encerra a sessão na fila depois de qualquer um dos seguintes eventos acontecer: <p><p>- O fluxo de trabalho termina o processamento de mensagens disponíveis na fila. <br>- O fluxo de trabalho abandona a mensagem inicial porque algo correu mal. <p><p>Para mais detalhes, consulte [Fechar uma sessão numa fila e ter sucesso.](#close-session-succeed) |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>#1: Completar mensagem inicial na fila

| Name | Descrição |
|------|-------------|
| `Complete initial message in queue` | Esta ação do Ônibus de serviço marca uma mensagem recuperada com sucesso como completa e remove a mensagem da fila para evitar o reprocessamento. Para mais detalhes, consulte [tratar da mensagem inicial](#handle-initial-message). |
| `While there are more messages for the session in the queue` | Isto [ **até** ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) que o loop continue a receber mensagens enquanto as mensagens existem ou até passar uma hora. Para obter mais informações sobre as ações neste ciclo, consulte [Enquanto há mais mensagens para a sessão na fila](#while-more-messages-for-session). |
| **`Set isDone = true`** | Quando não existem mais mensagens, este Conjunto de ação [ **variável** ](../logic-apps/logic-apps-create-variables-store-values.md#set-variable) define `isDone` para `true` . |
| **`Renew session lock until cancelled`** | Isto [ **Até** o loop](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garante que o bloqueio da sessão é mantido por esta aplicação lógica enquanto as mensagens existem ou até passar uma hora. Para mais informações sobre as ações neste ciclo, consulte Renovar o bloqueio da [sessão até ser cancelado](#renew-session-while-messages-exist). |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>Branch #2: Abandone a mensagem inicial da fila

Se a ação que trata a primeira mensagem falhar, a ação do Service Bus, Abandone a **mensagem inicial da fila,** lança a mensagem para outra corrida de fluxo de trabalho para captar e processar. Para mais detalhes, consulte [tratar da mensagem inicial](#handle-initial-message).

<a name="catch-scope"></a>

### <a name="catch-scope"></a>Âmbito "Catch"

Se as ações no `Try` âmbito falharem, a aplicação lógica ainda deve encerrar a sessão. A `Catch` [ação de âmbito](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) decorre quando a ação de âmbito `Try` resulta no `Failed` estado, ou `Skipped` `TimedOut` . O âmbito devolve uma mensagem de erro que inclui o ID da sessão onde o problema aconteceu, e termina a aplicação lógica.

Aqui está o fluxo de nível superior na ação de `Catch` âmbito quando os detalhes são colapsados:

![Fluxo de trabalho de ação de âmbito "Catch"](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| Name | Descrição |
|------|-------------|
| **`Close a session in a queue and fail`** | Esta ação do Ônibus de serviço encerra a sessão na fila para que o bloqueio da sessão não fique aberto. Para mais detalhes, consulte [Fechar uma sessão numa fila e falhar](#close-session-fail). |
| **`Find failure msg from 'Try' block`** | Esta ação [ **filter Array** ](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria uma matriz a partir das inputs e saídas de todas as ações dentro do `Try` âmbito com base nos critérios especificados. Neste caso, esta ação devolve as saídas das ações que resultaram no `Failed` estatuto. Para mais detalhes, consulte [Encontrar msg falha do bloco 'Try'.](#find-failure-message) |
| **`Select error details`** | Esta ação [ **Select** ](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON com base nos critérios especificados. Estes objetos JSON são construídos a partir dos valores da matriz criado pela ação anterior, `Find failure msg from 'Try' block` . Neste caso, esta ação devolve uma matriz que contém um objeto JSON criado a partir dos detalhes de erro devolvidos da ação anterior. Para mais detalhes, consulte [Selecione detalhes de erro](#select-error-details). |
| **`Terminate`** | Esta ação [ **de Cessação** ](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) interrompe a corrida para o fluxo de trabalho, cancela quaisquer ações em curso, ignora quaisquer ações restantes, e devolve o estado especificado, o ID da sessão e o erro resulta da `Select error details` ação. Para mais detalhes, consulte [a app lógica Terminate](#terminate-logic-app). |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>Complete o modelo

Para fornecer os valores para o gatilho e as ações na **entrega em encomenda correlacionadautilizando** o modelo de sessões de ônibus de serviço, siga estes passos. Tem de fornecer todos os valores necessários, que são marcados por um asterisco , antes de poder salvar a **\*** sua aplicação lógica.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>Inicializar a sessão

* Para o Quando uma mensagem é recebida num gatilho **de fila (peek-lock),** forneça esta informação para que o modelo possa inicializar uma sessão utilizando a propriedade **id** session, por exemplo:

  ![Detalhes do gatilho do ônibus de serviço para "Quando uma mensagem é recebida em uma fila (peek-lock)"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > Inicialmente, o intervalo de votação é definido para três minutos para que a aplicação lógica não corra com mais frequência do que se espera e resulte em taxas de faturação inesperadas. Idealmente, detete o intervalo e a frequência para 30 segundos para que a aplicação lógica dispare imediatamente quando uma mensagem chegar.

  | Propriedade | Necessário para este cenário | Valor | Descrição |
  |----------|----------------------------|-------|-------------|
  | **Nome da fila** | Sim | <*nome da fila*> | O nome da sua fila de ônibus de serviço previamente criada. Este exemplo utiliza "Fabrikam-Service-Bus-Queue". |
  | **Tipo de fila** | Sim | **Principal** | Sua fila de ônibus de serviço primário |
  | **Id da sessão** | Sim | **A seguir disponível** | Esta opção obtém uma sessão para cada execução do gatilho, com base no ID da sessão a partir da mensagem na fila do Ônibus de serviço. A sessão também está bloqueada para que nenhuma outra aplicação lógica ou outro cliente possa processar mensagens relacionadas com esta sessão. As ações subsequentes do fluxo de trabalho processam todas as mensagens que estão associadas a essa sessão, como descrito mais tarde neste artigo. <p><p>Aqui está mais informações sobre as outras opções **de id session:** <p>- **Nenhuma**: A opção predefinida, que não resulta em sessões e não pode ser usada para implementar o padrão sequencial do comboio. <p>- **Introduza**o valor personalizado : Utilize esta opção quando souber o ID da sessão que pretende utilizar e sempre deseja executar o gatilho para essa identificação da sessão. <p>**Nota:** O conector Service Bus pode guardar um número limitado de sessões únicas de cada vez, desde o Azure Service Bus até à cache do conector. Se a contagem de sessões exceder este limite, as sessões antigas são removidas da cache. Para mais informações, consulte [a Troca de Mensagens na nuvem com as Aplicações Da Lógica Azure e o Azure Service Bus.](../connectors/connectors-create-api-servicebus.md#connector-reference) |
  | **Intervalo** | Sim | <*número de intervalos*> | O número de unidades de tempo entre recorrências antes de verificar uma mensagem. |
  | **Frequência** | Sim | **Segundo**, **Minuto,** **Hora,** **Dia,** **Semana**ou **Mês** | A unidade de tempo para a recorrência usar ao verificar uma mensagem. <p>**Dica**: Para adicionar um **fuso horário** ou tempo de **início,** selecione estas propriedades na **lista de novos parâmetros Adicionar.** |
  |||||

  Para obter mais informações sobre o gatilho, consulte [Service Bus - Quando uma mensagem é recebida numa fila (espreitar)](https://docs.microsoft.com/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock)). O gatilho produz um [ServiceBusMessage](https://docs.microsoft.com/connectors/servicebus/#servicebusmessage).

Após a inicialização da sessão, o fluxo de trabalho utiliza a ação **variável Inicializar** para criar uma variável booleana que inicialmente se define `false` e indica quando as seguintes condições são verdadeiras: 

* Não há mais mensagens na sessão disponíveis para ler.

* O bloqueio da sessão já não precisa de ser renovado para que a atual instância de fluxo de trabalho possa terminar.

![Detalhes de ação "Inicializar Variável" para "Init isDone"](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

Em seguida, no bloco **Try,** o fluxo de trabalho executa ações na primeira mensagem que é lida.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>Lidar com a mensagem inicial

A primeira ação é uma ação placeplace Service Bus, **Enviar mensagem inicial para tópico**, que pode substituir por qualquer outra ação que queira lidar com a primeira mensagem da sessão na fila. O ID da sessão especifica a sessão de onde a mensagem originou.

A ação do placeplaceplace Service Bus envia a primeira mensagem para um tópico de ônibus de serviço que é especificado pela propriedade **Session Id.** Dessa forma, todas as mensagens associadas a uma sessão específica vão para o mesmo tema. Todas as propriedades de Id da **Sessão** para ações subsequentes neste modelo usam o mesmo valor de ID da sessão.

![Detalhes da ação do ônibus de serviço para "Enviar mensagem inicial para tópico"](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Na ação Service Bus, Complete a **mensagem inicial na fila,** forneça o nome para a sua fila de ônibus de serviço, e mantenha todos os outros valores de propriedade padrão na ação.

   ![Detalhes da ação do ônibus de serviço para "Complete mensagem inicial na fila"](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Na ação Service Bus, Abandone a **mensagem inicial da fila,** forneça o nome para a sua fila de ônibus de serviço e mantenha todos os outros valores de propriedade padrão na ação.

   ![Detalhes da ação do Bus de serviço para "Abandonar a mensagem inicial da fila"](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

Em seguida, fornecerá as informações necessárias para as ações que seguem a mensagem inicial Completa em ação **de fila.** Começará com as ações no **While have more messages for the session in the queue** loop.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>Enquanto há mais mensagens para a sessão na fila

Isto [ **até** que](../logic-apps/logic-apps-control-flow-loops.md#until-loop) o loop execute estas ações enquanto as mensagens existem na fila ou até passar uma hora. Para alterar o limite de tempo do loop, edite o valor da propriedade **Timeout** do loop.

* Obtenha mensagens adicionais da fila enquanto existem mensagens.

* Verifique o número de mensagens restantes. Se as mensagens ainda existirem, continue a processar mensagens. Se não existirem mais mensagens, o fluxo de trabalho define a `isDone` variável `true` para e sai do loop.

![Até loop - Processe as mensagens durante a fila](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Na ação Service Bus, **obtenha mensagens adicionais da sessão,** forneça o nome para a sua fila de ônibus de serviço. Caso contrário, mantenha todos os outros valores de propriedade predefinidos na ação.

   > [!NOTE]
   > Por padrão, o número máximo de mensagens está definido para, mas este limite é afetado pelo tamanho da mensagem e propriedade de tamanho máximo de `175` mensagem em Service Bus. Atualmente, este limite é de 256K para Standard e 1 MB para Premium.

   ![Ação de ônibus de serviço - "Obtenha mensagens adicionais da sessão"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   Em seguida, o fluxo de trabalho divide-se nestes ramos paralelos:

   * Se ocorrer um erro ou falha durante a verificação de mensagens adicionais, desloque a `isDone` variável para `true` .

   * As **mensagens do Processo se obtivermos alguma** condição verifica se o número de mensagens restantes é zero. Se existirem mensagens falsas e mais mensagens, continue a processar. Se existirem mensagens verdadeiras e não existirem mais mensagens, o fluxo de trabalho define a `isDone` variável para `true` .

   ![Condição - Processar mensagens se houver](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   Na secção **If false,** a **Para cada** loop processa cada mensagem em primeira ordem (FIFO). Nas **Definições**do loop , a definição de **Controlo de Moedaestá** definida para, pelo que apenas uma mensagem é processada de `1` cada vez.

   ![Loop "Para cada" - Processe cada mensagem uma de cada vez](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Para as ações do Ônibus de serviço, **complete a mensagem numa fila** e **abandone a mensagem numa fila**, forneça o nome para a sua fila de ônibus de serviço.

   ![Ações de ônibus de serviço - "Complete a mensagem em fila" e "Abandone a mensagem numa fila"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   Depois de **haver mais mensagens para a sessão na fila,** o fluxo de trabalho define a `isDone` variável para `true` .

Em seguida, fornecerá as informações necessárias para as ações no bloqueio da **sessão Renovar até o** loop cancelado.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>Renovar bloqueio de sessão até ser cancelado

Isto [ **Até** o loop](../logic-apps/logic-apps-control-flow-loops.md#until-loop) garante que o bloqueio da sessão é mantido por esta aplicação lógica enquanto as mensagens existem na fila ou até que uma hora passe executando estas ações. Para alterar o limite de tempo do loop, edite o valor da propriedade **Timeout** do loop.

* Atrase por 25 segundos ou uma quantidade de tempo inferior à duração do tempo de bloqueio para a fila que está a ser processada. A menor duração do bloqueio é de 30 segundos, por isso o valor predefinido é suficiente. No entanto, pode otimizar o número de vezes que o loop corre ajustando-se adequadamente.

* Verifique se a `isDone` variável está definida para `true` .

  * Se `isDone` não estiver `true` definido, o fluxo de trabalho ainda está a processar mensagens, pelo que o fluxo de trabalho renova o bloqueio da sessão na fila e verifica novamente a condição do loop.

    Você precisa fornecer o nome para a sua fila de ônibus de serviço na ação Service Bus, [**Renovar o bloqueio na sessão em uma fila**](#renew-lock-on-session).

  * Se `isDone` estiver `true` programado, o fluxo de trabalho não renova o bloqueio da sessão na fila e sai do laço.

  ![Até ao loop - "Renovar o bloqueio da sessão até ser cancelado"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>Renovar o bloqueio na sessão em fila

Esta ação do Ônibus de serviço renova o bloqueio da sessão na fila enquanto o fluxo de trabalho ainda está a processar mensagens.

* Na ação Service Bus, **Renovar o bloqueio na sessão numa fila,** forneça o nome para a sua fila de ônibus de serviço.

  ![Ação do Ônibus de serviço - "Renovar o bloqueio na sessão na fila"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

Em seguida, você fornecerá as informações necessárias para a ação do Ônibus de serviço, **feche uma sessão em fila e tenha sucesso**.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>Feche uma sessão em fila e tenha sucesso

Esta ação do Ônibus de serviço encerra a sessão na fila depois de o fluxo de trabalho terminar de processar todas as mensagens disponíveis na fila, ou o fluxo de trabalho abandona a mensagem inicial.

* Na ação Service Bus, **Feche uma sessão em fila e tenha sucesso,** forneça o nome para a sua fila de ônibus de serviço.

  ![Ação do Ônibus de Serviço - "Feche uma sessão em fila e tenha sucesso"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

As seguintes secções descrevem as ações na `Catch` secção, que lidam com erros e exceções que ocorrem no seu fluxo de trabalho.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>Feche uma sessão em fila e falhe

Esta ação do Ônibus de serviço funciona sempre como a primeira ação no `Catch` âmbito e encerra a sessão na fila.

* Na ação Service Bus, **feche uma sessão em fila e falhe,** forneça o nome para a sua fila de ônibus de serviço.

  ![Ação do Ônibus de Serviço - "Feche uma sessão em fila e falhe"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

Em seguida, o fluxo de trabalho cria uma matriz que tem as inputs e saídas de todas as ações no âmbito para que a aplicação lógica possa aceder a `Try` informações sobre o erro ou falha que aconteceu.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>Encontre falha no bloco 'Try'

Esta ação [ **Filter Array** ](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) cria uma matriz que tem as inputs e saídas de todas as ações dentro do `Try` âmbito com base nos critérios especificados utilizando a [ `result()` função](../logic-apps/workflow-definition-language-functions-reference.md#result). Neste caso, esta ação devolve as saídas das ações que têm `Failed` estatuto utilizando a [ `equals()` função](../logic-apps/workflow-definition-language-functions-reference.md#equals) e [ `item()` a função](../logic-apps/workflow-definition-language-functions-reference.md#item).

![Ação de matriz de filtro - "Encontre msg falha do bloco 'Tente'](./media/send-related-messages-sequential-convoy/find-failure-message.png)

Aqui está a definição jSON para esta ação:

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

Em seguida, o fluxo de trabalho cria uma matriz com um objeto JSON que contém a informação de erro na matriz devolvida da `Find failure msg from 'Try' block` ação.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>Selecione detalhes de erro

Esta ação [ **Select** ](../logic-apps/logic-apps-perform-data-operations.md#select-action) cria uma matriz que contém objetos JSON baseados na matriz de entrada que é saída da ação anterior, `Find failure msg from 'Try' block` . Especificamente, esta ação devolve uma matriz que tem apenas as propriedades especificadas para cada objeto na matriz. Neste caso, a matriz contém o nome de ação e as propriedades do resultado do erro.

![Selecione ação - "Selecione detalhes de erro"](./media/send-related-messages-sequential-convoy/select-error-details.png)

Aqui está a definição jSON para esta ação:

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

### <a name="terminate-logic-app-run"></a>Executar app lógica de terminar

Esta ação [ **de Terminate** ](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action) para a execução da aplicação lógica e retorna `Failed` como o estado da aplicação lógica, juntamente com o ID da sessão e o erro resulta da `Select error details` ação.

![Terminar ação para parar a execução de aplicações lógicas](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

Aqui está a definição jSON para esta ação:

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

## <a name="save-and-run-logic-app"></a>Salvar e executar app lógica

Depois de completar o modelo, agora pode salvar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Para testar a sua aplicação lógica, envie mensagens para a sua fila de ônibus de serviço. 

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre os [gatilhos e ações do conector de ônibus de serviço](https://docs.microsoft.com/connectors/servicebus/)

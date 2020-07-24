---
title: Construir fluxos de trabalho automatizados baseados em horários
description: Tutorial - Criar um fluxo de trabalho automatizado baseado em horários, recorrentes e automatizados utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: 977f138fad1f2eb1eae95049d2bd8a730ba5687e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048729"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados, baseados em horários e recorrentes utilizando apps Azure Logic

Este tutorial mostra como construir uma [aplicação lógica](../logic-apps/logic-apps-overview.md) e automatizar um fluxo de trabalho recorrente que funciona em um horário. Especificamente, esta aplicação lógica de exemplo corre todos os dias da semana e verifica o tempo de viagem, incluindo o tráfego, entre dois lugares. Se o tempo exceder um limite específico, a aplicação lógica envia um e-mail com o tempo de deslocação e o tempo adicional necessário para chegar ao destino.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicione um gatilho de recorrência que especifica o calendário da sua aplicação lógica.
> * Adicione uma ação Bing Maps que obtém o tempo de viagem para uma rota.
> * Adicione uma ação que cria uma variável, converte o tempo de viagem de segundos para minutos, e armazena que resultam na variável.
> * Adicionar uma condição que compara o tempo de deslocação face a um limite especificado.
> * Adicione uma ação que lhe envia e-mail se o tempo de viagem exceder o limite.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Visão geral do fluxo de trabalho de aplicações lógicas de alto nível](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se para uma conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* Uma conta de e-mail de um fornecedor de e-mail que é suportado por Aplicações Lógicas, como Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/). Este quickstart utiliza uma conta Office 365 Outlook. Se utilizar uma conta de e-mail diferente, os passos gerais permanecem os mesmos, mas a sua UI pode diferir ligeiramente.

  > [!IMPORTANT]
  > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Para obter o tempo de deslocação para um percurso, precisa de uma chave de acesso para a API do Mapas Bing. Para obter esta chave, siga os passos em [How to get a Bing Maps key](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) (Como obter uma chave do Mapas Bing).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. A partir do menu Azure principal, **selecione Criar uma**App lógica de  >  **integração**  >  **de**recursos.

   ![Crie o seu recurso de aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Em **Criar aplicação lógica**, indique estas informações sobre a sua aplicação lógica, conforme mostrado e descrito. Quando concluir, selecione **Criar**.

   ![Forneça informações sobre a sua aplicação lógica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | LA-TravelTime | O nome da sua aplicação lógica, que contém apenas letras, números, hífenes `-` (), sublinha `_` (), parênteses ( `(` , ) e `)` períodos `.` (). Este exemplo utiliza "LA-TravelTime". |
   | **Subscrição** | <*seu-Azure-nome de subscrição*> | O seu nome de subscrição Azure |
   | **Grupo de recursos** | LA-TravelTime-RG | O nome para o [grupo de recursos Azure,](../azure-resource-manager/management/overview.md)que é usado para organizar recursos relacionados. Este exemplo utiliza "LA-TravelTime-RG". |
   | **Localização** | E.U.A. Oeste | TThe região onde armazenar a sua informação lógica de aplicações. Este exemplo usa "West US". |
   | **Log Analytics** | Desligado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Depois de o Azure implementar a sua aplicação, na barra de **ferramentas**Azure, selecione Notifications  >  **Go para o recurso** para a sua aplicação lógica implementada.

   ![Vá para o seu novo recurso de aplicativo lógica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou, pode encontrar e selecionar a sua aplicação lógica digitando o nome na caixa de pesquisa.

   O Logic Apps Designer abre e mostra uma página com um vídeo de introdução e comumente usados gatilhos e padrões de aplicações lógicas. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecione modelo de aplicativo de lógica em branco](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Em seguida, adicione o [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts)de Recorrência , que dispara com base num horário especificado. Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adicione o gatilho de recorrência

1. No Logic App Designer, na caixa de pesquisa, introduza a "recorrência" como filtro. Na lista **'Gatilhos',** selecione o gatilho **de Recorrência.**

   ![Adicionar o gatilho "Recorrência"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na forma **de Recorrência,** selecione o botão **elipses** **(...**) e, em seguida, selecione **Rename**. Mude o nome do acionador com a descrição `Check travel time every weekday morning`

   ![Mude o nome da descrição do gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dentro do gatilho, mude estas propriedades.

   ![Alterar o intervalo e a frequência do gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Intervalo** | Yes | 1 | O número de intervalos de espera entre verificações |
   | **Frequência** | Yes | Semana | A unidade de tempo a utilizar para a periodicidade |
   |||||

1. Em **Intervalo** e **Frequência,** abra a nova lista **de parâmetros add** e selecione estas propriedades para adicionar ao gatilho.

   * **On these days** (Nestes dias)
   * **At these hours** (A estas horas)
   * **At these minutes** (A estes minutos)

   ![Adicione propriedades para gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Agora descreva os valores para as propriedades adicionais como mostrado e descrito aqui.

   ![Indicar os detalhes da agenda e da periodicidade](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **On these days** (Nestes dias) | Monday,Tuesday,Wednesday,Thursday,Friday | Disponível apenas se **Frequency** (Frequência) estiver definida como "Week" |
   | **At these hours** (A estas horas) | 7,8,9 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar as horas do dia para executar esta periodicidade. Este exemplo é executado nas marcas das 7, 8 e 9 horas. |
   | **At these minutes** (A estes minutos) | 0,15,30,45 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar os minutos do dia para executar esta periodicidade. Este exemplo é executado a cada 15 minutos, começando na marca de hora zero. |
   ||||

   Este acionador é acionado todos os dias úteis, a cada 15 minutos a partir das 7:00 até às 9:45. A caixa **Pré-visualização** mostra a agenda da periodicidade. Para obter mais informações, veja [Schedule tasks and workflows](../connectors/connectors-native-recurrence.md) (Agendar tarefas e fluxos de trabalho) e [Workflow actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) (Ações e acionadores de fluxos de trabalho).

1. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título da forma.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não ser executada periodicamente. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de deslocação para um percurso

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de deslocação entre dois sítios. O Logic Apps disponibiliza um conector para a API do Mapas Bing para que possa obter facilmente estas informações. Antes de começar esta tarefa, confirme que tem uma chave de API do Mapas Bing, conforme descrito nos pré-requisitos do tutorial.

1. No Logic App Designer, sob o seu gatilho, selecione **Novo passo**.

1. Em **Escolha uma ação**, selecione **Standard**. Na caixa de pesquisa, insira "bing maps" como filtro e selecione a ação **de rota Get.**

   ![Selecione ação "Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se não tiver uma ligação ao Mapas Bing, é-lhe pedido que a crie. Forneça estes detalhes de conexão e selecione **Criar**.

   ![Criar ligação à Bing Maps API](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de conexão** | Yes | BingMapsConnection | Indique um nome para a ligação. Este exemplo utiliza "BingMapsConnection". |
   | **Chave de API** | Yes | <*sua-Bing-Maps-chave*> | Introduza a chave do Mapas Bing que recebeu anteriormente. Se não tiver uma chave do Mapas Bing, saiba [como obtê-la](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Mude o nome da ação com a descrição `Get route and travel time with traffic`

1. Dentro da ação, abra a **nova lista de parâmetros Add**e selecione estas propriedades para adicionar à ação.

   * **Otimizar**
   * **Distance unit**
   * **Travel mode**

   ![Adicione propriedades à ação "Obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Agora descreva os valores para as propriedades da ação como mostrado e descrito aqui.

   ![Forneça detalhes para a ação "Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Waypoint 1** | Yes | <*localização inicial*> | A origem do percurso |
   | **Waypoint 2** | Yes | <*localização final*> | O destino do percurso |
   | **Otimizar** | No | timeWithTraffic | Um parâmetro para otimizar o percurso, por exemplo, a distância, o tempo de deslocação com o tráfego atual e assim sucessivamente. Selecione o parâmetro "timeWithTraffic". |
   | **Distance unit** | No | <*sua preferência*> | A unidade de distância do percurso. Este exemplo usa "Mile" como unidade. |
   | **Travel mode** | No | Driving | O modo de deslocação para o percurso. Selecione o modo "Condução". |
   ||||

   Para obter mais informações sobre estes parâmetros, veja [Calculate a route](/bingmaps/rest-services/routes/calculate-a-route) (Calcular um percurso).

1. Guarde a sua aplicação lógica.

Em seguida, crie uma variável para que possa converter e armazenar o tempo de deslocação atual como minutos em vez de segundos. Desta forma, evita repetir a conversão e pode utilizar o valor mais facilmente em passos posteriores. 

## <a name="create-a-variable-to-store-travel-time"></a>Criar uma variável para armazenar o tempo de viagem

Por vezes, pode querer executar operações em dados no seu fluxo de trabalho e, em seguida, usar os resultados em ações posteriores. Para guardar estes resultados de modo a poder reutilizá-los ou referenciá-los facilmente, pode criar variáveis para armazená-los depois de os processar. Só pode criar variáveis ao nível superior na sua aplicação lógica.

Por predefinição, a ação de **rota Get** anterior devolve o tempo de viagem atual com tráfego em segundos a partir da propriedade **Travel Duration Traffic.** Ao converter e armazenar este valor em minutos, será mais fácil utilizá-lo mais tarde sem que tenha de reconvertê-lo.

1. No âmbito da ação **de rota Get,** selecione **Novo passo**.

1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza "variáveis", e selecione a ação **variável Initialize.**

   ![Selecione ação "Inicializar variável"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Mude o nome desta ação com a descrição `Create variable to store travel time`

1. Indique os detalhes da variável, conforme descrito aqui:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Yes | travelTime | O nome para a sua variável. Este exemplo utiliza "TravelTime". |
   | **Tipo** | Yes | Integer (Número inteiro) | O tipo de dados da variável |
   | **Valor** | No| Uma expressão que converte o tempo de deslocação atual de segundos em minutos (ver os passos nesta tabela). | O valor iniciar da variável |
   ||||

   1. Para criar a expressão para a propriedade **Valor,** clique dentro da caixa para que a lista de conteúdos dinâmicos apareça. Se necessário, alargue o browser até que a lista apareça. Na lista de conteúdos dinâmicos, selecione **Expression**.

      ![Fornecer informações para a ação "Initialize variable"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando clica dentro de algumas caixas de edição, aparece a lista de conteúdos dinâmicos. Esta lista mostra quaisquer propriedades de ações anteriores que pode usar como entradas no seu fluxo de trabalho. A lista de conteúdos dinâmicos tem um editor de expressão onde pode selecionar funções para executar operações. Este editor de expressões só aparece na lista de conteúdo dinâmico.

   1. No editor de expressões, introduza a expressão `div(,60)`

      ![Introduzir a expressão "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Coloque o cursor dentro da expressão, entre o parêntesis de abertura (**(**) e a vírgula (**,**). 
   **selecione conteúdo dinâmico**.

      ![Cursor de posição, selecione "Conteúdo dinâmico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na lista de conteúdo dinâmico, selecione **Travel Duration Traffic**.

      ![Selecione propriedade "Tráfego de Duração de Viagem"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Depois de o valor da propriedade resolver dentro da expressão, selecione **OK**.

      ![Para terminar a construção da expressão, selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A propriedade **Value** aparece agora como mostrado aqui:

      ![A propriedade "Valor" aparece com expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Guarde a sua aplicação lógica.

Em seguida, adicione uma condição que verifica se o tempo de deslocação atual é superior a um limite específico.

## <a name="compare-the-travel-time-with-limit"></a>Compare o tempo de viagem com o limite

1. De acordo com a ação anterior, selecione **Novo passo**.

1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza "condição" como filtro. Na lista de ações, selecione a ação **'Condição'.**

   ![Selecione ação "Condição"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Mude o nome da condição com a descrição `If travel time exceeds limit`

1. Construa uma condição que verifique se o valor da propriedade **travelTime** excede o seu limite especificado como descrito e mostrado aqui:

   1. Na condição, clique no interior da **Caixa de valor** no lado esquerdo da condição.

   1. A partir da lista de conteúdos dinâmicos que aparece, em **Variáveis,** selecione a propriedade **TravelTime.**

      ![Construa o lado esquerdo da condição](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Na caixa de comparação média, selecione o **é maior do que** o operador.

   1. Na **caixa de valor escolha uma** caixa de valor no lado direito da condição, insira este limite:`15`

      Quando terminar, a condição parece este exemplo:

      ![Condições terminadas para verificar o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Guarde a sua aplicação lógica.

Em seguida, adicione a ação a ser executada quando o tempo de viagem exceder o seu limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar um e-mail quando o limite for excedido

Agora, adicione uma ação que envia um e-mail quando o tempo de deslocação excede o limite. Este e-mail inclui o tempo de deslocação atual e o tempo extra necessário para percorrer o percurso especificado.

1. No ramo se for **verdadeiro,** **selecione Adicione uma ação**.

1. Em **Escolha uma ação**, selecione **Standard**. Na caixa de pesquisa, insira "enviar e-mail". A lista retorna muitos resultados, por isso, primeiro selecione o conector de e-mail que deseja, por exemplo:

   ![Selecione o conector de e-mail que deseja](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365).
   * Par contas Microsoft pessoais, selecione **Outlook.com**.

1. Quando as ações do conector aparecerem, selecione "enviar ação de e-mail" que pretende utilizar, por exemplo:

   ![Selecionar a ação "enviar e-mail"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail.

   O Logic Apps cria uma ligação para a sua conta de e-mail.

1. Mude o nome da ação com a descrição `Send email with travel time`

1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. Para fins de teste, utilize o seu endereço de e-mail.

1. Na caixa **Assunto**, especifique o assunto do e-mail e inclua a variável **travelTime**.

   1. Introduza o texto `Current travel time (minutes):` com um espaço à direita. 

   1. Na lista de conteúdos **dinâmicos, em Variáveis,** selecione Ver **mais**.

      ![Encontre a variável "TravelTime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Depois de **viajar O tempo** aparece em **Variáveis,** selecione **travelTime**.

      ![Introduzir o texto do assunto e a expressão que devolve o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Na caixa **Corpo**, especifique o conteúdo do corpo do e-mail.

   1. Introduza o texto `Add extra travel time (minutes):` com um espaço à direita.

   1. Na lista de conteúdos dinâmicos, selecione **Expression**.

      ![Criar expressão para o corpo do e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. No editor de expressões, introduza a expressão ```sub(,15)```, para poder calcular o número de minutos que excedem o limite.

      ![Introduzir a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Coloque o cursor dentro da expressão, entre o parêntesis de abertura (**(**) e a vírgula (**,**). Selecione **conteúdo dinâmico**.

      ![Continuar a criar a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Em **Variáveis**, selecione **travelTime**.

      ![Selecione a propriedade "TravelTime" para usar na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Depois de a propriedade resolver dentro da expressão, selecione **OK**.

      ![Depois de resolver a propriedade "Body", selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A propriedade **Body** agora aparece como mostrado aqui:

      ![Propriedade "Body" resolvida em expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Guarde a sua aplicação lógica.

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Fluxo de trabalho de aplicativo de lógica de exemplo terminado](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de barras de ferramentas do designer, selecione **Run**.

* Se o tempo de viagem atual permanecer abaixo do seu limite, a sua aplicação lógica não faz mais nada e espera ou o intervalo seguinte antes de verificar novamente. 

* Se o tempo de viagem atual exceder o seu limite, obtém um e-mail com o tempo de viagem atual e o número de minutos acima do seu limite. Eis um e-mail de exemplo que a sua aplicação lógica envia:

![Exemplo enviado e-mail que mostra o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Acabou de criar e executar uma aplicação lógica recorrente e baseada numa agenda. 

Para criar outras aplicações lógicas que usam o gatilho **de Recorrência,** confira estes modelos, que estão disponíveis depois de criar uma aplicação lógica:

* Receber lembretes diários.
* Eliminar blobs do Azure mais antigos.
* Adicionar uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar da aplicação lógica da amostra, elimine o grupo de recursos que contém a sua aplicação lógica e recursos relacionados. 

1. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos para a sua aplicação lógica.

1. No menu do grupo de recursos, selecione **Overview**  >  **Delete resource group**. 

   !["Descrição geral" > "Eliminar grupo de recursos"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Introduza o nome do grupo de recursos como confirmação e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que verifica o tráfego com base numa agenda específica (nos dias úteis de manhã) e realiza uma ação (envia e-mails) se o tempo de deslocação exceder um limite especificado. Agora, saiba como compilar uma aplicação lógica que envia pedidos de listas de correio para aprovação através da integração de serviços do Azure, de serviços Microsoft e de outras aplicações de SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md) (Gerir pedidos de listas de correio)

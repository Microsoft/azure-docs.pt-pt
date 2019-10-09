---
title: Criar fluxos de trabalho automatizados baseados em agenda-aplicativos lógicos do Azure
description: Tutorial – criar um fluxo de trabalho automatizado, recorrente e com base em agendamento usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/12/2019
ms.openlocfilehash: eae2319e8d1c162969a04f8dafa18eec671ee1d0
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034635"
---
# <a name="tutorial-create-automated-schedule-based-recurring-workflows-by-using-azure-logic-apps"></a>Tutorial: Crie fluxos de trabalho recorrentes, baseados em agendamento e automatizados usando aplicativos lógicos do Azure

Este tutorial mostra como criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) e automatizar um fluxo de trabalho recorrente que é executado em um agendamento. Especificamente, esse aplicativo lógico de exemplo é executado a cada dia da manhã e verifica o tempo de viagem, incluindo o tráfego, entre dois lugares. Se o tempo exceder um limite específico, a aplicação lógica envia um e-mail com o tempo de deslocação e o tempo adicional necessário para chegar ao destino.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicione um gatilho de recorrência que especifica a agenda para seu aplicativo lógico.
> * Adicione uma ação do Bing Maps que obtém o tempo de viagem de uma rota.
> * Adicione uma ação que cria uma variável, converte o tempo de viagem de segundos em minutos e armazena o resultado na variável.
> * Adicionar uma condição que compara o tempo de deslocação face a um limite especificado.
> * Adicione uma ação que envia email se o tempo de viagem exceder o limite.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Visão geral do fluxo de trabalho do aplicativo lógico de alto nível](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura, [Inscreva-se para uma conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

* Uma conta de email de um provedor de email com suporte de aplicativos lógicos, como o Office 365 Outlook, Outlook.com ou gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Este guia de início rápido usa uma conta do Outlook do Office 365. Se você usar uma conta de email diferente, as etapas gerais permanecerão as mesmas, mas a interface do usuário poderá ser ligeiramente diferente.

* Para obter o tempo de deslocação para um percurso, precisa de uma chave de acesso para a API do Mapas Bing. Para obter esta chave, siga os passos em [How to get a Bing Maps key](https://docs.microsoft.com/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) (Como obter uma chave do Mapas Bing).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No menu principal do Azure, selecione **criar um recurso** > **integração** > **aplicativo lógico**.

   ![Criar seu recurso de aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. Em **Criar aplicação lógica**, indique estas informações sobre a sua aplicação lógica, conforme mostrado e descrito. Quando terminar, selecione **Criar**.

   ![Fornecer informações sobre seu aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Value | Descrição |
   |----------|-------|-------------|
   | **Name** | LA-TravelTime | O nome do aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa "LA-Viajatime". |
   | **Subscrição** | <*your-Azure-subscription-name*> | O nome da sua assinatura do Azure |
   | **Grupo de recursos** | LA-TravelTime-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que é usado para organizar os recursos relacionados. Este exemplo usa "LA-Viagenstime-RG". |
   | **Location** | EUA Oeste | Região Tnão em que armazenar as informações do aplicativo lógico. Este exemplo usa "oeste dos EUA". |
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Depois que o Azure implantar seu aplicativo, na barra de ferramentas do Azure, selecione **notificações** > **vá para o recurso** para seu aplicativo lógico implantado.

   ![Vá para o novo recurso de aplicativo lógico](./media/tutorial-build-scheduled-recurring-logic-app-workflow/go-to-new-logic-app-resource.png)

   Ou, você pode encontrar e selecionar seu aplicativo lógico digitando o nome na caixa de pesquisa.

   O designer de aplicativos lógicos é aberto e mostra uma página com um vídeo de introdução e os gatilhos comumente usados e os padrões de aplicativo lógico. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecionar modelo de aplicativo lógico em branco](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Em seguida, adicione o [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts)recorrência, que é acionado com base em um agendamento especificado. Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adicionar o gatilho de recorrência

1. No designer do aplicativo lógico, na caixa de pesquisa, digite "recorrência" como filtro. Na lista de **gatilhos** , selecione o gatilho **recorrência** .

   ![Adicionar gatilho de "recorrência"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na forma **recorrência** , selecione o botão de **reticências** ( **...** ) e, em seguida, selecione **renomear**. Mude o nome do acionador com a descrição `Check travel time every weekday morning`

   ![Renomear a descrição do gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dentro do gatilho, altere essas propriedades.

   ![Alterar o intervalo e a frequência do gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Intervalo** | Sim | 1 | O número de intervalos de espera entre verificações |
   | **Frequência** | Sim | Week (Semana) | A unidade de tempo a utilizar para a periodicidade |
   |||||

1. Em **intervalo** e **frequência**, abra a lista **Adicionar novo parâmetro** e selecione essas propriedades para adicionar ao gatilho.

   * **On these days** (Nestes dias)
   * **At these hours** (A estas horas)
   * **At these minutes** (A estes minutos)

   ![Adicionar propriedades para gatilho de recorrência](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Agora, defina os valores para as propriedades adicionais, conforme mostrado e descrito aqui.

   ![Indicar os detalhes da agenda e da periodicidade](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriedade | Value | Descrição |
   |----------|-------|-------------|
   | **On these days** (Nestes dias) | Monday,Tuesday,Wednesday,Thursday,Friday | Disponível apenas se **Frequency** (Frequência) estiver definida como "Week" |
   | **At these hours** (A estas horas) | 7,8,9 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar as horas do dia para executar esta periodicidade. Este exemplo é executado nas marcas das 7, 8 e 9 horas. |
   | **At these minutes** (A estes minutos) | 0,15,30,45 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar os minutos do dia para executar esta periodicidade. Este exemplo é executado a cada 15 minutos, começando na marca de hora zero. |
   ||||

   Este acionador é acionado todos os dias úteis, a cada 15 minutos a partir das 7:00 até às 9:45. A caixa **Pré-visualização** mostra a agenda da periodicidade. Para obter mais informações, veja [Schedule tasks and workflows](../connectors/connectors-native-recurrence.md) (Agendar tarefas e fluxos de trabalho) e [Workflow actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) (Ações e acionadores de fluxos de trabalho).

1. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título da forma.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

A sua aplicação lógica está agora ativa, mas não faz mais nada que não ser executada periodicamente. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de deslocação para um percurso

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de deslocação entre dois sítios. O Logic Apps disponibiliza um conector para a API do Mapas Bing para que possa obter facilmente estas informações. Antes de começar esta tarefa, confirme que tem uma chave de API do Mapas Bing, conforme descrito nos pré-requisitos do tutorial.

1. No designer de aplicativo lógico, em seu gatilho, selecione **nova etapa**.

1. Em **escolher uma ação**, selecione **padrão**. Na caixa de pesquisa, insira "Bing Maps" como filtro e selecione a ação **obter rota** .

   ![Selecione a ação "obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se não tiver uma ligação ao Mapas Bing, é-lhe pedido que a crie. Forneça esses detalhes de conexão e selecione **criar**.

   ![Criar conexão com a API do Bing Maps](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da Ligação** | Sim | BingMapsConnection | Indique um nome para a ligação. Este exemplo usa "BingMapsConnection". |
   | **Chave de API** | Sim | <*your-Bing-Maps-key*> | Introduza a chave do Mapas Bing que recebeu anteriormente. Se não tiver uma chave do Mapas Bing, saiba [como obtê-la](https://msdn.microsoft.com/library/ff428642.aspx). |
   |||||

1. Mude o nome da ação com a descrição `Get route and travel time with traffic`

1. Dentro da ação, abra a **lista Adicionar novo parâmetro**e selecione essas propriedades para adicionar à ação.

   * **Optimize**
   * **Distance unit**
   * **Travel mode**

   ![Adicionar propriedades à ação "obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Agora, defina os valores das propriedades da ação, conforme mostrado e descrito aqui.

   ![Fornecer detalhes para a ação "obter rota"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Waypoint 1** | Sim | <*start-location*> | A origem do percurso |
   | **Waypoint 2** | Sim | <*end-location*> | O destino do percurso |
   | **Optimize** | Não | timeWithTraffic | Um parâmetro para otimizar o percurso, por exemplo, a distância, o tempo de deslocação com o tráfego atual e assim sucessivamente. Selecione o parâmetro "timeWithTraffic". |
   | **Distance unit** | Não | <*your-preference*> | A unidade de distância do percurso. Este exemplo usa "milha" como a unidade. |
   | **Travel mode** | Não | Driving | O modo de deslocação para o percurso. Selecione o modo de "condução". |
   ||||

   Para obter mais informações sobre estes parâmetros, veja [Calculate a route](https://docs.microsoft.com/bingmaps/rest-services/routes/calculate-a-route) (Calcular um percurso).

1. Guarde a aplicação lógica.

Em seguida, crie uma variável para que possa converter e armazenar o tempo de deslocação atual como minutos em vez de segundos. Desta forma, evita repetir a conversão e pode utilizar o valor mais facilmente em passos posteriores. 

## <a name="create-a-variable-to-store-travel-time"></a>Criar uma variável para armazenar o tempo de viagem

Às vezes, talvez você queira executar operações em dados em seu fluxo de trabalho e, em seguida, usar os resultados em ações posteriores. Para guardar estes resultados de modo a poder reutilizá-los ou referenciá-los facilmente, pode criar variáveis para armazená-los depois de os processar. Só pode criar variáveis ao nível superior na sua aplicação lógica.

Por padrão, a ação **obter rota** anterior retorna o tempo de viagem atual com o tráfego em segundos da propriedade **tráfego de duração da viagem** . Ao converter e armazenar este valor em minutos, será mais fácil utilizá-lo mais tarde sem que tenha de reconvertê-lo.

1. Na ação **obter rota** , selecione **nova etapa**.

1. Em **escolher uma ação**, selecione **interno**. Na caixa de pesquisa, insira "variáveis" e selecione a ação **inicializar variável** .

   ![Selecione a ação "inicializar variável"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Mude o nome desta ação com a descrição `Create variable to store travel time`

1. Indique os detalhes da variável, conforme descrito aqui:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Name** | Sim | travelTime | O nome da variável. Este exemplo usa "viagem". |
   | **Tipo** | Sim | Integer | O tipo de dados da variável |
   | **Valor** | Não| Uma expressão que converte o tempo de deslocação atual de segundos em minutos (ver os passos nesta tabela). | O valor iniciar da variável |
   ||||

   1. Para criar a expressão para a propriedade **Value** , clique dentro da caixa para que a lista de conteúdo dinâmico seja exibida. Se necessário, alargue o browser até que a lista apareça. Na lista conteúdo dinâmico, selecione **expressão**.

      ![Fornecer informações para a ação "inicializar variável"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando você clica dentro de algumas caixas de edição, a lista de conteúdo dinâmico é exibida. Esta lista mostra todas as propriedades de ações anteriores que você pode usar como entradas em seu fluxo de trabalho. A lista de conteúdo dinâmico tem um editor de expressão no qual você pode selecionar funções para executar operações. Este editor de expressões só aparece na lista de conteúdo dinâmico.

   1. No editor de expressões, introduza a expressão `div(,60)`

      ![Introduzir a expressão "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Coloque o cursor dentro da expressão, entre o parêntesis de abertura ( **(** ) e a vírgula ( **,** ). 
   Selecione **conteúdo dinâmico**.

      ![Posicionar cursor, selecionar "conteúdo dinâmico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na lista de conteúdo dinâmico, selecione **Travel Duration Traffic**.

      ![Selecione a propriedade "tráfego de duração da viagem"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Depois que o valor da propriedade for resolvido dentro da expressão, selecione **OK**.

      ![Para concluir a criação da expressão, selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A propriedade **Value** agora aparece como mostrado aqui:

      ![A propriedade "value" aparece com a expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Guarde a aplicação lógica.

Em seguida, adicione uma condição que verifica se o tempo de deslocação atual é superior a um limite específico.

## <a name="compare-the-travel-time-with-limit"></a>Comparar o tempo de viagem com o limite

1. Na ação anterior, selecione **nova etapa**.

1. Em **escolher uma ação**, selecione **interno**. Na caixa de pesquisa, digite "Condition" como filtro. Na lista ações, selecione a ação **condição** .

   ![Selecionar ação de "condição"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Mude o nome da condição com a descrição `If travel time exceeds limit`

1. Crie uma condição que verifica se o valor da propriedade de **viagem** excede o limite especificado, conforme descrito e mostrado aqui:

   1. Na condição, clique dentro da caixa **escolher um valor** no lado esquerdo da condição.

   1. Na lista de conteúdo dinâmico que aparece, em **variáveis**, selecione a propriedade **viagem** .

      ![Criar o lado esquerdo da condição](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Na caixa comparação intermediária, selecione o operador **é maior que** .

   1. Na caixa **escolher um valor** no lado direito da condição, insira este limite: `15`

      Quando terminar, a condição será parecida com este exemplo:

      ![Condição concluída para verificar o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Guarde a aplicação lógica.

Em seguida, adicione a ação a ser executada quando o tempo de viagem exceder o limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar um e-mail quando o limite for excedido

Agora, adicione uma ação que envia um e-mail quando o tempo de deslocação excede o limite. Este e-mail inclui o tempo de deslocação atual e o tempo extra necessário para percorrer o percurso especificado.

1. Na ramificação **se verdadeiro** da condição, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, selecione **padrão**. Na caixa de pesquisa, digite "enviar email". A lista retorna muitos resultados, portanto, primeiro selecione o conector de email desejado, por exemplo:

   ![Selecione o conector de email que você deseja](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365).
   * Par contas Microsoft pessoais, selecione **Outlook.com**.

1. Quando as ações do conector forem exibidas, selecione "enviar ação de email" que você deseja usar, por exemplo:

   ![Selecionar a ação "enviar e-mail"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail.

   O Logic Apps cria uma ligação para a sua conta de e-mail.

1. Mude o nome da ação com a descrição `Send email with travel time`

1. Na caixa **Para**, introduza o endereço de e-mail do destinatário. Para fins de teste, utilize o seu endereço de e-mail.

1. Na caixa **Assunto**, especifique o assunto do e-mail e inclua a variável **travelTime**.

   1. Introduza o texto `Current travel time (minutes):` com um espaço à direita. 

   1. Na lista conteúdo dinâmico, em **variáveis**, selecione **Ver mais**.

      ![Localizar a variável "traveltime"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

   1. Após o **viajatime** aparecer em **variáveis**, selecione **traveltime**.

      ![Introduzir o texto do assunto e a expressão que devolve o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Na caixa **Corpo**, especifique o conteúdo do corpo do e-mail.

   1. Introduza o texto `Add extra travel time (minutes):` com um espaço à direita.

   1. Na lista conteúdo dinâmico, selecione **expressão**.

      ![Criar expressão para o corpo do e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. No editor de expressões, introduza a expressão ```sub(,15)```, para poder calcular o número de minutos que excedem o limite.

      ![Introduzir a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Coloque o cursor dentro da expressão, entre o parêntesis de abertura ( **(** ) e a vírgula ( **,** ). Selecione **conteúdo dinâmico**.

      ![Continuar a criar a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Em **Variáveis**, selecione **travelTime**.

      ![Selecione a propriedade "viagem" para usar na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Depois que a propriedade for resolvida dentro da expressão, selecione **OK**.

      ![Depois que a propriedade "Body" for resolvida, selecione "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A propriedade **Body** agora aparece como mostrado aqui:

      ![Propriedade "Body" resolvida na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Guarde a aplicação lógica.

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Exemplo de fluxo de trabalho do aplicativo lógico concluído](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente seu aplicativo lógico, na barra da barra de ferramentas do designer, selecione **executar**.

* Se o tempo de viagem atual permanecer abaixo do limite, seu aplicativo lógico não fará mais nada e aguardará ou o próximo intervalo antes de verificar novamente. 

* Se o tempo de viagem atual exceder o limite, você receberá um email com o tempo de viagem atual e o número de minutos acima do limite. Eis um e-mail de exemplo que a sua aplicação lógica envia:

![Exemplo de email enviado que mostra o tempo de viagem](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Acabou de criar e executar uma aplicação lógica recorrente e baseada numa agenda. 

Para criar outros aplicativos lógicos que usam o gatilho de **recorrência** , Confira esses modelos, que estão disponíveis depois de criar um aplicativo lógico:

* Receber lembretes diários.
* Eliminar blobs do Azure mais antigos.
* Adicionar uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais do aplicativo lógico de exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados. 

1. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos para a sua aplicação lógica.

1. No menu grupo de recursos, selecione **visão geral** > **excluir grupo de recursos**. 

   !["Descrição geral" > "Eliminar grupo de recursos"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. Insira o nome do grupo de recursos como confirmação e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que verifica o tráfego com base numa agenda específica (nos dias úteis de manhã) e realiza uma ação (envia e-mails) se o tempo de deslocação exceder um limite especificado. Agora, saiba como compilar uma aplicação lógica que envia pedidos de listas de correio para aprovação através da integração de serviços do Azure, de serviços Microsoft e de outras aplicações de SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md) (Gerir pedidos de listas de correio)

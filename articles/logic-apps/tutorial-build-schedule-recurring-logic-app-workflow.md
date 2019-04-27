---
title: Criar fluxos de trabalho automatizados com base no agendador - Azure Logic Apps | Microsoft Docs
description: Tutorial - Como criar um fluxo de trabalho recorrente, automatizado e baseado num programador com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: ebc6388f1ebc7546ffda07095ead50797bde4e8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60507543"
---
# <a name="check-traffic-on-a-schedule-with-azure-logic-apps"></a>Verificar o tráfego com base numa agenda com o Azure Logic Apps

O Azure Logic Apps ajuda-o a automatizar fluxos de trabalho que são executados segundo uma agenda. Este tutorial mostra como pode criar uma [aplicação lógica](../logic-apps/logic-apps-overview.md) com um acionador de agendador que é executado todos os dias úteis de manhã e que verifica o tempo de deslocação, incluindo o tráfego, entre dois sítios. Se o tempo exceder um limite específico, a aplicação lógica envia um e-mail com o tempo de deslocação e o tempo adicional necessário para chegar ao destino.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco. 
> * Adicionar um acionador que funciona como um agendador para a sua aplicação lógica.
> * Adicionar uma ação que obtém o tempo de deslocação para um percurso.
> * Adicionar uma ação que cria uma variável, converte o tempo de deslocação de segundos em minutos e guarda esse resultado na variável.
> * Adicionar uma condição que compara o tempo de deslocação face a um limite especificado.
> * Adicionar uma ação que envia um e-mail se o tempo de deslocação exceder esse limite.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Aplicação lógica de alto nível](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a> antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de um fornecedor de e-mail que o Logic Apps suporte, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Este início rápido utiliza uma conta do Outlook.com. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Para obter o tempo de deslocação para um percurso, precisa de uma chave de acesso para a API do Mapas Bing. Para obter esta chave, siga os passos em <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">How to get a Bing Maps key</a> (Como obter uma chave do Mapas Bing). 

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. No menu principal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. Em **Criar aplicação lógica**, indique estas informações sobre a sua aplicação lógica, conforme mostrado e descrito. Quando tiver terminado, escolha **Afixar ao dashboard** > **Criar**.

   ![Indicar as informações da aplicação lógica](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-TravelTime | O nome para a aplicação lógica | 
   | **Subscrição** | <*your-Azure-subscription-name*> | O nome para a subscrição do Azure | 
   | **Grupo de recursos** | LA-TravelTime-RG | O nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizado para organizar os recursos relacionados | 
   | **Localização** | EUA Leste 2 | A região onde pretende armazenar as informações da aplicação lógica | 
   | **Log Analytics** | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. | 
   |||| 

3. Depois de o Azure implementar a aplicação, o Estruturador de Aplicações Lógicas abre-se e mostra uma página com um vídeo de introdução e modelos de padrões de aplicações lógicas comuns. Em **Modelos**, escolha **Aplicação Lógica em Branco**.

   ![Escolher o modelo de aplicação lógica em branco](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Em seguida, adicione o [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts) de periodicidade, que é acionado com base numa agenda especificada. Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-scheduler-trigger"></a>Adicionar o acionador do agendador

1. No estruturador, introduza "recurrence" ("periodicidade") na caixa de pesquisa. Selecione este acionador: **Agendamento - periodicidade**

   ![Localizar e adicione o acionador "Schedule-Recurrence"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Na forma **Recurrence**, escolha o botão de **reticências** (**...**) e escolha **Mudar o nome**. Mude o nome do acionador com a descrição ```Check travel time every weekday morning```

   ![Mudar o nome do acionador](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Dentro de acionador, escolha **Mostrar opções avançadas**.

4. Indique os detalhes da agenda e da periodicidade do acionador, conforme mostrado e descrito:

   ![Indicar os detalhes da agenda e da periodicidade](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Intervalo** | 1 | O número de intervalos de espera entre verificações | 
   | **Frequência** | Week (Semana) | A unidade de tempo a utilizar para a periodicidade | 
   | **Time zone** (Fuso horário) | Nenhuma | Aplica-se apenas se especificar uma hora de início. É útil para especificar um fuso horário não local. | 
   | **Start time** (Hora de início) | Nenhuma | Atrasar a periodicidade até uma data e hora específicas. Para obter mais informações, veja [Schedule tasks and workflows that run regularly](../connectors/connectors-native-recurrence.md) (Agendar tarefas e fluxos de trabalho que são executados regularmente). | 
   | **On these days** (Nestes dias) | Monday,Tuesday,Wednesday,Thursday,Friday | Disponível apenas se **Frequency** (Frequência) estiver definida como "Week" | 
   | **At these hours** (A estas horas) | 7,8,9 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar as horas do dia para executar esta periodicidade. Este exemplo é executado nas marcas das 7, 8 e 9 horas. | 
   | **At these minutes** (A estes minutos) | 0,15,30,45 | Disponível apenas se **Frequency** estiver definida como "Week" ou “Day”. Selecionar os minutos do dia para executar esta periodicidade. Este exemplo é executado a cada 15 minutos, começando na marca de hora zero. | 
   ||||

   Este acionador é acionado todos os dias úteis, a cada 15 minutos a partir das 7:00 até às 9:45. 
   A caixa **Pré-visualização** mostra a agenda da periodicidade. 
   Para obter mais informações, veja [Schedule tasks and workflows](../connectors/connectors-native-recurrence.md) (Agendar tarefas e fluxos de trabalho) e [Workflow actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) (Ações e acionadores de fluxos de trabalho).

5. Para ocultar os detalhes do acionador por agora, clique no interior da barra de título da forma.

   ![Fechar forma para ocultar os detalhes](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. 

A sua aplicação lógica está agora ativa, mas não faz mais nada que não ser executada periodicamente. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de deslocação para um percurso

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de deslocação entre dois sítios. O Logic Apps disponibiliza um conector para a API do Mapas Bing para que possa obter facilmente estas informações. Antes de começar esta tarefa, confirme que tem uma chave de API do Mapas Bing, conforme descrito nos pré-requisitos do tutorial.

1. No Estruturador das Aplicações Lógicas, no acionador, escolha **+ Novo passo** > **Adicionar uma ação**.

2. Procure "maps" e selecione a ação: **Bing Maps - Get route**

3. Se não tiver uma ligação ao Mapas Bing, é-lhe pedido que a crie. Indique estes detalhes de ligação e escolha **Criar**.

   ![Selecionar ação "Bing Maps - Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Nome da Ligação** | BingMapsConnection | Indique um nome para a ligação. | 
   | **Chave de API** | <*your-Bing-Maps-key*> | Introduza a chave do Mapas Bing que recebeu anteriormente. Se não tiver uma chave do Mapas Bing, saiba <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">como obtê-la</a>. | 
   | | | |  

4. Mude o nome da ação com a descrição ```Get route and travel time with traffic```

5. Indique os detalhes para a ação **Get route**, conforme mostrado e descrito aqui, como, por exemplo:

   ![Indicar informações para a ação "Bing Maps - Get route"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Definição | Valor | Descrição |
   | ------- | ----- | ----------- |
   | **Waypoint 1** | <*start-location*> | A origem do percurso | 
   | **Waypoint 2** | <*end-location*> | O destino do percurso | 
   | **Avoid** | Nenhuma | Todos os elementos a evitar no percurso, como autoestradas, portagens, etc. | 
   | **Optimize** | timeWithTraffic | Um parâmetro para otimizar o percurso, por exemplo, a distância, o tempo de deslocação com o tráfego atual e assim sucessivamente. Selecione o parâmetro "timeWithTraffic" | 
   | **Distance unit** | <*your-preference*> | A unidade de distância do percurso. Este artigo utiliza a unidade: "Milha"  | 
   | **Travel mode** | Driving | O modo de deslocação para o percurso. Selecione o modo: "Driving" | 
   | **Transit Date-Time** | Nenhuma | Aplica-se apenas ao modo de tráfego | 
   | **Date-Time Type** | Nenhuma | Aplica-se apenas ao modo de tráfego | 
   |||| 

   Para obter mais informações sobre estes parâmetros, veja [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcular um percurso).

6. Guarde a aplicação lógica.

Em seguida, crie uma variável para que possa converter e armazenar o tempo de deslocação atual como minutos em vez de segundos. Desta forma, evita repetir a conversão e pode utilizar o valor mais facilmente em passos posteriores. 

## <a name="create-variable-to-store-travel-time"></a>Criar a variável para armazenar o tempo de deslocação

Por vezes, poderá querer executar operações nos dados do seu fluxo de trabalho e utilizar os resultados em ações subsequentes. Para guardar estes resultados de modo a poder reutilizá-los ou referenciá-los facilmente, pode criar variáveis para armazená-los depois de os processar. Só pode criar variáveis ao nível superior na sua aplicação lógica.

Por predefinição, a ação **Get route** anterior devolve o tempo de deslocação atual com tráfego em segundos no campo **Travel Duration Traffic** (Duração da Viagem com Tráfego). Ao converter e armazenar este valor em minutos, será mais fácil utilizá-lo mais tarde sem que tenha de reconvertê-lo.

1. Na ação **Get route**, escolha **+ Novo passo** > **Adicionar uma ação**.

2. Procure "variables" e selecione a ação: **Variables - Initialize variable**

   ![Selecionar a ação "Variables - Initialize variable"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Mude o nome desta ação com a descrição ```Create variable to store travel time```

4. Indique os detalhes da variável, conforme descrito aqui:

   | Definição | Valor | Descrição | 
   | ------- | ----- | ----------- | 
   | **Nome** | travelTime | O nome da variável | 
   | **Tipo** | Número inteiro | O tipo de dados da variável | 
   | **Valor** | Uma expressão que converte o tempo de deslocação atual de segundos em minutos (ver os passos nesta tabela). | O valor iniciar da variável | 
   |||| 

   1. Para criar a expressão para o campo **Valor**, clique dentro do campo de modo a apresentar a lista de conteúdo dinâmico. 
   Se necessário, alargue o browser até que a lista apareça. 
   Na lista de conteúdo dinâmico, escolha **Expressão**. 

      ![Indicar informações para a ação "Variables - Initialize variable"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Quando clicar dentro de algumas caixas de edição, aparece uma lista de conteúdo dinâmico ou uma lista de parâmetros inline. Esta lista mostra todos os parâmetros de ações anteriores que pode utilizar como entradas no seu fluxo de trabalho. 
      A lista de conteúdo dinâmico tem um editor de expressões onde pode selecionar funções para realizar operações. 
      Este editor de expressões só aparece na lista de conteúdo dinâmico.

      A largura do browser determina a lista que é apresentada. 
      Se o browser for largo, é apresentada a lista de conteúdo dinâmico. 
      Se for estreito, aparece uma lista de parâmetros inline abaixo da caixa de edição que tem o foco neste exemplo.

   2. No editor de expressões, introduza a expressão ```div(,60)```

      ![Introduzir a expressão "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Coloque o cursor dentro da expressão, entre o parêntesis de abertura (**(**) e a vírgula (**,**). 
   Escolha **Conteúdo dinâmico**.

      ![Posicionar o cursor e escolhr "Conteúdo dinâmico"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. Na lista de conteúdo dinâmico, selecione **Travel Duration Traffic**.

      ![Selecionar o campo "Travel Duration Traffic"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Quando o campo for resolvido dentro da expressão, escolha **OK**.

      ![Escolher "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      É agora apresentado o campo **Valor**, conforme mostrado aqui:

      ![Campo "Valor" com a expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Guarde a aplicação lógica.

Em seguida, adicione uma condição que verifica se o tempo de deslocação atual é superior a um limite específico.

## <a name="compare-travel-time-with-limit"></a>Comparar o tempo de deslocação com o limite

1. Na ação anterior, escolha **+ Novo passo** > **Adicionar uma condição**. 

2. Mude o nome da condição com a descrição ```If travel time exceeds limit```

3. Crie uma condição que verifica se **travelTime** excede o limite que especificou, conforme descrito e mostrado aqui:

   1. Dentro da condição, clique dentro da caixa **Escolher um valor**, que está no lado esquerdo (vista de browser alargada) ou na parte superior (vista de browser estreita).

   2. Na lista de conteúdo dinâmico ou na lista de parâmetros, selecione o campo **travelTime** em **Variáveis**.

   3. Na caixa de comparação, selecione o operador **is greater than** (é maior do que)

   4. Na caixa **Escolher um valor** no lado direito (vista alargada) ou na parte inferior (vista estreita), introduza o limite ```15```

   Por exemplo, se estiver a trabalhar na vista estreita, eis como criar esta condição:

   ![Criar a condição na vista estreita](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Guarde a aplicação lógica.

Em seguida, adicione a ação a realizar quando o tempo de deslocação excede o limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar um e-mail quando o limite for excedido

Agora, adicione uma ação que envia um e-mail quando o tempo de deslocação excede o limite. Este e-mail inclui o tempo de deslocação atual e o tempo extra necessário para percorrer o percurso especificado. 

1. No ramo **Se verdadeiro** da condição, escolha **Adicionar uma ação**.

2. Procure "send email" e selecione o conector de e-mail e a ação "send email" que quer utilizar.

   ![Localizar e selecionar a ação "send email"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Par contas Microsoft pessoais, selecione **Outlook.com**. 
   * Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365).

3. Se ainda não tiver uma ligação, é-lhe pedido que inicie sessão na sua conta de e-mail.

   O Logic Apps cria uma ligação para a sua conta de e-mail.

4. Mude o nome da ação com a descrição ```Send email with travel time```

5. Na caixa **Para**, introduza o endereço de e-mail do destinatário. Para fins de teste, utilize o seu endereço de e-mail.

6. Na caixa **Assunto**, especifique o assunto do e-mail e inclua a variável **travelTime**.

   1. Introduza o texto ```Current travel time (minutes):``` com um espaço à direita. 
   
   2. Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **travelTime** em **Variáveis**. 
   
      Por exemplo, se o browser estiver na vista estreita:

      ![Introduzir o texto do assunto e a expressão que devolve o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. Na caixa **Corpo**, especifique o conteúdo do corpo do e-mail. 

   1. Introduza o texto ```Add extra travel time (minutes):``` com um espaço à direita. 
   
   2. Se necessário, alargue o browser até que a lista de conteúdo dinâmico apareça. 
   Na lista de conteúdo dinâmico, escolha **Expressão**.

      ![Criar expressão para o corpo do e-mail](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. No editor de expressões, introduza a expressão ```sub(,15)```, para poder calcular o número de minutos que excedem o limite.

      ![Introduzir a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Coloque o cursor dentro da expressão, entre o parêntesis de abertura (**(**) e a vírgula (**,**). Escolha **Conteúdo dinâmico**.

      ![Continuar a criar a expressão para calcular os minutos adicionais para o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. Em **Variáveis**, selecione **travelTime**.

      ![Selecione o campo "travelTime" para utilizar na expressão](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Quando o campo for resolvido dentro da expressão, escolha **OK**.

      ![Campo "Corpo" com a expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      É agora apresentado o campo **Corpo**, conforme mostrado aqui:

      ![Campo "Corpo" com a expressão resolvida](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Guarde a aplicação lógica.

Em seguida, teste a sua aplicação lógica, que é agora semelhante a este exemplo:

![Aplicação lógica concluída](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do estruturador, escolha **Executar**. Se o tempo de deslocação atual estiver dentro do limite, a aplicação lógica não faz mais nada e espera pelo próximo intervalo antes de voltar a verificar.
Contudo, se esse tempo for superior, receberá um e-mail com o tempo de deslocação atual e o número de minutos em que o limite é excedido. Eis um e-mail de exemplo que a sua aplicação lógica envia:

![E-mail enviado com o tempo de deslocação](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Acabou de criar e executar uma aplicação lógica recorrente e baseada numa agenda. 

Para criar outras aplicações lógicas que utilizem o acionador **Schedule - Recurrence**, veja estes modelos, que estão disponíveis depois de criar uma aplicação lógica:

* Receber lembretes diários.
* Eliminar blobs do Azure mais antigos.
* Adicionar uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos que contém a aplicação lógica e os recursos relacionados. No menu principal do Azure, aceda a **Grupos de recursos** e selecione o grupo de recursos para a sua aplicação lógica. Escolha **Eliminar grupo de recursos**. Introduza o nome do grupo de recursos como confirmação e escolha **Eliminar**.

!["Descrição geral" > "Eliminar grupo de recursos"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma aplicação lógica que verifica o tráfego com base numa agenda específica (nos dias úteis de manhã) e realiza uma ação (envia e-mails) se o tempo de deslocação exceder um limite especificado. Agora, saiba como compilar uma aplicação lógica que envia pedidos de listas de correio para aprovação através da integração de serviços do Azure, de serviços Microsoft e de outras aplicações de SaaS.

> [!div class="nextstepaction"]
> [Manage mailing list requests](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md) (Gerir pedidos de listas de correio)

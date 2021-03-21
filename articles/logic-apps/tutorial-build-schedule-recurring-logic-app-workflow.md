---
title: Construa fluxos de trabalho de automatização baseados em horários com a Azure
description: Tutorial - Crie um fluxo de trabalho de automatização baseado em horários e recorrente que se integre em todos os serviços na nuvem utilizando apps Azure Logic.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054064"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho de automatização baseados em horários e recorrentes com apps Azure Logic

Este tutorial mostra como construir uma [aplicação lógica](../logic-apps/logic-apps-overview.md) de exemplo que automatiza um fluxo de trabalho que funciona em um horário recorrente. Especificamente, este exemplo de aplicação lógica verifica o tempo de viagem, incluindo o tráfego, entre dois lugares e corre todas as manhãs da semana. Se o tempo exceder um limite específico, a aplicação lógica envia-lhe um e-mail que inclui o tempo de viagem e o tempo extra necessário para chegar ao seu destino. O fluxo de trabalho inclui várias etapas, que começam com um gatilho baseado no horário seguido de uma ação Bing Maps, uma ação de operações de dados, uma ação de fluxo de controlo e uma ação de notificação de e-mail.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica em branco.
> * Adicione um gatilho de recorrência que especifica o calendário da sua aplicação lógica.
> * Adicione uma ação Bing Maps que obtém o tempo de viagem para uma rota.
> * Adicione uma ação que cria uma variável, converte o tempo de viagem de segundos para minutos, e armazena que resultam na variável.
> * Adicionar uma condição que compara o tempo de deslocação face a um limite especificado.
> * Adicione uma ação que lhe envia e-mail se o tempo de viagem exceder o limite.

Quando terminar, a aplicação lógica é semelhante a este fluxo de trabalho a alto nível:

![Screenshot que mostra a visão geral de alto nível para um fluxo de trabalho de aplicações lógicas de exemplo.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma conta de e-mail de um fornecedor de e-mail que é suportado por Aplicações Lógicas, como Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/). Este quickstart usa o Office 365 Outlook com uma conta de trabalho ou escola. Se utilizar uma conta de e-mail diferente, os passos gerais permanecem os mesmos, mas a sua UI pode diferir ligeiramente.

  > [!IMPORTANT]
  > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Para obter o tempo de deslocação para um percurso, precisa de uma chave de acesso para a API do Mapas Bing. Para obter esta chave, siga os passos em [How to get a Bing Maps key](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) (Como obter uma chave do Mapas Bing).

* Se a sua aplicação lógica necessitar de comunicar através de uma firewall que limite o tráfego a endereços IP específicos, essa firewall precisa de permitir o acesso tanto aos endereços IP [de entrada](logic-apps-limits-and-config.md#inbound) *como* [de saída](logic-apps-limits-and-config.md#outbound) utilizados pelo serviço De aplicações lógicas ou ao tempo de funcionamento na região de Azure, onde existe a sua aplicação lógica. Se a sua aplicação lógica também utilizar [conectores geridos](../connectors/apis-list.md#managed-api-connectors)– como o conector Office 365 Outlook ou o conector SQL, ou utilizar [conectores personalizados](/connectors/custom-connectors/)– a firewall também precisa de permitir o acesso de *todos os* [endereços IP de saída geridos](logic-apps-limits-and-config.md#outbound) do conector na região Azure da sua aplicação lógica.

## <a name="create-your-logic-app"></a>Criar uma aplicação lógica

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure. Na página inicial do Azure, selecione **Criar um recurso**.

1. No menu Azure Marketplace, selecione **Integration**  >  **Logic App**.

   ![Screenshot que mostra o menu Azure Marketplace com "Integração" e "Logic App" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. No painel **da Aplicação Lógica,** forneça as informações descritas aqui sobre a aplicação lógica que pretende criar.

   ![Screenshot que mostra o painel de criação da Logic App e a informação para fornecer a nova aplicação lógica.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Subscrição** | <*Nome de subscrição Azure*> | O nome da subscrição do Azure. Este exemplo utiliza `Pay-As-You-Go`. |
   | **Grupo de recursos** | LA-TravelTime-RG | O nome para o [grupo de recursos Azure,](../azure-resource-manager/management/overview.md)que é usado para organizar recursos relacionados. Este exemplo cria um novo grupo de recursos chamado `LA-TravelTime-RG` . |
   | **Nome** | LA-TravelTime | O nome da sua aplicação lógica, que contém apenas letras, números, hífenes `-` (), sublinha `_` (), parênteses ( `(` , ) e `)` períodos `.` (). Este exemplo utiliza `LA-TravelTime`. |
   | **Localização** | E.U.A. Oeste | A região onde armazenar a sua informação lógica de aplicações. Este exemplo utiliza `West US`. |
   | **Log Analytics** | Desligado | Mantenha a definição **Desativado** para o registo de diagnósticos. |
   ||||

1. Quando terminar, selecione **Review + create**. Depois de o Azure validar as informações sobre a sua aplicação lógica, selecione **Criar**.

1. Depois de o Azure implementar a sua aplicação, selecione **Ir para o recurso**.

   O Azure abre o painel de seleção do modelo de Apps Lógicas, que mostra um vídeo de introdução, gatilhos geralmente usados e padrões de modelo de aplicações lógicas.

1. Percorra as secções de gatilhos para baixo para a secção **Modelos** e selecione **A App lógica em branco**.

   ![Screenshot que mostra o painel de seleção do modelo de Apps Lógicas com "Blank Logic App" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Em seguida, adicione o [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts)de Recorrência , que executa o fluxo de trabalho com base num horário especificado. Todas as aplicações lógicas têm de ser iniciadas com um acionador, que é acionado quando um evento específico acontece ou quando dados novos satisfazem uma condição específica. Para obter mais informações, veja [Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adicione o gatilho de recorrência

1. Na caixa de pesquisa do Design de Aplicações Lógicas, introduza `recurrence` e selecione o gatilho denominado **Recorrência**.

   ![Screenshot que mostra a caixa de pesquisa do Logic Apps Designer que contém o termo de pesquisa de "recorrência" e na lista "Triggers", o gatilho "Recorrence" aparece selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na forma **de Recorrência,** selecione o botão **elipses** **(...**) e, em seguida, selecione **Rename**. Mude o nome do acionador com a descrição `Check travel time every weekday morning`

   ![Screenshot que mostra o botão de elipses selecionado, a lista "Definições" aberta e o comando "Renomear" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dentro do gatilho, altere estas propriedades como descrito e mostrado aqui.

   ![Screenshot que mostra as alterações no intervalo e frequência do gatilho.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Intervalo** | Yes | 1 | O número de intervalos de espera entre verificações |
   | **Frequência** | Yes | Semana | A unidade de tempo a utilizar para a periodicidade |
   |||||

1. Em **Intervalo** e **Frequência,** abra a nova lista **de parâmetros add** e selecione estas propriedades para adicionar ao gatilho.

   * **On these days** (Nestes dias)
   * **At these hours** (A estas horas)
   * **At these minutes** (A estes minutos)

   ![Screenshot que mostra a lista aberta "Adicionar novo parâmetro" e estas propriedades selecionadas: "Nestes dias", "Nestas horas" e "Nestes minutos".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Agora descreva os valores para as propriedades adicionais como mostrado e descrito aqui.

   ![Screenshot que mostra as propriedades adicionais definidas para os valores descritos na tabela seguinte.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **On these days** (Nestes dias) | Monday,Tuesday,Wednesday,Thursday,Friday | Esta definição só está disponível quando definir a **Frequência** para a **Semana**. |
   | **At these hours** (A estas horas) | 7,8,9 | Esta definição só está disponível quando definir a **Frequência** para **Semana** ou **Dia**. Para esta recorrência, selecione as horas do dia. Este exemplo é executado nas `7` `8` marcas , e `9` hora. |
   | **At these minutes** (A estes minutos) | 0,15,30,45 | Esta definição só está disponível quando definir a **Frequência** para **Semana** ou **Dia**. Para esta recorrência, selecione as atas do dia. Este exemplo começa na marca de zero horas e funciona a cada 15 minutos. |
   ||||

   Este acionador é acionado todos os dias úteis, a cada 15 minutos a partir das 7:00 até às 9:45. A caixa **Pré-visualização** mostra a agenda da periodicidade. Para obter mais informações, veja [Schedule tasks and workflows](../connectors/connectors-native-recurrence.md) (Agendar tarefas e fluxos de trabalho) e [Workflow actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) (Ações e acionadores de fluxos de trabalho).

1. Para ocultar os detalhes do gatilho por enquanto, desabar a forma clicando dentro da barra de título da forma.

   ![Screenshot que mostra a forma do gatilho colapsado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Guarde a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

A sua aplicação lógica está agora ao vivo no portal Azure, mas não faz outra coisa que não seja o gatilho com base no calendário especificado. Por isso, adicione uma ação que responde quando o acionador é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de deslocação para um percurso

Agora que tem um acionador, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de deslocação entre dois sítios. O Logic Apps disponibiliza um conector para a API do Mapas Bing para que possa obter facilmente estas informações. Antes de começar esta tarefa, confirme que tem uma chave de API do Mapas Bing, conforme descrito nos pré-requisitos do tutorial.

1. No Logic App Designer, sob o gatilho de Recorrência, selecione **Novo passo**.

1. Em **Seleção de uma operação**, selecione **Standard**. Na caixa de pesquisa, insira `bing maps` e selecione a ação chamada **Rota Get**.

   ![Screenshot que mostra a lista "Escolha uma operação" filtrada por ações de "bing maps" e a ação "Get route" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se não tiver uma ligação ao Mapas Bing, é-lhe pedido que a crie. Forneça os detalhes da ligação como mostrado e descrito e, em seguida, selecione **Criar**.

   ![Screenshot que mostra a caixa de conexão Bing Maps com o nome de ligação especificado e a chave API Bing Maps.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome de conexão** | Yes | BingMapsConnection | Indique um nome para a ligação. Este exemplo utiliza `BingMapsConnection`. |
   | **Chave de API** | Yes | <*Bing-Maps-API-chave*> | Insira a chave API Bing Maps que recebeu anteriormente. Se não tiver uma chave do Mapas Bing, saiba [como obtê-la](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Rebatize a ação com esta descrição: `Get route and travel time with traffic` .

1. Na ação, abra a **nova lista de parâmetros Add** e selecione estas propriedades.

   * **Otimizar**
   * **Distance unit**
   * **Travel mode**

   ![Screenshot que mostra a "Rota da Saída..." ação com as propriedades "Otimizar", "Unidade de distância" e "Modo de viagem" selecionadas.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Agora insira os valores para as propriedades mostradas e descritas aqui.

   ![Screenshot que mostra valores adicionais de propriedade para a ação "Get route".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Waypoint 1** | Yes | <*localização inicial*> | A origem da sua rota. Este exemplo especifica um endereço inicial de exemplo. |
   | **Waypoint 2** | Yes | <*localização final*> | O destino da sua rota. Este exemplo especifica um endereço de destino exemplo. |
   | **Otimizar** | No | timeWithTraffic | Um parâmetro para otimizar o percurso, por exemplo, a distância, o tempo de deslocação com o tráfego atual e assim sucessivamente. Selecione o valor do parâmetro, **timeWithTraffic**. |
   | **Distance unit** | No | <*sua preferência*> | A unidade de distância do percurso. Este exemplo usa **Mile** como unidade. |
   | **Travel mode** | No | Driving | O modo de deslocação para o percurso. Selecione o modo **de condução.** |
   |||||

   Para obter mais informações sobre estes parâmetros e valores, consulte [calcular uma rota.](/bingmaps/rest-services/routes/calculate-a-route)

1. Na barra de ferramentas do designer, **selecione Save**.

Em seguida, crie uma variável para que possa converter e armazenar o tempo de deslocação atual como minutos em vez de segundos. Desta forma, evita repetir a conversão e pode utilizar o valor mais facilmente em passos posteriores. 

## <a name="create-a-variable-to-store-travel-time"></a>Criar uma variável para armazenar o tempo de viagem

Por vezes, pode querer executar operações em dados no seu fluxo de trabalho e, em seguida, usar os resultados em ações posteriores. Para guardar estes resultados para que possa reutilizá-los ou reutilizá-los facilmente, pode criar variáveis que armazenam esses resultados após o processamento. Só pode criar variáveis ao nível superior na sua aplicação lógica.

Por predefinição, a ação **de rota Get** devolve o tempo de viagem atual com tráfego em segundos a partir da propriedade Travel Duration **Traffic.** Ao converter e armazenar este valor em minutos, será mais fácil utilizá-lo mais tarde sem que tenha de reconvertê-lo.

1. No designer, no âmbito da ação **de rota Get,** selecione **Novo passo**.

1. Em **Selecione uma operação,** selecione **Built-in**. Na caixa de pesquisa, insira `variables` e selecione a ação **denominada Variável Initialize**.

   ![Screenshot que mostra a ação "Initialize variable" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Mude o nome desta ação com a descrição `Create variable to store travel time`

1. Forneça esta informação para a sua variável, como mostrado nesta tabela e nos passos abaixo da tabela:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Yes | travelTime | O nome para a sua variável. Este exemplo utiliza `travelTime`. |
   | **Tipo** | Yes | Número inteiro | O tipo de dados da variável |
   | **Valor** | No | Uma expressão que converte o tempo de viagem atual de segundos para minutos (ver os passos por baixo desta tabela). | O valor iniciar da variável |
   |||||

   1. Para criar a expressão para a propriedade **Valor,** clique dentro da caixa para que a lista de conteúdos dinâmicos apareça. Se necessário, aumente o seu navegador até que a lista dinâmica apareça. Na lista de conteúdos dinâmicos, selecione **Expression**, que mostra o editor de expressão.

      ![Screenshot que mostra a ação "Initialize variable" com o cursor dentro da propriedade "Value", que abre a lista de conteúdos dinâmicos.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      A lista de conteúdos dinâmicos mostra as saídas de ações anteriores que estão disponíveis para selecionar como entradas para as ações subsequentes no seu fluxo de trabalho. A lista de conteúdos dinâmicos inclui um editor de expressão que pode usar para selecionar funções que realizam operações na sua expressão. Este editor de expressão só está disponível na lista de conteúdos dinâmicos.

   1. No editor de expressões, introduza a expressão `div(,60)`

      ![Screenshot que mostra o editor de expressão com a expressão "div(,60)" inserida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Dentro da expressão, coloque o cursor entre o parênteses esquerdo **()** e a vírgula (**,**) e selecione **conteúdo dinâmico**.

      ![Screenshot que mostra onde colocar o cursor na expressão "div(,60)" com "Conteúdo dinâmico" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na lista de conteúdos dinâmicos, em seleção do valor da propriedade, **Tráfego de Duração de Viagem.**

      ![Screenshot que mostra o valor da propriedade "Travel Duration Traffic" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Depois de o valor da propriedade resolver dentro da expressão, selecione **OK**.

      ![Screenshot que mostra o botão "OK" como selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A propriedade **Value** aparece agora como mostrado aqui:

      ![Screenshot que mostra a propriedade "Valor" com a expressão resolvida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Guarde a sua aplicação lógica.

Em seguida, adicione uma condição que verifica se o tempo de deslocação atual é superior a um limite específico.

## <a name="compare-the-travel-time-with-limit"></a>Compare o tempo de viagem com o limite

1. Sob a **variável Criar para armazenar ação no tempo de viagem,** selecione **Novo passo**.

1. Em **Selecione uma operação,** selecione **Built-in**. Na caixa de pesquisa, introduza `condition`. e da lista de ações, selecione a ação **denominada Condição**.

   ![Screenshot que mostra a ação "Condição" selecionada](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Mude o nome da condição com a descrição `If travel time exceeds limit`

1. Construa uma condição que verifique se o valor da propriedade **travelTime** excede o seu limite especificado como descrito e mostrado aqui:

   1. Na condição, no lado esquerdo da condição, clique no interior da caixa **de valor Escolha.**

   1. A partir da lista de conteúdos dinâmicos que aparece, em **Variáveis,** selecione a propriedade chamada **TravelTime**.

      ![Screenshot que mostra a caixa "Escolha um valor" no lado esquerdo da condição com a lista de conteúdos dinâmicos aberta e a propriedade "TravelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Na caixa de comparação média, selecione que o operador nomeado **é maior do que**.

   1. No lado direito da condição, na caixa **de valor Escolha,** insira este limite: `15`

      Quando terminar, a condição parece este exemplo:

      ![Screenshot que mostra a condição final para comparar o tempo de viagem com o limite especificado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Guarde a sua aplicação lógica.

Em seguida, adicione a ação a ser executada quando o tempo de viagem exceder o seu limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar um e-mail quando o limite for excedido

Agora, adicione uma ação que lhe envia e-mail quando o tempo de viagem exceder o seu limite. Este e-mail inclui o tempo de deslocação atual e o tempo extra necessário para percorrer o percurso especificado.

1. No ramo **True** da condição, selecione **Adicione uma ação**.

1. Em **Seleção de uma operação**, selecione **Standard**. Na caixa de pesquisa, introduza `send email`. A lista devolve muitos resultados, por isso, para ajudá-lo a filtrar a lista, selecione primeiro o conector de e-mail que deseja.

   Por exemplo, se tiver uma conta de e-mail do Outlook, selecione o conector para o seu tipo de conta:

   * Para contas escolares ou profissionais do Azure, selecione **Office 365 Outlook** (Outlook do Office 365).
   * Par contas Microsoft pessoais, selecione **Outlook.com**.

   Este exemplo continua selecionando o Office 365 Outlook.

   ![Screenshot que mostra a "Escolha uma lista de operações" com a categoria "Standard" e o conector "Office 365 Outlook" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Quando as ações do conector aparecerem, selecione a ação que envia o e-mail, por exemplo:

   ![Screenshot que mostra a ação "Enviar um e-mail" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se ainda não tiver uma ligação, faça o seu sômis e autente o acesso à sua conta de e-mail quando solicitado.

   A Azure Logic Apps cria uma ligação à sua conta de e-mail.

1. Mude o nome da ação com a descrição `Send email with travel time`

1. Para a propriedade **To,** insira o endereço de e-mail do destinatário. Para efeitos de teste, pode utilizar o seu endereço de e-mail.

1. Para a propriedade **Assunto,** especifique o assunto do e-mail e inclua a variável **TravelTime** seguindo estes passos:

   1. Introduza o texto `Current travel time (minutes):` com um espaço à direita. Mantenha o cursor na caixa **'Assunto'** para que a lista de conteúdos dinâmicos permaneça aberta.

   1. A partir da lista de conteúdos **dinâmicos,** na rubrica Variáveis, selecione **Ver mais** para que a variável nomeada **TravelTime** apareça.

      ![Screenshot que mostra a lista de conteúdos dinâmicos com a secção "Variáveis" e "Ver mais" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > A lista de conteúdos dinâmicos não mostra automaticamente a variável **TravelTime** porque a propriedade **Subject** espera um valor de corda, enquanto **o TravelTime** é um valor inteiro.

      ![Screenshot que mostra a lista de conteúdos dinâmicos com a variável "TravelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Para a propriedade **Body,** especifique o conteúdo do corpo de e-mail seguindo estes passos:

   1. Introduza o texto `Add extra travel time (minutes):` com um espaço à direita. Mantenha o cursor na caixa **Do Corpo** para que a lista de conteúdos dinâmicos permaneça aberta.

   1. Na lista de conteúdos dinâmicos, selecione **Expression**, que mostra o editor de expressão.

      ![Screenshot que mostra a lista de conteúdos dinâmicos com "Expression" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. No editor de expressão, `sub(,15)` insira para que possa calcular o número de minutos que excedem o seu limite: 

      ![Screenshot que mostra o editor de expressão com a expressão "sub(15)" inserida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Dentro da expressão, coloque o cursor entre o parênteses esquerdo **()** e a vírgula (**,**) e selecione **conteúdo dinâmico**.

      ![Screenshot que mostra onde colocar o cursor na expressão "sub(,15)" com "Conteúdo dinâmico" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Em **Variáveis**, selecione **travelTime**.

      ![Screenshot que mostra a lista de conteúdos dinâmicos com a variável "TravelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Depois de a propriedade resolver dentro da expressão, selecione **OK**.

      ![Screenshot que mostra a lista de conteúdos dinâmicos e "OK" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A propriedade **Body** agora aparece como mostrado aqui:

      ![Screenshot que mostra a lista de conteúdos dinâmicos com a expressão resolvida na propriedade "Body" da ação de e-mail.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Guarde a sua aplicação lógica.

Em seguida, teste e execute a sua app lógica, que agora se parece com este exemplo:

![Screenshot que mostra o fluxo de trabalho de aplicação lógica de exemplo final](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar a aplicação lógica

Para iniciar manualmente a sua aplicação lógica, na barra de ferramentas do designer, selecione **Run**.

* Se o tempo de viagem atual permanecer abaixo do seu limite, a sua aplicação lógica não faz mais nada e espera ou o intervalo seguinte antes de verificar novamente. 

* Se o tempo de viagem atual exceder o seu limite, obtém um e-mail com o tempo de viagem atual e o número de minutos acima do seu limite. Eis um e-mail de exemplo que a sua aplicação lógica envia:

  ![Screenshot que mostra um e-mail de exemplo que relata o tempo de viagem atual e o tempo extra de viagem que excede o seu limite especificado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Se não receber nenhuma mensagem de e-mail, verifique a pasta de lixo do e-mail. O filtro de lixo de e-mail poderá redirecionar estes tipos de mensagem de e-mail. Caso contrário, se não tiver a certeza de que a aplicação lógica foi executada corretamente, veja [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md) (Resolver problemas da sua aplicação lógica).

Parabéns! Acabou de criar e executar uma aplicação lógica recorrente e baseada numa agenda. 

Para criar outras aplicações lógicas que usam o gatilho **de Recorrência,** confira estes modelos, que estão disponíveis depois de criar uma aplicação lógica:

* Receber lembretes diários.
* Eliminar blobs do Azure mais antigos.
* Adicionar uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

A sua aplicação lógica continua a funcionar até desativar ou eliminar a aplicação. Quando já não precisar da aplicação lógica da amostra, elimine o grupo de recursos que contém a sua aplicação lógica e recursos relacionados.

1. Na caixa de pesquisa do portal Azure, insira o nome para o grupo de recursos que criou. A partir dos resultados, em **Grupos de Recursos,** selecione o grupo de recursos.

   Este exemplo criou o grupo de recursos denominado `LA-TravelTime-RG` .

   ![Screenshot que mostra a caixa de pesquisa Azure com "la-travel-time-rg" inserida e **LA-TravelTime-RG** selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Se a página inicial do Azure mostrar o grupo de recursos sob **recursos recentes,** pode selecionar o grupo na página inicial.

1. No menu do grupo de recursos, verifique **se a visão geral** está selecionada. Na barra de **ferramentas** do painel de ferramentas, selecione **Delete resource group**.

   ![Screenshot que mostra o painel "Visão Geral" do grupo de recursos e na barra de ferramentas do painel, "Delete resource group" é selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. No painel de confirmação que aparece, introduza o nome do grupo de recursos e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação lógica que verifica o tráfego com base num horário especificado (nas manhãs da semana) e toma medidas (envia um e-mail) quando o tempo de viagem excede um limite especificado. Agora, aprenda a construir uma aplicação lógica que envia pedidos de aprovação de listas de correio eletrónico, integrando serviços Azure, serviços microsoft e outras aplicações de Software-as-a-Service (SaaS).

> [!div class="nextstepaction"]
> [Manage mailing list requests](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md) (Gerir pedidos de listas de correio)

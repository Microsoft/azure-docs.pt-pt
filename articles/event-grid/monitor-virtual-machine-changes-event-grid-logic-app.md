---
title: Monitorizar alterações de máquina virtual – Azure Event Grid e Logic Apps | Microsoft Docs
description: Verifique a existência de alterações de configuração em máquinas virtuais (VMs) com o Azure Event Grid e o Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 01/12/2019
ms.openlocfilehash: e735c9773971a4c594c32e9ae29eeb295c32810c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824818"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Tutorial: Monitorizar alterações de máquina virtual com o Azure Event Grid e o Logic Apps

Pode iniciar um [fluxo de trabalho de aplicação lógica](../logic-apps/logic-apps-overview.md) automatizado quando ocorrerem eventos específicos em recursos do Azure ou recursos de terceiros. Estes recursos podem publicar esses eventos numa [grelha de eventos do Azure](../event-grid/overview.md). Por sua vez, a grelha de eventos envia esses eventos aos subscritores que têm filas, webhooks ou [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos finais. Como subscritor, a aplicação lógica pode aguardar esses eventos na grelha de eventos antes de executar fluxos de trabalho automatizados para efetuar tarefas, sem ter de escrever qualquer código.

Por exemplo, eis alguns eventos que os editores podem enviar aos subscritores através do serviço Azure Event Grid:

* Criar, ler, atualizar ou eliminar um recurso. Por exemplo, pode monitorizar as alterações que possam implicar custos na sua subscrição do Azure e afetam a fatura. 
* Adicionar ou remover uma pessoa de uma subscrição do Azure.
* A aplicação executa uma ação específica.
* É apresentada uma nova mensagem numa fila.

Este tutorial cria uma aplicação lógica que monitoriza as alterações a uma máquina virtual e envia e-mails sobre essas alterações. Quando criar uma aplicação lógica com uma subscrição de eventos para um recurso do Azure, existe um fluxo de eventos entre esse recurso através de uma grelha de eventos e a aplicação lógica. O tutorial explica-lhe como criar esta aplicação lógica:

![Descrição geral – monitorizar uma máquina virtual com uma grelha de eventos e uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica que monitoriza os eventos a partir de uma grelha de eventos.
> * Adicionar uma condição que verifica especificamente a existência de alterações de máquina virtual.
> * Enviar um e-mail quando a máquina virtual for alterada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de e-mail de [qualquer fornecedor de e-mail suportado pelo Azure Logic Apps](../connectors/apis-list.md), como o Outlook do Office 365, o Outlook.com ou o Gmail, para enviar notificações. Este tutorial utiliza o Outlook do Office 365.

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines). Se ainda não o fez, crie uma máquina virtual através do tutorial [Criar uma VM](https://docs.microsoft.com/azure/virtual-machines/). Para que a máquina virtual publique eventos, [não precisa de fazer mais nada](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Criar uma aplicação lógica que monitorize eventos a partir de uma grelha de eventos

Primeiro, crie uma aplicação lógica e adicione um acionador do Event Grid que monitorize o grupo de recursos para a máquina virtual. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 

2. No canto superior esquerdo do menu principal do Azure, selecione **Criar um recurso** > **Enterprise Integration** > **Aplicação Lógica**.

   ![Criar uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Crie a sua aplicação lógica com as definições especificadas na seguinte tabela:

   ![Indicar os detalhes da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *{your-logic-app-name}* | Indique um nome exclusivo para a aplicação lógica. | 
   | **Subscrição** | *{your-Azure-subscription}* | Selecione a mesma subscrição do Azure para todos os serviços neste tutorial. | 
   | **Grupo de recursos** | *{your-Azure-resource-group}* | Selecione o mesmo grupo de recursos do Azure para todos os serviços neste tutorial. | 
   | **Localização** | *{your-Azure-region}* | Selecione a mesma região para todos os serviços neste tutorial. | 
   | | | 

4. Quando estiver pronto, selecione **Afixar ao dashboard** e escolha **Criar**.

   Acabou de criar um recurso do Azure para a aplicação lógica. 
   Quando o Azure implementar a sua aplicação lógica, o Estruturador de Aplicações Lógicas mostra-lhe modelos de padrões comuns, para que possa começar mais depressa.

   > [!NOTE] 
   > Quando selecionar **Afixar ao dashboard**, a aplicação lógica abre automaticamente no Estruturador de Aplicações Lógicas. Caso contrário, pode encontrar e abrir a aplicação lógica manualmente.

5. Escolha agora o modelo de aplicação lógica. Em **Modelos**, selecione **Aplicação Lógica em Branco** para criar a aplicação lógica do zero.

   ![Escolher o modelo da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O Estruturador de Aplicações Lógicas mostra-lhe agora [*conectores*](../connectors/apis-list.md) e [*acionadores*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que pode utilizar para iniciar a aplicação lógica, bem como as ações que pode adicionar após um acionador para efetuar tarefas. Um acionador é um evento que cria uma instância de aplicação lógica e inicia o fluxo de trabalho da aplicação lógica. 
   A aplicação lógica precisa de um acionador como primeiro item.

6. Na caixa de pesquisa, introduza "event grid" como filtro. Selecione este acionador: **O Azure Event Grid - num evento de recurso**

   ![Selecione este acionador: "O azure Event Grid - num evento de recursos"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Quando lhe for solicitado, inicie sessão no Azure Event Grid com as suas credenciais do Azure.

   ![Iniciar sessão com as credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se iniciou sessão com uma conta Microsoft pessoal, como @outlook.com ou @hotmail.com, o acionador do Event Grid pode não aparecer corretamente. Como solução, selecione [Ligar com o Principal de Serviço](../active-directory/develop/howto-create-service-principal-portal.md) ou autentique-se como membro do Azure Active Directory associado à sua subscrição do Azure, por exemplo, *nome de utilizador*@emailoutlook.onmicrosoft.com.

8. Agora, subscreva a sua aplicação lógica para eventos de editor. Forneça os detalhes para a subscrição de eventos, conforme especificado na tabela seguinte:

   ![Fornecer detalhes para a subscrição de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Subscrição** | *{virtual-machine-Azure-subscription}* | Selecione a subscrição do Azure do editor de eventos. Para este tutorial, selecione a subscrição do Azure para a sua máquina virtual. | 
   | **Tipo de Recurso** | Microsoft.Resources.resourceGroups | Selecione o tipo de recurso do editor de eventos. Para este tutorial, selecione o valor especificado para a aplicação lógica monitorizar apenas grupos de recursos. | 
   | **Nome do Recurso** | *{virtual-machine-resource-group-name}* | Selecione o nome do recurso do editor. Para este tutorial, selecione o nome do grupo de recursos para a sua máquina virtual. | 
   | Para definições opcionais, selecione **Mostrar opções avançadas**. | *{see descriptions}* | * **Filtro de prefixo**: Para este tutorial, deixe esta definição está vazio. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Filtro de sufixo**: Para este tutorial, deixe esta definição está vazio. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de sufixo como filtro, por exemplo, uma extensão de nome de ficheiro, quando quiser apenas tipos de ficheiro específicos.<p>* **Nome da subscrição**: Indique um nome exclusivo para a subscrição de eventos. |
   | | | 

   Quando estiver pronto, o acionador do Event Grid pode ter o seguinte aspeto neste exemplo:
   
   ![Detalhes do acionador do Event Grid de exemplo](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. Para fechar e ocultar os detalhes de uma ação na aplicação lógica, selecione a barra de título da ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando guardar a aplicação lógica com um acionador do Event Grid, o Azure cria automaticamente uma subscrição de eventos para a aplicação lógica para o recurso selecionado. Assim, quando o recurso publicar um evento na grelha de eventos, esta coloca automaticamente o evento na aplicação lógica. Este evento aciona a aplicação lógica e cria e executa uma instância do fluxo de trabalho definido nos próximos passos.

A aplicação lógica está agora publicada e escuta eventos da grelha de eventos, mas não faz nada até adicionar ações ao fluxo de trabalho. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Adicionar uma condição que verifica a existência de alterações de máquina virtual

Para executar o fluxo de trabalho da aplicação lógica apenas quando ocorre um evento específico, adicione uma condição que verifica a existência de operações de "escrita" da máquina virtual. Quando esta condição for verdadeira, a aplicação lógica envia-lhe um e-mail com detalhes sobre a máquina virtual atualizada.

1. No Estruturador de Aplicações Lógicas, no acionador do Event Grid, selecione **Novo passo** > **Adicionar uma condição**.

   ![Adicionar uma condição à aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   O Estruturador de Aplicações Lógicas adiciona uma condição em branco ao fluxo de trabalho, incluindo caminhos de ação a seguir com base na condição ser verdadeira ou falsa.

   ![Condição em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Na caixa **Condição**, selecione **Editar em modo avançado**.
Introduza a expressão:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   A sua condição terá agora este aspeto:

   ![Condição em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Esta expressão verifica o evento `body` relativamente a um objeto `data` em que a propriedade `operationName` é a operação `Microsoft.Compute/virtualMachines/write`. 
   Saiba mais sobre o [esquema de eventos do Event Grid](../event-grid/event-schema.md).

3. Para fornecer uma descrição para a condição, selecione o botão **reticências** (**...** ) na forma da condição e, em seguida, selecione **Mudar o nome**.

   > [!NOTE] 
   > Os exemplos posteriores neste tutorial também fornecem descrições para os passos no fluxo de trabalho da aplicação lógica.

4. Agora, selecione **Editar no modo básico** para que a expressão seja resolvida automaticamente conforme mostrado:

   ![Condição da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Guarde a aplicação lógica.

## <a name="send-email-when-your-virtual-machine-changes"></a>Enviar um e-mail quando a máquina virtual for alterada

Agora, adicione uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) para receber um e-mail quando a condição especificada for verdadeira.

1. Na caixa da condição **Se verdadeiro**, selecione **Adicionar uma ação**.

   ![Adicionar ação quando a condição for verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Na caixa de pesquisa, introduza "e-mail" como filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo: 

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook. 
   * Em contas Microsoft pessoais, selecione o conector Outlook.com. 
   * Em contas do Gmail, selecione o conector Gmail. 

   Vamos continuar a utilizar o conector Office 365 Outlook. 
   Se utilizar outro fornecedor, os passos são os mesmos, mas a IU poderá ser diferente. 

   ![Selecionar a ação "enviar e-mail"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Se ainda não tiver uma ligação para o seu fornecedor de e-mail, inicie sessão na sua conta de e-mail quando lhe for pedido para efetuar a autenticação.

4. Forneça os detalhes do e-mail conforme especificado na tabela seguinte:

   ![Ação de e-mail em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar de entre os campos disponíveis no fluxo de trabalho, clique no interior de uma caixa de edição, para que seja aberta a lista **Conteúdo dinâmico**, ou selecione **Adicionar conteúdo dinâmico**. Para obter mais campos, selecione **Ver mais** para cada secção na lista. Para fechar a lista **Conteúdo dinâmico**, selecione **Adicionar conteúdo dinâmico**.

   | Definição | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Para** | *{recipient-email-address}* |Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. | 
   | **Assunto** | Recurso atualizado: **Assunto**| Introduza o conteúdo para o assunto do e-mail. Neste tutorial, introduza o texto sugerido e selecione o campo **Assunto** do evento. Aqui, o assunto do e-mail inclui o nome do recurso atualizado (máquina virtual). | 
   | **Corpo** | Grupo de recursos: **Tópico** <p>Tipo de evento: **Tipo de evento**<p>ID de evento: **ID**<p>Hora: **Hora do evento** | Introduza o conteúdo para o corpo do e-mail. Para este tutorial, introduza o texto sugerido e selecione os campos **Tópico**, **Tipo de Evento**, **ID** e **Hora do Evento** do evento para que o e-mail inclua o nome do grupo de recursos, o tipo de evento, o carimbo de data/hora do evento e o ID do evento para a atualização. <p>Para adicionar linhas em branco ao conteúdo, prima Shift + Enter. | 
   | | | 

   > [!NOTE] 
   > Se selecionar um campo que representa uma matriz, o estruturador adiciona automaticamente um ciclo **For each** em torno da ação que referencia essa matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

   Agora, a ação de e-mail pode ter o seguinte aspeto neste exemplo:

   ![Selecionar dados a incluir no e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Por fim, a aplicação lógica concluída pode ter o seguinte aspeto neste exemplo:

   ![Aplicação lógica concluída](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Guarde a aplicação lógica. Para fechar e ocultar os detalhes de cada ação na aplicação lógica, selecione a barra de título da ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   A aplicação lógica está agora publicada, mas aguarda as alterações à máquina virtual antes de efetuar alguma ação. 
   Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho da aplicação lógica

1. Para verificar se a aplicação lógica está a obter os eventos especificados, atualize a máquina virtual. 

   Por exemplo, pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Após alguns instantes, deve obter um e-mail. Por exemplo:

   ![E-mail sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Para rever o histórico de execuções e acionadores da sua aplicação lógica, no menu da mesma, selecione **Descrição geral**. Para ver mais detalhes sobre uma execução, selecione a linha relativa à mesma.

   ![Histórico de execuções da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Para ver as entradas e saídas de cada passo, expanda o passo que pretende rever. Estas informações podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação lógica.
 
   ![Detalhes do histórico de execuções da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Parabéns, criou e executou uma aplicação lógica que monitoriza eventos de recursos através de uma grelha de eventos e e-mails, quando esses eventos ocorrem. Também ficou a saber como pode criar facilmente fluxos de trabalho que automatizam processos e integram sistemas e serviços cloud.

Pode monitorizar outras alterações de configuração com grelhas de eventos e aplicações lógicas, por exemplo:

* Uma máquina virtual obtém direitos de controlo de acesso baseado em funções (RBAC).
* As alterações são efetuadas a um grupo de segurança de rede (NSG) numa interface de rede (NIC).
* São adicionados ou removidos discos para uma máquina virtual.
* Um endereço IP público é atribuído a uma NIC de máquina virtual.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utiliza recursos e realiza ações que incorrem em custos na sua subscrição do Azure. Assim, quando concluir o tutorial e os testes, certifique-se de que desativa ou elimina quaisquer recursos nos casos em que não quer incorrer em custos.

* Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. No menu da aplicação lógica, escolha **Descrição geral**. Na barra de ferramentas, escolha **Desativar**.

  ![Desativar a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

* Para eliminar permanentemente a sua aplicação lógica, no menu da mesma, selecione **Descrição geral**. Na barra de ferramentas, escolha **Eliminar**. Confirme que pretende eliminar a sua aplicação lógica e escolha **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar e encaminhar eventos personalizados com o Event Grid](../event-grid/custom-event-quickstart.md)

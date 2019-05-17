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
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801108"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Tutorial: Monitorizar alterações de máquina virtual com o Azure Event Grid e o Logic Apps

Pode iniciar um [fluxo de trabalho de aplicação lógica](../logic-apps/logic-apps-overview.md) automatizado quando ocorrerem eventos específicos em recursos do Azure ou recursos de terceiros. Estes recursos podem publicar esses eventos numa [grelha de eventos do Azure](../event-grid/overview.md). Por sua vez, a grelha de eventos envia esses eventos aos subscritores que têm filas, webhooks ou [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos finais. Como subscritor, a aplicação lógica pode aguardar esses eventos na grelha de eventos antes de executar fluxos de trabalho automatizados para efetuar tarefas, sem ter de escrever qualquer código.

Por exemplo, eis alguns eventos que os editores podem enviar aos subscritores através do serviço Azure Event Grid:

* Criar, ler, atualizar ou eliminar um recurso. Por exemplo, pode monitorizar as alterações que possam implicar custos na sua subscrição do Azure e afetam a fatura. 
* Adicionar ou remover uma pessoa de uma subscrição do Azure.
* A aplicação executa uma ação específica.
* É apresentada uma nova mensagem numa fila.

Este tutorial cria uma aplicação lógica que monitoriza alterações a uma máquina virtual e envia e-mails sobre essas alterações. Quando criar uma aplicação lógica com uma subscrição de eventos para um recurso do Azure, existe um fluxo de eventos entre esse recurso através de uma grelha de eventos e a aplicação lógica. O tutorial explica-lhe como criar esta aplicação lógica:

![Descrição geral – monitorizar uma máquina virtual com uma grelha de eventos e uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica que monitoriza os eventos a partir de uma grelha de eventos.
> * Adicionar uma condição que verifica especificamente a existência de alterações de máquina virtual.
> * Enviar um e-mail quando a máquina virtual for alterada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma conta de e-mail de um fornecedor de e-mail suportada pelo Logic Apps para o envio de notificações, como o Outlook do Office 365, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/). 

  Este tutorial utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines). Se ainda não o fez, crie uma máquina virtual através da [criar um tutorial VM](../virtual-machines/windows/quick-create-portal.md). Para que a máquina virtual publique eventos, [não precisa de fazer mais nada](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Criar uma aplicação lógica em branco

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure. 

1. No menu principal do Azure, selecione **criar um recurso** > **integração** > **aplicação lógica**.

   ![Criar uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Sob **aplicação lógica**, fornecer informações sobre a sua aplicação lógica. Quando tiver terminado, escolha **Create** (Criar).

   ![Indicar os detalhes da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriedade | Valor sugerido | Descrição |
   | -------- | --------------- | ----------- |
   | **Nome** | <*logic-app-name*> | Forneça um nome exclusivo para a aplicação lógica. |
   | **Subscrição** | <*Azure-subscription-name*> | Selecione a mesma subscrição do Azure para todos os serviços neste tutorial. |
   | **Grupo de recursos** | <*Azure-resource-group*> | Selecione o mesmo grupo de recursos do Azure para todos os serviços neste tutorial. |
   | **Localização** | <*Azure-datacenter-region*> | Selecione a mesma região para todos os serviços neste tutorial. |
   |||

   Acabou de criar um recurso do Azure para a aplicação lógica. 

1. Quando o Azure implementar a aplicação lógica, Designer de aplicações lógicas mostra uma página com uma introdução em vídeo e os acionadores habitualmente utilizados. Percorra o ecrã até passar o vídeo e os acionadores. 

1. Em **Modelos**, escolha **Aplicação Lógica em Branco**.

   ![Escolher o modelo da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O estruturador de aplicações lógicas mostra-lhe agora [ *acionadores* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) que pode utilizar para iniciar a sua aplicação lógica. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. 
   Sempre que o acionador é acionado, Azure Logic Apps cria uma instância de fluxo de trabalho que executa a aplicação lógica.

## <a name="add-event-grid-trigger"></a>Adicionar o acionador do Event Grid 

Agora, adicione o acionador do Event Grid que monitoriza o grupo de recursos para a máquina virtual. 

1. No estruturador, na caixa de pesquisa, introduza "event grid" como o filtro. Na lista de disparadores, selecione este acionador: **Quando recursos ocorre um evento - Azure Event Grid**

   ![Selecione este acionador: "Num evento de recursos"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Quando lhe for pedido, inicie sessão no Azure Event Grid com as suas credenciais de conta do Azure. Na **inquilino** lista, que mostra o inquilino do Azure Active Directory que está associada à sua subscrição do Azure, verifique se aparece o inquilino correto.

   ![Iniciar sessão com as credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se iniciou sessão com uma conta Microsoft pessoal, como @outlook.com ou @hotmail.com, o acionador do Event Grid pode não aparecer corretamente. Como solução, selecione [Ligar com o Principal de Serviço](../active-directory/develop/howto-create-service-principal-portal.md) ou autentique-se como membro do Azure Active Directory associado à sua subscrição do Azure, por exemplo, *nome de utilizador*@emailoutlook.onmicrosoft.com.

1. Agora, subscreva a sua aplicação lógica para eventos de editor. Forneça os detalhes para a subscrição de eventos, conforme especificado na tabela seguinte:

   ![Fornecer detalhes para a subscrição de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Propriedade | Necessário | Value | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Subscrição** | Sim | <*event-publisher-Azure-subscription-name*> | Selecione o nome da subscrição do Azure associada com o publicador de eventos. Neste tutorial, selecione o nome da subscrição do Azure para a máquina virtual. |
   | **Tipo de Recurso** | Sim | <*event-publisher-Azure-resource-type*> | Selecione o tipo de recurso do Azure para o publicador de eventos. Neste tutorial, selecione este valor para monitorizar os grupos de recursos do Azure: <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Nome do Recurso** |  Sim | <*event-publisher-Azure-resource-name*> | Selecione o nome para o recurso do Azure para o publicador de eventos. Esta lista varia consoante o tipo de recurso que selecionou. Para este tutorial, selecione o nome para o grupo de recursos do Azure para a máquina virtual. |
   | **Item de tipo de evento** |  Não | <*event-types*> | Selecione um ou mais tipos de evento específico para filtrar e enviar para o event grid. Por exemplo, opcionalmente, pode adicionar estes tipos de eventos para detectar quando recursos forem alterados ou eliminados: <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>Para obter mais informações, veja estes tópicos: <p><p>- [Compreender a filtragem de eventos](../event-grid/event-filtering.md) <br>- [Filtro de eventos do Event Grid](../event-grid/how-to-filter-events.md) <br>- [Esquema de eventos do Azure Event Grid para grupos de recursos](../event-grid/event-schema-resource-groups.md) |
   | **Nome da subscrição** | Não | <*event-subscription-name*> | Indique um nome exclusivo para a subscrição de eventos. |
   | Para obter definições opcionais, escolha **adicione o novo parâmetro**. | Não | {consulte as descrições} | * **Filtro de prefixo**: Para este tutorial, deixe esta propriedade está vazio. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Filtro de sufixo**: Para este tutorial, deixe esta propriedade está vazio. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de sufixo como filtro, por exemplo, uma extensão de nome de ficheiro, quando quiser apenas tipos de ficheiro específicos. |
   |||

   Quando tiver terminado, o acionador do Event Grid pode ser semelhante a este exemplo:

   ![Detalhes de Acionador do Event Grid de exemplo](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do estruturador, escolha **Guardar**. Para fechar e ocultar os detalhes de uma ação na aplicação lógica, selecione a barra de título da ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando guardar a aplicação lógica com um acionador do Event Grid, o Azure cria automaticamente uma subscrição de eventos para a aplicação lógica para o recurso selecionado. Assim, quando o recurso publicar um evento na grelha de eventos, esta coloca automaticamente o evento na aplicação lógica. Este evento aciona a aplicação lógica e cria e executa uma instância do fluxo de trabalho definido nos próximos passos.

A aplicação lógica está agora publicada e escuta eventos da grelha de eventos, mas não faz nada até adicionar ações ao fluxo de trabalho. 

## <a name="add-condition"></a>Adicionar condição

Para executar o fluxo de trabalho da aplicação lógica apenas quando ocorre um evento específico, adicione uma condição que verifica a existência de operações de "escrita" da máquina virtual. Quando esta condição for verdadeira, a aplicação lógica envia-lhe um e-mail com detalhes sobre a máquina virtual atualizada.

1. No Estruturador da aplicação lógica, sob o acionador do event grid, escolha **novo passo**.

   ![Selecione "Novo passo"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Na caixa de pesquisa, introduza "condição" como o filtro. Na lista de ações, selecione a ação: **condição**

   ![Adicionar uma condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   O Estruturador de Aplicações Lógicas adiciona uma condição em branco ao fluxo de trabalho, incluindo caminhos de ação a seguir com base na condição ser verdadeira ou falsa.

   ![Condição em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Mudar o nome do título de condição para `If a virtual machine in your resource group has changed`. Na barra de título da condição, escolha as reticências (**...** ) e selecione **mudar o nome**.

   ![Mudar o nome da condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Criar uma condição que verifica o evento `body` para uma `data` objeto onde o `operationName` propriedade é igual ao `Microsoft.Compute/virtualMachines/write` operação. Saiba mais sobre o [esquema de eventos do Event Grid](../event-grid/event-schema.md).

   1. Na primeira linha, em **E**, clique dentro da caixa esquerda. Na lista de conteúdo dinâmico que aparece, escolha **expressão**.

      ![Escolha "Expressão"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. No editor de expressões, introduza a expressão e escolha **OK**: 

      `triggerBody()?['data']['operationName']`

      Por exemplo:

      ![Escolha "Expressão"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Na caixa do meio, mantenha o operador **é igual a**.

   1. Na caixa da direita, introduza este valor:

      `Microsoft.Compute/virtualMachines/write`

   A condição concluída agora este aspeto:

   ![Condição concluída](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Se mudar do modo de exibição de design para exibição e de volta à vista de estrutura de código, a expressão que especificou na condição é resolvido para o **data.operationName** token:

   ![Condição resolvida](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Guarde a aplicação lógica.

## <a name="send-email-notifications"></a>Enviar notificações por e-mail

Agora, adicione uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) para receber um e-mail quando a condição especificada for verdadeira.

1. Na caixa da condição **Se verdadeiro**, selecione **Adicionar uma ação**.

   ![Adicionar ação quando a condição for verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Na caixa de pesquisa, introduza "enviar e-mail" como o filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo: 

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook. 

   * Em contas Microsoft pessoais, selecione o conector Outlook.com. 

   * Em contas do Gmail, selecione o conector Gmail. 

   Este tutorial continua com o conector do Outlook do Office 365. 
   Se utilizar outro fornecedor, os passos são os mesmos, mas a IU poderá ser ligeiramente diferente. 

   ![Selecionar a ação "enviar e-mail"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Se ainda não tiver uma ligação para o seu fornecedor de e-mail, inicie sessão na sua conta de e-mail quando lhe for pedido para efetuar a autenticação.

1. Renomeie o título do e-mail de envio para este título: `Send email when virtual machine updated`

1. Forneça os detalhes do e-mail conforme especificado na tabela seguinte:

   ![Ação de e-mail em branco](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar a partir dos resultados dos passos anteriores no fluxo de trabalho, clique numa caixa de edição para que a lista de conteúdo dinâmico apareça ou escolher **adicionar conteúdo dinâmico**. Para obter mais resultados, escolha **ver mais** para cada seção na lista. Para fechar a lista de conteúdo dinâmico, escolha **adicionar conteúdo dinâmico** novamente.

   | Propriedade | Necessário | Value | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Para** | Sim | <*recipient\@domain*> | Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto** | Sim | Recurso atualizado: **Assunto** | Introduza o conteúdo para o assunto do e-mail. Para este tutorial, introduza o texto especificado e selecione o evento **assunto** campo. Aqui, o assunto do e-mail inclui o nome do recurso atualizado (máquina virtual). |
   | **Corpo** | Sim | Recurso: **Tópico** <p>Tipo de evento: **Tipo de evento**<p>ID de evento: **ID**<p>Hora: **Hora do evento** | Introduza o conteúdo para o corpo do e-mail. Neste tutorial, introduza o texto especificado e selecione o evento **tópico**, **tipo de evento**, **ID**, e **hora do evento** campos para que sua e-mail inclui o recurso que disparou o evento, o tipo de evento, a timestamp de evento e o ID de evento para a atualização. Para este tutorial, o recurso é o grupo de recursos do Azure selecionado no acionador. <p>Para adicionar linhas em branco ao conteúdo, prima Shift + Enter. |
   ||||

   > [!NOTE]
   > Se selecionar um campo que representa uma matriz, o estruturador adiciona automaticamente um ciclo **For each** em torno da ação que referencia essa matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

   Agora, a ação de e-mail pode ter o seguinte aspeto neste exemplo:

   ![Selecionar dados a incluir no e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Por fim, a aplicação lógica concluída pode ter o seguinte aspeto neste exemplo:

   ![Aplicação lógica concluída](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Guarde a aplicação lógica. Para fechar e ocultar os detalhes de cada ação na aplicação lógica, selecione a barra de título da ação.

   A aplicação lógica está agora publicada, mas aguarda as alterações à máquina virtual antes de efetuar alguma ação. 
   Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho da aplicação lógica

1. Para verificar se a aplicação lógica está a obter os eventos especificados, atualize a máquina virtual.

   Por exemplo, pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Após alguns instantes, deve obter um e-mail. Por exemplo:

   ![E-mail sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Para rever as execuções e acionar o histórico para a sua aplicação lógica, no menu da aplicação lógica, selecione **descrição geral**. Para ver mais detalhes sobre uma execução, selecione a linha relativa à mesma.

   ![Histórico de execuções da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Para ver as entradas e saídas de cada passo, expanda o passo que pretende rever. Estas informações podem ajudá-lo a diagnosticar e depurar problemas na sua aplicação lógica.

   ![Detalhes do histórico de execuções da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Parabéns, criou e executou uma aplicação lógica que monitoriza eventos de recursos através de uma grelha de eventos e e-mails, quando esses eventos ocorrem. Também ficou a saber como pode criar facilmente fluxos de trabalho que automatizam processos e integram sistemas e serviços cloud.

Pode monitorizar outras alterações de configuração com grelhas de eventos e aplicações lógicas, por exemplo:

* Uma máquina virtual obtém direitos de controlo de acesso baseado em funções (RBAC).
* As alterações são efetuadas a um grupo de segurança de rede (NSG) numa interface de rede (NIC).
* São adicionados ou removidos discos para uma máquina virtual.
* Um endereço IP público é atribuído a uma NIC de máquina virtual.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial utiliza recursos e realiza ações que incorrem em custos na sua subscrição do Azure. Assim, quando concluir o tutorial e os testes, certifique-se de que desativa ou elimina quaisquer recursos nos casos em que não quer incorrer em custos.

* Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. No menu da aplicação lógica, selecione **descrição geral**. Na barra de ferramentas, escolha **Desativar**.

  ![Desativar a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

* Para eliminar permanentemente a sua aplicação lógica, no menu da aplicação lógica, selecione **descrição geral**. Na barra de ferramentas, escolha **Eliminar**. Confirme que pretende eliminar a aplicação lógica e escolha **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar e encaminhar eventos personalizados com o Event Grid](../event-grid/custom-event-quickstart.md)

---
title: Monitorar alterações de máquinas virtuais-grade de eventos do Azure & aplicativos lógicos
description: Verificar alterações em VMs (máquinas virtuais) usando a grade de eventos do Azure e os aplicativos lógicos
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: 5d852378812d8e69480ceb2c5dcea95f1d5f3770
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488618"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Tutorial: monitorar alterações de máquina virtual usando a grade de eventos do Azure e os aplicativos lógicos

Para monitorar e responder a eventos específicos que ocorrem em recursos do Azure ou em recursos de terceiros, você pode automatizar e executar tarefas como um fluxo de trabalho criando um [aplicativo lógico](../logic-apps/logic-apps-overview.md) que usa o código mínimo. Esses recursos podem publicar eventos em uma [grade de eventos do Azure](../event-grid/overview.md). Por sua vez, a grelha de eventos envia esses eventos aos subscritores que têm filas, webhooks ou [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos finais. Como assinante, seu aplicativo lógico pode aguardar esses eventos da grade de eventos antes de executar fluxos de trabalho automatizados para executar tarefas.

Por exemplo, eis alguns eventos que os editores podem enviar aos subscritores através do serviço Azure Event Grid:

* Criar, ler, atualizar ou eliminar um recurso. Por exemplo, pode monitorizar as alterações que possam implicar custos na sua subscrição do Azure e afetam a fatura.

* Adicionar ou remover uma pessoa de uma subscrição do Azure.

* A aplicação executa uma ação específica.

* É apresentada uma nova mensagem numa fila.

Este tutorial cria um aplicativo lógico que monitora as alterações em uma máquina virtual e envia emails sobre essas alterações. Quando criar uma aplicação lógica com uma subscrição de eventos para um recurso do Azure, existe um fluxo de eventos entre esse recurso através de uma grelha de eventos e a aplicação lógica. O tutorial explica-lhe como criar esta aplicação lógica:

![Descrição geral – monitorizar uma máquina virtual com uma grelha de eventos e uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma aplicação lógica que monitoriza os eventos a partir de uma grelha de eventos.
> * Adicionar uma condição que verifica especificamente a existência de alterações de máquina virtual.
> * Enviar um e-mail quando a máquina virtual for alterada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma conta de email de um provedor de email com suporte dos aplicativos lógicos para enviar notificações, como o Office 365 Outlook, Outlook.com ou gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/).

  Este tutorial usa uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines) que está sozinha em seu próprio grupo de recursos do Azure. Se você ainda não tiver feito isso, crie uma máquina virtual por meio do [tutorial criar uma VM](../virtual-machines/windows/quick-create-portal.md). Para que a máquina virtual publique eventos, [não precisa de fazer mais nada](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Criar uma aplicação lógica em branco

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. No menu principal do Azure, selecione **criar um recurso** > **integração** > **aplicativo lógico**.

   ![Criar uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Em **aplicativo lógico**, forneça informações sobre o recurso do aplicativo lógico. Quando terminar, selecione **Criar**.

   ![Indicar os detalhes da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*lógica-nome do aplicativo*> | Forneça um nome exclusivo para seu aplicativo lógico. |
   | **Subscrição** | Sim | <*Azure-subscription-name*> | Selecione a mesma assinatura do Azure para todos os serviços neste tutorial. |
   | **Grupo de recursos** | Sim | <*Azure-Resource-group*> | O nome do grupo de recursos do Azure para seu aplicativo lógico, que você pode selecionar para todos os serviços neste tutorial. |
   | **Localização** | Sim | <*do Azure-region*> | Selecione a mesma região para todos os serviços neste tutorial. |
   |||

1. Depois que o Azure implanta seu aplicativo lógico, o designer de aplicativos lógicos mostra uma página com um vídeo de introdução e gatilhos comumente usados. Percorra o ecrã até passar o vídeo e os acionadores.

1. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecionar modelo de aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O designer de aplicativos lógicos agora mostra os [*gatilhos*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que você pode usar para iniciar seu aplicativo lógico. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho é disparado, o aplicativo lógico do Azure cria uma instância de fluxo de trabalho que executa sua aplicação lógica.

## <a name="add-an-event-grid-trigger"></a>Adicionar um gatilho de grade de eventos

Agora, adicione o gatilho de grade de eventos, que você usa para monitorar o grupo de recursos para sua máquina virtual.

1. No designer, na caixa de pesquisa, insira `event grid` como seu filtro. Na lista de gatilhos, selecione o gatilho **quando um evento de recurso ocorre** .

   ![Selecione este gatilho: "em um evento de recurso"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Quando solicitado, entre na grade de eventos do Azure com suas credenciais de conta do Azure. Na lista de **locatários** , que mostra o locatário Azure Active Directory associado à sua assinatura do Azure, verifique se o locatário correto é exibido, por exemplo:

   ![Iniciar sessão com as credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se iniciou sessão com uma conta Microsoft pessoal, como @outlook.com ou @hotmail.com, o acionador do Event Grid pode não aparecer corretamente. Como alternativa, selecione [conectar-se com a entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md)ou autenticar como um membro do Azure Active Directory associado à sua assinatura do Azure, por exemplo,@emailoutlook.onmicrosoft.comde nome de *usuário* .

1. Agora, assine seu aplicativo lógico para eventos do Publicador. Forneça os detalhes sobre sua assinatura de evento, conforme descrito na tabela a seguir, por exemplo:

   ![Fornecer detalhes para a subscrição de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Subscrição** | Sim | <*evento-Publisher-Azure-Subscription-name*> | Selecione o nome da assinatura do Azure que está associado ao *Publicador de eventos*. Para este tutorial, selecione o nome da assinatura do Azure para sua máquina virtual. |
   | **Tipo de Recurso** | Sim | *evento de <-Publisher-Azure-Resource-type*> | Selecione o tipo de recurso do Azure para o editor de eventos. Para obter mais informações sobre os tipos de recursos do Azure, consulte [tipos e provedores de recursos do Azure](../azure-resource-manager/resource-manager-supported-services.md). Para este tutorial, selecione o valor `Microsoft.Resources.ResourceGroups` para monitorar os grupos de recursos do Azure. |
   | **Nome do Recurso** |  Sim | <*Event-Publisher-Azure-Resource-name*> | Selecione o nome do recurso do Azure para o editor de eventos. Essa lista varia de acordo com o tipo de recurso que você selecionou. Para este tutorial, selecione o nome do grupo de recursos do Azure que inclui sua máquina virtual. |
   | **Item de tipo de evento** |  Não | <*tipos de evento*> | Selecione um ou mais tipos de eventos específicos para filtrar e enviar à sua grade de eventos. Por exemplo, opcionalmente, você pode adicionar esses tipos de evento para detectar quando os recursos são alterados ou excluídos: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Para obter mais informações, consulte estes tópicos: <p><p>- o [esquema de eventos da grade de eventos do Azure para grupos de recursos](../event-grid/event-schema-resource-groups.md) <br>- [entender a filtragem de eventos](../event-grid/event-filtering.md) <br>- [filtrar eventos para a grade de eventos](../event-grid/how-to-filter-events.md) |
   | Para adicionar propriedades opcionais, selecione **Adicionar novo parâmetro**e, em seguida, selecione as propriedades desejadas. | Não | {Veja as descrições} | **filtro de prefixo**de * : para este tutorial, deixe essa propriedade vazia. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>**filtro de sufixo*** : para este tutorial, deixe essa propriedade vazia. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de sufixo como filtro, por exemplo, uma extensão de nome de ficheiro, quando quiser apenas tipos de ficheiro específicos. <p>**nome da assinatura**do * : para este tutorial, você pode fornecer um nome exclusivo para sua assinatura de evento. |
   |||

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**. Para recolher e ocultar os detalhes de uma ação em seu aplicativo lógico, selecione a barra de título da ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando guardar a aplicação lógica com um acionador do Event Grid, o Azure cria automaticamente uma subscrição de eventos para a aplicação lógica para o recurso selecionado. Assim, quando o recurso publicar um evento na grelha de eventos, esta coloca automaticamente o evento na aplicação lógica. Este evento aciona a aplicação lógica e cria e executa uma instância do fluxo de trabalho definido nos próximos passos.

A aplicação lógica está agora publicada e escuta eventos da grelha de eventos, mas não faz nada até adicionar ações ao fluxo de trabalho.

## <a name="add-a-condition"></a>Adicionar uma condição

Se você quiser que seu aplicativo lógico seja executado somente quando ocorrer um evento ou operação específica, adicione uma condição que verifica a operação de `Microsoft.Compute/virtualMachines/write`. Quando esta condição for verdadeira, a aplicação lógica envia-lhe um e-mail com detalhes sobre a máquina virtual atualizada.

1. No designer de aplicativo lógico, no gatilho de grade de eventos, selecione **nova etapa**.

   ![Selecione "nova etapa"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Em **escolher uma ação**, na caixa de pesquisa, insira `condition` como filtro. Na lista ações, selecione a ação **condição** .

   ![Adicionar uma condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   O Estruturador de Aplicações Lógicas adiciona uma condição em branco ao fluxo de trabalho, incluindo caminhos de ação a seguir com base na condição ser verdadeira ou falsa.

   ![Uma condição vazia é exibida](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Renomeie o título da condição para `If a virtual machine in your resource group has changed`. Na barra de título da condição, selecione o botão de reticências ( **...** ) e selecione **renomear**.

   ![Renomear a condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Crie uma condição que verifica o `body` de eventos para um objeto `data` em que a propriedade `operationName` é igual à operação de `Microsoft.Compute/virtualMachines/write`. Saiba mais sobre o [esquema de eventos do Event Grid](../event-grid/event-schema.md).

   1. Na primeira linha, em **E**, clique dentro da caixa esquerda. Na lista conteúdo dinâmico que aparece, selecione **expressão**.

      ![Selecione "expressão" para abrir o editor de expressão](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. No editor de expressão, insira esta expressão, que retorna o nome da operação do gatilho e selecione **OK**:

      `triggerBody()?['data']['operationName']`

      Por exemplo:

      ![Insira a expressão para extrair o nome da operação](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Na caixa do meio, mantenha o operador **é igual a**.

   1. Na caixa à direita, digite esse valor, que é a operação específica que você deseja monitorar:

      `Microsoft.Compute/virtualMachines/write`

   Agora, sua condição concluída é semelhante a este exemplo:

   ![Condição concluída que compara a operação](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Se você alternar de modo de exibição de design para modo de exibição de código e de volta para o modo de design, a expressão que você especificou na condição será resolvida para o token **Data. operationName** :

   ![Tokens resolvidos na condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Guarde a aplicação lógica.

## <a name="send-email-notifications"></a>Enviar notificações por e-mail

Agora, adicione uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) para que você possa receber um email quando a condição especificada for verdadeira.

1. Na caixa condição **If true** , selecione **Adicionar uma ação**.

   ![Adicionar ação quando a condição for verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Em **escolher uma ação**, na caixa de pesquisa, insira `send an email` como filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo:

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook.

   * Em contas Microsoft pessoais, selecione o conector Outlook.com.

   * Em contas do Gmail, selecione o conector Gmail.

   Este tutorial continua com o conector do Outlook do Office 365. Se você usar um provedor diferente, as etapas permanecerão as mesmas, mas sua interface do usuário poderá parecer um pouco diferente.

   ![Selecionar a ação "enviar e-mail"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Se ainda não tiver uma ligação para o seu fornecedor de e-mail, inicie sessão na sua conta de e-mail quando lhe for pedido para efetuar a autenticação.

1. Renomeie a ação enviar email para este título: `Send email when virtual machine updated`

1. Forneça informações sobre o email conforme especificado na tabela a seguir:

   ![Fornecer informações sobre a ação de email](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar a saída das etapas anteriores no fluxo de trabalho, clique dentro de uma caixa de edição para que a lista de conteúdo dinâmico seja exibida ou selecione **adicionar conteúdo dinâmico**. Para obter mais resultados, selecione **Ver mais** para cada seção na lista. Para fechar a lista de conteúdo dinâmico, selecione **adicionar conteúdo dinâmico** novamente.

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Para** | Sim | <*destinatário\@domínio*> | Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto** | Sim | `Resource updated:` **Subject** | Introduza o conteúdo para o assunto do e-mail. Para este tutorial, insira o texto especificado e selecione o campo **assunto** do evento. Aqui, o assunto do e-mail inclui o nome do recurso atualizado (máquina virtual). |
   | **Corpo** | Sim | `Resource:` **tópico** <p>**tipo de evento** `Event type:`<p>**ID** de `Event ID:`<p>`Time:` **tempo do evento** | Introduza o conteúdo para o corpo do e-mail. Para este tutorial, insira o texto especificado e selecione os campos de **tópico**, **tipo de evento**, **ID**e **hora** do evento para que seu email inclua o recurso que disparou o evento, o tipo de evento, o carimbo de data/hora do evento e a ID do evento para o cumulativo. Para este tutorial, o recurso é o grupo de recursos do Azure selecionado no gatilho. <p>Para adicionar linhas em branco ao conteúdo, prima Shift + Enter. |
   ||||

   > [!NOTE]
   > Se selecionar um campo que representa uma matriz, o estruturador adiciona automaticamente um ciclo **For each** em torno da ação que referencia essa matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

   Agora, a ação de e-mail pode ter o seguinte aspeto neste exemplo:

   ![Selecionar dados a incluir no e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Por fim, a aplicação lógica concluída pode ter o seguinte aspeto neste exemplo:

   ![Aplicação lógica concluída](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Guarde a aplicação lógica. Para recolher e ocultar os detalhes de cada ação em seu aplicativo lógico, selecione a barra de título da ação.

   A aplicação lógica está agora publicada, mas aguarda as alterações à máquina virtual antes de efetuar alguma ação. Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho da aplicação lógica

1. Para verificar se a aplicação lógica está a obter os eventos especificados, atualize a máquina virtual.

   Por exemplo, pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Após alguns instantes, deve obter um e-mail. Por exemplo:

   ![E-mail sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Para examinar o histórico de execuções e de gatilho para seu aplicativo lógico, no menu do aplicativo lógico, selecione **visão geral**. Para exibir mais detalhes sobre uma execução, selecione a linha para essa execução.

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

* Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. No menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **desabilitar**.

  ![Desativar a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

* Para excluir permanentemente seu aplicativo lógico, no menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **excluir**. Confirme que você deseja excluir seu aplicativo lógico e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

* [Criar e encaminhar eventos personalizados com o Event Grid](../event-grid/custom-event-quickstart.md)

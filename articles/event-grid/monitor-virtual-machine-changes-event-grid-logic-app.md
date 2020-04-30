---
title: Monitorizar alterações nas máquinas virtuais - Redes de eventos azure & Aplicações lógicas
description: Verifique as alterações nas máquinas virtuais (VMs) utilizando a Grelha de Eventos Azure e aplicações lógicas
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: 045f6d50846092820014ccc7f11a81f1e2234311
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82144086"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Tutorial: Monitorize as mudanças de máquinavirtual utilizando a Grelha de Eventos Azure e aplicações lógicas

Para monitorizar e responder a eventos específicos que ocorram em recursos Do Azure ou recursos de terceiros, pode automatizar e executar tarefas como fluxo de trabalho criando uma [aplicação lógica](../logic-apps/logic-apps-overview.md) que utiliza código mínimo. Estes recursos podem publicar eventos numa grelha de [eventos Azure.](../event-grid/overview.md) Por sua vez, a grelha de eventos envia esses eventos aos subscritores que têm filas, webhooks ou [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos finais. Como subscritor, a sua aplicação lógica pode esperar por esses eventos a partir da grelha de eventos antes de executar fluxos de trabalho automatizados para executar tarefas.

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

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma conta de e-mail de um fornecedor de e-mail suportado por Apps Lógicas para o envio de notificações, tais como Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/).

  Este tutorial usa uma conta de Office 365 Outlook. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

  > [!IMPORTANT]
  > Se pretender utilizar o conector Gmail, apenas as contas de negócio g-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode criar uma aplicação de [cliente da Google para usar para autenticação com o seu conector Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Para mais informações, consulte as políticas de [segurança e privacidade dos dados para os conectores da Google em Aplicações Lógicas Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines) que está sozinha no seu próprio grupo de recursos Azure. Se ainda não o fez, crie uma máquina virtual através do [tutorial Create a VM](../virtual-machines/windows/quick-create-portal.md). Para que a máquina virtual publique eventos, [não precisa de fazer mais nada](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Criar uma aplicação lógica em branco

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. A partir do menu principal do Azure, selecione Criar uma**Aplicação Lógica**de**Integração** > de **Recursos.** > 

   ![Criar uma aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. No âmbito **da Logic App,** forneça informações sobre o seu recurso de aplicações lógicas. Quando terminar, selecione **Criar**.

   ![Indicar os detalhes da aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*lógica-app-nome*> | Forneça um nome único para a sua aplicação lógica. |
   | **Subscrição** | Sim | <*Nome de assinatura Azure*> | Selecione a mesma subscrição Azure para todos os serviços deste tutorial. |
   | **Grupo de recursos** | Sim | <*Grupo de recursos azure*> | O nome do grupo de recursos Azure para a sua aplicação lógica, que pode selecionar para todos os serviços deste tutorial. |
   | **Localização** | Sim | <*Região azul*> | Selecione a mesma região para todos os serviços neste tutorial. |
   |||

1. Depois de o Azure implementar a sua aplicação lógica, o Logic Apps Designer mostra uma página com um vídeo de introdução e gatilhos comumente usados. Percorra o ecrã até passar o vídeo e os acionadores.

1. Em **Modelos**, selecione **Aplicação Lógica em Branco**.

   ![Selecione modelo de aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O Logic Apps Designer mostra agora os [*gatilhos*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que pode usar para iniciar a sua aplicação lógica. Todas as aplicações lógicas têm de iniciar com um acionador, que é desencadeado quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, a Azure Logic Apps cria uma instância de fluxo de trabalho que executa a sua aplicação lógica.

## <a name="add-an-event-grid-trigger"></a>Adicione um gatilho da Grelha de Eventos

Adicione agora o gatilho da Grelha de Eventos, que utiliza para monitorizar o grupo de recursos para a sua máquina virtual.

1. No designer, na caixa de `event grid` pesquisa, introduza como filtro. A partir da lista de gatilhos, selecione o gatilho quando ocorre um evento de **recurso.**

   ![Selecione este gatilho: "Num evento de recursos"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Quando solicitado, inscreva-se na Azure Event Grid com as credenciais da sua conta Azure. Na lista de **Inquilinos,** que mostra o inquilino do Diretório Ativo Azure que está associado à sua assinatura Azure, verifique se o inquilino correto aparece, por exemplo:

   ![Iniciar sessão com as credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se iniciou sessão com uma conta Microsoft pessoal, como @outlook.com ou @hotmail.com, o acionador do Event Grid pode não aparecer corretamente. Como suposições, selecione [Connect with Service Principal](../active-directory/develop/howto-create-service-principal-portal.md), ou autenticacomo membro do Diretório Ativo Azure que está associado à sua subscrição Azure, por exemplo, nome de *utilizador*@emailoutlook.onmicrosoft.com.

1. Agora subscreva a sua aplicação lógica para eventos da editora. Forneça os detalhes sobre a subscrição do seu evento conforme descrito na tabela seguinte, por exemplo:

   ![Fornecer detalhes para a subscrição de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Subscrição** | Sim | <*evento-editor-Azure-assinatura-nome*> | Selecione o nome para a subscrição Azure que está associado à editora do *evento*. Para este tutorial, selecione o nome de subscrição Azure para a sua máquina virtual. |
   | **Tipo de recurso** | Sim | <*evento-editor-azure-tipo de recursos*> | Selecione o tipo de recurso Azure para a editora do evento. Para obter mais informações sobre os tipos de recursos do Azure, consulte os fornecedores e tipos de [recursos do Azure.](../azure-resource-manager/management/resource-providers-and-types.md) Para este tutorial, `Microsoft.Resources.ResourceGroups` selecione o valor para monitorizar os grupos de recursos azure. |
   | **Nome do recurso** |  Sim | <*evento-editor-azure-nome de recurso*> | Selecione o nome de recurso Azure para a editora do evento. Esta lista varia em função do tipo de recurso que selecionou. Para este tutorial, selecione o nome para o grupo de recursos Azure que inclui a sua máquina virtual. |
   | **Artigo do tipo de evento** |  Não | <*tipos de eventos*> | Selecione um ou mais tipos de eventos específicos para filtrar e enviar para a sua grelha de eventos. Por exemplo, pode adicionar opcionalmente estes tipos de eventos para detetar quando os recursos são alterados ou eliminados: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Para obter mais informações, veja estes tópicos: <p><p>- [Esquema de evento sinuoso do evento Azure Event Grid para grupos de recursos](../event-grid/event-schema-resource-groups.md) <br>- [Compreender a filtragem de eventos](../event-grid/event-filtering.md) <br>- [Filtrar eventos para grelha de eventos](../event-grid/how-to-filter-events.md) |
   | Para adicionar propriedades opcionais, selecione **Adicionar novo parâmetro,** e, em seguida, selecione as propriedades que deseja. | Não | {see descriptions} | * **Filtro prefixo**: Para este tutorial, deixe esta propriedade vazia. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Filtro de sufixo**: Para este tutorial, deixe esta propriedade vazia. O comportamento predefinido corresponde a todos os valores. No entanto, pode especificar uma cadeia de sufixo como filtro, por exemplo, uma extensão de nome de ficheiro, quando quiser apenas tipos de ficheiro específicos. <p>* **Nome de subscrição**: Para este tutorial, pode fornecer um nome único para a subscrição do seu evento. |
   |||

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**. Para colapsar e esconder os detalhes de uma ação na sua aplicação lógica, selecione a barra de títulos da ação.

   ![Guardar a aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Quando guardar a aplicação lógica com um acionador do Event Grid, o Azure cria automaticamente uma subscrição de eventos para a aplicação lógica para o recurso selecionado. Assim, quando o recurso publicar um evento na grelha de eventos, esta coloca automaticamente o evento na aplicação lógica. Este evento aciona a aplicação lógica e cria e executa uma instância do fluxo de trabalho definido nos próximos passos.

A aplicação lógica está agora publicada e escuta eventos da grelha de eventos, mas não faz nada até adicionar ações ao fluxo de trabalho.

## <a name="add-a-condition"></a>Adicionar uma condição

Se quiser que a sua aplicação lógica seja executada apenas quando ocorrer `Microsoft.Compute/virtualMachines/write` um evento ou operação específico, adicione uma condição que verifique a operação. Quando esta condição for verdadeira, a aplicação lógica envia-lhe um e-mail com detalhes sobre a máquina virtual atualizada.

1. No Logic App Designer, sob o gatilho da grelha de eventos, selecione **Novo passo**.

   ![Selecione "Novo passo"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Em **'Escolha uma ação**' `condition` na caixa de pesquisa, introduza como filtro. Na lista de ações, selecione a ação **Condição.**

   ![Adicionar uma condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   O Estruturador de Aplicações Lógicas adiciona uma condição em branco ao fluxo de trabalho, incluindo caminhos de ação a seguir com base na condição ser verdadeira ou falsa.

   ![Uma condição vazia aparece](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Mude o nome `If a virtual machine in your resource group has changed`do título da condição para . Na barra de título da condição, selecione o botão elipses (**...**) e selecione **Rename**.

   ![Mude o nome da condição](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Crie uma condição `body` que `data` verifique o `operationName` evento para `Microsoft.Compute/virtualMachines/write` um objeto onde a propriedade é igual à operação. Saiba mais sobre o [esquema de eventos do Event Grid](../event-grid/event-schema.md).

   1. Na primeira linha, em **E**, clique dentro da caixa esquerda. Na lista de conteúdos dinâmicos que aparecem, selecione **Expression**.

      ![Selecione "Expression" para abrir o editor de expressão](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. No editor de expressão, introduza esta expressão, que devolve o nome de funcionamento do gatilho, e selecione **OK:**

      `triggerBody()?['data']['operationName']`

      Por exemplo:

      ![Introduza expressão para extrair o nome da operação](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Na caixa do meio, mantenha o operador **é igual a**.

   1. Na caixa certa, introduza este valor, que é a operação específica que monitoriza:

      `Microsoft.Compute/virtualMachines/write`

   A sua condição acabada agora parece com este exemplo:

   ![Condição completa que compara a operação](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Se mudar da vista de design para a vista de código e voltar para a vista de design, a expressão que especificou na condição resolve-se com o **token data.operationName:**

   ![Fichas resolvidas em condições](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Guarde a aplicação lógica.

## <a name="send-email-notifications"></a>Enviar notificações por e-mail

Adicione agora uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) para que possa receber um e-mail quando a condição especificada for verdadeira.

1. Na condição se a caixa **verdadeira,** **selecione Adicionar uma ação**.

   ![Adicionar ação quando a condição for verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Em **'Escolha uma ação**' `send an email` na caixa de pesquisa, introduza como filtro. Com base no seu fornecedor de e-mail, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar e-mail" para o conector. Por exemplo:

   * Numa conta escolar ou profissional do Azure, selecione o conector Office 365 Outlook.

   * Em contas Microsoft pessoais, selecione o conector Outlook.com.

   * Em contas do Gmail, selecione o conector Gmail.

   Este tutorial continua com o conector Office 365 Outlook. Se utilizar um fornecedor diferente, os passos permanecem os mesmos, mas o seu UI pode parecer ligeiramente diferente.

   ![Selecionar a ação "enviar e-mail"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Se ainda não tiver uma ligação para o seu fornecedor de e-mail, inicie sessão na sua conta de e-mail quando lhe for pedido para efetuar a autenticação.

1. Mude o nome da ação de envio de e-mail para este título:`Send email when virtual machine updated`

1. Fornecer informações sobre o e-mail conforme especificado no seguinte quadro:

   ![Fornecer informações sobre ação de e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar a saída dos passos anteriores no seu fluxo de trabalho, clique dentro de uma caixa de edição para que a lista de conteúdos dinâmicos apareça, ou **selecione Adicionar conteúdo dinâmico**. Para mais resultados, selecione **mais** para cada secção da lista. Para fechar a lista de conteúdos dinâmicos, **selecione Adicionar conteúdo dinâmico** novamente.

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Para** | Sim | <*domínio\@destinatário*> | Introduza o endereço de e-mail do destinatário. Para fins de teste, pode utilizar o seu próprio endereço de e-mail. |
   | **Assunto** | Sim | `Resource updated:` **Assunto** | Introduza o conteúdo para o assunto do e-mail. Para este tutorial, introduza o texto especificado e selecione o campo **Assunto** do evento. Aqui, o assunto do e-mail inclui o nome do recurso atualizado (máquina virtual). |
   | **Corpo** | Sim | `Resource:`**Tópico** <p>`Event type:` **Tipo de Evento**<p>`Event ID:`**ID**<p>`Time:`**Hora do Evento** | Introduza o conteúdo para o corpo do e-mail. Para este tutorial, introduza o texto especificado e selecione os campos **Tópico**, Tipo de **Evento,** **ID**e Hora do **Evento** para que o seu e-mail inclua o recurso que disparou o evento, tipo de evento, carimbo de tempo do evento e ID do evento para a atualização. Para este tutorial, o recurso é o grupo de recursos Azure selecionado no gatilho. <p>Para adicionar linhas em branco ao conteúdo, prima Shift + Enter. |
   ||||

   > [!NOTE]
   > Se selecionar um campo que representa uma matriz, o estruturador adiciona automaticamente um ciclo **For each** em torno da ação que referencia essa matriz. Desta forma, a sua aplicação lógica realiza essa ação em cada item da matriz.

   Agora, a ação de e-mail pode ter o seguinte aspeto neste exemplo:

   ![Selecionar dados a incluir no e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Por fim, a aplicação lógica concluída pode ter o seguinte aspeto neste exemplo:

   ![Aplicação lógica concluída](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Guarde a aplicação lógica. Para colapsar e esconder os detalhes de cada ação na sua aplicação lógica, selecione a barra de título seletiva da ação.

   A aplicação lógica está agora publicada, mas aguarda as alterações à máquina virtual antes de efetuar alguma ação. Para testar a sua aplicação lógica agora, avance para a secção seguinte.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho da aplicação lógica

1. Para verificar se a aplicação lógica está a obter os eventos especificados, atualize a máquina virtual.

   Por exemplo, pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Após alguns instantes, deve obter um e-mail. Por exemplo:

   ![E-mail sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Para rever as execuções e desencadear o histórico para a sua aplicação lógica, no menu de aplicações lógicas, selecione **Visão Geral**. Para ver mais detalhes sobre uma corrida, selecione a linha para essa corrida.

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

* Para parar de executar a sua aplicação lógica sem eliminar o seu trabalho, desative-a. No menu de aplicativos lógicos, selecione **Visão Geral**. Na barra de ferramentas, selecione **Desativar**.

  ![Desativar a sua aplicação lógica](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se não vir o menu da aplicação lógica, experimente regressar ao dashboard do Azure e reabra-a.

* Para eliminar permanentemente a sua aplicação lógica, no menu de aplicações lógicas, selecione **Visão Geral**. Na barra de ferramentas, selecione **Delete**. Confirme que pretende eliminar a sua aplicação lógica e **selecione Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar e encaminhar eventos personalizados com o Event Grid](../event-grid/custom-event-quickstart.md)

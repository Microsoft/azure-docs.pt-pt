---
title: Fluxos de exportação de Power Automate para Azure Logic Apps
description: Migrar flui de Power Automate para Azure Logic Apps exportando como modelos de Gestor de Recursos Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428888"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implementá-los no Azure Logic Apps

Para alargar e expandir as capacidades do seu fluxo, pode migrar esse fluxo de [Power Automate](https://flow.microsoft.com) para [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Você pode exportar o seu fluxo como um modelo de Gestor de Recursos Azure para uma aplicação lógica, implementar esse modelo de aplicação lógica para um grupo de recursos Azure, e, em seguida, abrir essa aplicação lógica no Logic App Designer.

> [!NOTE]
> Nem todos os conectores Power Automate estão disponíveis em Aplicações Lógicas Azure. Pode importar fluxos que tenham [conectores equivalentes](../connectors/apis-list.md) em Aplicações Lógicas Azure. Por exemplo, o gatilho do botão, o conector de homologação de aprovação e o conector de notificação são específicos do Power Automate.
>
> Os fluxos baseados em OpenAPI exportados da Power Automate não são atualmente suportados para implementação como modelos de aplicações lógicas. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O fluxo que pretende exportar da Power Automate

## <a name="export-a-flow"></a>Exportar um fluxo

1. Inscreva-se no [Power Automate](https://flow.microsoft.com)e selecione **Os meus fluxos**. Encontre e selecione o seu fluxo. Na barra de ferramentas, selecione o botão elipses (**...**). Selecione modelo de aplicativos de lógica de **exportação** > **(.json)**.

   ![Exportar fluxo](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Guarde o seu modelo para o local que deseja.

Para mais informações, consulte [Grow up até Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implementar modelo utilizando o portal Azure

1. Assine no [portal Azure](https://portal.azure.com) com a sua conta Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, introduza "implantação do modelo". Selecione **a implementação do modelo (implante utilizando modelos personalizados)** e, em seguida, selecione **Criar**.

   ![Selecione "Implementação do modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Sob **implementação personalizada,** selecione **Construa o seu próprio modelo no editor**.

   ![Selecione "Construa o seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. A partir da barra de ferramentas **de modelo Editar,** selecione **ficheiro load**. Encontre e selecione o modelo JSON que exportou da Power Automate e selecione **Open**.

   ![Selecione "Ficheiro de carga"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Depois de o editor mostrar o JSON, parâmetros e recursos no seu modelo, selecione **Save**.
  
   ![Guardar o modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora especifique estes parâmetros de entrada para o modelo:

   * Assinatura Azure para usar para faturação
   * Grupo de recursos do Azure
   * Localização para o grupo de recursos Azure
   * Nome para o recurso de aplicação lógica
   * Localização para o recurso lógico da aplicação, se diferente do grupo de recursos Azure
   * O nome de quaisquer ligações previamente criadas que a aplicação lógica pode reutilizar

      Se estiver a criar a sua primeira aplicação lógica, todas as ligações são criadas como novas, para que possa aceitar os nomes padrão. Caso contrário, pode especificar os nomes para ligações previamente criadas, que pode utilizar em várias aplicações lógicas.

   Depois de fornecer esta informação para o modelo, reveja e concorde com os Termos e Condições do Mercado Azure para criar os recursos e faturação necessários da sua subscrição Azure em conformidade, e, em seguida, selecione **Comprar**.
  
   ![Especificar parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   O Azure implementa o seu modelo como uma aplicação lógica para o seu grupo de recursos especificado. Todas as aplicações lógicas que migra da Power Automate são implantadas num estado desativado.

1. Antes de ativar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. Abra a aplicação lógica que criou. No menu da aplicação lógica, selecione **Logic app designer**.

      Cada ligação que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada passo que requer uma ligação autorizada, expanda esse passo e selecione **Adicionar novo**.

      ![Adicione nova ligação](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Inscreva-se em cada serviço ou forneça as credenciais necessárias para autorizar a ligação.

1. Guarde a aplicação lógica. Quando estiver pronto para ativar a sua aplicação lógica, no menu da aplicação lógica, selecione **Visão Geral**, e depois selecione **Enable**.

   ![Ativar app lógica](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de que desativa ou elimina o fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implementar modelo usando o Estúdio Visual

Se criou o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicações lógicas, pode implementar o seu modelo exportado do Visual Studio para as Aplicações Lógicas Azure.

1. No Estúdio Visual, abra o ficheiro de modelo que exportou da Power Automate.

1. No Estúdio Visual, crie um projeto do Grupo de Recursos Azure e selecione o modelo de **Aplicação Lógica** seguindo os passos em [Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho com aplicações lógicas Azure - Estúdio Visual,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)por exemplo:

   ![Criar projeto do Grupo de Recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Do Solution Explorer, abra o ficheiro **LogicApp.json,** caso o ficheiro ainda não esteja aberto.

1. Copie os conteúdos do modelo exportado e sobreescreva os conteúdos no ficheiro **LogicApp.json.**

1. Antes de implementar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. Abra o menu de atalho **LogicApp.json** e, em seguida, selecione **Open With Logic App Designer**.

      ![Modelo aberto com designer de aplicações lógica](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione o grupo de subscrição e recursos Azure que pretende utilizar para implementar a sua aplicação lógica.

      ![Selecione grupo de subscrição e recursos Azure](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Depois da sua aplicação lógica aparecer no designer, quaisquer ligações que exijam autorização mostram ícones de aviso:

      ![Conexões com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada passo que requer uma ligação autorizada, expanda esse passo e selecione **Adicionar novo**.

      ![Adicione nova ligação](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Inscreva-se em cada serviço ou forneça as credenciais necessárias para autorizar a ligação.

   1. Guarde a sua solução antes de implementar a aplicação lógica.

1. No Solution Explorer, abra o menu de atalho do projeto e selecione **Implementar** > **Novo**. Se tal lhe for pedido, inicie sessão com a sua conta do Azure.

1. Quando solicitado, confirme a subscrição do Azure, o grupo de recursos Azure e quaisquer outras definições que pretenda utilizar para a implementação, como um ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md) para utilizar para passar os valores dos parâmetros do modelo e, em seguida, selecione **Deploy**.

   ![Confirmar as definições de implementação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se aparecer a caixa de parâmetros de **edição,** forneça o nome para o seu recurso de aplicação lógica em Azure e selecione **Save**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Quando a implementação é iniciada, o estado de implementação da sua aplicação é apresentado na janela **Saída** do Visual Studio. Se o estado não for apresentado, abra a lista **Mostrar saída de** e selecione o seu grupo de recursos do Azure. Por exemplo:

   ![Janela de saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se alguma ligação na sua aplicação lógica necessitar de entrada de si, uma janela PowerShell abre-se em segundo plano e solicita quaisquer senhas ou chaves secretas necessárias. Após introduzir estas informações, a implementação continua.

   ![Ligações autenticadas](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após a implementação termina, a sua aplicação lógica é publicada mas não é ativada no portal Azure.

1. Quando estiver pronto para ativar a sua aplicação lógica no portal Azure, encontre e abra a sua aplicação lógica no Logic App Designer. No menu da aplicação lógica, selecione **Visão Geral**, e, em seguida, selecione **Enable**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de que desativa ou elimina o fluxo original.

Para obter mais informações sobre estes passos de implementação, consulte [Quickstart: Criar tarefas automatizadas, processos e fluxos de trabalho com aplicações lógicas azure - Estúdio Visual](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [Conectores para Aplicações Lógicas Azure](../connectors/apis-list.md)
* Saiba mais sobre [as aplicações da Lógica Azure](../logic-apps/logic-apps-overview.md)

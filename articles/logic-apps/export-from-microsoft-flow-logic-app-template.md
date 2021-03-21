---
title: Exportação flui de Power Automamate para Azure Logic Apps
description: Migrar fluxos de Power Automamate para Azure Logic Apps exportando como modelos de Gestor de Recursos Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699101"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implementá-los no Azure Logic Apps

Para alargar e expandir as capacidades do seu fluxo, pode migrar esse fluxo de [Power Automamate](https://flow.microsoft.com) para [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Você pode exportar o seu fluxo como um modelo de Gestor de Recursos Azure para uma aplicação lógica, implementar esse modelo de aplicação lógica para um grupo de recursos Azure e, em seguida, abrir essa aplicação lógica no Logic App Designer.

> [!IMPORTANT]
> As aplicações de exportação para a Logic não estão disponíveis para os fluxos Power Automamate criados a seguir a agosto de 2020. Em outubro de 2020, a Power Automamate padronizou a criação de novos fluxos no [protocolo OpenAPI 2.0](https://swagger.io/specification/v2/). Novos fluxos baseados neste protocolo são incompatíveis com os fluxos de trabalho da Logic Apps, pelo que exportar estes fluxos para a Logic Apps foi desativado. Em vez disso, deve criar manualmente [aplicações lógicas](quickstart-create-first-logic-app-workflow.md) para estes fluxos.

Nem todos os conectores Power Automate estão disponíveis em Azure Logic Apps. Só pode migrar fluxos Power Automamate que tenham os conectores equivalentes em Azure Logic Apps. Por exemplo, o gatilho do botão, o conector de aprovação e o conector de notificação são específicos do Automatismo de Energia. 

* Para descobrir quais os conectores Power Automamate que não têm equivalentes de Aplicações Lógicas, consulte [conectores Power Automamate](/connectors/connector-reference/connector-reference-powerautomate-connectors).

* Para descobrir quais os conectores de Aplicações Lógicas que não possuem equivalentes power automate, consulte [conectores de Aplicações Lógicas](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O fluxo que pretende exportar da Power Automamate

## <a name="export-your-flow"></a>Exporte o seu fluxo

1. Inscreva-se no [Automatismo](https://flow.microsoft.com)de Potência e selecione **Os meus fluxos**. Encontre e selecione o seu fluxo. Na barra de ferramentas, selecione as elipses **(...**) botão > modelo **de Apps lógicas de exportação**  >  **(.json)**.

   ![Fluxo de exportação da Power Automate](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Guarde o ficheiro .json do seu modelo para o local que pretende.

Para mais informações, consulte [Grow up to Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implementar o modelo utilizando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

1. Na página inicial do Azure, na caixa de pesquisa, insira `custom template` . A partir dos resultados, **selecione Implementar um modelo personalizado**  >  **Criar**.

   ![Localizar e selecionar "Implementação do modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implementação personalizada,** selecione **Construa o seu próprio modelo no editor**.

   ![Selecione "Construa o seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na barra de ferramentas **do modelo editar,** selecione **Ficheiro de carga**.

   ![Selecione "Carregar ficheiro"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Navegue para o local onde guardou o ficheiro de modelo JSON que exportou da Power Automamate. Selecione o ficheiro do modelo > **Abrir**.

1. Depois de o editor mostrar o JSON, parâmetros e recursos no seu modelo, **selecione Save**.

   ![Guardar o modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora forneça mais informações sobre a sua aplicação lógica.

   1. Selecione ou especifique os valores do parâmetro de entrada para o seu modelo.

      | Propriedade | Descrição |
      |----------|-------------|
      | **Subscrição** | A assinatura Azure para usar para faturação |
      | **Grupo de recursos** | O grupo de recursos Azure para usar para a sua aplicação lógica. Pode utilizar um grupo existente ou criar um novo grupo. |
      | **Localização** | A região de Azure para usar se criar um novo grupo de recursos |
      | **Nome de aplicativo lógica** | O nome a usar para o seu recurso de aplicação lógica |
      | **Localização de aplicativos lógicos** | A região Azure onde pretende criar o recurso de aplicação lógica, se diferente do grupo de recursos Azure |
      | <*nome de conexão*> | Um ou vários nomes para quaisquer ligações previamente criadas que a aplicação lógica pode reutilizar <p><p>**Nota:** Se esta aplicação lógica for a sua primeira, todas as ligações são criadas como novas, para que possa aceitar os nomes predefinidos. Caso contrário, pode especificar os nomes para ligações previamente criadas, que pode utilizar em várias aplicações lógicas. |
      |||

      Por exemplo:

      ![Especifique os parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Quando terminar, reveja os **Termos e Condições** sobre a criação dos recursos Azure necessários e faturar a sua subscrição Azure em conformidade.

   1. Quando estiver pronto, **selecione concordo com os termos e condições acima**  >  **da Compra.**

      O Azure implementa o seu modelo como uma aplicação lógica para o seu grupo de recursos especificado.

1. Todas as aplicações lógicas que migrar do Power Automamate são implementadas num estado desativado. Antes de ativar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. No portal Azure, abra a app lógica que criou. No menu da aplicação lógica, selecione **Logic app designer**.

      Cada ligação que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada passo que requer uma ligação autorizada, expanda esse passo e **selecione Adicione novo**.

      ![Screenshot que mostra o botão "Adicionar novo" selecionado na janela Outlook "Connections".](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Inscreva-se em cada serviço ou forneça as credenciais necessárias para autorizar a ligação.

   1. Depois de atualizar as suas ligações, na barra de ferramentas do designer, selecione **Save**.

1. Quando estiver pronto para ativar a sua aplicação lógica, no menu da aplicação lógica, selecione **Overview** e, em seguida, selecione **Enable**.

   ![Ativar aplicativo de lógica](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou eliminar o seu fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implementar o modelo usando o Estúdio Visual

Se configurar o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicações lógicas, pode implementar o seu modelo exportado para Azure Logic Apps utilizando o Visual Studio.

1. No Visual Studio, navegue e abra o ficheiro .json para o modelo de aplicação lógica que exportou a partir do Power Automamate.

1. No Visual Studio, crie um projeto **do Grupo de Recursos Azure** que utiliza o modelo de **Aplicação Lógica** seguindo os passos em [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados com Apps Azure Logic - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Este exemplo cria uma solução Visual Studio chamada "ImportedLogicApp".

   ![Criar projeto do Grupo de Recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Depois de a solução ser criada, no Solution Explorer, abra a **LogicApp.jsem** ficheiro, caso o ficheiro ainda não esteja aberto.

1. Copie o conteúdo do modelo exportado e substitua o conteúdo do **LogicApp.jsem** ficheiro.

1. Antes de implementar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. Abra a **LogicApp.jsno** menu de atalho e, em seguida, selecione Open With Logic **App Designer**.

      ![Modelo aberto com designer de aplicativos de lógica](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione o grupo de subscrição e recursos Azure que pretende utilizar para implementar a sua aplicação lógica.

      ![Selecione a subscrição e grupo de recursos da Azure](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Após a sua aplicação lógica aparecer no designer, quaisquer ligações que exijam autorização mostram ícones de aviso:

      ![Ligações com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada passo que requer uma ligação autorizada, expanda esse passo e **selecione Adicione novo**.

      ![Adicionar nova ligação](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Inscreva-se em cada serviço ou forneça as credenciais necessárias para autorizar a ligação.

   1. Guarde a sua solução antes de implementar a aplicação lógica.

1. No Solution Explorer, abra o menu de atalho do projeto e selecione **Implementar**  >  **Novo**. Se tal lhe for pedido, inicie sessão com a sua conta do Azure.

1. Quando solicitado, confirme a subscrição do Azure, o grupo de recursos Azure e quaisquer outras definições que pretenda utilizar para implantação, como um [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md) para usar para passar valores de parâmetros do modelo e, em seguida, selecione **Implementar**.

   ![Confirmar definições de implantação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se aparecer a caixa **de parâmetros de edição,** forneça o nome do seu recurso de aplicação lógica em Azure e selecione **Save**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Quando a implementação é iniciada, o estado de implementação da sua aplicação é apresentado na janela **Saída** do Visual Studio. Se o estado não for apresentado, abra a lista **Mostrar saída de** e selecione o seu grupo de recursos do Azure. Por exemplo:

   ![Janela de saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se alguma ligação na sua aplicação lógica precisar de entrada de si, uma janela PowerShell abre em segundo plano e pede quaisquer senhas ou chaves secretas necessárias. Após introduzir estas informações, a implementação continua.

   ![Autenticar ligações](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após a implementação, a sua aplicação lógica é publicada mas não é ativada no portal Azure.

1. Quando estiver pronto para ativar a sua aplicação lógica no portal Azure, encontre e abra a sua aplicação lógica no Logic App Designer. No menu da aplicação lógica, selecione **Overview** e, em seguida, selecione **Enable**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou eliminar o seu fluxo original.

Para obter mais informações sobre estes passos de implementação, consulte [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados com Apps Azure Logic - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [Conectores para Apps Azure Logic](../connectors/apis-list.md)
* Saiba mais sobre [a Azure Logic Apps](../logic-apps/logic-apps-overview.md)

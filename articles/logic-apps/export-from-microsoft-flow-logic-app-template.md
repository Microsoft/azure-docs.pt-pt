---
title: Exportar fluxos da energia automatizada para o aplicativo lógico do Azure
description: Migrar fluxos da Power mate para o aplicativo lógico do Azure exportando-os como modelos de Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 4512fd8a236c888937eb60a75d01cfb0ef2c1913
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792638"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implementá-los no Azure Logic Apps

Para estender e expandir os recursos do seu fluxo, você pode migrar esse fluxo de [Power mate](https://flow.microsoft.com) para [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md). Você pode exportar seu fluxo como um modelo de Azure Resource Manager para um aplicativo lógico, implantar esse modelo de aplicativo lógico em um grupo de recursos do Azure e, em seguida, abrir esse aplicativo lógico no designer de aplicativo lógico.

> [!NOTE]
> Nem todos os conectores de energia automatizada estão disponíveis nos aplicativos lógicos do Azure. Você pode importar fluxos que têm [conectores equivalentes](../connectors/apis-list.md) em aplicativos lógicos do Azure. Por exemplo, o gatilho de botão, o conector de aprovação e o conector de notificação são específicos para a automatização de energia.
>
> Os fluxos com base em OpenAPI exportados da energia automatizada atualmente não têm suporte para implantação como modelos de aplicativos lógicos. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O fluxo que você deseja exportar da energia automatizada

## <a name="export-a-flow"></a>Exportar um fluxo

1. Entre para [automatizar a energia](https://flow.microsoft.com)e selecione **meus fluxos**. Localize e selecione seu fluxo. Na barra de ferramentas, selecione o botão de reticências ( **...** ). Selecione **exportar** > **modelo de aplicativos lógicos (. JSON)** .

   ![Fluxo de exportação](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salve o modelo no local desejado.

Para obter mais informações, consulte [aumentar até os aplicativos lógicos do Azure](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implantar modelo usando o portal do Azure

1. Entre na [portal do Azure](https://portal.azure.com) com sua conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, insira "implantação de modelo". Selecione **implantação de modelo (implantar usando modelos personalizados)** e, em seguida, selecione **criar**.

   ![Selecione "Implantação de modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implantação personalizada**, selecione **criar seu próprio modelo no editor**.

   ![Selecione "criar seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na barra de ferramentas **Editar modelo** , selecione **carregar arquivo**. Localize e selecione o modelo JSON que você exportou da energia automatizada e selecione **abrir**.

   ![Selecione "carregar arquivo"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Depois que o editor mostrar o JSON, os parâmetros e os recursos em seu modelo, selecione **salvar**.
  
   ![Guardar modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora, especifique estes parâmetros de entrada para o modelo:

   * Assinatura do Azure a ser usada para cobrança
   * Grupo de recursos do Azure
   * Local do grupo de recursos do Azure
   * Nome do recurso do aplicativo lógico
   * Local do recurso de aplicativo lógico, se for diferente do grupo de recursos do Azure
   * O nome de todas as conexões criadas anteriormente que o aplicativo lógico pode reutilizar

      Se você estiver criando seu primeiro aplicativo lógico, todas as conexões serão criadas como novas, para que você possa aceitar os nomes padrão. Caso contrário, você pode especificar os nomes para as conexões criadas anteriormente, que podem ser usadas em vários aplicativos lógicos.

   Depois de fornecer essas informações para o modelo, examine e concorde com os termos e condições do Azure Marketplace para criar os recursos do Azure necessários e cobrança de sua assinatura do Azure de acordo e, em seguida, selecione **comprar**.
  
   ![Especificar parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   O Azure implanta seu modelo como um aplicativo lógico para o grupo de recursos especificado. Todos os aplicativos lógicos que você migra da energia automatizada são implantados em um estado desabilitado.

1. Antes de ativar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. Abra o aplicativo lógico que você criou. No menu do aplicativo lógico, selecione **Designer de aplicativo lógico**.

      Cada conexão que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

1. Guarde a aplicação lógica. Quando você estiver pronto para ativar seu aplicativo lógico, no menu do aplicativo lógico, selecione **visão geral**e, em seguida, selecione **habilitar**.

   ![Habilitar aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implantar modelo usando o Visual Studio

Se você tiver configurado o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para criar aplicativos lógicos, poderá implantar o modelo exportado do Visual Studio para os aplicativos lógicos do Azure.

1. No Visual Studio, abra o arquivo de modelo que você exportou da energia automatizada.

1. No Visual Studio, crie um projeto de grupo de recursos do Azure e selecione o modelo **aplicativo lógico** seguindo as etapas em [início rápido: criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), por exemplo:

   ![Criar projeto do Grupo de Recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Em Gerenciador de Soluções, abra o arquivo **LogicApp. JSON** , se o arquivo ainda não estiver aberto.

1. Copie o conteúdo do modelo exportado e substitua o conteúdo no arquivo **LogicApp. JSON** .

1. Antes de implantar seu aplicativo lógico, autorize as novas conexões seguindo estas etapas:

   1. Abra o menu de atalho **LogicApp. JSON** e, em seguida, selecione **abrir com o designer de aplicativo lógico**.

      ![Abrir modelo com o designer de aplicativo lógico](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se solicitado, selecione a assinatura do Azure e o grupo de recursos que você deseja usar para implantar seu aplicativo lógico.

      ![Selecione a assinatura do Azure e o grupo de recursos](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Depois que seu aplicativo lógico aparece no designer, todas as conexões que exigem autorização mostram ícones de aviso:

      ![Conexões com ícones de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Para cada etapa que requer uma conexão autorizada, expanda essa etapa e selecione **Adicionar nova**.

      ![Adicionar nova conexão](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Entre em cada serviço ou forneça as credenciais necessárias para autorizar a conexão.

   1. Salve sua solução antes de implantar o aplicativo lógico.

1. No Gerenciador de Soluções, abra o menu de atalho projeto e selecione **implantar** > **novo**. Se tal lhe for pedido, inicie sessão com a sua conta do Azure.

1. Quando solicitado, confirme a assinatura do Azure, o grupo de recursos do Azure e todas as outras configurações que você deseja usar para a implantação, como um [arquivo de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) a ser usado para passar os valores de parâmetro de modelo e, em seguida, selecione **implantar**.

   ![Confirmar configurações de implantação](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça o nome para o recurso de aplicativo lógico no Azure e selecione **salvar**.  

   ![Editar parâmetros de implantação](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Quando a implementação é iniciada, o estado de implementação da sua aplicação é apresentado na janela **Saída** do Visual Studio. Se o estado não for apresentado, abra a lista **Mostrar saída de** e selecione o seu grupo de recursos do Azure. Por exemplo:

   ![Janela de saída](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se qualquer conexão em seu aplicativo lógico precisar de entrada, uma janela do PowerShell será aberta em segundo plano e solicitará as senhas ou chaves secretas necessárias. Após introduzir estas informações, a implementação continua.

   ![Autenticar conexões](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Após a conclusão da implantação, seu aplicativo lógico é publicado, mas não é ativado no portal do Azure.

1. Quando estiver pronto para ativar seu aplicativo lógico no portal do Azure, localize e abra seu aplicativo lógico no designer do aplicativo lógico. No menu do aplicativo lógico, selecione **visão geral**e, em seguida, selecione **habilitar**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou excluir o fluxo original.

Para obter mais informações sobre essas etapas de implantação, consulte [início rápido: criar tarefas, processos e fluxos de trabalho automatizados com aplicativos lógicos do Azure – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
* Saiba mais sobre os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)

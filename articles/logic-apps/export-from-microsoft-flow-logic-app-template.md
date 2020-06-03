---
title: Exportação flui de Power Automamate para Azure Logic Apps
description: Migrar fluxos de Power Automamate para Azure Logic Apps exportando como modelos de Gestor de Recursos Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: b30a2ae8d90a193e23229dc6743c7e92ebf83b52
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298725"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportar fluxos do Power Automate e implementá-los no Azure Logic Apps

Para alargar e expandir as capacidades do seu fluxo, pode migrar esse fluxo de [Power Automamate](https://flow.microsoft.com) para [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Você pode exportar o seu fluxo como um modelo de Gestor de Recursos Azure para uma aplicação lógica, implementar esse modelo de aplicação lógica para um grupo de recursos Azure e, em seguida, abrir essa aplicação lógica no Logic App Designer.

> [!NOTE]
> Nem todos os conectores Power Automate estão disponíveis em Azure Logic Apps. Só pode migrar fluxos Power Automamate que tenham os conectores equivalentes em Azure Logic Apps. Por exemplo, o gatilho do botão, o conector de aprovação e o conector de notificação são específicos do Automatismo de Energia. Atualmente, os fluxos baseados no OpenAPI no Power Automamate não são suportados para exportação e implementação como modelos de aplicações lógicas.
>
> * Para descobrir quais os conectores Power Automamate que não têm equivalentes de Aplicações Lógicas, consulte [conectores Power Automamate](https://docs.microsoft.com/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Para descobrir quais os conectores de Aplicações Lógicas que não possuem equivalentes power automate, consulte [conectores de Aplicações Lógicas](https://docs.microsoft.com/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O fluxo que pretende exportar da Power Automamate

## <a name="export-a-flow"></a>Exportar um fluxo

1. Inscreva-se no [Automatismo](https://flow.microsoft.com)de Potência e selecione **Os meus fluxos**. Encontre e selecione o seu fluxo. Na barra de ferramentas, selecione o botão elipses **(...**) . Selecione modelo de Apps lógicas **de exportação**  >  **(.json)**.

   ![Exportar fluxo](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Guarde o seu modelo para o local que pretende.

Para mais informações, consulte [Grow up to Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Implementar o modelo utilizando o portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, introduza "implantação do modelo". Selecione **a implementação do modelo (implementar usando modelos personalizados)** e, em seguida, selecione **Criar**.

   ![Selecione "Implantação do modelo"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Em **implementação personalizada,** selecione **Construa o seu próprio modelo no editor**.

   ![Selecione "Construa o seu próprio modelo no editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. A partir da barra de ferramentas **do modelo editar,** selecione **Ficheiro de carga**. Encontre e selecione o modelo JSON que exportou a partir do Power Automamate e selecione **Open**.

   ![Selecione "Carregar ficheiro"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Depois de o editor mostrar o JSON, parâmetros e recursos no seu modelo, **selecione Save**.
  
   ![Guardar o modelo](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Agora especifique estes parâmetros de entrada para o modelo:

   * Assinatura Azure para usar para faturação
   * Grupo de recursos do Azure
   * Localização para o grupo de recursos Azure
   * Nome para o recurso de aplicação lógica
   * Localização para o recurso de aplicação lógica, se diferente do grupo de recursos Azure
   * O nome de quaisquer ligações previamente criadas que a aplicação lógica pode reutilizar

      Se estiver a criar a sua primeira aplicação lógica, todas as ligações são criadas como novas, para que possa aceitar os nomes predefinidos. Caso contrário, pode especificar os nomes para ligações previamente criadas, que pode utilizar em várias aplicações lógicas.

   Depois de fornecer estas informações para o modelo, reveja e concorde com os Termos e Condições do Mercado Azure para criar os recursos Azure necessários e faturar a sua subscrição Azure em conformidade, e, em seguida, selecione **Comprar**.
  
   ![Especifique os parâmetros de entrada para o modelo](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   O Azure implementa o seu modelo como uma aplicação lógica para o seu grupo de recursos especificado. Todas as aplicações lógicas que migrar do Power Automamate são implementadas num estado desativado.

1. Antes de ativar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. Abra a aplicação lógica que criou. No menu da aplicação lógica, selecione **Logic app designer**.

      Cada ligação que requer autorização mostra um ícone de aviso:

      ![Ícone de aviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Para cada passo que requer uma ligação autorizada, expanda esse passo e **selecione Adicione novo**.

      ![Adicionar nova ligação](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Inscreva-se em cada serviço ou forneça as credenciais necessárias para autorizar a ligação.

1. Guarde a aplicação lógica. Quando estiver pronto para ativar a sua aplicação lógica, no menu da aplicação lógica, selecione **Overview**e, em seguida, selecione **Enable**.

   ![Ativar aplicativo de lógica](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou eliminar o seu fluxo original.

## <a name="deploy-template-by-using-visual-studio"></a>Implementar o modelo usando o Estúdio Visual

Se configurar o Visual Studio com os [pré-requisitos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) para a criação de aplicações lógicas, pode implementar o seu modelo exportado de Visual Studio para Azure Logic Apps.

1. No Visual Studio, abra o ficheiro de modelo que exportou da Power Automamate.

1. No Visual Studio, crie um projeto do Grupo de Recursos Azure e selecione o modelo **de Aplicação Lógica** seguindo os passos em [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados com Apps Azure Logic - Visual Studio,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)por exemplo:

   ![Criar projeto do Grupo de Recursos do Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. A partir do Solution Explorer, abra o ficheiro **LogicApp.json,** se o ficheiro ainda não estiver aberto.

1. Copie o conteúdo do modelo exportado e substitua o conteúdo no ficheiro **LogicApp.json.**

1. Antes de implementar a sua aplicação lógica, autorize quaisquer novas ligações seguindo estes passos:

   1. Abra o menu de atalho **LogicApp.json** e, em seguida, **selecione Open With Logic App Designer**.

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

1. Quando estiver pronto para ativar a sua aplicação lógica no portal Azure, encontre e abra a sua aplicação lógica no Logic App Designer. No menu da aplicação lógica, selecione **Overview**e, em seguida, selecione **Enable**.

1. Para evitar a execução de fluxos de trabalho duplicados, certifique-se de desativar ou eliminar o seu fluxo original.

Para obter mais informações sobre estes passos de implementação, consulte [Quickstart: Criar tarefas, processos e fluxos de trabalho automatizados com Apps Azure Logic - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [Conectores para Apps Azure Logic](../connectors/apis-list.md)
* Saiba mais sobre [a Azure Logic Apps](../logic-apps/logic-apps-overview.md)

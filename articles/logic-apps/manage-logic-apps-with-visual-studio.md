---
title: Editar e gerenciar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
description: Editar, atualizar, gerenciar, adicionar ao controle do código-fonte e implantar aplicativos lógicos usando o Visual Studio com o Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: ed48082c52a5b4f79fd2030303dbe2bb7bedafe6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456599"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerenciar aplicativos lógicos com o Visual Studio

Embora seja possível criar, editar, gerenciar e implantar aplicativos lógicos no [portal do Azure](https://portal.azure.com), você também pode usar o Visual Studio quando quiser adicionar seus aplicativos lógicos ao controle do código-fonte, publicar versões diferentes e criar [Azure Resource Manager](../azure-resource-manager/management/overview.md) modelos para vários ambientes de implantação. Com o Visual Studio Cloud Explorer, você pode encontrar e gerenciar seus aplicativos lógicos junto com outros recursos do Azure. Por exemplo, você pode abrir, baixar, editar, executar, exibir histórico de execução, desabilitar e habilitar aplicativos lógicos que já estão implantados no portal do Azure. Se você não estiver familiarizado com o trabalho com os aplicativos lógicos do Azure no Visual Studio, saiba [como criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Implantar ou publicar um aplicativo lógico do Visual Studio substitui a versão desse aplicativo no portal do Azure. Portanto, se você fizer alterações no portal do Azure que deseja manter, certifique-se de [atualizar o aplicativo lógico no Visual Studio](#refresh) do portal do Azure antes da próxima implantação ou publicação a partir do Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio 2019, 2017 ou 2015-Community Edition ou superior](https://aka.ms/download-visual-studio). Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, certifique-se de selecionar a carga de trabalho de **desenvolvimento do Azure** .
    > Para obter mais informações, consulte [gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Para instalar o Cloud Explorer para Visual Studio 2015, [Baixe o Cloud Explorer do Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Para obter mais informações, consulte [gerenciar recursos associados às suas contas do Azure no Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [SDK do Azure (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/)

  * [O Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * As ferramentas mais recentes dos aplicativos lógicos do Azure para a extensão do Visual Studio para a versão que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

* Acesso à Web ao usar o designer de aplicativos lógicos inseridos

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica. Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador verifica se a instância do CRM tem propriedades personalizadas e predefinidas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Encontre seus aplicativos lógicos

No Visual Studio, você pode encontrar todos os aplicativos lógicos associados à sua assinatura do Azure e implantados no portal do Azure usando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Exibir** , selecione **Cloud Explorer**.

1. No Cloud Explorer, selecione **Gerenciamento de conta**. Selecione a assinatura do Azure associada aos seus aplicativos lógicos e, em seguida, selecione **aplicar**. Por exemplo:

   ![Selecione "gerenciamento de contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Com base em se você está pesquisando por **grupos de recursos** ou **tipos de recursos**, siga estas etapas:

   * **Grupos de recursos**: em sua assinatura do Azure, o Cloud Explorer mostra todos os grupos de recursos associados a essa assinatura. Expanda o grupo de recursos que contém seu aplicativo lógico e selecione seu aplicativo lógico.

   * **Tipos de recursos**: em sua assinatura do Azure, expanda **aplicativos lógicos**. Depois que o Cloud Explorer Mostrar todos os aplicativos lógicos implantados associados à sua assinatura, selecione seu aplicativo lógico.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, você pode abrir aplicativos lógicos criados anteriormente e implantados diretamente por meio do portal do Azure ou como projetos do grupo de recursos do Azure com o Visual Studio.

1. Abra o Cloud Explorer e localize seu aplicativo lógico.

1. No menu de atalho do aplicativo lógico, selecione **abrir com o editor de aplicativo lógico**.

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio.

   Este exemplo mostra os aplicativos lógicos por tipo de recurso, para que seus aplicativos lógicos apareçam na seção **aplicativos lógicos** .

   ![Abrir aplicativo lógico implantado do portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois que o aplicativo lógico é aberto no designer de aplicativos lógicos, na parte inferior do designer, você pode selecionar **modo de exibição de código** para poder examinar a estrutura de definição de aplicativo lógico subjacente. Se você quiser criar um modelo de implantação para o aplicativo lógico, saiba [como baixar um modelo de Azure Resource Manager](#download-logic-app) para esse aplicativo lógico. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/template-deployment-overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar do Azure

Você pode baixar aplicativos lógicos do [portal do Azure](https://portal.azure.com) e salvá-los como [Azure Resource Manager](../azure-resource-manager/management/overview.md) modelos. Você pode editar os modelos localmente com o Visual Studio e personalizar os aplicativos lógicos para diferentes ambientes de implantação.  O download de aplicativos lógicos *parametriza* automaticamente suas definições dentro de [modelos do Resource Manager](../azure-resource-manager/template-deployment-overview.md), que também usam JavaScript Object Notation (JSON).

1. No Visual Studio, abra o Cloud Explorer. Localize e selecione o aplicativo lógico que você deseja baixar do Azure.

1. No menu de atalho do aplicativo, selecione **abrir com o editor de aplicativo lógico**.

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio.

   O designer do aplicativo lógico é aberto e mostra o aplicativo lógico. Para revisar a definição e a estrutura subjacentes do aplicativo lógico, na parte inferior do designer, selecione **modo de exibição de código**.

1. Na barra de ferramentas do designer, selecione **baixar**.

   ![Baixar aplicativo lógico de portal do Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando for solicitado um local, navegue até esse local e salve o modelo do Resource Manager para a definição do aplicativo lógico no formato de arquivo JSON (. JSON).

   Sua definição de aplicativo lógico aparece na subseção `resources` dentro do modelo do Resource Manager. Agora você pode editar a definição do aplicativo lógico e o modelo do Resource Manager com o Visual Studio. Você também pode adicionar o modelo como um [projeto de grupo de recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) a uma solução do Visual Studio. Saiba mais sobre os [projetos do grupo de recursos do Azure para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Vincular à conta de integração

Para criar aplicativos lógicos para cenários de integração empresarial B2B (entre empresas), você pode vincular seu aplicativo lógico a uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) criada anteriormente que existe na mesma região que o seu aplicativo lógico. Uma conta de integração contém artefatos B2B, como parceiros comerciais, contratos, esquemas e mapas, e permite que seu aplicativo lógico use conectores B2B para validação de XML e codificação ou decodificação de arquivo simples. Embora seja possível [criar esse link usando o portal do Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), você também pode usar o Visual Studio depois de atender aos [pré-requisitos](#requirements), e seu aplicativo lógico existe como um arquivo JSON (. JSON) dentro de um [projeto do grupo de recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Saiba mais sobre os [projetos do grupo de recursos do Azure para aplicativos lógicos no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. No Visual Studio, abra o projeto do grupo de recursos do Azure que contém seu aplicativo lógico.

1. Em Gerenciador de Soluções, abra o menu de atalho **< lógica-app-name >. JSON** e selecione **abrir com o designer de aplicativo lógico**. (Teclado: CTRL + L)

   ![Abrir o arquivo. JSON do aplicativo lógico com o designer de aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio e a extensão de ferramentas de aplicativos lógicos do Azure.

1. Certifique-se de que o designer do aplicativo lógico tenha foco selecionando a guia ou a superfície do designer para que o janela Propriedades mostre a propriedade da **conta de integração** para seu aplicativo lógico.

   ![Janela Propriedades-Propriedade "conta de integração"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se o janela Propriedades ainda não estiver aberto, no menu **Exibir** , selecione **janela Propriedades**. (Teclado: Pressione F4)

1. Abra a lista de propriedades da **conta de integração** e selecione a conta de integração que você deseja vincular ao seu aplicativo lógico, por exemplo:

   ![Abrir a lista de propriedades da "conta de integração"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Quando tiver terminado, lembre-se de salvar sua solução do Visual Studio.

Quando você define a propriedade de **conta de integração** no Visual Studio e salva seu aplicativo lógico como um modelo de Azure Resource Manager, esse modelo também inclui uma declaração de parâmetro para a conta de integração selecionada. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [visão geral: automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Alterar local de implantação

No Visual Studio, se o aplicativo lógico existir como um arquivo JSON (. JSON) dentro de um [projeto de grupo de recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) que você usa para automatizar a implantação, esse aplicativo lógico será definido como um tipo de local e um local específico. Esse local é uma região do Azure ou um [ambiente de serviço de integração (ISE)](connect-virtual-network-vnet-isolated-environment.md)existente.

Para alterar o tipo de local ou o local do seu aplicativo lógico, você precisa abrir o arquivo de definição de fluxo de trabalho do aplicativo lógico (. JSON) de Gerenciador de Soluções usando o designer do aplicativo lógico. Você não pode alterar essas propriedades usando o Cloud Explorer.

> [!IMPORTANT]
> Alterar o tipo de local de **região** para [**ambiente de serviço de integração**](connect-virtual-network-vnet-isolated-environment-overview.md) afeta o modelo de [preços](logic-apps-pricing.md#fixed-pricing) do aplicativo lógico usado para cobrança, [limites](logic-apps-limits-and-config.md#integration-account-limits), [suporte de conta de integração](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e assim por diante. Antes de selecionar um tipo de local diferente, certifique-se de entender o impacto resultante em seu aplicativo lógico.

1. No Visual Studio, abra o projeto do grupo de recursos do Azure que contém seu aplicativo lógico.

1. No Gerenciador de Soluções, abra o menu de atalho do arquivo de `<logic-app-name>.json` e selecione **abrir com o designer de aplicativo lógico**. (Teclado: CTRL + L)

   ![Abrir o arquivo. JSON do aplicativo lógico com o designer de aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio e a extensão de ferramentas de aplicativos lógicos do Azure.

1. Certifique-se de que o designer do aplicativo lógico tenha foco selecionando a guia ou a superfície do designer para que a janela Propriedades mostre as propriedades do **tipo de local** e do **local** de seu aplicativo lógico. O tipo de local do projeto é definido como **região** ou **ambiente de serviço de integração**.

   ![Janela Propriedades-Propriedades "escolher tipo de local" & "localização"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se o janela Propriedades ainda não estiver aberto, no menu **Exibir** , selecione **janela Propriedades**. (Teclado: Pressione F4)

1. Para alterar o tipo de local, abra a lista de propriedades **escolher tipo de local** e selecione o tipo de local desejado.

   Por exemplo, se o tipo de local for **ambiente de serviço de integração**, você poderá selecionar **região**.

   ![Propriedade "escolher tipo de local"-alterar tipo de local](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Para alterar o local específico, abra a lista de propriedades **local** . Com base no tipo de local, selecione o local desejado, por exemplo:

   * Selecione uma região diferente do Azure:

     ![Abra a lista de propriedades "local", selecione outra região do Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecione um ISE diferente:

     ![Abra a lista de propriedades "local", selecione outro ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Quando tiver terminado, lembre-se de salvar sua solução do Visual Studio.

Quando você altera o tipo de local ou o local no Visual Studio e salva seu aplicativo lógico como um modelo de Azure Resource Manager, esse modelo também inclui declarações de parâmetro para esse tipo de local e local. Para obter mais informações sobre parâmetros de modelo e aplicativos lógicos, consulte [visão geral: automatizar a implantação do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar do Azure

Se você editar seu aplicativo lógico no portal do Azure e quiser manter essas alterações, certifique-se de atualizar a versão do aplicativo no Visual Studio com essas alterações.

* No Visual Studio, na barra de ferramentas do designer do aplicativo lógico, selecione **Atualizar**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **Atualizar**.

![Atualizar aplicativo lógico com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações do aplicativo lógico

Quando você estiver pronto para implantar as atualizações do aplicativo lógico do Visual Studio para o Azure, na barra de ferramentas do designer do aplicativo lógico, selecione **publicar**.

![Publicar aplicativo lógico atualizado para portal do Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente seu aplicativo lógico

Você pode disparar manualmente um aplicativo lógico implantado no Azure a partir do Visual Studio. Na barra de ferramentas do designer do aplicativo lógico, selecione **executar gatilho**.

![Executar o gatilho manualmente para seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Para verificar o status e diagnosticar problemas com as execuções de aplicativo lógico, você pode examinar os detalhes, como entradas e saídas, para as execuções no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **abrir histórico de execução**.

   ![Abrir o histórico de execução para seu aplicativo lógico](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Para exibir os detalhes de uma execução específica, clique duas vezes em uma execução. Por exemplo:

   ![Exibir informações sobre execução específica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, selecione o cabeçalho da coluna para essa propriedade.

1. Expanda as etapas cujas entradas e saídas você deseja examinar, por exemplo:

   ![Exibir entradas e saídas para cada etapa](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desabilitar ou habilitar o aplicativo lógico

Sem excluir seu aplicativo lógico, você pode interromper o acionamento do gatilho na próxima vez em que a condição do gatilho for atendida. Desabilitar seu aplicativo lógico impede que o mecanismo de aplicativos lógicos crie e execute futuras instâncias de fluxo de trabalho para seu aplicativo lógico. No Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **desabilitar**.

![Desabilitar seu aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Quando você desabilita um aplicativo lógico, nenhuma execução nova é instanciada. Todas as execuções em andamento e pendentes continuarão até que sejam concluídas, o que pode levar tempo para ser concluído.

Para reativar seu aplicativo lógico, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **habilitar**.

![Habilitar aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Eliminar a aplicação lógica

Para excluir seu aplicativo lógico do portal do Azure, no Cloud Explorer, abra o menu de atalho do aplicativo lógico e selecione **excluir**.

![Exclua seu aplicativo lógico do portal do Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir. 

## <a name="troubleshooting"></a>Resolução de problemas

Ao abrir seu projeto de aplicativo lógico no designer de aplicativos lógicos, talvez você não tenha a opção de selecionar sua assinatura do Azure. Em vez disso, seu aplicativo lógico é aberto com uma assinatura do Azure que não é aquela que você deseja usar. Esse comportamento ocorre porque, depois de abrir o arquivo. JSON de um aplicativo lógico, o Visual Studio armazena em cache a primeira assinatura selecionada para uso futuro. Para resolver esse problema, tente uma destas etapas:

* Renomeie o arquivo. JSON do aplicativo lógico. O cache de assinatura depende do nome do arquivo.

* Para remover as assinaturas selecionadas anteriormente para *todos os* aplicativos lógicos em sua solução, exclua a pasta de configurações ocultas do Visual Studio (. vs) no diretório da solução. Esse local armazena as informações de sua assinatura.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a gerenciar aplicativos lógicos implantados com o Visual Studio. Em seguida, saiba mais sobre como personalizar definições de aplicativo lógico para implantação:

> [!div class="nextstepaction"]
> [Criar definições de aplicativo lógico em JSON](../logic-apps/logic-apps-author-definitions.md)

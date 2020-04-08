---
title: Editar e gerir aplicações lógicas utilizando o Estúdio Visual com o Cloud Explorer
description: Editar, atualizar, gerir, adicionar ao controlo de fontes e implementar aplicações lógicas usando o Visual Studio com o Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803195"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerir aplicações lógicas com o Visual Studio

Embora possa criar, editar, gerir e implementar aplicações lógicas no [portal Azure,](https://portal.azure.com)também pode utilizar o Visual Studio quando pretende adicionar as suas aplicações lógicas ao controlo de fontes, publicar diferentes versões e criar modelos de [Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) para vários ambientes de implementação. Com o Visual Studio Cloud Explorer, pode encontrar e gerir as suas aplicações lógicas juntamente com outros recursos Do Azure. Por exemplo, pode abrir, descarregar, editar, executar, ver o histórico de executar, desativar e ativar aplicações lógicas que já estão implantadas no portal Azure. Se é novo a trabalhar com apps Azure Logic no Estúdio Visual, aprenda [a criar aplicações lógicas com o Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

Também pode [gerir as suas aplicações lógicas no portal Azure.](manage-logic-apps-with-azure-portal.md)

> [!IMPORTANT]
> A implementação ou publicação de uma aplicação lógica do Visual Studio substitui a versão dessa aplicação no portal Azure. Por isso, se fizer alterações no portal Azure que pretende manter, certifique-se de que [atualiza a aplicação lógica no Visual Studio](#refresh) a partir do portal Azure antes da próxima vez que implementar ou publicar no Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Estúdio Visual 2019, 2017 ou 2015 - Edição comunitária ou maior.](https://aka.ms/download-visual-studio) Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Quando instalar o Visual Studio 2019 ou 2017, certifique-se de que seleciona a carga de trabalho de **desenvolvimento do Azure.**
    > Para mais informações, consulte [Gerir recursos associados às suas contas Azure no Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Para instalar o Cloud Explorer para o Visual Studio 2015, [baixe o Cloud Explorer do Visual Studio Marketplace.](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015) Para mais informações, consulte [Gerir recursos associados às suas Contas Azure no Visual Studio Cloud Explorer (2015)](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Azure SDK (2.9.1 ou mais tarde)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * As mais recentes ferramentas de aplicações da Azure Logic apps para a extensão do Estúdio Visual para a versão que deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Estúdio Visual 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

* Acesso à web enquanto utiliza o designer de aplicações lógicas incorporadas

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica. Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador verifica se a instância do CRM tem propriedades personalizadas e predefinidas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Encontre as suas aplicações lógicas

No Visual Studio, pode encontrar todas as aplicações lógicas que estão associadas à sua subscrição Azure e que estão implantadas no portal Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **'Ver',** selecione **Cloud Explorer**.

1. No Cloud Explorer, selecione **Gestão de Conta.** Selecione a subscrição Azure associada às suas aplicações lógicas e, em seguida, selecione **Apply**. Por exemplo:

   ![Selecione "Gestão de Conta"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Com base em se está a pesquisar por **Grupos** de Recursos ou Tipos de **Recursos,** siga estes passos:

   * **Grupos de Recursos**: Sob a sua assinatura Azure, o Cloud Explorer mostra todos os grupos de recursos que estão associados a essa subscrição. Expanda o grupo de recursos que contém a sua aplicação lógica e, em seguida, selecione a sua aplicação lógica.

   * **Tipos de Recursos**: Sob a sua subscrição Azure, expanda **aplicações lógicas.** Depois do Cloud Explorer mostrar todas as aplicações lógicas implementadas que estão associadas à sua subscrição, selecione a sua aplicação lógica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, pode abrir aplicações lógicas anteriormente criadas e implementadas diretamente através do portal Azure ou como projetos do Grupo de Recursos Azure com o Visual Studio.

1. Abra o Cloud Explorer e encontre a sua aplicação lógica.

1. No menu de atalho da aplicação lógica, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

   Este exemplo mostra aplicações lógicas por tipo de recurso, pelo que as suas aplicações lógicas aparecem na secção **Apps Lógicas.**

   ![Aplicação lógica aberta do portal Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois da aplicação lógica abrir no Logic Apps Designer, na parte inferior do designer, pode selecionar **o Code View** para que possa rever a estrutura de definição de aplicações lógica subjacente. Se quiser criar um modelo de implementação para a aplicação lógica, aprenda a descarregar um modelo de Gestor de [Recursos Azure](#download-logic-app) para essa aplicação lógica. Saiba mais sobre [os modelos do Gestor de Recursos.](../azure-resource-manager/templates/overview.md)

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Baixar a partir de Azure

Você pode baixar aplicações lógicas do [portal Azure](https://portal.azure.com) e guardá-las como modelos [de Gestor de Recursos Azure.](../azure-resource-manager/management/overview.md) Em seguida, pode editar localmente os modelos com O Estúdio Visual e personalizar aplicações lógicas para diferentes ambientes de implementação.  O descarregamento de aplicações *lógicas parametiliza* automaticamente as suas definições dentro dos [modelos do Gestor](../azure-resource-manager/templates/overview.md)de Recursos, que também usam a Notação de Objetos JavaScript (JSON).

1. No Estúdio Visual, abra o Cloud Explorer. Encontre e selecione a aplicação lógica que pretende descarregar a partir do Azure.

1. No menu de atalho da aplicação, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

   O Logic App Designer abre e mostra a aplicação lógica. Para rever a definição e estrutura subjacentes da aplicação lógica, na parte inferior do designer, selecione **Code View**.

1. Na barra de ferramentas de design, selecione **Download**.

   ![Descarregue a aplicação lógica do portal Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando for solicitado para uma localização, navegue para esse local e guarde o modelo de Gestor de Recursos para a definição de aplicação lógica no formato de ficheiro JSON (.json).

   A definição de aplicação lógica aparece na `resources` subsecção dentro do modelo de Gestor de Recursos. Agora pode editar a definição de aplicações lógicas e o modelo de Gestor de Recursos com O Estúdio Visual. Também pode adicionar o modelo como um projeto do [Grupo de Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a uma solução de Estúdio Visual. Conheça os [projetos do Grupo de Recursos Azure para aplicações lógicas no Estúdio Visual.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Ligação à conta de integração

Para construir aplicativos lógicos para cenários de integração empresarial (B2B), pode ligar a sua aplicação lógica a uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) previamente criada que existe na mesma região que a sua aplicação lógica. Uma conta de integração contém artefactos B2B, tais como parceiros comerciais, acordos, schemas e mapas, e permite que a sua aplicação lógica utilize conectores B2B para validação XML e codificação de ficheiros planos ou descodificação. Embora possa [criar este link utilizando o portal Azure,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)também pode utilizar o Visual Studio depois de cumprir os [pré-requisitos](#requirements), e a sua aplicação lógica existe como um ficheiro JSON (.json) dentro de um projeto do Grupo de [Recursos Azure.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Conheça os [projetos do Grupo de Recursos Azure para aplicações lógicas no Estúdio Visual.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)

1. No Estúdio Visual, abra o projeto do Grupo de Recursos Azure que contém a sua aplicação lógica.

1. No Solution Explorer, abra o menu de atalho do ficheiro **>.json** de nome lógico<e selecione **Open With Logic App Designer**. (Teclado: CTRL + L)

   ![Arquivo .json da app de lógica aberta com Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as mais recentes atualizações para o Visual Studio e para a extensão de Ferramentas de Aplicações Lógicas Azure.

1. Certifique-se de que o Logic App Designer tem foco selecionando o separador ou superfície do designer para que a janela Properties mostre a propriedade **da Conta de Integração** para a sua aplicação lógica.

   ![Janela de propriedades - propriedade "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, a partir do menu **'Ver',** selecione **Janela de Propriedades**. (Teclado: Prima F4)

1. Abra a lista de propriedades **da Conta de Integração** e selecione a conta de integração que pretende ligar à sua aplicação lógica, por exemplo:

   ![Abrir lista de propriedades "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Quando terminar, lembre-se de guardar a sua solução de Estúdio Visual.

Ao definir a propriedade **da Conta de Integração** no Estúdio Visual e guardar a sua aplicação lógica como modelo de Gestor de Recursos Azure, esse modelo também inclui uma declaração de parâmetro para a conta de integração selecionada. Para obter mais informações sobre parâmetros de modelo e aplicações lógicas, consulte [a visão geral: implementação de aplicações lógicas automatizadas](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Alterar a localização da implementação

No Visual Studio, se a sua aplicação lógica existe como um ficheiro JSON (.json) dentro de um projeto do [Grupo de Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) que utiliza para automatizar a implementação, essa aplicação lógica está definida para um tipo de localização e uma localização específica. Esta localização é ou uma região azure ou um ambiente de serviço de integração existente [(ISE)](connect-virtual-network-vnet-isolated-environment.md).

Para alterar o tipo ou localização da sua aplicação lógica, tem de abrir o ficheiro de definição de fluxo de trabalho (.json) da aplicação lógica (.json) do Solution Explorer utilizando o Logic App Designer. Não pode alterar estas propriedades usando o Cloud Explorer.

> [!IMPORTANT]
> Mudar o tipo de localização da **Região** para o Serviço de [**Integração O ambiente**](connect-virtual-network-vnet-isolated-environment-overview.md) afeta o modelo de [preços](logic-apps-pricing.md#fixed-pricing) da sua aplicação lógica que é usado para faturar, [limites,](logic-apps-limits-and-config.md#integration-account-limits)suporte à conta de [integração,](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e assim por diante. Antes de selecionar um tipo de localização diferente, certifique-se de que compreende o impacto resultante na sua aplicação lógica.

1. No Estúdio Visual, abra o projeto do Grupo de Recursos Azure que contém a sua aplicação lógica.

1. No Solution Explorer, `<logic-app-name>.json` abra o menu de atalho do ficheiro e selecione **Open With Logic App Designer**. (Teclado: CTRL + L)

   ![Arquivo .json da app de lógica aberta com Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as mais recentes atualizações para o Visual Studio e para a extensão de Ferramentas de Aplicações Lógicas Azure.

1. Certifique-se de que o Designer de Aplicações Lógicas tem foco selecionando o separador ou superfície do designer para que a janela Properties mostre as propriedades **do Tipo** de Localização e **Localização** escolha para a sua aplicação lógica. O tipo de localização do projeto está definido para **a Região** ou para o Ambiente de Serviço de **Integração.**

   ![Janela de propriedades - "Escolha Tipo de Localização" & propriedades "Localização"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, a partir do menu **'Ver',** selecione **Janela de Propriedades**. (Teclado: Prima F4)

1. Para alterar o tipo de localização, abra a lista de propriedades **do Tipo de Localização Escolha** e selecione o tipo de localização que deseja.

   Por exemplo, se o tipo de localização for Ambiente de Serviço de **Integração,** pode selecionar **Região**.

   ![Propriedade "Choose Location Type" - alterar o tipo de localização](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Para alterar a localização específica, abra a lista de propriedades **de Localização.** Com base no tipo de localização, selecione a localização que deseja, por exemplo:

   * Selecione uma região azure diferente:

     ![Abrir lista de propriedades "Localização", selecione outra região do Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecione um ISE diferente:

     ![Abrir lista de propriedades "Localização", selecione outro ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Quando terminar, lembre-se de guardar a sua solução de Estúdio Visual.

Quando muda o tipo de localização ou localização no Estúdio Visual e guarda a sua aplicação lógica como modelo de Gestor de Recursos Azure, esse modelo também inclui declarações de parâmetros para esse tipo de localização e localização. Para obter mais informações sobre parâmetros de modelo e aplicações lógicas, consulte [a visão geral: implementação de aplicações lógicas automatizadas](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Refresh de Azure

Se editar a sua aplicação lógica no portal Azure e pretender manter essas alterações, certifique-se de que atualiza a versão da aplicação no Visual Studio com essas alterações.

* No Estúdio Visual, na barra de ferramentas Logic App Designer, selecione **Refresh**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Refresh**.

![Atualizar app lógica com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicação lógica

Quando estiver pronto para implementar as atualizações da sua aplicação lógica do Visual Studio para o Azure, na barra de ferramentas Logic App Designer, **selecione Publish**.

![Publicar app lógica atualizada para portal Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente a aplicação lógica

Pode acionar manualmente uma aplicação lógica implantada em Azure a partir do Visual Studio. Na barra de ferramentas Logic App Designer, selecione **'Executar Gatilho**' .

![Executar manualmente o gatilho para a sua aplicação lógica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Para verificar o estado e diagnosticar problemas com aplicações lógicas, pode rever os detalhes, tais como inputs e saídas, para aqueles que correm no Estúdio Visual.

1. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **o histórico de open run**.

   ![Histórico de execução aberta para a sua aplicação lógica](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Para ver os detalhes para uma execução específica, clique duas vezes numa corrida. Por exemplo:

   ![Ver informações sobre execução específica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, selecione o cabeçalho da coluna para essa propriedade.

1. Expanda os passos cujas inputs e saídas pretende rever, por exemplo:

   ![Ver inputs e saídas para cada passo](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desativar ou ativar aplicação lógica

Sem apagar a sua aplicação lógica, pode impedir que o gatilho dispare da próxima vez que a condição do gatilho for satisfeita. Desativar a sua aplicação lógica impede que o motor Logic Apps crie e execute futuras instâncias de fluxo de trabalho para a sua aplicação lógica. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Disable**.

![Desative a sua aplicação lógica no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Quando desativa uma aplicação lógica, não são instantâneas novas execuções. Todas as corridas em curso e pendentes continuarão até que terminem, o que pode levar tempo a concluir.

Para reativar a sua aplicação lógica, no Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Enable**.

![Ativar aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Eliminar a aplicação lógica

Para eliminar a sua aplicação lógica do portal Azure, no Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Delete**.

![Elimine a sua aplicação lógica do portal Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir. 

## <a name="troubleshooting"></a>Resolução de problemas

Ao abrir o seu projeto de aplicação lógica no Logic Apps Designer, poderá não ter a opção de selecionar a sua subscrição Azure. Em vez disso, a sua aplicação lógica abre com uma subscrição Azure que não é a que pretende utilizar. Este comportamento acontece porque depois de abrir o ficheiro .json de uma aplicação lógica, o Visual Studio caches a primeira subscrição selecionada para uso futuro. Para resolver este problema, experimente um destes passos:

* Mude o nome do ficheiro .json da aplicação lógica. A cache de subscrição depende do nome do ficheiro.

* Para remover subscrições previamente selecionadas para *todas as* aplicações lógicas na sua solução, elimine a pasta de definições do Estúdio Visual Oculto (.vs) no diretório da sua solução. Esta localização armazena as suas informações de subscrição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a gerir aplicações lógicas implementadas com o Visual Studio. Em seguida, aprenda sobre personalizar definições de aplicativos lógicos para implementação:

> [!div class="nextstepaction"]
> [Definições de aplicativológica de autor em JSON](../logic-apps/logic-apps-author-definitions.md)

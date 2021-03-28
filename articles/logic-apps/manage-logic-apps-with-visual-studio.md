---
title: Editar e gerir aplicativos lógicos utilizando o Visual Studio com o Cloud Explorer
description: Editar, atualizar, gerir, adicionar ao controlo de origem e implementar aplicações lógicas utilizando o Visual Studio com o Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 56b74e440fcb09ab206bbb069517dd756221f809
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639566"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerir aplicações lógicas com o Visual Studio

Embora possa criar, editar, gerir e implementar aplicações lógicas no [portal Azure,](https://portal.azure.com)também pode utilizar o Visual Studio quando pretende adicionar as suas aplicações lógicas ao controlo de origem, publicar diferentes versões e criar modelos [de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) para vários ambientes de implementação. Com o Visual Studio Cloud Explorer, pode encontrar e gerir as suas aplicações lógicas juntamente com outros recursos Azure. Por exemplo, pode abrir, descarregar, editar, executar, ver o histórico de execução, desativar e ativar aplicações lógicas que já estão implantadas no portal Azure. Se é novo a trabalhar com a Azure Logic Apps em Visual Studio, aprenda [a criar aplicações lógicas com o Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

Também pode [gerir as suas aplicações lógicas no portal Azure.](manage-logic-apps-with-azure-portal.md)

> [!IMPORTANT]
> A implementação ou publicação de uma aplicação lógica do Visual Studio substitui a versão dessa aplicação no portal Azure. Por isso, se fizer alterações no portal Azure que pretende manter, certifique-se de que [atualiza a aplicação lógica no Estúdio Visual](#refresh) a partir do portal Azure antes da próxima vez que implementar ou publicar a partir do Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio 2019, 2017 ou 2015 - Edição comunitária ou superior.](https://aka.ms/download-visual-studio) Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Quando instalar o Visual Studio 2019 ou 2017, certifique-se de que seleciona a carga de trabalho de desenvolvimento do **Azure.**
    > Para obter mais informações, consulte [Gerir os recursos associados às suas contas Azure no Visual Studio Cloud Explorer.](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)

    Para instalar o Cloud Explorer para o Visual Studio 2015, [baixe o Cloud Explorer do Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Para obter mais informações, consulte [Gerir os recursos associados às suas Contas Azure no Visual Studio Cloud Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true).

  * [Azure SDK (2.9.1 ou mais tarde)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * As mais recentes Ferramentas de Aplicações Lógicas Azure para a extensão do Estúdio Visual para a versão que deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

  * Para utilizar subscrições do Governo Azure com o Visual Studio, consulte estes tópicos para configuração adicional:

    * Visual Studio 2019: [Quickstart: Connect to Azure Government with Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Introduzindo a extensão do Azure Environment Seletor Visual Studio,](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/)que pode descarregar e instalar a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Acesso à web enquanto utiliza o Designer de Aplicações Lógicas incorporado

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Encontre aplicativos lógicos

No Visual Studio, pode encontrar todas as aplicações lógicas associadas à sua subscrição Azure e são implementadas no portal Azure utilizando o Cloud Explorer.

1. Abra o Visual Studio. No menu **Ver,** selecione **Cloud Explorer**.

1. No Cloud Explorer, selecione o ícone **de Gestão de Contas.** Selecione a subscrição Azure associada às suas aplicações lógicas e selecione **Apply**. Por exemplo:

   ![Selecione "Gestão de Contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Junto ao ícone **de Gestão de Conta,** selecione **Tipos de Recursos.** Sob a subscrição do Azure, expanda **as Aplicações Lógicas** para que possa ver todas as aplicações lógicas implementadas que estão associadas à sua subscrição.

Em seguida, abra a sua aplicação lógica no Logic App Editor.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Abrir aplicativos de lógica no Estúdio Visual

No Visual Studio, pode abrir aplicações lógicas previamente criadas e implementadas diretamente através do portal Azure ou como projetos do Azure Resource Group com o Visual Studio.

1. [Abra o Cloud Explorer e encontre a sua aplicação lógica.](#find-logic-apps-vs)

1. A partir do menu de atalho da aplicação lógica, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

   ![Aplicativo de lógica aberto aberto a partir do portal Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois de a aplicação lógica abrir no Logic Apps Designer, na parte inferior do designer, pode selecionar **Code View** para que possa rever a estrutura de definição de aplicações lógicas subjacente. Se quiser criar um modelo de implementação para a aplicação lógica, aprenda [a descarregar um modelo de Gestor de Recursos Azure](#download-logic-app) para essa aplicação lógica. Saiba mais sobre [os modelos do Gestor de Recursos.](../azure-resource-manager/templates/overview.md)

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Download a partir de Azure

Você pode [baixar](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) aplicativos lógicos a partir do [portal Azure](https://portal.azure.com) e guardá-los como modelos [Azure Resource Manager.](../azure-resource-manager/management/overview.md) Em seguida, pode editar os modelos localmente com o Visual Studio e personalizar aplicações lógicas para diferentes ambientes de implementação.  O download de aplicações *lógicas parametriza* automaticamente as suas definições dentro [dos modelos do Gestor de Recursos](../azure-resource-manager/templates/overview.md), que também utilizam a Notação de Objetos JavaScript (JSON).

1. No Visual Studio, utilizando o Cloud Explorer, [abra a aplicação lógica que pretende descarregar a partir do Azure.](#open-designer)

1. A partir do menu de atalho da aplicação lógica, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

   A aplicação lógica abre no Logic App Designer.

1. Na barra de ferramentas do designer, selecione **Download**.

   ![Descarregue aplicativo lógico a partir do portal Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Quando for solicitado para uma localização, navegue para esse local e guarde o modelo de Gestor de Recursos para a definição de aplicação lógica no formato de ficheiro JSON (.json).

   A definição da aplicação lógica aparece na `resources` subsecção dentro do modelo de Gestor de Recursos. Agora pode editar a definição de aplicativo lógica e o modelo de Gestor de Recursos com o Visual Studio. Também pode adicionar o modelo como um [projeto do Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a uma solução visual Studio. Saiba mais sobre [projetos do Azure Resource Group para aplicações lógicas no Estúdio Visual.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Ligação à conta de integração

Para construir aplicativos lógicos para cenários de integração empresarial (B2B), pode ligar a sua aplicação lógica a uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) previamente criada que existe na mesma região que a sua aplicação lógica. Uma conta de integração contém artefactos B2B, tais como parceiros de negociação, acordos, esquemas e mapas, e permite que a sua aplicação lógica utilize conectores B2B para validação de XML e codificação ou descodificamento de ficheiros planos. Embora possa [criar este link utilizando o portal Azure,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)também pode utilizar o Visual Studio depois de cumprir os [pré-requisitos](#requirements), e a sua aplicação lógica existe como um ficheiro JSON (.json) dentro de um [projeto do Grupo de Recursos Azure.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Saiba mais sobre [projetos do Azure Resource Group para aplicações lógicas no Estúdio Visual.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)

1. No Visual Studio, abra o projeto Azure Resource Group que contém a sua aplicação lógica.

1. In Solution Explorer, abra o **<nome de aplicação lógica>.jsno** menu de atalho do ficheiro e selecione Open With Logic App **Designer**. (Teclado: CTRL + L)

   ![Arquivo de .json da app lógica aberta com o Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio e a extensão Azure Logic Apps Tools.

1. Certifique-se de que o Designer de Aplicações Lógicas tem foco ao selecionar o separador ou superfície do designer para que a janela Propriedades mostre a propriedade **Da Conta de Integração** para a sua aplicação lógica.

   ![Janela de propriedades - propriedade "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, a partir do menu **Ver,** selecione **Janela propriedades**. (Teclado: Pressione F4)

1. Abra a lista de propriedades **da Conta de Integração** e selecione a conta de integração que pretende ligar à sua aplicação lógica, por exemplo:

   ![Abra a lista de propriedades "Conta de Integração"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Quando terminar, lembre-se de guardar a sua solução visual Studio.

Quando define a propriedade **da Conta de Integração** no Estúdio Visual e guarda a sua aplicação lógica como modelo de Gestor de Recursos Azure, esse modelo também inclui uma declaração de parâmetro para a conta de integração selecionada. Para obter mais informações sobre os parâmetros do modelo e aplicações lógicas, consulte [a visão geral: Automatizar a implementação da aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Alterar localização de implementação

No Visual Studio, se a sua aplicação lógica existe como um ficheiro JSON (.json) dentro de um [projeto do Grupo de Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) que utiliza para automatizar a implementação, essa aplicação lógica está definida para um tipo de localização e uma localização específica. Esta localização é uma região de Azure ou um ambiente de serviço de integração existente [(ISE)](connect-virtual-network-vnet-isolated-environment.md).

Para alterar o tipo ou localização da sua aplicação lógica, tem de abrir o ficheiro de definição de fluxo de trabalho (.json) da sua aplicação lógica a partir do Solution Explorer utilizando o Logic App Designer. Não é possível alterar estas propriedades utilizando o Cloud Explorer.

> [!IMPORTANT]
> A alteração do tipo de localização de **Região** para Ambiente de Serviço de [**Integração**](connect-virtual-network-vnet-isolated-environment-overview.md) afeta o modelo de [preços](logic-apps-pricing.md#fixed-pricing) da sua aplicação lógica que é usada para faturação, [limites,](logic-apps-limits-and-config.md#integration-account-limits) [suporte de conta de integração,](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)e assim por diante. Antes de selecionar um tipo de localização diferente, certifique-se de que compreende o impacto resultante na sua aplicação lógica.

1. No Visual Studio, abra o projeto Azure Resource Group que contém a sua aplicação lógica.

1. No Solution Explorer, abra o `<logic-app-name>.json` menu de atalho do ficheiro e selecione **Open With Logic App Designer**. (Teclado: CTRL + L)

   ![Arquivo de .json da app lógica aberta com o Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio e a extensão Azure Logic Apps Tools.

1. Certifique-se de que o Designer de Aplicações Lógicas se concentra ao selecionar o separador ou superfície do designer para que a janela Propriedades mostre as propriedades do Tipo de **Localização** e **Localização** escolha para a sua aplicação lógica. O tipo de localização do projeto está definido para o **Ambiente de Serviço de Região** ou **Integração.**

   ![Janela de propriedades - "Escolha o tipo de localização" & propriedades "Localização"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Se a janela Propriedades ainda não estiver aberta, a partir do menu **Ver,** selecione **Janela propriedades**. (Teclado: Pressione F4)

1. Para alterar o tipo de localização, abra a lista de propriedades **Escolha Localização** e selecione o tipo de localização que deseja.

   Por exemplo, se o tipo de localização for **Ambiente de Serviço de Integração,** pode selecionar **Região.**

   ![Propriedade "Escolha o Tipo de Localização" - alterar tipo de localização](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Para alterar a localização específica, abra a lista de propriedades **localização.** Com base no tipo de localização, selecione a localização que deseja, por exemplo:

   * Selecione uma região de Azure diferente:

     ![Abra a lista de propriedades "Localização", selecione outra região do Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Selecione um ISE diferente:

     ![Abrir a lista de propriedades "Localização", selecione outra ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Quando terminar, lembre-se de guardar a sua solução visual Studio.

Quando altera o tipo de localização ou localização no Estúdio Visual e guarda a sua aplicação lógica como modelo de Gestor de Recursos Azure, esse modelo também inclui declarações de parâmetros para esse tipo de localização e localização. Para obter mais informações sobre os parâmetros do modelo e aplicações lógicas, consulte [a visão geral: Automatizar a implementação da aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Refresh from Azure

Se editar a sua aplicação lógica no portal Azure e quiser manter essas alterações, certifique-se de atualizar a versão da aplicação no Visual Studio com essas alterações.

* No Estúdio Visual, na barra de ferramentas Logic App Designer, selecione **Refresh**.

  -ou-

* No Visual Studio Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Refresh**.

![Atualizar app lógica com atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicação lógica

Quando estiver pronto para implementar as atualizações da sua aplicação lógica de Visual Studio para Azure, na barra de ferramentas Logic App Designer, **selecione Publicar**.

![Publique app lógica atualizada para o portal Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente a aplicação lógica

Pode desencadear manualmente uma aplicação lógica implantada em Azure a partir do Visual Studio. Na barra de ferramentas Logic App Designer, selecione **'Executar Trigger'.**

![Executar manualmente o gatilho para a sua aplicação lógica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Para verificar o estado e diagnosticar problemas com aplicações lógicas, pode rever os detalhes, tais como entradas e saídas, para as que são executados no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **O histórico de execução aberta**.

   ![Histórico de execução aberta para a sua app lógica](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Para ver os detalhes para uma execução específica, clique duas vezes numa corrida. Por exemplo:

   ![Ver informações sobre execução específica](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para classificar a tabela por propriedade, selecione o cabeçalho da coluna para essa propriedade.

1. Expanda os passos cujas entradas e saídas pretende rever, por exemplo:

   ![Ver entradas e saídas para cada passo](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desativar ou ativar aplicação lógica

Sem eliminar a sua aplicação lógica, pode impedir que o gatilho dispare da próxima vez que a condição do gatilho for satisfeita. Desativar a sua aplicação lógica impede que o motor de Aplicações Lógicas crie e executa futuras instâncias de fluxo de trabalho para a sua aplicação lógica. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Disable**.

![Desativar a sua aplicação lógica no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Quando desativa uma aplicação lógica, não há novas execuções instantâneas. Todas as corridas em curso e pendentes continuarão até que terminem, o que pode levar algum tempo a ser concluído.

Para reativar a sua aplicação lógica, no Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Enable**.

![Ativar aplicativo lógico no Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Eliminar a aplicação lógica

Para eliminar a sua aplicação lógica do portal Azure, no Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **Delete**.

![Elimine a sua aplicação lógica do portal Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir.

> [!NOTE]
> Se eliminar e recriar uma aplicação de lógica infantil, deve voltar a levantar a aplicação lógica dos pais. A aplicação infantil recriada terá metadados diferentes.
> Se não voltar a sair da aplicação lógica dos pais depois de recriar o seu filho, as suas chamadas para a aplicação de lógica infantil falharão com um erro de "não autorizado". Este comportamento aplica-se a aplicações lógicas de pais e filhos, por exemplo, aquelas que usam artefactos em contas de integração ou chamam funções de Azure.

## <a name="troubleshooting"></a>Resolução de problemas

Quando abrir o seu projeto de aplicação lógica no Logic Apps Designer, poderá não ter a opção de selecionar a sua subscrição Azure. Em vez disso, a sua aplicação lógica abre com uma subscrição Azure que não é a que quer usar. Este comportamento acontece porque depois de abrir o ficheiro .json de uma aplicação lógica, o Visual Studio caches a primeira subscrição selecionada para uso futuro. Para resolver este problema, experimente um destes passos:

* Mude o nome do ficheiro .json da aplicação lógica. A cache de subscrição depende do nome do ficheiro.

* Para remover subscrições previamente selecionadas para *todas as* aplicações lógicas na sua solução, elimine a pasta de definições de Estúdio Visual oculto (.vs) no diretório da sua solução. Este local armazena as informações da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a gerir aplicações lógicas implementadas com o Visual Studio. Em seguida, saiba personalizar definições de aplicativos lógicos para implementação:

> [!div class="nextstepaction"]
> [Definições de aplicativos lógicos de autor em JSON](../logic-apps/logic-apps-author-definitions.md)

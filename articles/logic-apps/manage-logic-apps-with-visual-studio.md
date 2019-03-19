---
title: Gerir aplicações lógicas com o Visual Studio - Azure Logic Apps | Documentos da Microsoft
description: Gerir aplicações lógicas e outros recursos do Azure com o Explorador de nuvem do Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.openlocfilehash: 9f626e2a5c277cdce78c6e90046a1d40dac2d588
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078674"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gerir aplicações lógicas com o Visual Studio

Embora possa criar, editar, gerir e implementar aplicações lógicas no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, também pode utilizar o Visual Studio quando quiser adicionar aplicações lógicas ao controlo de origem, publicar versões diferentes e criar [recursos do Azure Manager](../azure-resource-manager/resource-group-overview.md) modelos para ambientes de implementação diferentes. Com o Explorador de nuvem do Visual Studio, pode localizar e gerir as aplicações lógicas, juntamente com outros recursos do Azure. Por exemplo, pode abrir, transfira, editar, executar, ver o histórico de execuções, desativar e ativar logic apps que já estão implementados no portal do Azure. Se estiver familiarizado com a trabalhar com o Azure Logic Apps no Visual Studio, saiba [como criar aplicações lógicas com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Implementar ou publicar uma aplicação lógica a partir do Visual Studio substitui a versão dessa aplicação no portal do Azure. Portanto, se fizer alterações no portal do Azure que quer manter, certifique-se de que [atualizar a aplicação lógica no Visual Studio](#refresh) do portal do Azure antes da próxima vez que implementa ou publicar a partir do Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

* Transfira e instale estas ferramentas, se ainda não as tiver: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 ou Visual Studio 2015 – edição Community ou superior</a>. 
  Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">SDK do Azure (2.9.1 ou posterior)</a> e <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Ferramentas do Azure Logic Apps para o Visual Studio 2017</a> ou a <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">versão Visual Studio 2015</a> 
  
    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">como instalar esta extensão a partir do Visual Studio</a>. 
    Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

* Acesso à web ao utilizar o estruturador de aplicações lógicas embedded

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica. 
  Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador verifica se a instância do CRM tem propriedades personalizadas e predefinidas disponíveis.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Encontre as aplicações lógicas

No Visual Studio, pode encontrar todas as aplicações lógicas que estão associados a sua subscrição do Azure e são implementadas no portal do Azure com o Cloud Explorer.

1. Abra o Visual Studio. Sobre o **View** menu, selecione **Cloud Explorer**.

2. No Cloud Explorer, escolha **gestão de contas**. Selecione a subscrição do Azure associada com as logic apps, em seguida, escolha **aplicar**. Por exemplo:

   ![Escolha "Gestão de contas"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Com base em se está a procurar por **grupos de recursos** ou **tipos de recurso**, siga estes passos:

   * **Grupos de recursos**: Com a sua subscrição do Azure, o Cloud Explorer mostra todos os grupos de recursos que estão associados essa subscrição. 
   Expanda o grupo de recursos que contém a aplicação lógica e, em seguida, selecione a sua aplicação lógica.

   * **Tipos de recurso**: Com a sua subscrição do Azure, expanda **Logic Apps**. Depois do Cloud Explorer mostra todas as aplicações de lógica implementado que estão associadas a sua subscrição, selecione a aplicação lógica.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Abrir no Visual Studio

No Visual Studio, é possível abrir as aplicações lógicas anteriormente criado e implementado diretamente através do portal do Azure ou que os projetos do Azure Resource Manager com o Visual Studio.

1. Abra o Explorador de Cloud e encontre a sua aplicação lógica. 

2. No menu de atalho da aplicação lógica, selecione **aberto com o Editor de aplicação lógica**.

   Este exemplo mostra as aplicações lógicas por tipo de recurso, para que as aplicações lógicas são apresentados no **Logic Apps** secção.

   ![Aplicação de lógica de implementado abertos do portal do Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Depois da aplicação lógica abre no estruturador de aplicações lógicas, na parte inferior do designer, pode escolher **vista de código** , de modo a que possa rever a estrutura de definição de aplicação lógica subjacente. 
   Se quiser criar um modelo de implementação para a aplicação lógica, saiba [como transferir um modelo Azure Resource Manager](#download-logic-app) para essa aplicação lógica. Saiba mais sobre [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Transferir a partir do Azure

Pode transferir as aplicações lógicas a partir da <a href="https://portal.azure.com" target="_blank">portal do Azure</a> e guarde-as como [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modelos. Pode, em seguida, localmente editar os modelos com o Visual Studio e personalizar aplicações lógicas para ambientes de implementação diferentes. Transferir as aplicações lógicas automaticamente *parametriza* as respetivas definições dentro [modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), que também utilizar JavaScript Object Notation (JSON).

1. No Visual Studio, abra o Explorador de Cloud, em seguida, localize e selecione a aplicação lógica que pretende transferir a partir do Azure.

2. No menu de atalho essa aplicação, selecione **aberto com o Editor de aplicação lógica**.

   Estruturador da aplicação lógica é aberto e mostra a aplicação lógica. 
   Para rever a definição subjacente da aplicação lógica e a estrutura, na parte inferior do designer, escolha **vista de código**. 

3. Na barra de ferramentas da estruturador, escolha **transferir**.

   ![Selecione "Transferir"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Quando lhe for pedido para um local, navegue para essa localização e guardar o modelo do Resource Manager para a definição da aplicação lógica no formato de ficheiro JSON (. JSON). 

A definição da aplicação lógica aparece no `resources` subsecção dentro do modelo do Resource Manager. Agora pode editar a definição da aplicação lógica e o modelo do Resource Manager com o Visual Studio. Também pode adicionar o modelo como um projeto do Azure Resource Manager para uma solução do Visual Studio. Saiba mais sobre [projetos do Resource Manager para aplicações lógicas no Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Atualizar a partir do Azure

Se editar a sua aplicação lógica no portal do Azure e pretende manter essas alterações, certifique-se de que atualiza a versão dessa aplicação no Visual Studio com essas alterações. 

* No Visual Studio, na barra de ferramentas da Estruturador da aplicação lógica, escolha **atualizar**.

  -ou-

* No Explorador de nuvem do Visual Studio, abra o menu de atalho da sua aplicação lógica e selecione **atualizar**. 

![Atualizar a aplicação lógica com as atualizações](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publicar atualizações de aplicação lógica

Quando estiver pronto para implementar as atualizações de aplicação lógica a partir do Visual Studio para o Azure, na barra de ferramentas do Estruturador da aplicação lógica, escolha **publicar**.

![Publicar a aplicação lógica atualizada](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Executar manualmente a aplicação lógica

Pode acionar manualmente uma aplicação de lógica implementada no Azure a partir do Visual Studio. Na barra de ferramentas da Estruturador da aplicação lógica, escolha **executar acionador**.

![Executar manualmente a aplicação lógica](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Rever o histórico de execuções

Para verificar o estado e diagnosticar problemas com execuções da aplicação lógica, pode rever os detalhes, como entradas e saídas, para aqueles que é executado no Visual Studio.

1. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **histórico de execuções de Open**.

   ![Abra o histórico de execuções](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Para ver os detalhes de uma execução específica, clique duas vezes numa execução. Por exemplo:

   ![Histórico de execução detalhado](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Para ordenar a tabela pela propriedade, escolha o cabeçalho de coluna para essa propriedade. 

3. Expanda as etapas cuja entradas e saídas pretende rever. Por exemplo:

   ![Ver entradas e saídas de cada passo](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Desativar ou ativar aplicação lógica

Sem eliminar a aplicação lógica, pode parar o acionador de acionamento da próxima vez em quando a condição de acionamento for cumprida. Desativar a sua aplicação lógica, impede que o motor do Logic Apps de criação e execução de instâncias de fluxo de trabalho futuras para a aplicação lógica.
No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **desativar**.

![Desativar a sua aplicação lógica](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Quando desativa uma aplicação lógica, não existem novas execuções são instanciadas. Todos em curso e execuções pendentes continuará até terminarem, que pode levar tempo a concluir. 

Quando estiver pronto para a sua aplicação lógica retomar a operação, pode reativar a sua aplicação lógica. No Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **ativar**.

![Ativar a sua aplicação lógica](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Eliminar a aplicação lógica

Para eliminar a aplicação lógica no portal do Azure, no Cloud Explorer, abra o menu de atalho da sua aplicação lógica e selecione **eliminar**.

![Eliminar a aplicação lógica](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Quando elimina uma aplicação lógica, não são instanciadas novas execuções. Todas as execuções em curso e pendentes são canceladas. Se tiver milhares de execuções, o cancelamento pode demorar muito tempo a concluir. 

## <a name="troubleshooting"></a>Resolução de problemas

Quando abrir o seu projeto de aplicação lógica no Designer de aplicações lógicas, não poderá obter a opção para selecionar a sua subscrição do Azure. Em vez disso, a sua aplicação lógica abre-se com uma subscrição do Azure que não é a que pretende utilizar. Este comportamento acontece porque depois de abrir o ficheiro. JSON de uma aplicação lógica, o Visual Studio coloca em cache a subscrição selecionada primeiro para utilização futura. Para resolver este problema, experimente um destes passos:

* Mudar o nome de ficheiro. JSON da aplicação lógica. A cache de subscrição depende do nome de ficheiro. 
* Para remover subscrições selecionadas anteriormente para *todos os* eliminar aplicações lógicas na sua solução, o *ocultos* .vs pasta no diretório da sua solução. Esta localização armazena informações da sua subscrição. 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como gerir aplicações lógicas implementado com o Visual Studio. Em seguida, saiba mais sobre como personalizar as definições de aplicação lógica para a implementação:

> [!div class="nextstepaction"]
> [Criar definições de aplicação lógica em JSON](../logic-apps/logic-apps-author-definitions.md)

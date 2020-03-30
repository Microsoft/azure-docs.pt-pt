---
title: Construa a primeira aplicação sem servidores no Estúdio Visual
description: Construir, implementar e gerir uma aplicação sem servidores utilizando aplicações da Azure Logic e funções azure no Estúdio Visual
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981153"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Construa a sua primeira aplicação sem servidores utilizando aplicações da Azure Logic e funções azure no Estúdio Visual

Pode desenvolver e implementar rapidamente aplicações em nuvem utilizando as ferramentas e capacidades sem servidores no Azure, tais como [Aplicações lógicas Azure](../logic-apps/logic-apps-overview.md) e [Funções Azure.](../azure-functions/functions-overview.md) Este artigo mostra como começar a construir uma aplicação sem servidores, que usa uma aplicação lógica que chama uma função Azure, no Estúdio Visual. Para saber mais sobre soluções sem servidor no Azure, consulte [O Servidor Azure com Funções e Aplicações Lógicas](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para construir uma aplicação sem servidores no Estúdio Visual, você precisa:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* As seguintes ferramentas. Se ainda não os tiver, faça o download e instale-os.

  * [Estúdio Visual 2019, 2017 ou 2015 (Comunidade ou outra edição)](https://aka.ms/download-visual-studio). 
  Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Quando instalar o Visual Studio 2019 ou 2017, certifique-se de que seleciona a carga de trabalho de **desenvolvimento do Azure.**

  * [Microsoft Azure SDK para .NET (versão 2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell.](https://github.com/Azure/azure-powershell#installation)

  * Ferramentas de aplicações lógicas azure para a versão Do Estúdio Visual que deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Estúdio Visual 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

  * [Funções Azure Ferramentas nucleares](https://www.npmjs.com/package/azure-functions-core-tools) para funções de depuração local.

* Acesso à web enquanto utiliza o Designer de Aplicações Lógica incorporada.

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica. 
  Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador verifica se a instância do CRM tem propriedades personalizadas e predefinidas disponíveis.

## <a name="create-a-resource-group-project"></a>Criar um projeto de grupo de recursos

Para começar, crie um projeto do Grupo de [Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para a sua aplicação sem servidores. No Azure, cria-se recursos dentro de um grupo de *recursos*, que é uma coleção lógica que utiliza para organizar, gerir e implementar recursos para toda uma aplicação como um único ativo. Para uma aplicação sem servidores em Azure, o seu grupo de recursos inclui recursos tanto para aplicações lógicas azure como para funções Azure. Saiba mais sobre [recursos e grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Inicie o Estúdio Visual e inicie o início usando a sua conta Azure.

1. No menu **Ficheiro**, selecione **Novo** > **Projeto**.

   ![Criar novo projeto no Estúdio Visual](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Em seguida, selecione **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Se a categoria **Cloud** ou o projeto **do Grupo de Recursos Azure** não existirem, certifique-se de que instalou o Azure SDK para Estúdio Visual.

   Se estiver a usar o Visual Studio 2019, siga estes passos:

   1. Na **Create a new project** box, selecione o modelo de projeto do Grupo de **Recursos Azure** para visual C# ou Visual Basic, e depois selecione **Next**.

   1. Forneça o nome e outras informações do projeto que pretende utilizar para o grupo de recursos Azure. Quando terminar, selecione **Criar**.

1. Dê ao seu projeto um nome e uma localização e, em seguida, selecione **OK**.

   O Visual Studio pede-lhe que selecione um modelo da lista de modelos. 
   Este exemplo utiliza um modelo Azure QuickStart para que possa construir uma aplicação sem servidores que inclua uma aplicação lógica e uma chamada para uma função Azure.

   > [!TIP]
   > Em cenários em que não pretende pré-implantar a sua solução num grupo de recursos Azure, pode utilizar o modelo de **Aplicação Lógica** Em branco, que apenas cria uma aplicação lógica vazia.

1. A partir dos **modelos show desta** lista de localização, selecione **Azure QuickStart (github.com/Azure/azure-quickstart-templates)**.

1. Na caixa de pesquisa, introduza "logic-app" como filtro. A partir dos resultados, selecione o modelo **de 101-logic-app-e-function-app-app.**

   ![Selecione modelo QuickStart Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o seu projeto de grupo de recursos. 
   O modelo Azure QuickStart que selecionou cria um modelo de implantação chamado azuredeploy.json dentro do seu projeto de grupo de recursos. Este modelo de implementação inclui a definição para uma aplicação lógica simples que é desencadeada por um pedido HTTP, chama uma função Azure, e devolve o resultado como resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, implemente a sua solução para o Azure. Tem de o fazer antes de poder abrir o modelo de implementação e rever os recursos para a sua aplicação sem servidores.

## <a name="deploy-your-solution"></a>Implementar a sua solução

Antes de poder abrir a sua aplicação lógica no Logic App Designer no Visual Studio, deve ter um grupo de recursos Azure que já está implantado no Azure. O designer pode então criar ligações a recursos e serviços na sua aplicação lógica. Para esta tarefa, siga estes passos para implementar a sua solução do Estúdio Visual para o portal Azure:

1. No Solution Explorer, a partir do menu de atalho do seu projeto de recursos, selecione **Implementar** > **Novo**.

   ![Criar uma nova implementação para o grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se ainda não estiverem selecionados, selecione a subscrição Do Azure e o grupo de recursos para o qual pretende implementar. Em seguida, selecione **Deploy**.

   ![Definições da implementação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se aparecer a caixa **de parâmetros de edição,** forneça os nomes de recursos para utilizar para a sua aplicação lógica e a sua aplicação de função Azure na implementação e, em seguida, guarde as suas definições. Certifique-se de que utiliza um nome globalmente único para a sua aplicação de funções.

   ![Forneça nomes para a sua aplicação lógica e aplicação de função](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio começa a ser implantado para o seu grupo de recursos especificado, o estado de implementação da sua solução aparece na janela de **saída** do Estúdio Visual. 
   Após a implementação termina, a sua aplicação lógica está ao vivo no portal Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Editar a sua aplicação lógica no Estúdio Visual

Para editar a sua aplicação lógica após a implementação, abra a sua aplicação lógica utilizando o Logic App Designer no Visual Studio.

1. No Solution Explorer, a partir do menu de atalho do ficheiro azuredeploy.json, **selecione Open With Logic App Designer**.

   ![Open azuredeploy.json in Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

1. Depois da caixa **Logic App Properties** aparecer, no âmbito da **Subscrição,** selecione a sua subscrição Azure se ainda não estiver selecionada. No **Grupo de Recursos,** selecione o grupo de recursos e a localização onde implementou a sua solução e, em seguida, selecione **OK**.

   ![Propriedades de aplicativos lógicos](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Após a abertura do Logic App Designer, pode continuar a adicionar passos ou alterar o fluxo de trabalho e guardar as suas atualizações.

   ![App lógica aberta no Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Crie o seu projeto Funções Azure

Para criar o seu projeto e função funções, utilizando JavaScript, Python, F#, PowerShell, Batch ou Bash, siga os passos em [Trabalho com ferramentas nucleares de funções azure](../azure-functions/functions-run-local.md). Para desenvolver a sua função Azure utilizando C# dentro da sua solução, utilize uma biblioteca de classe C# seguindo os passos em [Publicar uma biblioteca de classe .NET como App de Funções](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Implementar funções do Estúdio Visual

O seu modelo de implementação implementa quaisquer funções Azure que tenha na sua solução a partir do repo Git especificado por variáveis no ficheiro azuredeploy.json. Se criar e autorizar o seu projeto Funções na sua solução, pode verificar esse projeto no controlo de origem Git (por exemplo, GitHub ou Azure DevOps) e, em seguida, atualizar a `repo` variável de modo a que o modelo implemente a sua função Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerir aplicativos lógicos e ver o histórico de executar

Para aplicações lógicas já implantadas no Azure, ainda pode editar, gerir, ver o histórico de executar e desativar essas aplicações do Visual Studio.

1. Do menu **View** em Estúdio Visual, abra **o Cloud Explorer.**

1. Em **todas as subscrições,** selecione a subscrição Azure associada às aplicações lógicas que pretende gerir e, em seguida, selecione **Apply**.

1. Em **Aplicações Lógicas,** selecione a sua aplicação lógica. A partir do menu de atalho da aplicação, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

Já pode descarregar a aplicação lógica já publicada no seu projeto de grupo de recursos. Assim, embora possa ter iniciado uma aplicação lógica no portal Azure, ainda pode importar e gerir essa aplicação no Visual Studio. Para mais informações, consulte [Gerir aplicações lógicas com o Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)

## <a name="next-steps"></a>Passos seguintes

* [Gerir aplicações lógicas com o Visual Studio](manage-logic-apps-with-visual-studio.md)

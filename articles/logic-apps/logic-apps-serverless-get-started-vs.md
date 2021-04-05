---
title: Construa a primeira aplicação sem servidor no Visual Studio
description: Construa, implemente e gere uma aplicação sem servidor utilizando apps Azure Logic e Azure Functions em Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96749152"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Construa a sua primeira aplicação sem servidor utilizando apps Azure Logic e Azure Functions em Visual Studio

Pode desenvolver e implementar rapidamente aplicações em nuvem utilizando as ferramentas e capacidades sem servidor em Azure, tais como [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Azure Functions](../azure-functions/functions-overview.md). Este artigo mostra como começar a construir uma aplicação sem servidor, que usa uma aplicação lógica que chama uma função Azure, no Visual Studio. Para saber mais sobre soluções sem servidor no Azure, consulte [O Azure Serverless com Funções e Aplicações Lógicas](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Para construir uma aplicação sem servidor no Visual Studio, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* As seguintes ferramentas. Se ainda não os tem, descarregue-os e instale-os.

  * [Visual Studio 2019, 2017 ou 2015 (Comunidade ou outra edição)](https://aka.ms/download-visual-studio). 
  Este início rápido utiliza o Visual Studio Community 2017, que é gratuito.

    > [!IMPORTANT]
    > Quando instalar o Visual Studio 2019 ou 2017, certifique-se de que seleciona a carga de trabalho de desenvolvimento do **Azure.**

  * [Microsoft Azure SDK para .NET (versão 2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Ferramentas de aplicações lógicas Azure para a versão visual do Estúdio que deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) for localmente depuração Funções.

* Acesso à web enquanto utiliza o Designer de Aplicações Lógicas incorporado.

  O estruturador precisa de uma ligação à Internet para criar recursos no Azure e ler as propriedades e os dados a partir de conectores na sua aplicação lógica. 
  Por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador verifica se a instância do CRM tem propriedades personalizadas e predefinidas disponíveis.

## <a name="create-a-resource-group-project"></a>Criar um projeto de grupo de recursos

Para começar, crie um [projeto do Grupo de Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) para a sua aplicação sem servidor. No Azure, cria recursos dentro de um *grupo de recursos*, que é uma coleção lógica que utiliza para organizar, gerir e implantar recursos para uma aplicação inteira como um único ativo. Para uma aplicação sem servidor no Azure, o seu grupo de recursos inclui recursos tanto para apps Azure Logic como para Funções Azure. Saiba mais sobre [recursos e grupos de recursos do Azure](../azure-resource-manager/management/overview.md).

1. Inicie o Visual Studio e inicie seduca através da sua conta Azure.

1. No menu **Ficheiro**, selecione **Novo** > **Projeto**.

   ![Criar novo projeto no Estúdio Visual](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Em seguida, selecione **Cloud**  >  **Azure Resource Group**.

   > [!NOTE]
   > Se o projeto **Cloud** ou **Azure Resource Group** não existir, certifique-se de que instalou o Azure SDK para o Visual Studio.

   Se estiver a utilizar o Visual Studio 2019, siga estes passos:

   1. Na nova caixa de **projeto,** selecione o modelo de projeto **do Grupo de Recursos Azure** para visual C# ou Visual Basic e, em seguida, selecione **Next**.

   1. Forneça o nome e outras informações do projeto que pretende utilizar para o grupo de recursos Azure. Quando concluir, selecione **Criar**.

1. Dê ao seu projeto um nome e uma localização e, em seguida, selecione **OK**.

   O Visual Studio pede-lhe que selecione um modelo da lista de modelos. 
   Este exemplo utiliza um modelo Azure QuickStart para que possa construir uma aplicação sem servidor que inclua uma aplicação lógica e uma chamada para uma função Azure.

   > [!TIP]
   > Em cenários em que não queira pré-desapromupar a sua solução num grupo de recursos Azure, pode utilizar o modelo **de Aplicação Lógica** em branco, que apenas cria uma aplicação lógica vazia.

1. A partir dos modelos Show desta lista **de localização,** selecione **Azure QuickStart (github.com/Azure/azure-quickstart-templates)**.

1. Na caixa de pesquisa, introduza "logic-app" como filtro. A partir dos resultados, selecione o modelo **de aplicação de aplicação lógica 101 lógica e aplicação de função.**

   ![Selecione o modelo Azure QuickStart](./media/logic-apps-serverless-get-started-vs/select-template.png)

   O Visual Studio cria e abre uma solução para o seu projeto de grupo de recursos. 
   O modelo Azure QuickStart que selecionou cria um modelo de implementação chamado azuredeploy.jsdentro do seu projeto de grupo de recursos. Este modelo de implementação inclui a definição para uma simples aplicação lógica que é desencadeada por um pedido HTTP, chama uma função Azure e devolve o resultado como uma resposta HTTP.

   ![Nova solução sem servidor](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Em seguida, desloque a sua solução para Azure. Tem de o fazer antes de poder abrir o modelo de implementação e rever os recursos para a sua aplicação sem servidor.

## <a name="deploy-your-solution"></a>Implemente a sua solução

Antes de poder abrir a sua aplicação lógica no Logic App Designer em Visual Studio, deve ter um grupo de recursos Azure que já está implantado no Azure. O designer pode então criar ligações a recursos e serviços na sua aplicação lógica. Para esta tarefa, siga estes passos para implementar a sua solução do Visual Studio para o portal Azure:

1. No Solution Explorer, a partir do menu de atalho do seu projeto de recursos, **selecione Implementar**  >  **Novo**.

   ![Criar nova implementação para grupo de recursos](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se ainda não estiverem selecionados, selecione a sua subscrição Azure e o grupo de recursos para o qual pretende implementar. Em seguida, **selecione Implementar**.

   ![Definições da implementação](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se aparecer a caixa **de parâmetros de edição,** forneça os nomes dos recursos para utilizar para a sua aplicação lógica e para a sua aplicação de função Azure na implementação e, em seguida, guarde as suas definições. Certifique-se de que utiliza um nome globalmente único para a sua aplicação de função.

   ![Fornecer nomes para a sua app lógica e app de função](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando o Visual Studio começa a ser implantado para o seu grupo de recursos especificado, o estado de implantação da sua solução aparece na janela **de saída** do Estúdio Visual. 
   Após a implementação terminada, a sua aplicação lógica está ao vivo no portal Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Edite a sua aplicação lógica no Visual Studio

Para editar a sua aplicação lógica após a implementação, abra a sua aplicação lógica utilizando o Logic App Designer no Visual Studio.

1. No Solution Explorer, a partir do menu de atalho do azuredeploy.jsem ficheiro, **selecione Open With Logic App Designer**.

   ![Abra azuredeploy.jsem logic app designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

1. Depois de aparecer a caixa **Logic App Properties,** em **Subscrição,** selecione a subscrição do Azure se ainda não estiver selecionada. No **Grupo de Recursos,** selecione o grupo de recursos e a localização onde implementou a sua solução e, em seguida, selecione **OK**.

   ![Propriedades de aplicativos lógicos](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Após a abertura do Logic App Designer, pode continuar a adicionar passos ou alterar o fluxo de trabalho e guardar as suas atualizações.

   ![Aplicativo de lógica aberto no Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Crie o seu projeto Azure Functions

Para criar o seu projeto e função funções utilizando JavaScript, Python, F#, PowerShell, Batch ou Bash, siga os passos em [Work with Azure Functions Core Tools](../azure-functions/functions-run-local.md). Para desenvolver a sua função Azure utilizando C# dentro da sua solução, utilize uma biblioteca de classe C# seguindo os passos na [Publicação de uma biblioteca de classe .NET como uma App de Função](https://azure.microsoft.com/blog/).

## <a name="deploy-functions-from-visual-studio"></a>Implementar funções do Visual Studio

O seu modelo de implementação implementa quaisquer funções Azure que tenha na sua solução a partir do repo Git que é especificado por variáveis no azuredeploy.jsno ficheiro. Se criar e autorizar o seu projeto Funções na sua solução, pode verificar esse projeto no controlo de fontes do Git (por exemplo, GitHub ou Azure DevOps) e, em seguida, atualizar a `repo` variável de modo a que o modelo implemente a sua função Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gerir aplicativos lógicos e ver a história do run

Para aplicações lógicas já implementadas no Azure, ainda é possível editar, gerir, ver o histórico de execução e desativar essas aplicações do Visual Studio.

1. A partir do menu **Ver** no Estúdio Visual, open **Cloud Explorer**.

1. Em **Todas as subscrições**, selecione a subscrição Azure associada às aplicações lógicas que pretende gerir e, em seguida, selecione **Apply**.

1. Em **Aplicações Lógicas,** selecione a sua aplicação lógica. A partir do menu de atalho da aplicação, selecione **Open with Logic App Editor**.

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

Agora pode descarregar a aplicação lógica já publicada no seu projeto de grupo de recursos. Assim, apesar de ter iniciado uma aplicação lógica no portal Azure, ainda pode importar e gerir essa aplicação no Visual Studio. Para obter mais informações, consulte [Gerir aplicações lógicas com o Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)

## <a name="next-steps"></a>Passos seguintes

* [Gerir aplicações lógicas com o Visual Studio](manage-logic-apps-with-visual-studio.md)

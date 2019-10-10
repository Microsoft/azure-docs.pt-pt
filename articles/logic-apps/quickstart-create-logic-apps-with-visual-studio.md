---
title: Criar fluxos de trabalho automatizados com o Visual Studio – aplicativos lógicos do Azure
description: Automatizar tarefas, processos de negócios e fluxos de trabalho para a integração corporativa usando o aplicativo lógico do Azure e o Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.manager: carmonm
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/25/2019
ms.openlocfilehash: 47b7609fe111ecbe41a161bfbff1f7225ad66357
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165921"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Início rápido: criar tarefas, processos e fluxos de trabalho automatizados com os aplicativos lógicos do Azure-Visual Studio

Com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o Visual Studio, pode criar fluxos de trabalho para automatizarem tarefas e processos que integram aplicações, dados, sistemas e serviços nas empresas e organizações. Este guia de início rápido mostra como você pode projetar e criar esses fluxos de trabalho criando aplicativos lógicos no Visual Studio e implantando esses aplicativos no Azure. Embora você possa executar essas tarefas no portal do Azure, o Visual Studio permite que você adicione seus aplicativos lógicos ao controle do código-fonte, publique diferentes versões e crie Azure Resource Manager modelos para diferentes ambientes de implantação.

Se você for novo no aplicativo lógico do Azure e quiser apenas os conceitos básicos, experimente o guia [de início rápido para criar um aplicativo lógico no portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md). O designer do aplicativo lógico funciona de modo semelhante no portal do Azure e no Visual Studio.

Neste guia de início rápido, você cria o mesmo aplicativo lógico com o Visual Studio como o guia de início rápido portal do Azure. Esse aplicativo lógico monitora o RSS feed de um site e envia um email para cada novo item nesse feed. Seu aplicativo lógico concluído é semelhante a este fluxo de trabalho de alto nível:

![Visão geral do fluxo de trabalho do aplicativo lógico de alto nível](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Transfira e instale estas ferramentas, se ainda não as tiver:

  * [Visual Studio 2019, 2017 ou 2015-Community Edition ou superior](https://aka.ms/download-visual-studio). 
  Este guia de início rápido usa o Visual Studio Community 2017.

    > [!IMPORTANT]
    > Ao instalar o Visual Studio 2019 ou 2017, certifique-se de selecionar a carga de trabalho de **desenvolvimento do Azure** .

  * [Microsoft Azure SDK para .net (2.9.1 ou posterior)](https://azure.microsoft.com/downloads/). 
  Saiba mais sobre o [SDK do Azure para .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [O Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Ferramentas de aplicativos lógicos do Azure para a versão do Visual Studio que você deseja:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Pode transferir e instalar as Ferramentas do Azure Logic Apps diretamente a partir do Visual Studio Marketplace ou saiba [como instalar esta extensão a partir do Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Certifique-se de que reinicia o Visual Studio depois de concluir a instalação.

* Para trabalhar com diferentes ambientes do Azure, como o Azure governamental, você pode instalar e usar a extensão do [seletor de ambiente do Azure](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector) , que ajuda você a alternar com mais facilidade entre ambientes. Para obter mais informações, consulte [introdução à extensão do seletor do ambiente do Azure Visual Studio](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/).

* Acesso à Web durante a utilização do Estruturador da Aplicação Lógica incorporado

  O designer precisa de uma conexão com a Internet para criar recursos no Azure e para ler propriedades e dados de conectores em seu aplicativo lógico. 
  Por exemplo, para conexões do Dynamics CRM Online, o designer verifica a instância do CRM em busca de propriedades padrão e personalizadas.

* Uma conta de e-mail suportada pelo Logic Apps, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Este exemplo usa o Outlook do Office 365. Se utilizar outro fornecedor, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Criar projeto do Grupo de Recursos do Azure

Para começar, crie um [Projeto do Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Saiba mais sobre [recursos e grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

1. Inicie o Visual Studio. Entre com sua conta do Azure.

1. No menu **Ficheiro**, selecione **Novo** > **Projeto**. (Teclado: Ctrl+Shift+N)

   ![Criar novo projeto do Visual Studio](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Em **Instalado**, selecione **Visual C#** ou **Visual Basic**. Selecione **Cloud** > **Grupo de Recursos do Azure**. Atribua um nome ao projeto, por exemplo:

   ![Criar projeto do Grupo de Recursos do Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Os nomes de grupos de recursos podem conter apenas letras, números, pontos (`.`), sublinhados (`_`), hifens (`-`) e parênteses (`(`, `)`), mas não podem *terminar* com pontos (`.`).
   >
   > Se a **nuvem** ou o **grupo de recursos do Azure** não aparecer, certifique-se de instalar o SDK do Azure para Visual Studio.

   Se você estiver usando o Visual Studio 2019, siga estas etapas:

   1. Na caixa **criar um novo projeto** , selecione o projeto do **grupo de recursos do Azure** para Visual C# ou Visual Basic. Escolha **Seguinte**.

   1. Forneça um nome para o grupo de recursos do Azure que você deseja usar e outras informações do projeto. Escolha **Criar**.

1. Na lista modelo, selecione o modelo **aplicativo lógico** . Escolha **OK**.

   ![Selecione o modelo "aplicativo lógico" para criar o projeto](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Depois de o Visual Studio criar o projeto, o Explorador de Soluções é aberto e mostra a sua solução. 
   Em sua solução, o arquivo **LogicApp. JSON** não apenas armazena sua definição de aplicativo lógico, mas também é um modelo de Azure Resource Manager que você pode usar para a implantação.

   ![O Explorador de Soluções mostra a nova solução de aplicação lógica e o ficheiro de implementação](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Criar uma aplicação lógica em branco

Quando você tiver seu projeto de grupo de recursos do Azure, crie seu aplicativo lógico com o modelo de **aplicativo lógico em branco** .

1. No Gerenciador de Soluções, abra o menu de atalho do arquivo **LogicApp. JSON** . Selecione **Abrir com o Estruturador da Aplicação Lógica**. (Teclado: Ctrl+L)

   ![Abrir o ficheiro .json da aplicação lógica com o Estruturador da Aplicação Lógica](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio.

   O Visual Studio solicita sua assinatura do Azure e um grupo de recursos do Azure para criar e implantar recursos para seu aplicativo lógico e conexões.

1. Para **assinatura**, selecione sua assinatura do Azure. Para **grupo de recursos**, selecione **criar novo** para criar um novo grupo de recursos do Azure.

   ![Selecionar a subscrição do Azure, o grupo de recursos e a localização dos recursos](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Definição | Valor de exemplo | Descrição |
   | ------- | ------------- | ----------- |
   | Lista do perfil de utilizador | Contoso <br> jamalhartnett@contoso.com | Por predefinição, a conta que utilizou para iniciar sessão |
   | **Subscrição** | "Pay-As-You-Go" <br> (jamalhartnett@contoso.com) | O nome da sua subscrição do Azure e a conta associada |
   | **Grupo de Recursos** | MyLogicApp-RG <br> (E.U.A. Oeste) | O grupo de recursos do Azure e o local para armazenar e implantar os recursos do aplicativo lógico |
   | **Localização** | MyLogicApp-RG2 <br> (E.U.A. Oeste) | Uma localização diferente, se não quiser utilizar a localização do grupo de recursos |
   ||||

1. O designer de aplicativos lógicos abre uma página que mostra um vídeo de introdução e gatilhos comumente usados. Role para baixo após o vídeo e os gatilhos para **modelos**e selecione **aplicativo lógico em branco**.

   ![Selecionar "Aplicação Lógica em Branco"](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Criar fluxo de trabalho da aplicação lógica

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) de RSS que é acionado quando um novo item de feed é exibido. Cada aplicativo lógico começa com um gatilho, que é disparado quando critérios específicos são atendidos. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica que executa o fluxo de trabalho.

1. No designer de aplicativo lógico, na caixa de pesquisa, escolha **tudo**.
Na caixa de pesquisa, digite "RSS". Na lista de gatilhos, selecione este gatilho: **quando um item de feed é publicado-RSS**

   ![Criar a sua aplicação lógica com a adição de um acionador e ações](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Depois que o gatilho aparecer no designer, conclua a criação do aplicativo lógico seguindo as etapas do fluxo de trabalho no [portal do Azure início rápido](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)e, em seguida, retorne a este artigo. Quando tiver terminado, a aplicação lógica terá o aspeto deste exemplo:

   ![Exemplo de fluxo de trabalho do aplicativo lógico concluído](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Salve sua solução do Visual Studio. (Teclado: Ctrl+S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Implementar a aplicação lógica no Azure

Antes de poder executar e testar seu aplicativo lógico, implante o aplicativo no Azure do Visual Studio.

1. No Explorador de Soluções, no menu de atalho do projeto, selecione **Implementar** > **Novo...** . Se tal lhe for pedido, inicie sessão com a sua conta do Azure.

   ![Criar nova implantação de aplicativo lógico](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Para essa implantação, mantenha a assinatura padrão do Azure, o grupo de recursos e outras configurações. Escolha **implantar**.

   ![Implementar a aplicação lógica no grupo de recursos do Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Se a caixa **Editar parâmetros** for exibida, forneça um nome de recurso para seu aplicativo lógico. Salve suas configurações.

   ![Fornecer nome da implementação para a aplicação lógica](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Quando a implementação é iniciada, o estado de implementação da sua aplicação é apresentado na janela **Saída** do Visual Studio. Se o estado não for apresentado, abra a lista **Mostrar saída de** e selecione o seu grupo de recursos do Azure.

   ![Status da implantação na janela de saída do Visual Studio](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Se os conectores selecionados precisarem de entrada de você, uma janela do PowerShell será aberta em segundo plano e solicitará as senhas ou chaves secretas necessárias. Após introduzir estas informações, a implementação continua.

   ![Prompt do PowerShell para senhas ou chaves secretas](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Após a conclusão da implantação, seu aplicativo lógico fica ativo na portal do Azure e é executado no agendamento especificado (a cada minuto). Se o gatilho encontrar novos itens de feed, o gatilho será acionado, o que cria uma instância de fluxo de trabalho que executa as ações do seu aplicativo lógico. Seu aplicativo lógico envia um email para cada novo item. Caso contrário, se o gatilho não encontrar novos itens, o gatilho não será acionado e "ignorará" instanciando o fluxo de trabalho. Seu aplicativo lógico aguarda até o próximo intervalo antes de verificar.

   Aqui estão os emails de exemplo enviados por esse aplicativo lógico. 
   Se não receber nenhum e-mail, veja a pasta de e-mail de lixo.

   ![O Outlook envia uma mensagem de e-mail por cada item de RSS novo](./media/quickstart-create-logic-apps-with-visual-studio/example-outlook-email.png)

Parabéns, você criou e implantou com êxito seu aplicativo lógico com o Visual Studio. Para gerir a sua aplicação lógica e analisar o respetivo histórico de execuções, consulte [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Adicionar novo aplicativo lógico

Quando você tiver um projeto existente do grupo de recursos do Azure, poderá adicionar um novo aplicativo lógico em branco a esse projeto usando a janela estrutura de tópicos JSON.

1. Em Gerenciador de Soluções, abra o arquivo `<logic-app-name>.json`.

1. No menu **Exibir** , selecione outro**estrutura de tópicos JSON**do **Windows** > .

1. Para adicionar um recurso ao arquivo de modelo, escolha **Adicionar recurso** na parte superior da janela estrutura de tópicos JSON. Ou, na janela estrutura de tópicos JSON, clique com o botão direito do mouse em **recursos**e selecione **Adicionar novo recurso**.

   ![Na janela estrutura de tópicos JSON, adicione novo recurso](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Na caixa de diálogo **Adicionar recurso** , localize e selecione **aplicativo lógico**. Nomeie seu aplicativo lógico e escolha **Adicionar**.

   ![Adicionar novo recurso de aplicativo lógico ao projeto](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar seu aplicativo lógico, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com a mesma conta utilizada para criar a aplicação lógica.

1. No menu principal do Azure, selecione **Grupos de recursos**.
Selecione o grupo de recursos do aplicativo lógico e selecione **visão geral**.

1. Na página **Descrição geral**, selecione **Eliminar grupo de recursos**. Introduza o nome do grupo de recursos como confirmação e escolha **Eliminar**.

   ![Eliminar grupo de recursos da aplicação lógica](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Elimine a solução do Visual Studio do seu computador local.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou, implementou e executou a sua aplicação lógica com o Visual Studio. Para saber mais sobre como gerenciar e executar a implantação avançada para aplicativos lógicos com o Visual Studio, consulte estes artigos:

> [!div class="nextstepaction"]
> * [Gerir aplicações lógicas com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

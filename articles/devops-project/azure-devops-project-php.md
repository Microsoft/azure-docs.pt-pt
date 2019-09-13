---
title: 'Início rápido: Criar um pipeline de CI/CD para PHP com Azure DevOps Projects'
description: DevOps Projects facilita a introdução ao Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899580"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para PHP com Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada que cria recursos do Azure e configura um pipeline de integração contínua (CI) e entrega contínua (CD) para seu aplicativo PHP no Azure Pipelines.  

Se você não tiver uma assinatura do Azure, poderá obter uma gratuitamente por meio de [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

 DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização gratuita do Azure DevOps ou usar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione o ícone **criar um recurso** e procure **DevOps Projects**.  

3. Selecione **Criar**.

    ![Iniciando a configuração de entrega contínua](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o aplicativo de exemplo PHP.  
        Os exemplos de PHP incluem uma opção de várias estruturas de aplicativo. A estrutura de exemplo padrão é Laravel. 
        
2. Deixe a configuração padrão e, em seguida, selecione **Avançar**.  

1. O aplicativo Web para contêineres é o destino de implantação padrão.  
    A estrutura do aplicativo, que você escolheu anteriormente, dita o tipo de destino de implantação do serviço do Azure que está disponível aqui.  Deixe o serviço padrão e, em seguida, selecione **Avançar**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente. 

    a. Escolha um nome para seu projeto no Azure DevOps. 
    
    b. Selecione sua assinatura e local do Azure, insira um nome para seu aplicativo e, em seguida, selecione **concluído**.   
        Após alguns minutos, o painel de DevOps Projects é exibido na portal do Azure. Um aplicativo de exemplo é configurado em um repositório na sua organização do Azure DevOps, uma compilação é executada e seu aplicativo é implantado no Azure. Esse painel fornece visibilidade do seu repositório de códigos, do pipeline de CI/CD e de seu aplicativo no Azure.  
        
2. Selecione **procurar** para exibir o aplicativo em execução.

    ![Vista do Dashboard](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Projects configurou automaticamente um gatilho de Build e versão de CI.  Agora está pronto para colaborar com uma equipa na sua aplicação PHP, com um processo de CI/CD que implementa automaticamente o seu trabalho mais recente no seu site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

 DevOps Projects cria um repositório git no Azure Repos ou no GitHub. Para exibir o repositório e fazer alterações de código em seu aplicativo, execute as seguintes etapas:

1. À esquerda do painel de DevOps Projects, selecione o link para o Branch mestre.   
    Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL de clone do repositório, selecione **Clone** na parte superior direita do browser.   
    Pode clonar o repositório Git no seu IDE preferido. Nas próximas etapas, use o navegador da Web para fazer e confirmar alterações de código diretamente no Branch mestre.

1. À esquerda, vá para o arquivo **Resources/views/Welcome. Blade. php** .

1. Selecione **Editar**e, em seguida, faça uma alteração em algum texto.  Por exemplo, altere algum texto para uma das etiquetas div.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No navegador, vá para o painel de DevOps Projects.  
Agora você deve ver uma compilação em andamento. As alterações que você acabou de fazer são criadas automaticamente e implantadas por meio de um pipeline de CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

 DevOps Projects configura automaticamente um pipeline de CI/CD completo no Azure Pipelines. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
    Esse link abre uma guia do navegador e o pipeline de compilação para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as **reticências** (...).  
    Um menu exibe várias opções, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa uma variedade de tarefas, como busca de fontes do repositório git, restauração de dependências e publicação de saídas que são usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do pipeline de compilação para algo mais descritivo, selecione, **salve & fila**e, em seguida, selecione **salvar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
    O painel **histórico** exibe uma trilha de auditoria de suas alterações recentes para a compilação. Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
      Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
     DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um pipeline, que define o processo de liberação. 

12. Em **Artefactos**, selecione **Remover**.  
    O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato. 

1. Junto a **Drop** ícone, selecione a **acionador de implementação contínua**.   
    Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que há um novo artefato de compilação disponível.  Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**.  
        As tarefas são as atividades que executa o processo de implementação.  Neste exemplo, uma tarefa foi criada para ser implantada no serviço Azure App.

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma de suas versões e, em seguida, selecione **abrir**.  
        Estão disponíveis vários menus para explorar nesta vista, como um resumo de versões, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
        Esta vista mostra as confirmações de código que estão associadas a implementação específica. 

1. Selecionar **Registos**.  
        Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir Azure App serviço e outros recursos relacionados quando não precisar mais deles. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Quando você configurou o processo de CI/CD, os pipelines de compilação e de versão foram criados automaticamente. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o pipeline de CI/CD, consulte este tutorial:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

---
title: Criar um pipeline de CI/CD para node. js com Azure DevOps Projects
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 11edeb35119e2c598fd83fd89c65ba4dc4679650
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256135"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Início rápido: criar um pipeline de CI/CD no Azure Pipelines para node. js com Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada que cria recursos do Azure e configura um pipeline de integração contínua (CI) e entrega contínua (CD) para seu aplicativo node. js no Azure Pipelines.  

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e procure **DevOps Projects**. 

    ![Criar recurso de configuração de entrega contínua](_img/azure-devops-project-nodejs/create-azure-resource.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e o serviço do Azure

1. Selecione o aplicativo de exemplo node. js.  
    Os exemplos Node.js incluem várias opções de arquiteturas de aplicações.

1. A estrutura de exemplo padrão é Express. js. Deixe a configuração padrão e, em seguida, selecione **Avançar**.  
    **A Aplicação Web no Windows** é o destino de implementação predefinido.  A estrutura do aplicativo, que você escolheu anteriormente, dita o tipo de destino de implantação de serviço do Azure disponível aqui.  

2. Deixe o serviço padrão e, em seguida, selecione **Avançar**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure 

1. Crie uma nova organização de DevOps do Azure ou escolha uma organização existente. 

    a. Insira um nome para o seu projeto.

    b. Selecione sua assinatura e local do Azure, insira um nome para seu aplicativo e, em seguida, selecione **concluído**.  
    Depois de alguns minutos, o painel de DevOps Projects é exibido na portal do Azure. Um aplicativo de exemplo é configurado em um repositório em sua organização DevOps do Azure, uma compilação é executada e seu aplicativo é implantado no Azure. Esse painel fornece visibilidade do seu repositório de códigos, do pipeline de CI/CD e de seu aplicativo no Azure.
     
3. Selecione **procurar** para exibir o aplicativo em execução.

    ![Exibição de painel do pipeline de CI/CD](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 
    
DevOps Projects configurou automaticamente um gatilho de Build e versão de CI.  Agora você está pronto para colaborar com uma equipe em um aplicativo node. js com um processo de CI/CD que implanta automaticamente o trabalho mais recente em seu site da Web.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Projects cria um repositório git no Azure Repos ou no GitHub. Execute as etapas a seguir para exibir o repositório e fazer alterações de código em seu aplicativo.

1. À esquerda do painel de DevOps Projects, selecione o link para o Branch mestre.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para exibir a URL de clone do repositório, selecione **clonar** na parte superior direita do navegador.   
    Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do navegador, vá para o arquivo **views/index. Pug** .

1. Selecione **Editar**e, em seguida, faça uma alteração no título H2.  
    Por exemplo, digite introdução imediatamente **com Azure DevOps Projects** ou faça alguma outra alteração.

1. Selecione **confirmar**e salve as alterações.

1. No navegador, vá para o painel de DevOps Projects.   
Agora você deve ver uma compilação em andamento. As alterações que você acabou de fazer são criadas automaticamente e implantadas por meio de um pipeline de CI/CD.

## <a name="examine-the-azure-cicd-pipeline"></a>Examinar o pipeline de CI/CD do Azure

Na etapa anterior, DevOps Projects configurou automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline, conforme necessário. Execute as etapas a seguir para se familiarizar com os pipelines de compilação e versão.

1. Na parte superior do painel de DevOps Projects, selecione **criar pipelines**.  
Esse link abre uma guia do navegador e o pipeline de compilação para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...).  
    Essa ação abre um menu em que você pode iniciar várias atividades, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Nesse painel, você pode examinar as várias tarefas para o pipeline de compilação.  
A compilação executa uma variedade de tarefas, como busca de fontes do repositório git, restauração de dependências e publicação de saídas que são usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do pipeline de compilação para algo mais descritivo, selecione **salvar & fila**e, em seguida, selecione **salvar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
No painel **histórico** , você vê uma trilha de auditoria de suas alterações recentes para a compilação.  Azure Pipelines controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione **Acionadores**.   
 DevOps Projects criou automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
 DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. À esquerda, selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
O pipeline de lançamento define o processo de liberação.

12. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação que examinou nos passos anteriores produz a saída utilizada para o artefacto. 

1. Ao lado do ícone de **soltar** , selecione o **gatilho de implantação contínua**.  
Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que há um novo artefato de compilação disponível. Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam execução manual. 


1. À esquerda, selecione **tarefas**.   
As tarefas são as atividades que o processo de implantação executa. Neste exemplo, uma tarefa foi criada para ser implantada no serviço Azure App.


1. À direita, selecione **Exibir versões**.  
Esta vista mostra um histórico das versões.

1. Selecione as reticências (...) ao lado de uma de suas versões e, em seguida, selecione **abrir**.  
Há vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.   
Esta exibição mostra as confirmações de código associadas à implantação específica.

1. Selecione **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir Azure App serviço e outros recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade **excluir** no painel DevOps Projects.


## <a name="next-steps"></a>Passos seguintes

Quando você configurou o processo de CI/CD, os pipelines de compilação e de versão foram criados automaticamente. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]

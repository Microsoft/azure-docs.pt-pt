---
title: 'Início rápido: Criar um pipeline CI/CD para o Python com projetos de DevOps do Azure'
description: Projetos de DevOps torna mais fácil começar a utilizar com o Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 695056be67294c6a0787bad61bf5ee00378275b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111434"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>Criar um pipeline CI/CD para o Python com projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada que cria os recursos do Azure e configura um pipeline de entrega contínua (CD) para a sua aplicação de Python e integração contínua (CI).  

Se não tiver uma subscrição do Azure, pode obter uma gratuitamente através de [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

 Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure.  Pode criar uma nova organização de DevOps do Azure gratuita ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

2. Selecione o **criar um recurso** ícone no painel esquerdo e, em seguida, procure **projetos de DevOps**.  

3. Selecione **Criar**.

    ![Iniciar a configuração da Entrega Contínua](_img/azure-devops-project-python/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione a aplicação de exemplo do Python. Os exemplos Python incluem várias opções de arquiteturas de aplicações.

1. A estrutura de exemplo do padrão é Django. Deixe a predefinição e, em seguida, selecione **seguinte**.    
Aplicação Web para contentores é o destino de implementação do padrão. A estrutura da aplicação, que escolheu anteriormente, dita o tipo de destino da implementação do serviço do Azure disponível aqui. 

3. Deixe o serviço predefinido e, em seguida, selecione **seguinte**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps ou escolha uma organização existente. 

    a. Introduza um nome para o seu projeto de DevOps do Azure.  

    b. Selecione a subscrição do Azure e a localização, introduza um nome para a sua aplicação e, em seguida, selecione **feito**.  
     Após alguns minutos, o dashboard do projeto é apresentado no portal do Azure. Um aplicativo de exemplo está configurado num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o Azure. Este dashboard fornece visibilidade para o repositório de código, seu pipeline CI/CD e a sua aplicação no Azure.  
    
2. Selecione **procurar** para ver a sua aplicação em execução.

    ![Vista do Dashboard](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   Projetos de DevOps configura automaticamente uma compilação CI e a versão de Acionador. Agora, está pronto para colaborar com uma equipe de uma aplicação Python ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente ao seu Web site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

 Projetos de DevOps cria um repositório de Git em repositórios do Azure ou do GitHub. Para ver o repositório e fazer alterações de código no aplicativo, faça o seguinte: 

1. No lado esquerdo do dashboard de projetos de DevOps, selecione a ligação para o ramo principal.  
        Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL de clone do repositório, selecione **Clone** na parte superior direita do browser.   
Pode clonar o repositório Git no seu IDE preferido.  Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. À esquerda, vá para o **app/templates/app/index.html** ficheiro.

1. Selecione **Editar** e faça uma alteração a algumas partes do texto. Por exemplo, altere algum texto para uma das etiquetas div.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No seu browser, aceda ao dashboard de projetos de DevOps.   
    Deverá ver uma compilação em curso. As alterações que acabou de criar são automaticamente criadas e implementadas através de um pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

No passo anterior, os projetos de DevOps configurado automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os pipelines de compilação e versão, faça o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
Um separador do browser apresenta o pipeline de compilação para o novo projeto.

1. Aponte para o **Status** campo e, em seguida, selecione a **reticências** (...).  
        Um menu apresenta várias opções, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
        A compilação realiza várias tarefas, como a obtenção de origens do restauro de dependências, o repositório de Git e saídas de publicação para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
        Verá um registo de auditoria das alterações recentes da compilação.  DevOps do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
         Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
        Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **criar e lançar**e, em seguida, escolha **versões**.   
 Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
Pipeline de versões define o processo de liberação.  
        
12. Em **Artefactos**, selecione **Remover**.   
O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione a **acionador de implementação contínua**.  
        O pipeline de versão tem um acionador de CD ativado, o que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**.   
As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implantar o App Service do Azure.

1. À direita, selecione **ver versões** para apresentar um histórico de versões.  
        
1. Selecione as reticências (...) junto a um dos seus lançamentos e, em seguida, selecione **aberto**.  
        Existem vários menus para explorar a partir desta vista, por exemplo, uma versão em Resumo, testes e itens de trabalho associados.

1. Selecione **Consolidações**. 
        Esta vista mostra as consolidações do código que estão associadas a implementação específica. 

1. Selecionar **Registos**.   
Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e os recursos relacionados, quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Quando configurou o seu processo de CI/CD, compilação e lançar pipelines criados automaticamente. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

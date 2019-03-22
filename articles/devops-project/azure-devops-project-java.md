---
title: 'Início rápido: Criar um pipeline de CI/CD para Java com projetos de DevOps do Azure'
description: Projetos de DevOps torna mais fácil começar a utilizar no Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
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
ms.openlocfilehash: 38be92a006ae6dbb7a550d2d91d886df820fb883
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093570"
---
# <a name="create-a-cicd-pipeline-for-java-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para Java com projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada que cria os recursos do Azure e configura uma integração contínua (CI) e o pipeline de entrega contínua (CD) para a sua aplicação Java nos Pipelines do Azure.  

Se não tiver uma subscrição do Azure, pode obter uma gratuitamente através de [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

  Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e, em seguida, procure **projetos de DevOps**.  

2. Selecione **Criar**.

    ![Iniciar a configuração da Entrega Contínua](_img/azure-devops-project-java/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione a aplicação de exemplo de Java.  
Os exemplos de Java incluem várias opções de arquiteturas de aplicações.

1. A estrutura de exemplo do padrão é Spring. Deixe a predefinição e, em seguida, selecione **seguinte**.  Aplicação Web para contentores é o destino de implementação do padrão. A estrutura da aplicação, que escolheu anteriormente, dita o tipo de destino da implementação do serviço do Azure disponível aqui. 

2. Deixe o serviço predefinido e, em seguida, selecione **seguinte**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure 

1. Crie uma nova organização do Azure DevOps ou escolha uma organização existente. 

    a. Escolha um nome para o seu projeto. 

    b. Selecione a subscrição do Azure e a localização, escolha um nome para a sua aplicação e, em seguida, selecione **feito**.  
    Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Um aplicativo de exemplo está configurado num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o Azure. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.
    

2. Selecione **procurar** para ver a sua aplicação em execução.

    ![Vista do Dashboard](_img/azure-devops-project-java/dashboardnopreview.png) 
    
   Projetos de DevOps configurado automaticamente uma compilação CI e a versão de Acionador.  Agora, está pronto para colaborar com uma equipa na sua aplicação Java com um processo de CI/CD que implementa automaticamente o seu trabalho mais recente no seu site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

Projetos de DevOps cria um repositório de Git em repositórios do Azure ou do GitHub. Para ver o repositório e fazer alterações de código no aplicativo, faça o seguinte:

1. À esquerda do dashboard de projetos de DevOps, selecione a ligação para o ramo principal.  
Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL de clone do repositório, selecione **Clone** na parte superior direita do navegador.   
    Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do browser, vá para o **src/main/webapp/index.html** ficheiro.

1. Selecione **editar**e, em seguida, faça uma alteração a algumas do texto.
    Por exemplo, altere algum texto para uma das etiquetas div.

1. Selecione **consolidar**e, em seguida, guarde as alterações.

1. No seu browser, aceda ao dashboard de projetos de DevOps.   
Deverá ver uma compilação em curso. As alterações que acabou de criar são automaticamente criadas e implementadas através de um pipeline CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

 No passo anterior, os projetos de DevOps configurado automaticamente um pipeline de CI/CD completo. Explore e personalize o pipeline, conforme necessário. Siga os passos seguintes para familiarizar-se com a compilação e lançar pipelines.

1. Na parte superior do dashboard de projetos de DevOps, selecione **criar Pipelines**.  
Esse link abre um separador do browser e o pipeline de compilação para o novo projeto.

1. Aponte para o **estado** campo e, em seguida, selecione as reticências (...).  
    Esta ação abre um menu onde pode iniciar várias atividades, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
A compilação efetua uma variedade de tarefas, como a obtenção de origens do repositório Git, a restauração de dependências e a publicação de saídas que são utilizadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
Na **histórico** painel, verá uma trilha de auditoria das alterações recentes para a compilação.  Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.   
 Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.   
Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
 Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. No lado esquerdo, selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
O pipeline de lançamento contém um pipeline, que define o processo de liberação.  
    
12. Em **Artefactos**, selecione **Remover**.  
O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto. 

1. Junto a **Drop** ícone, selecione a **acionador de implementação contínua**.  
Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

1. No lado esquerdo, selecione **tarefas**.   
As tarefas são as atividades que executa o processo de implementação. Neste exemplo, foi criada uma tarefa para implantar o App Service do Azure.

1. À direita, selecione **ver versões**.  
Esta vista mostra um histórico das versões.

1. Selecione as reticências (...) junto a um dos seus lançamentos e, em seguida, selecione **aberto**.  
Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.   
Esta vista mostra as confirmações de código que estão associadas a implementação específica. 

1. Selecionar **Registos**.  
Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos associados quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes

Quando configurou o seu processo de CI/CD, compilação e lançar pipelines criados automaticamente. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

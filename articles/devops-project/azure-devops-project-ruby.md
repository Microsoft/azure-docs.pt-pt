---
title: 'Quickstart: Criar um oleoduto CI/CD para a Ruby on Rails utilizando o Arranque azure DevOps'
description: O Arranque azure DevOps facilita o arranque do Azure. Você pode lançar uma aplicação web Ruby em um serviço Azure em alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: cde959d8e075b55cb6cbb37479ca49cdd8a8c0c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233742"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-starter"></a>Crie um oleoduto CI/CD para ruby on rails utilizando o Arranque de Azure DevOps

Configure a integração contínua (CI) e a entrega contínua (CD) para a sua aplicação Ruby on Rails utilizando o Arranque azure DevOps. DevOps Starter simplifica a configuração inicial de um pipeline de construção e lançamento de Azure DevOps.

Se não tiver uma subscrição do Azure, pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

O Arranque azure DevOps cria um oleoduto CI/CD em Azure Repos. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. DevOps Starter também cria recursos Azure na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-app-and-azure-service"></a>Selecione uma aplicação de amostra e serviço Azure

1. Selecione a aplicação de amostra **ruby.**

1. Selecione o framework da aplicação **Ruby on Rails**. Quando terminar, selecione **Next**.

1. **A Aplicação Web no Linux** é o destino de implementação predefinido.  Opcionalmente, pode selecionar **web app para contentores**. O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implantação de serviços Azure que está disponível aqui. 
    
1. Selecione o serviço-alvo à sua escolha e, em seguida, selecione **Next**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure 

1. Crie uma nova organização gratuita azure DevOps ou escolha uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione a sua subscrição e localização Azure, introduza um nome para a sua aplicação e, em seguida, selecione **Done**.  
    Após alguns minutos, o painel de arranque de DevOps é apresentado no portal Azure. Uma aplicação de amostra é configurada num repo na sua organização Azure DevOps, uma construção é executada, e a sua aplicação é implantada para o Azure. 
    
    O dashboard proporciona visibilidade no seu repo de código, no seu pipeline CI/CD e na sua aplicação em Azure. À direita, **selecione Browse** para visualizar a sua aplicação de execução.

    ![Vista do Dashboard](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Cometa as alterações de código e execute o CI/CD

O Azure DevOps Starter cria um repo Git em Pipelines Azure ou GitHub. Para ver o repo e fazer alterações de código na sua aplicação, faça o seguinte:

1. No painel de arranque de DevOps, à esquerda, selecione o link para o seu ramo principal. O link abre uma vista para o recém-criado repo Git.

1. Para ver o URL do clone repo, **selecione Clone** na parte superior direita. Podeclonar o seu repo Git no seu IDE favorito. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. À esquerda, vá ao ficheiro *app/views/pages/home.html.erb* e, em seguida, **selecione Editar**.

1. Faça uma alteração no ficheiro. Por exemplo, modifique algum texto dentro de uma das etiquetas de div.

1. Selecione **'Cometer'** e, em seguida, guardar as suas alterações.

1. No seu navegador, vá ao painel de arranque de DevOps. Uma construção deve estar em andamento. As alterações efetuadas são automaticamente construídas e implantadas através de um gasoduto CI/CD.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Examinar o gasoduto Azure CI/CD

O Arranque de Azure DevOps configura automaticamente um gasoduto CI/CD completo na sua organização Azure DevOps. Explore e personalize o pipeline, conforme necessário. Para se familiarizar com os Azure DevOps construir e libertar oleodutos, faça o seguinte:

1. Vá ao painel de arranque de DevOps.

1. Na parte superior, selecione **oleodutos .** Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu exibe várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção. A construção executa várias tarefas, tais como a busca de fontes do repo Git, o restabelecimento de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure DevOps acompanha quaisquer alterações feitas ao pipeline de construção, e permite-lhe comparar versões.

1. Selecione **Triggers**.  DevOps Starter cria automaticamente um gatilho CI, e cada compromisso com o repo inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**.  DevOps Starter cria um gasoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipsis (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou anteriormente produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **O gatilho de implantação contínua**. Este gasoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual. 

1. À esquerda, selecione **Tarefas**. As tarefas são as atividades que o seu processo de implantação realiza. Neste exemplo, foi criada uma tarefa para implantar no Azure App Service.

1. À direita, selecione **ver ver ver para** mostrar um histórico de lançamentos.

1. Selecione a elipse (...) junto a um lançamento e, em seguida, **selecione Open**. Pode explorar vários menus, tais como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra código compromete-se que estão associados a esta implantação. 

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não são necessários, pode eliminar a instância do Serviço de Aplicações Azure e os recursos relacionados que criou neste arranque rápido. Para isso, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre modificar os oleodutos de construção e lançamento para atender às necessidades da sua equipa, consulte:

> [!div class="nextstepaction"]
> [Defina o seu gasoduto de implantação contínua em várias fases (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

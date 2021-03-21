---
title: 'Quickstart: Criar um fluxo de trabalho CI/CD para Node.js - DevOps Starter para GitHub para implementar para Azure'
description: O DevOps Starter facilita o arranque do Azure utilizando as ações do GitHub.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588816"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>Configurar CI/CD para uma aplicação de Node.js com DevOps Starter usando ações do GitHub

Neste arranque rápido, utiliza a experiência de Arranque de DevOps simplificada para configurar um fluxo de trabalho de integração contínua (CI) e entrega contínua (CD) para a sua aplicação Node.js utilizando As Ações Do GitHub. Pode utilizar o DevOps Starter para configurar tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma conta do [GitHub](https://github.com/).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Starter cria um fluxo de trabalho CI/CD usando ações do GitHub. DevOps Starter também cria recursos Azure na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Clique no **arranque DevOps configurar com GitHub** no banner do lado direito.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Certifique-se de que o fornecedor de CI/CD é selecionado como **GitHub Actions**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Selecione o exemplo de aplicação **Node.js**. Os exemplos Node.js incluem várias opções de arquiteturas de aplicações.

1. A arquitetura de exemplo predefinida é **Express.js**. Deixe a definição predefinitiva e, em seguida, selecione **Seguinte**.   

2. A Aplicação Web do Windows é o alvo de implementação padrão. O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implantação de serviço Azure disponível aqui. Deixe o serviço predefinido e, em seguida, selecione **Seguinte**.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>Configurar a conta GitHub e uma subscrição do Azure 

1. Autenticar com GitHub.

   1. Clique no botão **Autor.** 
   
   1. Inscreva-se no GitHub. Se não tem uma conta GitHub, pode inscrever-se aqui também.

2. Escolha uma **organização GitHub** existente. 
   
   1. Escolha um nome para o seu repositório GitHub. 
   
   1. Selecione a subscrição e localização do Azure, escolha um nome para a sua aplicação e, em seguida, selecione **Fazer**.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Após alguns minutos, o painel de arranque de DevOps é apresentado no portal Azure. Uma aplicação de amostra é configurada num repositório na sua organização Azure DevOps, um fluxo de trabalho GitHub é ativado, e a sua aplicação é implantada para a Azure. Este dashboard proporciona visibilidade ao seu repositório de código, ao fluxo de trabalho gitHub e à sua aplicação em Azure.
   
3. **Selecione Procurar** para ver a sua aplicação de execução.
    
    O painel contém detalhes do fluxo de trabalho do GitHub e dos recursos do Azure. Para ver os detalhes do fluxo de trabalho do GitHub como a última execução, compromissos e estatuto de trabalho, você precisará **autorizar para GitHub**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

O DevOps Starter configurau automaticamente um fluxo de trabalho do GitHub com trabalhos de construção e implementação usando ações do GitHub. Agora, está pronto para colaborar com uma equipa na sua aplicação Node.js, com um processo de CI/CD que implementa automaticamente o seu trabalho mais recente no seu site.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Starter cria um repositório no GitHub. Para ver o repositório e fazer alterações de código na sua aplicação, faça o seguinte:

1. À esquerda do painel de arranque de DevOps, selecione o link para o seu ramo principal. Esta ligação abre uma vista para o recém-criado repositório GitHub.

1. Para ver o URL do clone do repositório, selecione **Clone** no direito superior do navegador. Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o navegador web para escoar e comprometer alterações de código diretamente para o ramo principal.

1. No lado esquerdo do navegador, aceda ao ficheiro **/Aplicação/vistas/index.pug.**

1. **Selecione Editar** e, em seguida, faça uma alteração em alguns dos textos.
    Por exemplo, altere algumas das etiquetas para uma das etiquetas.

1. **Selecione Cometer** e, em seguida, guarde as suas alterações.

1. No seu navegador, vá ao painel de arranque do DevOps.   
Agora deve ver um fluxo de trabalho gitHub construir trabalho em andamento. As alterações efetuadas são automaticamente construídas e implementadas através de um fluxo de trabalho GitHub.

## <a name="view-the-github-workflow"></a>Ver o fluxo de trabalho gitHub

No passo anterior, o DevOps Starter configurau automaticamente um fluxo de trabalho gitHub completo. Explore e personalize o fluxo de trabalho conforme necessário. Tome os seguintes passos para se familiarizar com o fluxo de trabalho.

1. À esquerda do painel de arranque de DevOps, selecione **o fluxo de trabalho Do GitHub**. Este link abre um separador de navegador e o fluxo de trabalho GitHub para o seu novo projeto.
    > [!NOTE]
    > Não mude o nome do ficheiro workflow. O nome do ficheiro de fluxo de trabalho deve ser **devops-starter-workflow.yml** para o painel de instrumentos para refletir as alterações

1. O ficheiro yaml do fluxo de trabalho contém todas as Ações GitHub necessárias para construir e implantar a aplicação. Clique na opção **de ficheiro de edição** para personalizar o seu ficheiro de fluxo de trabalho.

1. No separador **Código** do clique de repo em **compromissos**. Esta visão mostra compromissos de código que estão associados à implementação específica.

1. No separador **Ações** do repo, pode ver a história de todas as execuções de fluxo de trabalho do seu repositório.

1. Selecione a **última corrida** para ver todos os trabalhos que correram no fluxo de trabalho.

1. Clique nos **trabalhos** para ver os registos detalhados do fluxo de trabalho. Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

1. Clique no **separador pedido pull** para ver todos os pedidos de puxar no seu repositório

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o Azure App Service e outros recursos relacionados quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de arranque de devOps.

## <a name="next-steps"></a>Passos seguintes

Quando configuraste o teu processo ci/CD, o fluxo de trabalho do GitHub foi automaticamente criado. Pode modificar este fluxo de trabalho para atender às necessidades da sua equipa. Para saber mais sobre as ações do GitHub e fluxo de trabalho, consulte:

> [!div class="nextstepaction"]
> [Personalize o fluxo de trabalho do GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)

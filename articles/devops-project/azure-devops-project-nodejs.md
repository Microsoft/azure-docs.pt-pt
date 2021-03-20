---
title: Criar um pipeline CI/CD para um PWA com GatsbyJS e Azure DevOps Starter
description: Aprenda a criar uma aplicação web progressiva NodeJS (PWA) usando GatsbyJS e a experiência simplificada de criação Azure DevOps Starter.
ms.prod: devops
ms.technology: devops-cicd
documentationcenter: vs-devops-build
author: mijacobs
ms.author: mijacobs
ms.workload: web
ms.topic: quickstart
ms.date: 03/24/2020
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d6d8a10dffa3801fe343b3b479265fac5612b021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556564"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Criar um gasoduto CI/CD em Pipelines Azure para Node.js com arranque Azure DevOps

Neste quickstart, você cria uma aplicação web progressiva NodeJS (PWA) usando [GatsbyJS](https://www.gatsbyjs.org/) e a experiência simplificada de criação Azure DevOps Starter. Quando terminar, tem um pipeline de integração contínua (CI) e entrega contínua (CD) para o seu PWA em Pipelines Azure. O Azure DevOps Starter configura o que precisa para desenvolver, implantar e monitorizar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma organização [da Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. DevOps Starter também cria recursos Azure na subscrição Azure da sua escolha.

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e no painel esquerdo, selecione **Criar um recurso**. 

   ![Criar um recurso Azure no portal Azure](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação Node.js.   

    ![Selecione a amostra de Node.js](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. A arquitetura de exemplo predefinida é **Express.js**. Altere a seleção para **Simple Node.js App** e, em seguida, selecione **Seguinte**. 

    ![Selecione a App simple Node.js](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Os objetivos de implantação disponíveis neste passo são ditados pelo quadro de candidatura selecionado no passo 2. Neste exemplo, a **Windows Web App** é o alvo de implementação padrão. Deixe **a Web App para conjunto de recipientes** e selecione **Seguinte**.

    ![Selecione o alvo de implementação](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Configurar um nome de projeto e uma subscrição do Azure

1. No passo final do fluxo de trabalho de criação DevOps Starter, você atribui um nome de projeto, selecione uma subscrição Azure e selecione **Feito**.  

    ![Atribua um nome de projeto e selecione uma subscrição](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Uma página de resumo apresenta enquanto o seu projeto é construído e a sua aplicação é implantada para Azure. Após um breve período, um projeto é criado na sua [organização Azure DevOps](https://dev.azure.com/) que inclui um git repo, uma placa Kanban, um oleoduto de implantação, planos de teste e os artefactos exigidos pela sua app.  

## <a name="managing-your-project"></a>Gerir o seu projeto

1. Navegue para **todos os recursos** e encontre o seu DevOps Starter. Selecione o seu **DevOps Starter**.

    ![Painel de azure DevOps na lista de recursos](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Você é direcionado para um dashboard que fornece visibilidade para a sua página inicial do projeto, repositório de código, o pipeline CI/CD, e um link para a sua aplicação de execução. Selecione a **página inicial** do Projeto para ver a sua aplicação em **Azure DevOps** e, em outro separador de navegador, selecione o Ponto Final da **Aplicação** para ver a aplicação da amostra ao vivo. Mudamos esta amostra mais tarde para usar o GatsbyJS gerado PWA.

    ![Painel de Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. A partir do seu projeto Azure DevOps, você pode convidar os membros da equipa a colaborar e estabelecer um conselho Kanban para começar a rastrear o seu trabalho. Para mais informações, consulte [aqui.](/azure/devops/user-guide/what-is-azure-devops)

![Visão geral de Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Clone o repo e instale o seu Gatsby PWA

DevOps Starter cria um repositório de git em Azure Repos ou GitHub. Este exemplo criou um Azure Repo. O próximo passo é clonar o repo e fazer mudanças.

1. Selecione **Repos** do seu **Projeto DevOps** e, em seguida, clique em **Clone**.  Existem vários mecanismos para clonar o repo git para o seu ambiente de trabalho.  Escolha o que se adequa à sua experiência de desenvolvimento.  

    ![Clonar o Repositório](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Depois de clonar o repo para o seu ambiente de trabalho, faça algumas alterações no modelo de arranque. Comece por instalar o CLI GatsbyJS a partir do seu terminal.

   ```powershell
    npm install -g gatsby
   ```

1. Do terminal, navegue até a raiz do seu repo. Deve conter três pastas com este aspeto:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Não queremos todos os ficheiros da pasta Aplicação porque vamos substituí-lo por um arranque Gatsby. Executar os seguintes comandos, em sequência, para aparar.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Utilize o Gatsby CLI para gerar uma amostra de PWA. Corra `gatsby new` a partir do terminal para iniciar o assistente PWA e selecione para o seu modelo de `gatsby-starter-blog` arranque. Deve assemelhar-se a esta amostra:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Tem agora uma pasta chamada `my-gatsby-project` . Mude-o para `Application` e `Dockerfile` copie-o.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. No seu editor favorito, abra o Dockerfile e mude a primeira linha de `FROM node:8` `FROM node:12` . Esta alteração garante que o seu recipiente está a utilizar Node.js versão 12.x em vez da versão 8.x. GatsbyJS requer versões mais modernas de Node.js.

1. Em seguida, abra a package.jsno ficheiro na pasta Aplicação e edite o [campo scripts](https://docs.npmjs.com/files/package.json#scripts) para garantir que os seus servidores de desenvolvimento e produção ouçam todas as interfaces de rede disponíveis (por exemplo, 0.0.0.0) e a porta 80. Sem estas definições, o serviço de aplicações de contentores não consegue encaminhar o tráfego para a sua Node.js aplicação que corre dentro do seu contentor. O `scripts` campo deve assemelhar-se ao que está abaixo. Especificamente, quer alterar o `develop` , `serve` e `start` alvos de seus incumprimentos.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>Edite os seus oleodutos CI/CD

1. Antes de entregar o código na secção anterior, faça algumas alterações na sua construção e liberte os gasodutos. Edite o seu 'Build Pipeline' e atualize a tarefa Node para utilizar Node.js versão 12.x. Desateia o campo **da versão Tarefa** para 1.x e o campo **Versão** para 12.x.

    ![Atualizar Node.js para 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. Neste arranque rápido, não estamos a criar testes unitários e estamos a desativar esses passos no nosso oleoduto de construção. Ao escrever testes, pode voltar a ativar estes passos. Clique com o botão direito para selecionar as tarefas rotuladas **Instale as dependências** de teste e **testes de unidade de execução** e desative-as.

    ![Desativar testes de construção](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Edite o seu oleoduto de lançamento.

    ![Editar o Pipeline de Lançamento](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Tal como acontece com o gasoduto de construção, altere a tarefa Nó para utilizar 12.x e desative as duas tarefas de teste. A sua libertação deve assemelhar-se a esta imagem.

    ![Pipeline de lançamento concluído](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. No lado esquerdo do navegador, vá para o ficheiro **views/index.pug.**

1. **Selecione Editar** e, em seguida, faça uma alteração na posição h2.  Por exemplo, insira **Começar imediatamente com o Azure DevOps Starter** ou fazer outra alteração.

1. **Selecione Cometer** e, em seguida, guarde as suas alterações.

1. No seu navegador, vá ao painel de arranque do DevOps.   
Deve agora ver uma construção em progresso. As alterações efetuadas são automaticamente construídas e implementadas através de um gasoduto CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Cometa as suas alterações e examine o gasoduto Azure CI/CD

Nos dois passos anteriores, adicionou um Gatsby gerado PWA ao seu repo git e editou os seus oleodutos para construir e implementar o código. Podemos cometer o código, e vê-lo progredir através do oleoduto de construção e libertação.

1. A partir da raiz do seu projeto git repo em um terminal, executar os seguintes comandos para empurrar o seu código para o seu projeto Azure DevOps:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Uma construção é iniciada assim que `git push` termina. Pode acompanhar o progresso a partir do **Painel de DevOps Azure**.

3. Após alguns minutos, os seus gasodutos de construção e libertação devem terminar e o seu PWA deve ser colocado num recipiente. Clique no link de ponto final da **Aplicação** a partir do painel de instrumentos acima e deverá ver um projeto de arranque Gatsby para blogs.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o Azure App Service e outros recursos relacionados que criou quando já não precisa dos recursos. Utilize a funcionalidade **Eliminar** no painel de arranque de devOps.

## <a name="next-steps"></a>Passos seguintes

Quando configurar o seu processo CI/CD, os gasodutos de construção e libertação são automaticamente criados. Pode alterar estes oleodutos de construção e libertação para atender às necessidades da sua equipa. Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](/azure/devops/pipelines/release/define-multistage-release-process)

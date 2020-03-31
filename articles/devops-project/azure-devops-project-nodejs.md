---
title: Crie um oleoduto CI/CD para um PWA com projetos GatsbyJS e Azure DevOps
description: A DevOps Projects facilita o início do Azure. Ajuda-o a utilizar o seu próprio código e o repositório GitHub para lançar uma aplicação num serviço do Azure à sua escolha com alguns passos rápidos.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78209072"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Quickstart: Criar um oleoduto CI/CD em Pipelines Azure para Node.js com projetos Azure DevOps
Neste arranque rápido, cria-se uma aplicação web progressiva noNodeJS (PWA) utilizando [o GatsbyJS](https://www.gatsbyjs.org/) e a experiência simplificada de criação de projetos Azure DevOps. Quando terminar, tem um gasoduto de integração contínua (CI) e entrega contínua (CD) para o seu PWA em Pipelines Azure. Os Projetos Azure DevOps configuram o que você precisa para desenvolver, implantar e monitorizar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma organização [Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A DevOps Projects cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure na subscrição Azure à sua escolha.

1. Inscreva-se no [portal Azure](https://portal.azure.com), e no painel esquerdo, selecione **Criar um recurso**. 

   ![Criar um recurso Azure no portal Azure](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Procure e selecione **Projetos DevOps,** e, em seguida, **selecione Criar**.

 ![Criar um Projeto DevOps](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação Node.js.   

 ![Selecione a amostra Nó.js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. A arquitetura de exemplo predefinida é **Express.js**. Altere a seleção para **Simple Node.js App** e, em seguida, selecione **Next**. 

 ![Selecione a app Simple Node.js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Os objetivos de implantação disponíveis neste passo são ditados pelo quadro de aplicação selecionado no passo 2.  Neste exemplo, a **Aplicação Web do Windows** é o alvo de implementação predefinido.  Deixe **a Aplicação Web para conjunto de contentores** e selecione **Next**.

 ![Selecione o alvo de implementação](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Configure um nome de projeto e uma subscrição Azure

1. No passo final do fluxo de trabalho de criação de projeto DevOps, você atribui um nome de projeto, selecione uma subscrição Azure e selecione **Done**.  

 ![Atribuir um nome de projeto e selecionar uma subscrição](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Uma página de resumo exibe enquanto o seu projeto é construído e a sua aplicação é implantada para o Azure. Após um breve período, é criado um projeto na sua [organização Azure DevOps](https://dev.azure.com/) que inclui um git repo, uma placa Kanban, um pipeline de implantação, planos de teste e os artefactos exigidos pela sua app.  

## <a name="managing-your-project"></a>Gerir o seu projeto

1. Navegue para **todos os recursos** e encontre o seu Projeto DevOps. Selecione o seu **Projeto DevOps**.

![Painel de Instrumentos Azure DevOps na Lista de Recursos](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Você é direcionado para um dashboard que fornece visibilidade na sua página inicial do projeto, repositório de código, o pipeline CI/CD, e um link para a sua aplicação de execução. Selecione a **página inicial** do Projeto para visualizar a sua aplicação em **Azure DevOps** e, em outro separador de navegador, selecione o Ponto Final da **Aplicação** para visualizar a aplicação de amostras ao vivo.  Mudamos esta amostra mais tarde para usar o PwA gerado pelo GatsbyJS.

![Painel azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. A partir do seu projeto Azure DevOps, você pode convidar membros da equipa a colaborar e estabelecer um tabuleiro Kanban para começar a rastrear o seu trabalho.  Para mais informações, consulte [aqui.](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)

![Visão geral de Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Clone o repo e instale o seu Gatsby PWA

A DevOps Projects cria um repositório git em Azure Repos ou GitHub. Este exemplo criou um Azure Repo.  O próximo passo é clonar o repo e fazer alterações.

1. Selecione **Repos** do seu **Projeto DevOps** e, em seguida, clique em **Clone**.  Existem vários mecanismos para clonar o git repo para o seu ambiente de trabalho.  Escolha aquele que se adequa à sua experiência de desenvolvimento.  

![Clonar o Repositório](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Depois de o repo ser clonado para o seu ambiente de trabalho, faça algumas alterações no modelo de arranque. Comece por instalar o CLI GatsbyJS a partir do seu terminal.
```powershell
npm install -g gatsby
```

3. Do terminal, navegue até à raiz do seu repo. Deve conter três pastas que se parecem com esta:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Não queremos todos os ficheiros na pasta Aplicação porque vamos substituí-lo por um arranque de Gatsby. Executar os seguintes comandos, em sequência, para aparar.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Utilize o ClI Gatsby para gerar uma amostra de PWA. Corra `gatsby new` a partir do terminal para `gatsby-starter-blog` iniciar o assistente PWA e selecione para o seu modelo de arranque. Deve assemelhar-se a esta amostra:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Agora tem uma `my-gatsby-project`pasta chamada. Mude o `Application` nome para `Dockerfile` e copie o que está nele.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. No seu editor favorito, abra o Dockerfile `FROM node:8` `FROM node:12`e mude a primeira linha de . Esta alteração garante que o seu recipiente está a utilizar a versão Nó.js 12.x em vez da versão 8.x. GatsbyJS requer versões mais modernas de Node.js.

8. Em seguida, abra o ficheiro package.json na pasta Aplicação e edite o campo de [scripts](https://docs.npmjs.com/files/package.json#scripts) para garantir que os seus servidores de desenvolvimento e produção ouçam todas as interfaces de rede disponíveis (por exemplo, 0.0.0) e porta 80. Sem estas definições, o serviço de aplicações de contentores não pode encaminhar o tráfego para a sua aplicação Node.js que funciona dentro do seu contentor. O `scripts` campo deve assemelhar-se ao que está abaixo. Especificamente, quer alterar `develop` `serve`os `start` alvos e os alvos dos seus incumprimentos.
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

## <a name="edit-your-cicd-pipelines"></a>Editar os seus oleodutos CI/CD

1. Antes de comprometer o código na secção anterior, faça algumas alterações nos seus oleodutos de construção e lançamento. Edite o seu 'Build Pipeline' e atualize a tarefa nó para utilizar a versão Nó.js 12.x. Detete o campo de **versão Task** para 1.x e o campo **versão** para 12.x.
![Atualizar Nóde.js a 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. Neste arranque rápido, não estamos a criar testes unitários e estamos a desativar esses passos no nosso oleoduto de construção. Quando escreve saques, pode reativar estes passos. Clique à direita para selecionar as tarefas rotuladas **Instalar dependências** de teste e **executar testes** de unidade e desativá-las.

![Desativar testes de construção](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Edite o seu oleoduto de libertação.
![Editar o Pipeline de Lançamento](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Tal como acontece com o gasoduto de construção, altere a tarefa do Nó para utilizar 12.x e desative as duas tarefas de teste. A sua libertação deve assemelhar-se a esta imagem.

![Gasoduto de lançamento concluído](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Do lado esquerdo do navegador, vá ao ficheiro **views/index.pug.**

1. **Selecione Editar**e, em seguida, fazer uma alteração na rubrica h2.  
    Por exemplo, insira **Começar imediatamente com projetos Azure DevOps** ou fazer alguma outra alteração.

1. Selecione **'Cometer'** e, em seguida, guardar as suas alterações.

1. No seu navegador, vá ao painel de projetos DevOps.   
Agora deve ver uma construção em andamento. As alterações efetuadas são automaticamente construídas e implantadas através de um gasoduto CI/CD.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Cometa as suas alterações e examine o oleoduto Azure CI/CD

Nos dois passos anteriores, adicionou um PwA gerado por Gatsby ao seu repo git e editou os seus oleodutos para construir e implementar o código. Podemos comprometer o código e vê-lo progredir através do oleoduto de construção e libertação.

1. A partir da raiz do git repo do seu projeto num terminal, execute os seguintes comandos para empurrar o seu código para o seu projeto Azure DevOps:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. Uma construção começa `git push` assim que termina. Pode acompanhar o progresso do **Painel DevOps Azure**.

![Painel de Instrumentos Azure DevOps na Lista de Recursos](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Após alguns minutos, os seus oleodutos de construção e de libertação devem terminar e o seu PWA deve ser implantado num recipiente. Clique no link **final da Aplicação** a partir do dashboard acima e deverá ver um projeto de arranque de Gatsby para blogs.



## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar o Serviço de Aplicações Azure e outros recursos relacionados que criou quando já não precisa dos recursos. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.


## <a name="next-steps"></a>Passos seguintes

Quando configurar o seu processo CI/CD, os gasodutos de construção e de libertação são automaticamente criados. Pode alterar estes oleodutos de construção e libertação para atender às necessidades da sua equipa. Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)


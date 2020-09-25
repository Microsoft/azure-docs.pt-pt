---
title: 'Quickstart: Criar um pipeline CI/CD para Java - Azure DevOps Starter'
description: Aprenda a utilizar a experiência simplificada de Arranque Azure DevOps para configurar um pipeline de integração contínua (CI) e entrega contínua (CD) para a sua aplicação Java em Pipelines Azure.
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
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 29b05feab0a44629c3d450709a3bc05481be6e84
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330371"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>Crie um pipeline CI/CD para uma aplicação Java com Azure DevOps Starter

Neste quickstart, utiliza a experiência simplificada de arranque Azure DevOps para configurar um pipeline de integração contínua (CI) e entrega contínua (CD) para a sua aplicação Java em Pipelines Azure. Pode utilizar o Azure DevOps Starter para configurar tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Uma conta e organização [da Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. DevOps Starter também cria recursos Azure na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Selecione um exemplo de aplicação e serviço do Azure

1. Selecione o exemplo de aplicação Java. Os exemplos de Java incluem várias opções de arquiteturas de aplicações.

1. A arquitetura de exemplo predefinida é Spring. Deixe a definição predefinitiva e, em seguida, selecione **Seguinte**.  A Aplicação Web Para Contentores é o destino de implementação predefinido. O quadro de aplicação, que escolheu anteriormente, dita o tipo de alvo de implantação de serviço Azure disponível aqui. 

2. Deixe o serviço predefinido e, em seguida, selecione **Seguinte**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure 

1. Crie uma organização nova do Azure DevOps ou utilize uma organização existente. 
   
   1. Escolha um nome para o projeto. 
   
   1. Selecione a subscrição e localização do Azure, escolha um nome para a sua aplicação e, em seguida, selecione **Fazer**.  
   Após alguns minutos, o painel de arranque de DevOps é apresentado no portal Azure. Uma aplicação de amostra é configurada num repositório na sua organização Azure DevOps, uma construção é executada, e a sua aplicação é implantada para Azure. Este painel fornece visibilidade no seu repositório de código, no gasoduto CI/CD e na sua aplicação em Azure.
   
2. **Selecione Procurar** para ver a sua aplicação de execução.
   
   ![Ver painel de aplicações no portal Azure](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

O DevOps Starter configura automaticamente uma construção de CI e um gatilho de libertação.  Agora, está pronto para colaborar com uma equipa na sua aplicação Java com um processo de CI/CD que implementa automaticamente o seu trabalho mais recente no seu site.

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Starter cria um repositório git em Azure Repos ou GitHub. Para ver o repositório e fazer alterações de código na sua aplicação, faça o seguinte:

1. À esquerda do painel de arranque de DevOps, selecione o link para o seu ramo principal. Esta ligação abre uma vista para o repositório Git recentemente criado.

1. Para ver o URL do clone do repositório, selecione **Clone** no direito superior do navegador. Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do navegador, vá ao ficheiro **src/main/webapp/index.html.**

1. **Selecione Editar**e, em seguida, faça uma alteração em alguns dos textos.
    Por exemplo, altere algum texto para uma das etiquetas div.

1. **Selecione Cometer**e, em seguida, guarde as suas alterações.

1. No seu navegador, vá ao painel de arranque do DevOps.   
Deve agora ver uma construção em progresso. As alterações que acaba de fazer são automaticamente construídas e implementadas através de um gasoduto CI/CD.

## <a name="examine-the-cicd-pipeline"></a>Examinar o gasoduto CI/CD

 No passo anterior, o DevOps Starter configurau automaticamente um pipeline CI/CD completo. Explore e personalize o pipeline, conforme necessário. Tome os seguintes passos para se familiarizar com os gasodutos de construção e libertação.

1. No topo do painel de arranque de DevOps, selecione **Build Pipelines**. Este link abre um separador de navegador e o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Esta ação abre um menu onde pode iniciar várias atividades como fazer fila de uma nova construção, fazer uma pausa numa construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa uma variedade de tarefas, tais como buscar fontes do repositório git, restaurar dependências e publicar saídas que são usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, **selecione Save & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.   
No painel **história,** você vê um rasto de auditoria das suas recentes alterações para a construção.  A Azure Pipelines regista quaisquer alterações que sejam feitas ao oleoduto de construção, e permite comparar versões.

1. Selecione **Triggers**.  O DevOps Starter criou automaticamente um gatilho de CI, e cada compromisso com o repositório inicia uma nova construção.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

1. Selecione **Construir e Soltar**e, em seguida, selecione **Versões**.  
 O DevOps Starter cria um oleoduto de libertação para gerir as implementações para o Azure.

1. À esquerda, selecione a elipse (...) ao lado do seu pipeline de libertação e, em seguida, **selecione Editar**. O pipeline de lançamento contém um pipeline, que define o processo de lançamento.  
    
12. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. Ao lado do ícone **Drop,** selecione o **gatilho de implementação contínua**. Este oleoduto de lançamento tem um gatilho de CD ativado, que executa uma implantação sempre que há um novo artefacto de construção disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual. 

1. À esquerda, selecione **Tarefas**. As tarefas são as atividades que o seu processo de implantação realiza. Neste exemplo, foi criada uma tarefa para implementar no Azure App Service.

1. À direita, **selecione Ver lançamentos**. Esta vista mostra um histórico das versões.

1. Selecione a elipse (...) ao lado de uma das suas versões e, em seguida, selecione **Abrir**. Existem vários menus para explorar, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados à implementação específica. 

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o Azure App Service e outros recursos relacionados quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de arranque de devOps.

## <a name="next-steps"></a>Passos seguintes

Quando configuraste o teu processo ci/CD, foram criados automaticamente oleodutos de construção e libertação. Pode modificar estes pipelines de compilação e de lançamento para satisfazer as necessidades da sua equipa. Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Personalizar o processo de CD](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

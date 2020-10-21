---
title: 'Tutorial: Implementar a sua app de Node.js para a Azure Web App utilizando o DevOps Starter para ações do GitHub'
description: O DevOps Starter facilita o arranque do Azure e a implementação da sua aplicação Node.js para a Azure Web App em alguns passos rápidos.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 9a2abf7e714b75f2551a35a220e30c2465d86a49
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332749"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Tutorial: Implementar Node.js app para a Azure Web App usando DevOps Starter para ações do GitHub

DevOps Starter for GitHub Actions apresenta uma experiência simplificada onde você pode escolher uma aplicação de amostra para criar uma integração contínua (CI) e fluxo de trabalho de entrega contínua (CD) para implementar para Azure. 

DevOps Starter também:
* Cria automaticamente recursos Azure, como uma nova App Azure Web.
* Cria e configura um fluxo de trabalho no GitHub que inclui um trabalho de construção para o CI.
* O fluxo de trabalho também contém um trabalho de implantação para CD. 
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, vai:

> [!div class="checklist"]
> * Use o DevOps Starter para implementar uma aplicação Node.js
> * Configurar GitHub e uma assinatura Azure 
> * Examine o fluxo de trabalho do GitHub
> * Comprometa alterações no GitHub e desloque-as automaticamente para a Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Use o DevOps Starter para implementar uma aplicação Node.js

DevOps Starter cria um fluxo de trabalho no GitHub. Pode usar uma organização gitHub existente. O DevOps Starter também cria recursos Azure, como a Web App na subscrição Azure da sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Certifique-se de que o fornecedor de CI/CD é selecionado como **GitHub Actions**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Selecione **Node.js**e, em seguida, selecione **Seguinte**.

1. Em **Escolha um Quadro de Aplicações**, selecione **Express.js**e, em seguida, selecione **Seguinte**. O quadro de aplicação, que escolheu num passo anterior, dita o tipo de alvo de implantação de serviço Azure que está disponível aqui. 

1. Selecione a **Aplicação Web**do Windows e, em seguida, selecione **Seguinte**.

## <a name="configure-github-and-an-azure-subscription"></a>Configurar GitHub e uma assinatura Azure

1. **Autorizar** GitHub e selecione uma organização GitHub existente. 

1. Insira um nome para o seu **Repositório GitHub.** 

1. Selecione os seus serviços de subscrição Azure. Opcionalmente, pode selecionar **Alterar** e, em seguida, introduzir mais detalhes de configuração, como a localização dos recursos Azure.
 
1. Introduza um nome de Aplicação Web e, em seguida, selecione **Feito**. Após alguns minutos, a App Azure Web estará pronta. Uma amostra Node.js aplicação é criada num repo na sua organização GitHub, um fluxo de trabalho é ativado, e a sua aplicação é implementada para a recém-criada App Web Azure.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   Depois de concluído, o painel de arranque de DevOps é apresentado no portal Azure. Também pode navegar para o painel diretamente a partir de **todos os recursos** do portal Azure. 

   O painel de instrumentos dá visibilidade ao seu repo de código GitHub, ao seu fluxo de trabalho CI/CD e à sua aplicação de execução em Azure.   

   ![Vista do Dashboard](_img/azure-devops-project-nodejs/full-dashboard.png)

O DevOps Starter configura automaticamente um gatilho que implementa alterações de código no seu repo.
    
## <a name="examine-the-github-workflow"></a>Examine o fluxo de trabalho do GitHub

No passo anterior, o DevOps Starter configurau automaticamente um fluxo de trabalho gitHub completo. Explore e personalize o fluxo de trabalho conforme necessário. Tome os seguintes passos para se familiarizar com o fluxo de trabalho.

1. À esquerda do painel de arranque de DevOps, selecione **o fluxo de trabalho Do GitHub**. Este link abre um separador de navegador e o fluxo de trabalho GitHub para o seu novo projeto.
    > [!NOTE]
    > Não mude o nome do ficheiro workflow. O nome do ficheiro de fluxo de trabalho deve ser **devops-starter-workflow.yml** para o painel de instrumentos para refletir as alterações

1. O ficheiro yaml do fluxo de trabalho contém todas as Ações GitHub necessárias para construir e implantar a aplicação. Clique na opção **de ficheiro de edição** para personalizar o seu ficheiro de fluxo de trabalho.

1. No separador **Código** do clique de repo em **compromissos**. Esta visão mostra compromissos de código que estão associados à implementação específica.

1. No separador **Ações** do repo pode ver a história de todas as execuções de fluxo de trabalho do seu repositório.

1. Selecione a **última corrida** para ver todos os trabalhos que correram no fluxo de trabalho.

1. Clique nos **trabalhos** para ver os registos detalhados do fluxo de trabalho. Os registos contêm informações úteis sobre o processo de implementação. Podem ser vistos durante e após as implementações.

1. Clique no **separador pedido pull** para ver todos os pedidos de puxar no seu repositório

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Starter cria um repositório no GitHub. Para ver o repositório e fazer alterações de código na sua aplicação, faça o seguinte:

1. À esquerda do painel de arranque de DevOps, selecione o link para o seu ramo principal. Esta ligação abre uma vista para o recém-criado repositório GitHub.

1. Para ver o URL do clone do repositório, selecione **Clone** no direito superior do navegador. Pode clonar o repositório Git no seu IDE preferido. Nos próximos passos, pode utilizar o browser para fazer e consolidar alterações de código diretamente no ramo principal.

1. No lado esquerdo do navegador, aceda ao ficheiro **/Aplicação/vistas/index.pug.**

1. **Selecione Editar**e, em seguida, faça uma alteração em alguns dos textos.
    Por exemplo, altere algumas das etiquetas para uma das etiquetas.

1. **Selecione Cometer**e, em seguida, guarde as suas alterações.

1. No seu navegador, vá ao painel de arranque do DevOps.   
Agora deve ver um fluxo de trabalho gitHub construir trabalho em andamento. As alterações que acaba de fazer são automaticamente construídas e implementadas através de um fluxo de trabalho GitHub.

1. Depois de concluída a implementação, refresque a sua aplicação para verificar as suas alterações.

## <a name="configure-azure-application-insights-monitoring"></a>Configurar a monitorização do Azure Application Insights

Com o Azure Application Insights, pode monitorizar facilmente o desempenho e a utilização da aplicação. O DevOps Starter configura automaticamente um recurso Application Insights para a sua aplicação. Pode configurar vários alertas e capacidades de monitorização, conforme necessário.

1. No portal Azure, vá ao painel de arranque de DevOps. 

1. No centro direita, selecione o link **Application Insights** para a sua aplicação. O painel **de Insights de Aplicação** abre. Esta vista contém informações sobre a monitorização da utilização, do desempenho e da disponibilidade da aplicação.

   ![O painel de Insights de Aplicação](_img/azure-devops-project-github/appinsights.png) 

1. Selecione **o intervalo de tempo**e, em seguida, selecione Última **hora**. Para filtrar os resultados, selecione **Update**. Agora pode ver toda a atividade dos últimos 60 minutos. 
    
1. Para sair do intervalo de tempo, selecione **x**.

1. Selecione **Alertas**e, em seguida, **selecione Adicionar alerta métrico**. 

1. Insira um nome para o alerta.

1. Na lista **de recuos métricos,** examine as várias métricas de alerta. A predefinição de alerta é para um **tempo de resposta de servidor superior a 1 segundo**. Pode configurar facilmente vários alertas, para melhorar as capacidades de monitorização da aplicação.

1. Selecione a **Notificação através de e-mail proprietários, colaboradores e caixa** de verificação de leitores. Opcionalmente, pode executar ações adicionais quando um alerta é exibido executando uma aplicação lógica Azure.

1. Selecione **OK** para criar o alerta. Após alguns momentos, o alerta aparece como ativo no painel de instrumentos. 

1. Saia da área **de Alertas** e volte para o painel **de Insights de Aplicação.**

1. Selecione **Disponibilidade**e, em seguida, selecione **Adicionar teste**. 

1. Introduza um nome de teste e, em seguida, **selecione Criar**. Um teste de ping simples é criado para verificar a disponibilidade da sua aplicação. Após alguns minutos, os resultados do teste ficam disponíveis e o dashboard do Application Insights apresenta um estado de disponibilidade.

## <a name="clean-up-resources"></a>Limpar os recursos

Se estiver a testar, pode evitar acumular encargos de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar a máquina virtual Azure e os recursos relacionados que criou neste tutorial. Para tal, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps. 

> [!IMPORTANT]
> O procedimento que se segue elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto no DevOps Starter em ambos os Azure, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as indicações.

1. No portal Azure, vá ao painel de arranque de DevOps.
1. No topo direito, **selecione Delete**. 
1. A pedido, selecione **Sim** para *eliminar permanentemente* os recursos.

Pode modificar opcionalmente o fluxo de trabalho para atender às necessidades da sua equipa. Também pode usar este padrão CI/CD como modelo para os seus outros repositórios. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use o DevOps Starter para implementar uma aplicação Node.js
> * Configurar GitHub e uma assinatura Azure 
> * Examine o fluxo de trabalho do GitHub
> * Comprometa alterações no GitHub e desloque-as automaticamente para a Azure
> * Configurar a monitorização do Azure Application Insights
> * Limpar os recursos

Para saber mais sobre as ações do GitHub e fluxos de trabalho, consulte:

> [!div class="nextstepaction"]
> [Personalize o fluxo de trabalho do GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)

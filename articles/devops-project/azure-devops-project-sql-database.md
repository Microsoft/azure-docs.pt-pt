---
title: 'Tutorial: Implemente a sua app de ASP.NET e código de base de dados Azure SQL utilizando o Azure DevOps Starter'
description: DevOps Starter facilita o arranque do Azure. Com o DevOps Starter, pode implementar a sua aplicação ASP.NET e o código de base de dados Azure SQL em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e85b698e1b3c3a1c3e8730ab85e8b72b7b0d9f6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548483"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Tutorial: Implemente a sua app de ASP.NET e código de base de dados Azure SQL utilizando o Azure DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um pipeline de integração contínua (CI) e entrega contínua (CD) para Azure. 

DevOps Starter também:
* Cria automaticamente recursos Azure, como uma base de dados na Base de Dados Azure SQL.
* Cria e configura um gasoduto de libertação em Gasodutos Azure que inclui um gasoduto de construção para ci.
* Cria um oleoduto de libertação para CD. 
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, vai:

> [!div class="checklist"]
> * Utilize o Azure DevOps Starter para implementar a sua app ASP.NET e o código de base de dados Azure SQL
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure
> * Ligar à Base de Dados SQL do Azure 
> * Limpar os recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>Criar um projeto em Projetos DevOps para uma app ASP.NET e Azure SQL Database

DevOps Starter cria um gasoduto CI/CD em Pipelines Azure. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. O DevOps Starter também cria recursos Azure, como a Base de Dados Azure SQL, na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, escreva **DevOps Starter** e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e, em seguida, selecione **Seguinte**.

1. No **Âmbito de Escolha um quadro de aplicação**, selecione **ASP.NET**.

1. **Selecione Adicione uma base de dados** e, em seguida, selecione **Seguinte**. O quadro de aplicação, que escolheu num passo anterior, dita o tipo de alvo de implantação de serviço Azure que está disponível aqui. 
    
1. Selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar Azure DevOps e uma subscrição da Azure

1. Crie uma nova organização Azure DevOps ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione os seus serviços de subscrição Azure. Opcionalmente, para ver as definições adicionais de configuração do Azure e identificar o nome de utilizador na secção De Detalhes de Login do **Servidor de Bases de Dados,** pode selecionar **Alterar**. Guarde o nome de utilizador para os passos seguintes deste tutorial. Se realizar este passo opcional, saia da área de configuração Azure antes de selecionar **'Fazer'**
 
1. Selecione **Concluído**. Após alguns minutos, o processo está concluído e o painel de arranque de DevOps abre no portal Azure. Também pode navegar para o painel diretamente a partir de **todos os recursos** do portal Azure. À direita, **selecione Procurar** para ver a sua aplicação de execução.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um gasoduto CI/CD completo em Azure Repos. Pode explorar e personalizar o pipeline. Para se familiarizar com o gasoduto Azure DevOps, faça o seguinte:

1. No topo do painel de arranque de DevOps, selecione **Construir oleodutos**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu apresenta várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu oleoduto de construção. A construção executa várias tarefas, tais como buscar fontes do repositório git, restaurar dependências e publicar saídas usadas para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu pipeline de construção para algo mais descritivo, **selecione Save & fila** e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure Pipelines regista quaisquer alterações feitas ao oleoduto de construção, e permite comparar versões.

1. Selecione **Triggers**. O DevOps Starter cria automaticamente um gatilho de CI, e cada compromisso com o repositório inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de IC.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

O DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar Azure DevOps para a sua subscrição Azure. A automatização também cria um pipeline de CD, que fornece o CD à máquina virtual Azure. Para saber mais sobre o oleoduto Azure DevOps CD, faça o seguinte:

1. Selecione **Construir e Soltar** e, em seguida, selecione **Versões**. O DevOps Starter cria um oleoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipse (...) ao lado do seu pipeline de lançamento e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O gasoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **o gatilho de implementação contínua**. Este oleoduto de lançamento tem um disparador de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual. 

    O DevOps Starter configura uma senha SQL aleatória e utiliza-a para o pipeline de libertação.
    
1. À esquerda, **selecione Variáveis.** 

   > [!NOTE]
   > Execute o passo seguinte apenas se alterar a palavra-passe do SQL Server. Há uma única variável de senha.
  
1. Ao lado da caixa **Valor,** selecione o ícone do cadeado, introduza a nova palavra-passe e, em seguida, **selecione Guardar**.

1. À esquerda, selecione **Tarefas** e, em seguida, selecione o seu ambiente. As tarefas são as atividades que o seu processo de implantação executa, e são agrupadas por fases. Este oleoduto de lançamento tem uma única fase, que contém uma *tarefa de implementação do serviço de aplicações Azure* e *da implementação da base de dados Azure SQL.*

1. Selecione a tarefa *Executar Azure SQL* e examine as várias propriedades que são utilizadas para a implantação do SQL. No âmbito **do Pacote de Implantação,** a tarefa utiliza um ficheiro *SQL DACPAC.*

1. À direita, **selecione Ver ver lançamentos** para exibir um histórico de lançamentos.

1. Selecione a elipse (...) ao lado de uma versão e, em seguida, selecione **Abrir**. Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra compromissos de código que estão associados a esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as missões.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure 

 > [!NOTE]
 > O procedimento a seguir testa o gasoduto CI/CD com uma simples alteração de texto. Para testar o processo de implementação DO SQL, pode opcionalmente fazer uma alteração de esquema do SQL Server para a tabela.

Está agora pronto para colaborar com uma equipa na sua aplicação, utilizando um processo ci/CD que implementa automaticamente o seu último trabalho no seu website. Cada mudança para o git repo inicia uma construção em Azure DevOps, e um oleoduto de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou use outra técnica para comprometer alterações no seu repositório. As alterações de código iniciam o processo CI/CD e implementam automaticamente as alterações para o Azure.

1. No painel esquerdo, selecione **Código** e, em seguida, vá ao seu repositório.

1. Vá ao *sampleWebApplication\Views\Home* directy, selecione a elipse (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**. 

1. Faça uma alteração no ficheiro, como por exemplo adicionar algum texto dentro de uma das tags de div. 

1. No topo direito, **selecione Commit** e, em seguida, selecione **Comprometa-se** novamente para empurrar a sua mudança. Após alguns momentos, uma construção começa em Azure DevOps e um lançamento executa para implementar as alterações. Monitorize o estado de construção no painel de partidas de DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluída a versão, refresque a sua aplicação para verificar as suas alterações.

## <a name="connect-to-azure-sql-database"></a>Ligar à Base de Dados SQL do Azure

Precisa de permissões adequadas para se ligar à Base de Dados Azure SQL.

1. No painel de arranque de DevOps, selecione **A Base de Dados SQL** para ir à página de gestão da Base de Dados SQL.
   
1. Selecione **Definir firewall do servidor** e, em seguida, selecione Adicionar IP do **cliente**. 

1. Selecione **Guardar**. O IP do seu cliente tem agora acesso ao recurso SQL Server Azure.

1. Volte para o painel **de base de dados SQL.** 

1. À direita, selecione o nome do servidor para navegar para a página de configuração **do SQL Server**.

1. Selecione **Redefinir a palavra-passe,** introduza uma palavra-passe para o login de administração do SQL Server e, em seguida, selecione **Guardar**. Certifique-se de que mantém esta palavra-passe para usar mais tarde neste tutorial.

    Pode agora utilizar opcionalmente ferramentas de clientes como o SQL Server Management Studio ou o Visual Studio para ligar ao SQL Server e à Base de Dados Azure SQL. Utilize a propriedade **Nome do servidor** para ligar.

    Se não alterou o nome de utilizador da base de dados quando inicialmente configura o projeto em Projetos DevOps, o seu nome de utilizador é a parte local do seu endereço de e-mail. Por exemplo, se o seu endereço de e-mail for *\@ microsoft.com,* o seu nome de utilizador é *johndoe*.

   > [!NOTE]
   > Se alterar a sua palavra-passe para o login DO SQL, tem de alterar a palavra-passe na variável do pipeline de desbloqueio, conforme descrito na secção [de pipeline cd.](#examine-the-cd-pipeline)

## <a name="clean-up-resources"></a>Limpar os recursos

Se estiver a testar, pode evitar acumular encargos de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar a Base de Dados Azure SQL e os recursos relacionados que criou neste tutorial. Para tal, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

> [!IMPORTANT]
> O procedimento que se segue elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto no DevOps Starter tanto em Azure como em Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as indicações.

1. No portal Azure, vá ao painel de arranque de DevOps.
2. No topo direito, **selecione Delete**. 
3. A pedido, selecione **Sim** para *eliminar permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o Azure DevOps Starter para implementar a sua app ASP.NET e o código de base de dados Azure SQL
> * Configurar Azure DevOps e uma subscrição da Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Cometa alterações no Azure Repos e desloque-os automaticamente para a Azure
> * Ligar à Base de Dados SQL do Azure 
> * Limpar os recursos

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu pipeline de implantação contínua em várias fases (CD)](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>Vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
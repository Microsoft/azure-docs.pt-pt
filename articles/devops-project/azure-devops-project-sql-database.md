---
title: 'Tutorial: Implementar a sua aplicação ASP.NET e o código de base de dados do Azure SQL usando os projetos de DevOps do Azure'
description: Projetos de DevOps torna mais fácil começar a utilizar no Azure. Com os projetos de DevOps, pode implementar a sua aplicação ASP.NET e o código de base de dados do Azure SQL em poucos passos rápidos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 0d05a2f3de92791572f0a5e6313777b5388af3df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845227"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Tutorial: Implementar a sua aplicação ASP.NET e o código de base de dados do Azure SQL usando os projetos de DevOps do Azure

Projetos de DevOps do Azure apresenta uma experiência simplificada de onde pode colocar o seu código existente e o repositório de Git ou escolha um aplicativo de exemplo para criar um pipeline de entrega contínua (CD) para o Azure e a integração contínua (CI). 

Projetos de DevOps também:
* Cria automaticamente os recursos do Azure, como uma base de dados SQL do Azure.
* Cria e configura um pipeline de lançamento em Pipelines do Azure, que inclui um pipeline de compilação para CI.
* Define um pipeline de lançamento para o CD. 
* Cria um recurso do Application Insights do Azure para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilizar projetos de DevOps do Azure para implementar a sua aplicação ASP.NET e o código de base de dados do Azure SQL
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure
> * Ligar à base de dados SQL do Azure 
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Criar um projeto em projetos de DevOps para uma aplicação ASP.NET e uma base de dados SQL do Azure

Projetos de DevOps cria um pipeline CI/CD em Pipelines do Azure. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure, como uma base de dados SQL do Azure, na subscrição do Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, escreva **projetos de DevOps**e, em seguida, selecione **criar**.

    ![O dashboard de projetos de DevOps](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.NET**e, em seguida, selecione **próxima**.

1. Sob **escolher uma estrutura de aplicações**, selecione **ASP.NET**.

1. Selecione **adicionar uma base de dados**e, em seguida, selecione **próxima**.  
    A estrutura de aplicativos, que selecionou no passo anterior, dita o tipo de destino de implementação de serviço do Azure está disponível aqui. 
    
1. Selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização do Azure DevOps ou selecione uma organização existente. 

1. Introduza um nome para o seu projeto de DevOps do Azure. 

1. Selecione os serviços de subscrição do Azure.  
    Opcionalmente, para ver as definições de configuração do Azure adicionais e para identificar o nome de utilizador a **detalhes de início de sessão do servidor de base de dados** secção, pode selecionar **alteração**. Store o nome de utilizador para os próximos passos neste tutorial. Se realizar este passo opcional, sair da área de configuração do Azure antes de selecionar **feito**.
 
1. Selecione **Done** (Concluído).  
    Após alguns minutos, o processo é concluído e o dashboard de projetos de DevOps for apresentado no portal do Azure. Também pode navegar para o dashboard diretamente a partir **todos os recursos** no portal do Azure. No lado direito, selecione **procurar** para ver a sua aplicação em execução.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

Projetos de DevOps configura automaticamente um pipeline de CI/CD completo nos repositórios do Azure. Pode explorar e personalizar o pipeline. Para se familiarizar com o pipeline de compilação de DevOps do Azure, efetue o seguinte:

1. Na parte superior do dashboard de projetos de DevOps, selecione **Criar pipelines**.  
    Um separador do browser apresenta o pipeline de compilação para o novo projeto.

1. Aponte para o **estado** campo e, em seguida, selecione as reticências (...).  
    Um menu apresenta várias opções, como a colocação em fila uma nova compilação, colocar em pausa uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, tais como origens ao obter a partir do Git saídas do repositório, a restauração de dependências e a publicação é utilizada para implementações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Este painel apresenta uma trilha de auditoria das alterações recentes para a compilação. Pipelines do Azure mantém um registro de todas as alterações feitas no pipeline de compilação e permite-lhe comparar versões.

1. Selecione **Acionadores**.  
    Projetos de DevOps cria automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação. Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

Projetos de DevOps automaticamente cria e configura os passos necessários para implementar a partir de sua organização de DevOps do Azure à sua subscrição do Azure. Estes passos incluem a configuração de uma ligação de serviço do Azure para autenticar o DevOps do Azure à sua subscrição do Azure. A automação também cria um pipeline de CD, que fornece o CD para a máquina virtual do Azure. Para saber mais sobre o pipeline de CD de DevOps do Azure, faça o seguinte:

1. Selecione **criar e lançar**e, em seguida, selecione **versões**.  
    Projetos de DevOps cria um pipeline de lançamento para gerir implementações para o Azure.

1. Selecione as reticências (...) junto ao seu pipeline de lançamento e, em seguida, selecione **editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de compilação examinado nos passos anteriores produz a saída que é utilizada para o artefacto. 

1. No lado direito do **Drop** ícone, selecione **acionador de implementação contínua**.  
    Esta versão de pipeline tem um acionador de CD ativado, o que é executada uma implantação sempre que um novo artefacto de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

    Projetos de DevOps configura uma palavra-passe aleatória do SQL e utiliza-o para o pipeline de lançamento.
    
1. No lado esquerdo, selecione **variáveis**. 

   > [!NOTE]
   > Execute o seguinte passo apenas se tiver alterado a palavra-passe do SQL Server. Existe uma variável de palavra-passe única.
  
1. Junto a **valor** caixa, selecione o ícone de cadeado, introduza a palavra-passe nova e, em seguida, selecione **guardar**.

1. No lado esquerdo, selecione **tarefas**e, em seguida, selecione o seu ambiente.  
    As tarefas são as atividades que executa o seu processo de implantação e estão agrupados em fases. Esta versão de pipeline tem uma fase única, que contém um *implementação de serviço de aplicações do Azure* e *implementação de base de dados SQL do Azure* tarefas.

1. Selecione o *executar o Azure SQL* de tarefas e examine as diversas propriedades que são utilizadas para a implantação do SQL.  
    Sob **pacote de implementação**, a tarefa utiliza um *SQL DACPAC* ficheiro.

1. No lado direito, selecione **ver versões** para apresentar um histórico de versões.

1. Selecione as reticências (...) junto a uma versão e, em seguida, selecione **aberto**.  
     Pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
     Esta vista mostra as confirmações de código que estão associadas esta implementação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
     Os registos contêm informações úteis sobre o processo de implementação. Pode visualizá-las durante e depois das implementações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure 

 > [!NOTE]
 > O procedimento seguinte testa o pipeline de CI/CD com uma alteração de texto simples. Para testar o processo de implantação do SQL, pode fazer, opcionalmente, um esquema do SQL Server alterar para a tabela.

Agora, está pronto para colaborar com uma equipa na sua aplicação ao utilizar um processo de CI/CD que implementa automaticamente o seu trabalho mais recente ao seu Web site. Cada alteração para o repositório de Git inicia uma compilação em DevOps do Azure e um pipeline CD executa uma implementação para o Azure. Siga o procedimento nesta secção, ou utilize outra técnica para consolidar as alterações ao seu repositório. As alterações de código iniciam o processo de CI/CD e implementar automaticamente as suas alterações para o Azure.

1. No painel esquerdo, selecione **código**e, em seguida, vá para o seu repositório.

1. Vá para o *SampleWebApplication\Views\Home* diretório, selecione as reticências (...) junto aos *Index. cshtml* de ficheiros e, em seguida, selecione **editar**. 

1. Fazer uma alteração para o ficheiro, por exemplo, adicionar algum texto dentro de uma das etiquetas div. 

1. Na parte superior direita, selecione **consolidar**e, em seguida, selecione **consolidar** novamente para enviar sua alteração.  
    Após alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão executa para implementar as alterações. Monitorize o estado de compilação no dashboard de projetos de DevOps ou no navegador com a sua organização de DevOps do Azure.

1. Depois de concluída a versão, atualize a aplicação para verificar as suas alterações.

## <a name="connect-to-the-azure-sql-database"></a>Ligar à base de dados SQL do Azure

Precisa das permissões necessárias para ligar à base de dados SQL do Azure.

1. No dashboard de projetos de DevOps, selecione **base de dados SQL** para ir para a página de gestão para a base de dados do SQL.
   
1. Selecione **definir firewall do servidor**e, em seguida, selecione **Adicionar IP de cliente**. 

1. Selecione **Guardar**.  
    O IP de cliente tem agora acesso ao recurso do Azure do SQL Server.

1. Volte para o **base de dados SQL** painel. 

1. No lado direito, selecione o nome do servidor para navegar para a página de configuração **SQL Server**.

1. Selecione **Repor palavra-passe**, introduza uma palavra-passe para o início de sessão de administrador do SQL Server e, em seguida, selecione **guardar**.  
    Certifique-se de que manter esta palavra-passe para utilizar mais tarde neste tutorial.

    Agora pode utilizar, opcionalmente, ferramentas de cliente, como o SQL Server Management Studio ou o Visual Studio para ligar ao SQL Server e a base de dados SQL do Azure. Utilize a propriedade **Nome do servidor** para ligar.

    Se não alterar o nome de utilizador de base de dados quando configurou inicialmente o projeto em projetos de DevOps, o seu nome de utilizador é a parte local do seu endereço de e-mail. Por exemplo, se seu endereço de e-mail for *diogoandrade\@microsoft.com*, é o seu nome de utilizador *diogoandrade*.

   > [!NOTE]
   > Se alterar a palavra-passe para o início de sessão do SQL, tem de alterar a palavra-passe da variável de pipeline de lançamento, conforme descrito na secção "Examinar o pipeline CD".

## <a name="clean-up-resources"></a>Limpar recursos

Se estiver a testar, pode evitar acumulação de encargos de faturas por limpar seus recursos. Quando já não são necessários, pode eliminar a base de dados SQL do Azure e os recursos relacionados que criou neste tutorial. Para tal, utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente recursos. O *eliminar* funcionalidade destrói os dados que são criados pelo projeto em projetos de DevOps no Azure e do Azure DevOps, e não será possível recuperá-la. Utilize este procedimento apenas depois de ler atentamente as instruções.

1. No portal do Azure, vá para o dashboard de projetos de DevOps.
2. Na parte superior direita, selecione **eliminar**. 
3. Na linha de comandos, selecione **Sim** ao *eliminar permanentemente* os recursos.

## <a name="next-steps"></a>Passos Seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar projetos de DevOps do Azure para implementar a sua aplicação ASP.NET e o código de base de dados do Azure SQL
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Consolide as alterações para repositórios do Azure e implementá-las automaticamente para o Azure
> * Ligar à base de dados SQL do Azure 
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o seu pipeline de vários estágios de implementação contínua (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

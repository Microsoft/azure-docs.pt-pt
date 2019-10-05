---
title: 'Tutorial: Implantar o aplicativo ASP.NET e o código do banco de dados SQL do Azure usando Azure DevOps Projects'
description: DevOps Projects facilita a introdução ao Azure. Com o DevOps Projects, você pode implantar seu aplicativo ASP.NET e o código do banco de dados SQL do Azure em algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971491"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Tutorial: Implantar o aplicativo ASP.NET e o código do banco de dados SQL do Azure usando Azure DevOps Projects

Azure DevOps Projects apresenta uma experiência simplificada em que você pode colocar seu código existente e o repositório git ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e um CD (entrega contínua) para o Azure. 

DevOps Projects também:
* Cria automaticamente recursos do Azure, como um banco de dados SQL do Azure.
* Cria e configura um pipeline de liberação no Azure Pipelines que inclui um pipeline de compilação para CI.
* Configura um pipeline de liberação para CD. 
* Cria um recurso de informações de Aplicativo Azure para monitoramento.

Neste tutorial, irá:

> [!div class="checklist"]
> * Use Azure DevOps Projects para implantar seu aplicativo ASP.NET e o código do banco de dados SQL do Azure
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure
> * Conectar-se ao banco de dados SQL do Azure 
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma subscrição gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Criar um projeto no DevOps Projects para um aplicativo ASP.NET e um banco de dados SQL do Azure

DevOps Projects cria um pipeline de CI/CD no Azure Pipelines. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure, como um banco de dados SQL do Azure, na assinatura do Azure de sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**.

1. Na caixa de pesquisa, digite **DevOps Projects**e, em seguida, selecione **criar**.

    ![O painel DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Selecione **.net**e, em seguida, selecione **Avançar**.

1. Em **escolher uma estrutura de aplicativo**, selecione **ASP.net**.

1. Selecione **Adicionar um banco de dados**e, em seguida, selecione **Avançar**.  
    A estrutura do aplicativo, que você escolheu em uma etapa anterior, determina o tipo de destino de implantação do serviço do Azure que está disponível aqui. 
    
1. Selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configurar o Azure DevOps e uma subscrição do Azure

1. Crie uma nova organização de DevOps do Azure ou selecione uma organização existente. 

1. Insira um nome para seu projeto DevOps do Azure. 

1. Selecione os serviços de assinatura do Azure.  
    Opcionalmente, para exibir definições de configuração adicionais do Azure e identificar o nome de usuário na seção **detalhes de logon do servidor de banco de dados** , você pode selecionar **alterar**. Armazene o nome de usuário para etapas futuras neste tutorial. Se você executar essa etapa opcional, saia da área de configuração do Azure antes de selecionar **concluído**.
 
1. Selecione **Done** (Concluído).  
    Após alguns minutos, o processo é concluído e o painel de DevOps Projects é aberto na portal do Azure. Você também pode navegar até o painel diretamente de **todos os recursos** no portal do Azure. À direita, selecione **procurar** para exibir o aplicativo em execução.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Projects configura automaticamente um pipeline de CI/CD completo no Azure Repos. Pode explorar e personalizar o pipeline. Para se familiarizar com o pipeline de compilação do Azure DevOps, faça o seguinte:

1. Na parte superior do painel de DevOps Projects, selecione **criar pipelines**.  
    Uma guia do navegador exibe o pipeline de Build para seu novo projeto.

1. Aponte para o campo **status** e, em seguida, selecione as reticências (...).  
    Um menu exibe várias opções, como enfileirar uma nova compilação, pausar uma compilação e editar o pipeline de compilação.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de compilação.  
    A compilação executa várias tarefas, como a busca de fontes do repositório git, a restauração de dependências e a publicação de saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Altere o nome do seu pipeline de compilação para algo mais descritivo, selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**.  
    Esse painel exibe uma trilha de auditoria de suas alterações recentes para a compilação. Azure Pipelines controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

1. Selecione **Acionadores**.  
    DevOps Projects cria automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação. Opcionalmente, você pode optar por incluir ou excluir ramificações do processo de CI.

1. Selecione **Retenção**.  
    Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

DevOps Projects cria e configura automaticamente as etapas necessárias para implantar de sua organização do Azure DevOps em sua assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de CD, que fornece o CD para a máquina virtual do Azure. Para saber mais sobre o pipeline do CD do Azure DevOps, faça o seguinte:

1. Selecione **Compilar e liberar**e, em seguida, selecione **versões**.  
    DevOps Projects cria um pipeline de liberação para gerenciar implantações no Azure.

1. Selecione as reticências (...) ao lado de seu pipeline de lançamento e, em seguida, selecione **Editar**.  
    O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**.  
    O pipeline de Build que você examinou nas etapas anteriores produz a saída usada para o artefato. 

1. À direita do ícone de **soltar** , selecione **gatilho de implantação contínua**.  
    Este pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que um novo artefato de compilação está disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual. 

    DevOps Projects configura uma senha SQL aleatória e a usa para o pipeline de lançamento.
    
1. À esquerda, selecione **variáveis**. 

   > [!NOTE]
   > Execute a etapa a seguir somente se você tiver alterado o SQL Server senha. Há uma única variável de senha.
  
1. Ao lado da caixa **valor** , selecione o ícone de cadeado, insira a nova senha e, em seguida, selecione **salvar**.

1. À esquerda, selecione **tarefas**e, em seguida, selecione seu ambiente.  
    As tarefas são as atividades que seu processo de implantação executa e são agrupadas em fases. Este pipeline de versão tem uma única fase, que contém uma implantação de *serviço Azure app* e uma tarefa de *implantação de banco de dados SQL do Azure* .

1. Selecione a tarefa *executar Azure SQL* e examine as várias propriedades que são usadas para a implantação do SQL.  
    Em **pacote de implantação**, a tarefa usa um arquivo *DACPAC do SQL* .

1. À direita, selecione **Exibir liberações** para exibir um histórico de versões.

1. Selecione as reticências (...) ao lado de uma versão e, em seguida, selecione **abrir**.  
     Você pode explorar vários menus, como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**.  
     Esta exibição mostra as confirmações de código que estão associadas a essa implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecione **Registos**.  
     Os registos contêm informações úteis sobre o processo de implementação. Você pode exibi-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure 

 > [!NOTE]
 > O procedimento a seguir testa o pipeline de CI/CD com uma alteração de texto simples. Para testar o processo de implantação do SQL, você pode, opcionalmente, fazer uma alteração de esquema de SQL Server na tabela.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente seu trabalho mais recente em seu site. Cada alteração no repositório git inicia uma compilação no Azure DevOps e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações no repositório. As alterações de código iniciam o processo de CI/CD e implantam automaticamente suas alterações no Azure.

1. No painel esquerdo, selecione **código**e, em seguida, vá para o repositório.

1. Vá para o diretório *SampleWebApplication\Views\Home* , selecione as reticências (...) ao lado do arquivo *index. cshtml* e, em seguida, selecione **Editar**. 

1. Faça uma alteração no arquivo, como adicionar texto dentro de uma das marcas div. 

1. No canto superior direito, selecione **confirmar**e, em seguida, selecione **confirmar** novamente para enviar por push sua alteração.  
    Depois de alguns instantes, uma compilação é iniciada no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status da compilação no painel de DevOps Projects ou no navegador com sua organização DevOps do Azure.

1. Depois que o lançamento for concluído, atualize seu aplicativo para verificar suas alterações.

## <a name="connect-to-the-azure-sql-database"></a>Conectar-se ao banco de dados SQL do Azure

Você precisa de permissões apropriadas para se conectar ao banco de dados SQL do Azure.

1. No painel DevOps Projects, selecione **banco de dados SQL** para ir para a página de gerenciamento do banco de dados SQL.
   
1. Selecione **definir Firewall do servidor**e, em seguida, selecione **Adicionar IP do cliente**. 

1. Selecione **Guardar**.  
    O IP do cliente agora tem acesso ao recurso SQL Server Azure.

1. Volte para o painel do **banco de dados SQL** . 

1. À direita, selecione o nome do servidor para navegar até a página de configuração para **SQL Server**.

1. Selecione **Redefinir senha**, insira uma senha para o logon de administrador do SQL Server e, em seguida, selecione **salvar**.  
    Certifique-se de manter essa senha para usar posteriormente neste tutorial.

    Você pode, opcionalmente, usar ferramentas de cliente como o SQL Server Management Studio ou o Visual Studio para se conectar ao SQL Server e ao banco de dados SQL do Azure. Utilize a propriedade **Nome do servidor** para ligar.

    Se você não alterou o nome de usuário do banco de dados quando configurou inicialmente o projeto no DevOps Projects, seu nome de usuário é a parte local do seu endereço de email. Por exemplo, se seu endereço de email for *johndoe\@microsoft.com*, seu nome de usuário será *davibarros*.

   > [!NOTE]
   > Se você alterar sua senha para o logon do SQL, deverá alterar a senha na variável de pipeline de liberação, conforme descrito na seção "examinar o pipeline de CD".

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver testando, poderá evitar a acumulação de encargos de cobrança limpando seus recursos. Quando eles não forem mais necessários, você poderá excluir o banco de dados SQL do Azure e os recursos relacionados que você criou neste tutorial. Para fazer isso, use a funcionalidade **excluir** no painel DevOps Projects.

> [!IMPORTANT]
> O procedimento a seguir exclui permanentemente os recursos. A funcionalidade de *exclusão* destrói os dados criados pelo projeto em DevOps Projects no Azure e no Azure DevOps, e você não poderá recuperá-los. Use este procedimento somente depois de ler atentamente os prompts.

1. Na portal do Azure, vá para o painel de DevOps Projects.
2. No canto superior direito, selecione **excluir**. 
3. No prompt, selecione **Sim** para *excluir permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Use Azure DevOps Projects para implantar seu aplicativo ASP.NET e o código do banco de dados SQL do Azure
> * Configurar o Azure DevOps e uma subscrição do Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações para Azure Repos e implantá-las automaticamente no Azure
> * Conectar-se ao banco de dados SQL do Azure 
> * Limpar recursos

Para saber mais sobre o pipeline de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definir o pipeline de implantação contínua de vários estágios (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Os meus vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

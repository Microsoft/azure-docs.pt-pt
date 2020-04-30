---
title: 'Tutorial: Implemente a sua aplicação ASP.NET e o código de base de dados Azure SQL utilizando o Arranque de Azure DevOps'
description: DevOps Starter facilita o início do Azure. Com o DevOps Starter, pode implementar a sua aplicação ASP.NET e o código de base de dados Azure SQL em alguns passos rápidos.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 93b150d47f1703662ebda5b017e1824cf74b7ab0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233708"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Tutorial: Implemente a sua aplicação ASP.NET e o código de base de dados Azure SQL utilizando o Arranque de Azure DevOps

O Azure DevOps Starter apresenta uma experiência simplificada onde pode trazer o seu código existente e git repo ou escolher uma aplicação de amostra para criar um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure. 

DevOps Starter também:
* Cria automaticamente recursos Azure, como uma base de dados Azure SQL.
* Cria e configura um oleoduto de libertação em Pipelines Azure que inclui um pipeline de construção para CI.
* Estabelece um oleoduto de libertação para CD. 
* Cria um recurso Azure Application Insights para monitorização.

Neste tutorial, irá:

> [!div class="checklist"]
> * Utilize o Starter Azure DevOps para implementar a sua aplicação ASP.NET e código de base de dados Azure SQL
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure
> * Ligue-se à base de dados Azure SQL 
> * Limpar recursos

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Pode obter uma gratuita através do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Criar um projeto em Projetos DevOps para uma app ASP.NET e uma base de dados Azure SQL

DevOps Starter cria um oleoduto CI/CD em Pipelines Azure. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. DevOps Starter também cria recursos Azure, como uma base de dados Azure SQL, na subscrição Azure à sua escolha.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa, digite **DevOps Starter**e, em seguida, selecione. Clique em **Adicionar** para criar um novo.

    ![O painel de arranque de DevOps](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET**, e, em seguida, selecione **Next**.

1. Em **'Escolha um quadro de aplicação**,selecione **ASP.NET**.

1. Selecione **Adicionar uma base de dados**e, em seguida, selecione **Next**. O quadro de aplicação, que escolheu num passo anterior, dita o tipo de alvo de implantação de serviços Azure que está disponível aqui. 
    
1. Selecione **Seguinte**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configure Azure DevOps e uma subscrição Azure

1. Crie uma nova organização Azure DevOps, ou selecione uma organização existente. 

1. Insira um nome para o seu projeto Azure DevOps. 

1. Selecione os seus serviços de subscrição Azure. Opcionalmente, para visualizar configurações adicionais de configuração do Azure e para identificar o nome de utilizador na secção detalhes de início de sessão do servidor de **dados,** pode selecionar **Alterar**. Guarde o nome de utilizador para os passos seguintes deste tutorial. Se executar este passo opcional, saia da área de configuração Do Azure antes de selecionar **Done**.
 
1. Selecione **Done** (Concluído). Após alguns minutos, o processo está concluído e o painel de arranque de DevOps abre no portal Azure. Também pode navegar para o tablier diretamente a partir de **todos os recursos** no portal Azure. À direita, **selecione Browse** para visualizar a sua aplicação de execução.
    
## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

DevOps Starter configura automaticamente um oleoduto CI/CD completo em Azure Repos. Pode explorar e personalizar o pipeline. Para se familiarizar com o pipeline de construção Azure DevOps, faça o seguinte:

1. Na parte superior do painel de arranque de DevOps, selecione **os gasodutos Build**. Um separador de navegador exibe o pipeline de construção para o seu novo projeto.

1. Aponte para o campo **Status** e, em seguida, selecione a elipse (...). Um menu exibe várias opções, como fazer fila de uma nova construção, parar uma construção e editar o pipeline de construção.

1. Selecione **Editar**.

1. Neste painel, pode examinar as várias tarefas para o seu pipeline de construção. A construção executa várias tarefas, tais como a busca de fontes do repositório Git, restaurar dependências e publicar saídas usadas para implantações.

1. Na parte superior do pipeline de compilação, selecione o nome do pipeline de compilação.

1. Mude o nome do seu oleoduto de construção para algo mais descritivo, selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

1. No nome do pipeline de compilação, selecione **Histórico**. Este painel apresenta um rasto de auditoria das suas recentes alterações para a construção. A Azure Pipelines acompanha quaisquer alterações feitas ao pipeline de construção, e permite comparar versões.

1. Selecione **Triggers**. DevOps Starter cria automaticamente um gatilho CI, e cada compromisso com o repositório inicia uma nova construção. Opcionalmente, pode optar por incluir ou excluir balcões do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

## <a name="examine-the-cd-pipeline"></a>Examinar o pipeline de CD

DevOps Starter cria e configura automaticamente os passos necessários para implementar da sua organização Azure DevOps para a sua subscrição Azure. Estes passos incluem configurar uma ligação de serviço Azure para autenticar O Azure DevOps à sua subscrição Azure. A automatização também cria um pipeline de CD, que fornece o CD à máquina virtual Azure. Para saber mais sobre o pipeline de CD Azure DevOps, faça o seguinte:

1. Selecione **Construir e Soltar**e, em seguida, selecione **Lançamentos**. DevOps Starter cria um gasoduto de libertação para gerir as implementações para o Azure.

1. Selecione a elipsis (...) junto ao seu gasoduto de libertação e, em seguida, **selecione Editar**. O pipeline de lançamento contém um *pipeline*, que define o processo de lançamento.

1. Em **Artefactos**, selecione **Remover**. O oleoduto de construção que examinou nos passos anteriores produz a saída que é usada para o artefacto. 

1. À direita do ícone **Drop,** selecione **O gatilho de implantação contínua**. Este gasoduto de libertação tem um gatilho de CD ativado, que executa uma implantação sempre que um novo artefacto de construção está disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual. 

    DevOps Starter configura uma senha SQL aleatória e usa-a para o pipeline de lançamento.
    
1. À esquerda, selecione **Variáveis**. 

   > [!NOTE]
   > Execute o passo seguinte apenas se alterar a palavra-passe do SQL Server. Há uma única variável de senha.
  
1. Ao lado da caixa **Value,** selecione o ícone do cadeado, introduza a nova palavra-passe e, em seguida, selecione **Guardar**.

1. À esquerda, selecione **Tarefas**e, em seguida, selecione o seu ambiente. As tarefas são as atividades que o seu processo de implantação executa, e são agrupadas por fases. Este pipeline de lançamento tem uma única fase, que contém uma implementação de serviço de *aplicação Azure* e tarefa de implantação de bases de *dados Azure SQL.*

1. Selecione a tarefa *Execute Azure SQL* e examine as várias propriedades que são usadas para a implementação do SQL. No **pacote de implantação,** a tarefa utiliza um ficheiro *SQL DACPAC.*

1. À direita, selecione **ver ver ver para** mostrar um histórico de lançamentos.

1. Selecione a elipse (...) junto a um lançamento e, em seguida, **selecione Open**. Pode explorar vários menus, tais como um resumo de lançamento, itens de trabalho associados e testes.

1. Selecione **Consolidações**. Esta visão mostra código compromete-se que estão associados a esta implantação. Compare as versões para ver as diferenças de consolidação entre implementações.

1. Selecionar **Registos**. Os registos contêm informações úteis sobre o processo de implementação. Pode vê-los durante e após as implantações.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure 

 > [!NOTE]
 > O procedimento seguinte testa o gasoduto CI/CD com uma simples alteração de texto. Para testar o processo de implementação do SQL, pode opcionalmente fazer uma alteração de esquema sQL Server para a tabela.

Está agora pronto para colaborar com uma equipa na sua aplicação utilizando um processo CI/CD que implementa automaticamente o seu mais recente trabalho no seu website. Cada alteração para o repo Git inicia uma construção em Azure DevOps, e um pipeline de CD executa uma implantação para Azure. Siga o procedimento nesta secção ou utilize outra técnica para efetuar alterações no seu repositório. As alterações de código iniciam o processo CI/CD e implementam automaticamente as suas alterações para o Azure.

1. No painel esquerdo, selecione **Código**, e depois vá ao seu repositório.

1. Vá ao *sampleWebApplication\Views\Home* diretório, selecione a elipsis (...) ao lado do ficheiro *Index.cshtml* e, em seguida, selecione **Editar**. 

1. Faça uma alteração no ficheiro, como por exemplo, adicionar algum texto dentro de uma das etiquetas de div. 

1. Na parte superior direita, selecione **Comprometer**- e, em seguida, selecione **Comprometer** novamente para empurrar a sua mudança. Após alguns momentos, uma construção começa em Azure DevOps e uma libertação executa para implementar as mudanças. Monitorize o estado de construção no painel de arranque de DevOps ou no navegador com a sua organização Azure DevOps.

1. Depois de concluída a libertação, refresque a sua aplicação para verificar as suas alterações.

## <a name="connect-to-the-azure-sql-database"></a>Ligue-se à base de dados Azure SQL

Precisa de permissões adequadas para se ligar à base de dados Azure SQL.

1. No painel de arranque de DevOps, selecione Base de **Dados SQL** para ir à página de gestão da base de dados SQL.
   
1. Selecione **definir a firewall do servidor**, e, em seguida, selecione Adicionar IP do **cliente**. 

1. Selecione **Guardar**. O IP do seu cliente tem agora acesso ao recurso SQL Server Azure.

1. Volte para o painel de base de **dados SQL.** 

1. À direita, selecione o nome do servidor para navegar na página de configuração do **Servidor SQL**.

1. Selecione **a palavra-passe Deredefinida,** introduza uma palavra-passe para o login do servidor SQL e, em seguida, selecione **Guardar**. Certifique-se de manter esta senha para usar mais tarde neste tutorial.

    Pode agora utilizar opcionalmente ferramentas de cliente, como o SQL Server Management Studio ou o Visual Studio, para se ligar ao SQL Server e à base de dados Azure SQL. Utilize a propriedade **Nome do servidor** para ligar.

    Se não alterou o nome de utilizador da base de dados quando inicialmente configurou o projeto em Projetos DevOps, o seu nome de utilizador é a parte local do seu endereço de e-mail. Por exemplo, se o seu endereço de e-mail for *johndoe\@microsoft.com*, o seu nome de utilizador é *johndoe*.

   > [!NOTE]
   > Se alterar a sua palavra-passe para o login SQL, tem de alterar a palavra-passe na variável do pipeline de lançamento, tal como descrito na secção [de pipeline Examine.](#examine-the-cd-pipeline)

## <a name="clean-up-resources"></a>Limpar recursos

Se estiver a testar, pode evitar acumular taxas de faturação limpando os seus recursos. Quando já não são necessários, pode eliminar a base de dados Azure SQL e os recursos relacionados que criou neste tutorial. Para isso, utilize a funcionalidade **Eliminar** no painel de arranque de DevOps.

> [!IMPORTANT]
> O procedimento seguinte elimina permanentemente os recursos. A funcionalidade *Delete* destrói os dados criados pelo projeto em DevOps Starter tanto no Azure como no Azure DevOps, e não poderá recuperá-los. Utilize este procedimento apenas depois de ter lido atentamente as instruções.

1. No portal Azure, vá ao painel de arranque de DevOps.
2. Na parte superior direita, selecione **Delete**. 
3. No momento, selecione **Sim** para *apagar permanentemente* os recursos.

## <a name="next-steps"></a>Passos seguintes

Opcionalmente, pode modificar estes pipelines de compilação e versão para satisfazer as necessidades da sua equipa. Também pode utilizar este padrão de CI/CD como modelo para outros pipelines. Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilize o Starter Azure DevOps para implementar a sua aplicação ASP.NET e código de base de dados Azure SQL
> * Configure Azure DevOps e uma subscrição Azure 
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Comprometa alterações ao Azure Repos e desempene-as automaticamente para o Azure
> * Ligue-se à base de dados Azure SQL 
> * Limpar recursos

Para saber mais sobre o oleoduto CI/CD, consulte:

> [!div class="nextstepaction"]
> [Defina o seu gasoduto de implantação contínua em várias fases (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

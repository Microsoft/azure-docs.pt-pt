---
title: Tarefas para a equipa liderar na Equipa de Processos de Ciência de Dados
description: Uma passagem detalhada das tarefas para uma equipa liderar uma equipa de processos de ciência de dados de equipa
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321203"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Tarefas para a equipa liderar uma equipa de processos de ciência de dados de equipa

Este artigo descreve as tarefas que uma *equipa lidera* para a sua equipa de ciência de dados. O objetivo da equipa é estabelecer um ambiente de equipa colaborativo que normalize no Processo de Ciência de [Dados de Equipa](overview.md) (TDSP). O TDSP foi concebido para ajudar a melhorar a colaboração e a aprendizagem em equipa. 

O TDSP é uma metodologia ágil e iterativa da ciência dos dados para fornecer eficientemente soluções de análise preditivas e aplicações inteligentes. O processo destila as melhores práticas e estruturas da Microsoft e da indústria.  O objetivo é a implementação bem-sucedida de iniciativas de ciência de dados e a realização plena dos benefícios dos seus programas de análise. Para um esboço das funções de pessoal e das tarefas associadas para uma equipa de ciência de dados que esboça na TDSP, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md)

Um líder de equipa gere uma equipa composta por vários cientistas de dados na unidade de ciência de dados de uma empresa. Dependendo do tamanho e estrutura da unidade de ciência de dados, o [gestor do grupo](group-manager-tasks.md) e o líder da equipa podem ser a mesma pessoa, ou podem delegar as suas tarefas em substitutos. Mas as tarefas em si não mudam. 

O diagrama a seguir mostra o fluxo de trabalho para as tarefas que o líder da equipa completa para criar um ambiente de equipa:

![Fluxo de trabalho de tarefa de liderança da equipa](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Crie um **projeto de equipa** na organização do grupo em Azure DevOps. 
  
1. Mude o nome do repositório de equipa padrão para **TeamUtilities**.
  
1. Crie um novo repositório **TeamTemplate** no projeto da equipa. 
  
1. Importe o conteúdo dos repositórios **GroupUtilities** e **GroupProjectTemplate** do grupo nos repositórios **TeamUtilities** e **TeamTemplate.** 
  
1. Configurar **o controlo de segurança** adicionando membros da equipa e configurando as suas permissões.
  
1. Se necessário, crie dados de equipa e recursos de análise:
   - Adicione utilitários específicos da equipa ao repositório **TeamUtilities.** 
   - Crie **o armazenamento de ficheiros Azure** para armazenar ativos de dados que podem ser úteis para toda a equipa. 
   - Monte o armazenamento de ficheiros Azure na máquina virtual de ciência de **dados** (DSVM) do líder da equipa e adicione-lhe ativos de dados.

O seguinte tutorial percorre os degraus em detalhe.

> [!NOTE] 
> Este artigo utiliza a Azure DevOps e um DSVM para criar um ambiente de equipa TDSP, porque é assim que se implementa a TDSP na Microsoft. Se a sua equipa utilizar outras plataformas de hospedagem ou desenvolvimento de códigos, as tarefas de liderança da equipa são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram criados pelo seu [gestor de grupo:](group-manager-tasks.md)

- A **organização** Azure DevOps para a sua unidade de dados
- Repositórios **de GrupoProjectTemplate** e **GroupUtilities,** povoados com o conteúdo dos repositórios **projectTemplate** e **Utilities** da equipa da Microsoft TDSP
- Permissões na sua conta de organização para criar projetos e repositórios para a sua equipa

Para poder clonar repositórios e modificar o seu conteúdo na sua máquina local ou DSVM, ou configurar o armazenamento de ficheiros Azure e montá-lo no seu DSVM, precisa do seguinte:

- Uma subscrição do Azure.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as Plataformas e o [apêndice de ferramentas.](platforms-and-tools.md#appendix)
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criado e configurado em Azure. Para obter mais informações e instruções, consulte a [Documentação da Máquina Virtual da Ciência dos Dados.](../data-science-virtual-machine/index.yml)
- Para um DSVM Do Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **de Descarregamento e Instalação** e selecione o **instalador mais recente**. Descarregue o instalador *.exe* da página do instalador e execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção **de chaves públicas Create SSH** no [apêndice de ferramentas e ferramentas.](platforms-and-tools.md#appendix) 

## <a name="create-a-team-project-and-repositories"></a>Criar um projeto de equipa e repositórios

Nesta secção, cria os seguintes recursos na organização Azure DevOps do seu grupo:

- O projeto **MyTeam** em Azure DevOps
- O repositório **TeamTemplate**
- O repositório **teamutilities**

Os nomes especificados para os repositórios e diretórios neste tutorial assumem que pretende estabelecer um projeto separado para a sua própria equipa dentro da sua maior organização de ciência de dados. No entanto, todo o grupo pode optar por trabalhar no âmbito de um único projeto criado pelo gestor do grupo ou administrador da organização. Então, todas as equipas de ciência de dados criam repositórios no âmbito deste único projeto. Este cenário pode ser válido para:
- Um pequeno grupo de ciência de dados que não tem várias equipas de ciência de dados. 
- Um grupo de ciência de dados maior com várias equipas de ciência de dados que, no entanto, quer otimizar a colaboração inter-equipa com atividades como o planeamento de sprint a nível de grupo. 

Se as equipas optarem por ter os seus repositórios específicos da equipa no âmbito de um único projeto de grupo, a equipa lidera deve criar os repositórios com nomes como *\<TeamName> Modelo* e *\<TeamName> Utilitários.* Por exemplo: *TeamATemplate* e *TeamAUtilities*. 

Em todo o caso, as equipas lideram a necessidade de informar os seus membros da equipa sobre qual o modelo e os repositórios de utilidades para configurar e clonar. Os líderes do projeto devem seguir as tarefas de liderança do [projeto para uma equipa de ciência de dados](project-lead-tasks.md) para criar repositórios de projetos, seja em projetos separados ou num único projeto. 

### <a name="create-the-myteam-project"></a>Criar o projeto MyTeam

Para criar um projeto separado para a sua equipa:

1. No seu navegador web, aceda à página inicial da organização Azure DevOps do seu grupo em *\/ / \<server name> / \<organization name> HTTPS URL:* e selecione **Novo projeto**. 
   
   ![Selecione novo projeto](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. No diálogo do **projeto Create,** insira o nome da sua equipa, como *MyTeam,* com **o nome do Projeto,** e, em seguida, selecione **Advanced**. 
   
1. Sob **controlo de versão** , selecione **Git** , e no processo de produto **do trabalho** , selecione **Agile**. Em seguida, selecione **Criar**. 
   
   ![Criar o projeto](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
O projeto de equipa A página **de resumo** abre, com url de página *\/ / \<server name> / \<organization name> / \<team name> https:*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Mude o nome do repositório padrão myTeam para TeamUtilities

1. Na **Repos** página **resumo** do projeto **MyTeam,** em que serviço gostaria de **começar?** 
   
   ![Selecione Repos](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na página de repo do **MyTeam,** selecione o repositório **MyTeam** no topo da página e, em seguida, selecione **Gerir os repositórios** a partir do dropdown. 
   
   ![Selecione Gerir repositórios](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na página Definições do **Projeto,** selecione o **...** ao lado do repositório **MyTeam** e, em seguida, selecione **o repositório de renome**. 
   
   ![Selecione repositório de renomeação](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. No **retoco do repositório MyTeam,** insira *TeamUtilities* e, em seguida, selecione **Rename**. 

### <a name="create-the-teamtemplate-repository"></a>Criar o repositório TeamTemplate

1. Na página **Definições** do Projeto, selecione **Novo repositório.** 
   
   ![Selecione novo repositório](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Ou, selecione **Repos** da navegação à esquerda da página **sumária** do projeto **MyTeam,** selecione um repositório no topo da página e, em seguida, selecione **Novo repositório** a partir do dropdown.
   
1. No Criar um novo diálogo **de repositório,** certifique-se de que **o Git** é selecionado por **tipo**. Introduza *teamTemplate* com **o nome repositório** e, em seguida, selecione **Criar**.
   
   ![Criar repositório](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Confirme que pode ver os dois repositórios **TeamUtilities** e **TeamTemplate** na página de definições do seu projeto. 
   
   ![Dois repositórios de equipa](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importar o conteúdo dos repositórios comuns do grupo

Para povoar os repositórios da sua equipa com o conteúdo dos repositórios comuns do grupo criados pelo seu gestor de grupo:

1. Na página inicial do seu projeto **MyTeam,** selecione **Repos** na navegação à esquerda. Se receber uma mensagem de que o modelo **MyTeam** não foi encontrado, selecione o link em **Caso Contrário, navegue para o repositório padrão teamTemplate.** 
   
   O repositório **padrão teamTemplate** abre. 
   
1. Na **página TeamTemplate está a** página vazia, selecione **Import**. 
   
   ![Selecione Importar](./media/team-lead-tasks/import-repo.png)
   
1. No diálogo **repositório de repositório De Importação a Git,** selecione **Git** como o **tipo Origem** , e introduza o URL para o repositório de modelo comum do seu grupo em **URL clone**. O URL é *https: \/ / \<server name> / \<organization name> /_git/ \<repository name>*. Por exemplo: *https: \/ /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Selecione **Import** (Importar). O conteúdo do repositório de modelos de grupo é importado para o repositório do modelo da sua equipa. 
   
   ![Repositório de modelo comum grupo de importação](./media/team-lead-tasks/import-repo-2.png)
   
1. No topo da página **repos** do seu projeto, desça e selecione o repositório **TeamUtilities.**
   
1. Repita o processo de importação para importar o conteúdo do repositório de utilidades comuns do seu grupo, por exemplo *GroupUtilities,* no seu repositório **TeamUtilities.** 
   
Cada um dos seus dois repositórios de equipa agora contém os ficheiros do repositório comum do grupo correspondente. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Personalize o conteúdo dos repositórios da equipa

Se quiser personalizar o conteúdo dos repositórios da sua equipa para atender às necessidades específicas da sua equipa, pode fazê-lo agora. Pode modificar ficheiros, alterar a estrutura do diretório ou adicionar ficheiros e pastas.

Para modificar, carregar ou criar ficheiros ou pastas diretamente em Azure DevOps:

1. Na página **resumo** do projeto **MyTeam,** selecione **Repos**. 
   
1. No topo da página, selecione o repositório que pretende personalizar.

1. Na estrutura do reeso para o diretório, navegue para a pasta ou ficheiro que pretende alterar. 
   
   - Para criar novas pastas ou ficheiros, selecione a seta ao lado **de New**. 
     
     ![Criar novo arquivo](./media/team-lead-tasks/new-file.png)
     
   - Para fazer upload de ficheiros, **selecione upload file(s)**. 
     
     ![Carregar ficheiros](./media/team-lead-tasks/upload-files.png)
     
   - Para editar ficheiros existentes, navegue no ficheiro e, em seguida, **selecione Editar**. 
     
     ![Editar um ficheiro](./media/team-lead-tasks/edit-file.png)
     
1. Depois de adicionar ou editar ficheiros, **selecione Cometer**.
   
   ![Cometer alterações](./media/team-lead-tasks/commit.png)

Para trabalhar com repositórios na sua máquina local ou DSVM, primeiro copia ou *clona* os repositórios para a sua máquina local, e depois compromete-se e empurra as suas alterações para os repositórios de equipa partilhados, 

Para clonar repositórios:

1. Na página **resumo** do projeto **MyTeam,** selecione **Repos** , e no topo da página, selecione o repositório que pretende clonar.
   
1. Na página de repo, selecione **Clone** na parte superior direita.
   
1. No diálogo **do repositório clone,** na **linha Comando** , selecione **HTTPS** para uma ligação HTTP ou **SSH** para uma ligação SSH e copie o URL do clone para a sua área de transferência.
   
   ![URL de clone de cópia](./media/team-lead-tasks/clone.png)
   
1. Na sua máquina local, crie os seguintes diretórios:
   
   - Para janelas: **C:\GitRepos\MyTeam**
   - Para Linux, **$home/GitRepos/MyTeam** 
   
1. Mude para o diretório que criou.
   
1. Em Git Bash, executar o `git clone <clone URL>` comando, onde \<clone URL> está o URL que copiou do diálogo **Clone.**
   
   Por exemplo, utilize um dos seguintes comandos para clonar o repositório **TeamUtilities** para o diretório *MyTeam* na sua máquina local. 
   
   **Ligação HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Ligação SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Depois de fazer as alterações que quiser no clone local do seu repositório, comprometa-se e empurre as alterações para os repositórios de equipa partilhados. 

Executar os seguintes comandos Git Bash a partir do seu diretório local **GitRepos\MyTeam\TeamTemplate** ou **GitRepos\MyTeam\TeamUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se esta for a primeira vez que se compromete com um repositório git, poderá ter de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Executar os dois comandos seguintes:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se você está comprometendo-se com vários repositórios Git, use o mesmo nome e endereço de e-mail para todos eles. Usar o mesmo nome e endereço de e-mail é conveniente ao construir painéis power BI para rastrear as suas atividades git em vários repositórios.

## <a name="add-team-members-and-configure-permissions"></a>Adicione os membros da equipa e configuure permissões

Para adicionar membros à equipa:

1. Em Azure DevOps, a partir da página inicial do projeto **MyTeam,** selecione **as definições** do Projeto a partir da navegação à esquerda. 
   
1. A partir da navegação deixada por **Project Settings,** selecione **Equipas,** em seguida, na página **Equipas,** selecione a **Equipa MyTeam**. 
   
   ![Configure equipas](./media/team-lead-tasks/teams.png)
   
1. Na página **'Perfil da Equipa',** selecione **Adicionar**.
   
   ![Adicionar à Equipa MyTeam](./media/team-lead-tasks/add-to-team.png)
   
1. No **diálogo de utilizadores e grupos Adicionar,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Guardar as alterações**. 
   
   ![Adicionar utilizadores e grupos](./media/team-lead-tasks/add-users.png)
   

Para configurar permissões para membros da equipa:

1. A partir das **Definições** de Projeto deixou a navegação, selecione **Permissões**. 
   
1. Na página **Permissões,** selecione o grupo a que pretende adicionar membros. 
   
1. Na página para esse grupo, selecione **Membros** e, em seguida, **selecione Adicionar**. 
   
1. No popup de **membros do Convite,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Save**. 
   
   ![Conceder permissões aos membros](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Criar dados de equipa e recursos de análise

Este passo é opcional, mas partilhar dados e recursos de análise com toda a sua equipa tem benefícios de desempenho e custos. Os membros da equipa podem executar os seus projetos sobre os recursos partilhados, economizar em orçamentos e colaborar de forma mais eficiente. Pode criar o armazenamento de ficheiros Azure e montá-lo no seu DSVM para partilhar com os membros da equipa. 

Para obter informações sobre a partilha de outros recursos com a sua equipa, como os clusters Azure HDInsight Spark, consulte [Plataformas e ferramentas.](platforms-and-tools.md) Este tópico fornece orientação de uma perspetiva de ciência de dados sobre a seleção de recursos adequados às suas necessidades, e links para páginas de produtos e outros tutoriais relevantes e úteis.

>[!NOTE]
> Para evitar a transmissão de dados através de centros de dados, que podem ser lentos e dispendiosos, certifique-se de que o seu grupo de recursos Azure, a conta de armazenamento e o DSVM estão todos alojados na mesma região do Azure. 

### <a name="create-azure-file-storage"></a>Criar armazenamento de ficheiros Azure

1. Execute o seguinte script para criar o armazenamento de ficheiros Azure para os ativos de dados que são úteis para toda a sua equipa. O script solicita-lhe a informação de subscrição do Azure, por isso tenha isso pronto para entrar. 

   - Numa máquina Windows, execute o script a partir do pedido de comando PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Numa máquina Linux, execute o guião da concha linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Faça login na sua conta Microsoft Azure quando solicitado e selecione a subscrição que pretende utilizar.
   
1. Selecione a conta de armazenamento para usar ou crie uma nova sob a subscrição selecionada. Pode utilizar caracteres, números e hífens minúsculos para o nome de armazenamento de ficheiros Azure.
   
1. Para facilitar a montagem e partilha do armazenamento, prima Enter ou insira *Y* para guardar as informações de armazenamento de ficheiros Azure num ficheiro de texto no seu diretório atual. Pode consultar este ficheiro de texto no seu repositório **TeamTemplate,** idealmente no **âmbito do Docs\DataDictionaries,** para que todos os projetos da sua equipa possam aceder ao mesmo. Também precisa das informações do ficheiro para montar o armazenamento do seu ficheiro Azure no seu Azure DSVM na secção seguinte. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Armazenamento de ficheiros Mount Azure na sua máquina local ou DSVM

1. Para montar o armazenamento de ficheiros Azure na sua máquina local ou DSVM, utilize o seguinte script.
   
   - Numa máquina Windows, execute o script a partir do pedido de comando PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Numa máquina Linux, execute o guião da concha linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Prima Insira ou introduza *Y* para continuar, se guardou um ficheiro de informações de armazenamento de ficheiros Azure no passo anterior. Introduza o caminho completo e o nome do ficheiro que criou. 
   
   Se não tiver um ficheiro de informações de armazenamento de ficheiros Azure, *insira n* e siga as instruções para introduzir a sua subscrição, a conta de armazenamento Azure e as informações de armazenamento de ficheiros Azure.
   
1. Insira o nome de uma unidade local ou TDSP para montar a partilha de ficheiros. O ecrã apresenta uma lista de nomes de acionamento existentes. Forneça um nome de unidade que já não existe.
   
1. Confirme se a nova unidade e armazenamento está montado com sucesso na sua máquina.

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações com descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados de Equipa:

- [Tarefas do Gestor de Grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)
- [Projetar tarefas individuais de contribuinte para uma equipa de ciência de dados](project-ic-tasks.md)
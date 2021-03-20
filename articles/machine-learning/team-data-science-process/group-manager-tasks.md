---
title: Tarefas de gestor de grupo de team data science process
description: Acompanhe esta análise detalhada das tarefas que um gestor de grupo completa num projeto de equipa de data science.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94740231"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Tarefas de gestor de grupo de team data science process

Este artigo descreve as tarefas que um *gestor de grupo* completa para uma organização de ciência de dados. O gestor do grupo gere toda a unidade de ciência de dados numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada uma das quais está a trabalhar em muitos projetos de ciência de dados em verticais de negócios distintas. O objetivo do gestor do grupo é estabelecer um ambiente de grupo colaborativo que normalize no Processo de Ciência de [Dados de Equipa](overview.md) (TDSP). Para um esboço de todas as funções de pessoal e tarefas associadas tratadas por uma equipa de ciência de dados que normaliza na TDSP, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md)

O diagrama seguinte mostra as seis principais tarefas de configuração do gestor do grupo. Os gestores de grupos podem delegar as suas tarefas em substitutos, mas as tarefas associadas ao papel não mudam.

![Tarefas de gestor de grupo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Crie uma **organização Azure DevOps** para o grupo.
2. Crie o **projeto Default GroupCommon** na organização Azure DevOps.
3. Crie o repositório **GroupProjectTemplate** em Azure Repos.
4. Crie o repositório **GroupUtilities** em Azure Repos.
5. Importe os conteúdos dos repositórios **ProjectTemplate** e **Utilities** da equipa da Microsoft TDSP para os repositórios comuns do grupo.
6. Crie **membros** e permissões para os **membros** da equipa acederem ao grupo.

O seguinte tutorial percorre os degraus em detalhe. 

> [!NOTE] 
> Este artigo usa o Azure DevOps para criar um ambiente de grupo TDSP, porque é assim que se implementa a TDSP na Microsoft. Se o seu grupo utilizar outras plataformas de hospedagem ou desenvolvimento de códigos, as tarefas do Gestor do Grupo são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Criar uma organização e projeto em Azure DevOps

1. Vá a [visualstudio.microsoft.com](https://visualstudio.microsoft.com), selecione **Iniciar sôs-te na** parte superior direita e inscreve-te na tua conta Microsoft. 
   
   ![Inscreva-se na sua conta Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Se não tiver uma conta Microsoft, selecione **Iniciar sessão agora**, crie uma conta Microsoft e inscreva-se na utilização desta conta. Se a sua organização tiver uma subscrição do Visual Studio, inscreva-se com as credenciais para essa subscrição.
   
1. Depois de iniciar sessão, na parte superior direita da página Azure DevOps, selecione **Criar nova organização**.
   
   ![Criar nova organização](./media/group-manager-tasks/create-organization.png)
   
1. Se for solicitado que concorde com os Termos de Serviço, Declaração de Privacidade e Código de Conduta, selecione **Continue**.
   
1. No diálogo de inscrição, nomeie a sua organização Azure DevOps e aceite a atribuição da região anfitriã, ou desça e selecione uma região diferente. Em seguida, selecione **Continuar**. 

1. No âmbito **da Criação de um projeto para começar,** insira *o GroupCommon* e, em seguida, selecione **Create project**. 
   
   ![Criar o projeto](./media/group-manager-tasks/create-project.png)

A página **de resumo do** projeto **GroupCommon** abre. O URL da página é *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Página de resumo do projeto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Criar os repositórios comuns do grupo

Azure Repos acolhe os seguintes tipos de repositórios para o seu grupo:

- **Repositórios comuns do grupo**: Repositórios de uso geral que várias equipas dentro de uma unidade de ciência de dados podem adotar para muitos projetos de ciência de dados. 
- **Repositórios de equipas**: Repositórios para equipas específicas dentro de uma unidade de data science. Estes repositórios são específicos para as necessidades de uma equipa, e podem ser usados para vários projetos dentro dessa equipa, mas não são gerais o suficiente para serem usados em várias equipas dentro de uma unidade de data science.
- **Repositórios de projetos**: Repositórios para projetos específicos. Tais repositórios podem não ser suficientemente gerais para vários projetos dentro de uma equipa, ou para outras equipas numa unidade de ciência de dados.

Para configurar os repositórios comuns do grupo no seu projeto, você: 
- Mude o nome do repositório **padrão do GroupCommon** para **GroupProjectTemplate**
- Criar um novo repositório **groupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Mude o nome do repositório do projeto padrão para GroupProjectTemplate

Para renomear o repositório de **projetos GroupCommon** padrão para **GroupProjectTemplate:**

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. Esta ação leva-o ao repositório padrão do **GrupoCommon** do projeto GroupCommon, que está atualmente vazio.
   
1. No topo da página, desça a seta ao lado do **GroupCommon** e selecione **Repositórios de Gestão**.
   
   ![Gerir repositórios](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na página Definições do **Projeto,** selecione o **...** ao lado **do GroupCommon** e, em seguida, selecione **o repositório de renomeação**. 
   
   ![Selecione ... e, em seguida, selecione Renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. No **retoco do repositório GroupCommon,** insira *o GroupProjectTemplate* e, em seguida, selecione **Rename**. 
   
   ![Repositório de renomeação](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Criar o repositório groupUtilities

Para criar o repositório **GroupUtilities:**

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. 
   
1. No topo da página, desça a seta ao lado **do GroupProjectTemplate** e selecione **Novo repositório**.
   
   ![Selecione novo repositório](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. No Criar um novo diálogo **de repositório,** selecione **Git** como **tipo**, insira *groupUtilities* como **o nome repositório**, e, em seguida, selecione **Criar**.
   
   ![Criar repositório de groupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na página **Definições** do Projeto, selecione **Repositórios** sob **Repos** na navegação à esquerda para ver os dois repositórios de grupo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Dois repositórios de grupo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importar os repositórios da equipa da Microsoft TDSP

Nesta parte do tutorial, importa os conteúdos dos repositórios **ProjectTemplate** e **Utilities** geridos pela equipa microsoft TDSP para os repositórios **GroupProjectTemplate** e **GroupUtilities.** 

Para importar os repositórios da equipa da TDSP:

1. Na página inicial do projeto **GroupCommon,** selecione **Repos** na navegação à esquerda. O repo **de GrupoProjectTemplate predefinido** abre. 
   
1. Na **página GroupProjectTemplate está vazia** a página, selecione **Import**. 
   
   ![Selecione Importar](./media/group-manager-tasks/import-repo.png)
   
1. No diálogo **repositório de importação a Git,** selecione **Git** como o **tipo Fonte**, e introduza *https: \/ /github.com/Azure/Azure-TDSP-ProjectTemplate.git* para o **URL clone**. Em seguida, **selecione Import**. Os conteúdos do repositório ProjectTemplate da equipa da Microsoft TDSP são importados para o repositório GroupProjectTemplate. 
   
   ![Importar repositório de equipa da Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. No topo da página **Repos,** desça e selecione o repositório **GroupUtilities.**
   
Cada um dos seus dois repositórios de grupo agora contém todos os ficheiros, exceto os do diretório *.git,* do repositório correspondente da equipa da Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalize o conteúdo dos repositórios de grupo

Se quiser personalizar o conteúdo dos repositórios do seu grupo para atender às necessidades específicas do seu grupo, pode fazê-lo agora. Pode modificar os ficheiros, alterar a estrutura do diretório ou adicionar ficheiros que o seu grupo desenvolveu ou que são úteis para o seu grupo.

### <a name="make-changes-in-azure-repos"></a>Fazer alterações em Azure Repos

Para personalizar conteúdos de repositório:

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. 
   
1. No topo da página, selecione o repositório que pretende personalizar.

1. Na estrutura do reeso para o diretório, navegue para a pasta ou ficheiro que pretende alterar. 
   
   - Para criar novas pastas ou ficheiros, selecione a seta ao lado **de New**. 
     
     ![Criar novo arquivo](./media/group-manager-tasks/new-file.png)
     
   - Para fazer upload de ficheiros, **selecione upload file(s)**. 
     
     ![Carregar ficheiros](./media/group-manager-tasks/upload-files.png)
     
   - Para editar ficheiros existentes, navegue no ficheiro e, em seguida, **selecione Editar**. 
     
     ![Editar um ficheiro](./media/group-manager-tasks/edit-file.png)
     
1. Depois de adicionar ou editar ficheiros, **selecione Cometer**.
   
   ![Cometer alterações](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Faça alterações utilizando a sua máquina local ou DSVM

Se pretender fazer alterações utilizando a sua máquina local ou DSVM e empurrar as alterações para os repositórios de grupo, certifique-se de que tem os pré-requisitos para trabalhar com Git e DSVMs:

- Uma subscrição Azure, se quiser criar um DSVM.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as Plataformas e o [apêndice de ferramentas.](platforms-and-tools.md#appendix)
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criado e configurado em Azure. Para obter mais informações e instruções, consulte a [Documentação da Máquina Virtual da Ciência dos Dados.](../data-science-virtual-machine/index.yml)
- Para um DSVM Do Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **de Descarregamento e Instalação** e selecione o **instalador mais recente**. Descarregue o instalador *de .exe* a partir da página do instalador e execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção **de chaves públicas Create SSH** no [apêndice de ferramentas e ferramentas.](platforms-and-tools.md#appendix) 

Primeiro, copie ou *clone* o repositório para a sua máquina local. 
   
1. Na página de **resumo** do projeto **GroupCommon,** selecione **Repos**, e no topo da página, selecione o repositório que pretende clonar.
   
1. Na página de repo, selecione **Clone** na parte superior direita.
   
1. No diálogo **do repositório do Clone,** selecione **HTTPS** para uma ligação HTTP ou **SSH** para uma ligação SSH e copie o URL do clone sob **a linha de comando** para a sua área de transferência.
   
   ![Repo clone](./media/group-manager-tasks/clone.png)
   
1. Na sua máquina local, crie os seguintes diretórios:
   
   - Para janelas: **C:\GitRepos\GroupCommon**
   - Para Linux, **$/GitRepos/GroupCommon** no seu diretório de casa 
   
1. Mude para o diretório que criou.
   
1. Em Git Bash, comande o comando `git clone <clone URL>.`
   
   Por exemplo, qualquer um dos seguintes comandos clona o repositório **GroupUtilities** para o *diretório GroupCommon* na sua máquina local. 
   
   **Ligação HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Ligação SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Depois de fazer as alterações que quiser no clone local do seu repositório, pode impulsionar as alterações aos repositórios comuns do grupo partilhado. 

Executar os seguintes comandos Git Bash do seu diretório local **GroupProjectTemplate** ou **GroupUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se esta for a primeira vez que se compromete com um repositório de Git, poderá ter de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Executar os dois comandos seguintes:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se você está comprometendo-se com vários repositórios Git, use o mesmo nome e endereço de e-mail para todos eles. Usar o mesmo nome e endereço de e-mail é conveniente ao construir painéis power BI para rastrear as suas atividades git em vários repositórios.

## <a name="add-group-members-and-configure-permissions"></a>Adicione membros do grupo e configuure permissões

Para adicionar membros ao grupo:

1. Em Azure DevOps, a partir da página inicial do projeto **GroupCommon,** selecione **as definições** do Projeto a partir da navegação à esquerda. 
   
1. A partir da navegação deixada por **Projetos,** selecione **Equipas,** em seguida, na página **Equipas,** selecione a **Equipa GroupCommon**. 
   
   ![Configure equipas](./media/group-manager-tasks/teams.png)
   
1. Na página **'Perfil da Equipa',** selecione **Adicionar**.
   
   ![Adicionar à Equipa GroupCommon](./media/group-manager-tasks/add-to-team.png)
   
1. No **diálogo de utilizadores e grupos Adicionar,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Guardar as alterações**. 
   
   ![Adicionar utilizadores e grupos](./media/group-manager-tasks/add-users.png)
   

Para configurar permissões para membros:

1. A partir das **Definições** de Projeto deixou a navegação, selecione **Permissões**. 
   
1. Na página **Permissões,** selecione o grupo a que pretende adicionar membros. 
   
1. Na página para esse grupo, selecione **Membros** e, em seguida, **selecione Adicionar**. 
   
1. No popup de **membros do Convite,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Save**. 
   
   ![Conceder permissões aos membros](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações com descrições detalhadas das outras funções e tarefas no Processo de Ciência de Dados de Equipa:

- [Tarefas de liderança da equipa para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)
- [Projetar tarefas individuais de contribuinte para uma equipa de ciência de dados](project-ic-tasks.md)

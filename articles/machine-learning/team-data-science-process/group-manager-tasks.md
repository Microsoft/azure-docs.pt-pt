---
title: Tarefas de gestor de grupo de dados de dados de equipa
description: Acompanhe este passe detalhado das tarefas que um gestor de grupo completa num projeto da equipa de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721358"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Tarefas de gestor de grupo de dados de dados de equipa

Este artigo descreve as tarefas que um gestor de *grupo* completa para uma organização de ciência de dados. O gestor do grupo gere toda a unidade de ciência de dados numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada uma das quais está a trabalhar em muitos projetos de ciência de dados em verticais de negócios distintos. O objetivo do gestor do grupo é estabelecer um ambiente de grupo colaborativo que normalize no Processo de Ciência de Dados da [Equipa](overview.md) (TDSP). Para um esboço de todas as funções de pessoal e tarefas associadas tratadas por uma equipa de ciência de dados padronizada no TDSP, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa.

O diagrama seguinte mostra as seis tarefas de configuração do gestor do grupo principal. Os gestores do grupo podem delegar as suas tarefas em substitutos, mas as tarefas associadas ao papel não mudam.

![Tarefas de gestor de grupo](./media/group-manager-tasks/tdsp-group-manager.png)

1. Criar uma **organização Azure DevOps** para o grupo.
2. Crie o projeto padrão **GroupCommon** na organização Azure DevOps.
3. Crie o repositório **GroupProjectTemplate** em Azure Repos.
4. Crie o repositório **GroupUtilities** em Azure Repos.
5. Importe o conteúdo dos repositórios de **projecttemplate** e **utilitários** da equipa da Microsoft TDSP para os repositórios comuns do grupo.
6. Configurar **a adesão** e **permissões** para os membros da equipa acederem ao grupo.

O seguinte tutorial percorre os degraus em detalhe. 

> [!NOTE] 
> Este artigo utiliza o Azure DevOps para criar um ambiente de grupo TDSP, porque é assim que se implementa o TDSP na Microsoft. Se o seu grupo utilizar outras plataformas de hospedagem ou desenvolvimento de códigos, as tarefas do Gestor de Grupo são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Criar uma organização e projeto em Azure DevOps

1. Vá a [visualstudio.microsoft.com](https://visualstudio.microsoft.com), selecione **Iniciar sessão** na parte superior direita e inscreva-se na sua conta Microsoft. 
   
   ![Inscreva-se na sua conta Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Se não tiver uma conta Microsoft, **selecione 'Iniciar sessão' agora**, criar uma conta Microsoft e iniciar sessão utilizando esta conta. Se a sua organização tiver uma subscrição do Visual Studio, inscreva-se com as credenciais para essa subscrição.
   
1. Depois de iniciar sessão, na parte superior direita na página Azure DevOps, selecione **Criar uma nova organização**.
   
   ![Criar nova organização](./media/group-manager-tasks/create-organization.png)
   
1. Se for solicitado que concorde com os Termos de Serviço, Declaração de Privacidade e Código de Conduta, selecione **Continuar**.
   
1. No diálogo de inscrição, nomeie a sua organização Azure DevOps e aceite a atribuição da região anfitriã, ou desça e selecione uma região diferente. Em seguida, selecione **Continuar**. 

1. Em **Criar um projeto para começar,** entrar no *GroupCommon,* e depois selecionar **criar projeto**. 
   
   ![Criar o projeto](./media/group-manager-tasks/create-project.png)

Abre a página **sumária** do projeto **GroupCommon.** O URL da página é *https:\//\<nome de servidor>/\<nome de organização>/GroupCommon*.

![Página de resumo do projeto](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Criar os repositórios comuns do grupo

O Azure Repos acolhe os seguintes tipos de repositórios para o seu grupo:

- **Repositórios comuns**do grupo : Repositórios de propósito geral que várias equipas dentro de uma unidade de ciência de dados podem adotar para muitos projetos de ciência de dados. 
- **Repositórios de equipa**: Repositórios para equipas específicas dentro de uma unidade de ciência de dados. Estes repositórios são específicos para as necessidades de uma equipa, e podem ser usados para vários projetos dentro dessa equipa, mas não são gerais o suficiente para serem usados em várias equipas dentro de uma unidade de ciência de dados.
- **Repositórios de projetos**: Repositórios para projetos específicos. Tais repositórios podem não ser gerais o suficiente para vários projetos dentro de uma equipa, ou para outras equipas numa unidade de ciência de dados.

Para criar os repositórios comuns do grupo no seu projeto, você: 
- Mude o nome do repositório padrão **do Grupo Common** para o Modelo de Projeto de **Grupo**
- Criar um novo repositório **groupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Mude o nome do repositório padrão do projeto para o Modelo de Projeto de Grupo

Para renomear o repositório padrão do projeto **GroupCommon** para **o Modelo de Projeto de Grupo:**

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. Esta ação leva-o ao repositório padrão **do Grupo Common** do projeto GroupCommon, que está atualmente vazio.
   
1. No topo da página, deite a seta ao lado do **GroupCommon** e **selecione Gerir repositórios**.
   
   ![Gerir repositórios](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na página Definições do **Projeto,** selecione o **...** ao lado do **GroupCommon**, e, em seguida, selecione **Rename repositório**. 
   
   ![Selecione ... e, em seguida, selecionar renomear repositório](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. No **Renome The GroupCommon repositório** popup, introduza *groupProjectTemplate*, e, em seguida, selecione **Rename**. 
   
   ![Renomear o repositório](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Criar o repositório GroupUtilities

Para criar o repositório **GroupUtilities:**

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. 
   
1. No topo da página, deite a seta ao lado do **GroupProjectTemplate** e **selecione Novo repositório**.
   
   ![Selecione novo repositório](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. No Create um novo diálogo **de repositório,** selecione **Git** como **tipo,** introduza os *Utilitários* do Grupo como o **nome repositório**e, em seguida, selecione **Criar**.
   
   ![Criar repositório groupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na página Definições do **Projeto,** selecione **Repositórios** sob **Repos** na navegação à esquerda para ver os dois repositórios de grupo: **GroupProjectTemplate** e **GroupUtilities**.
   
   ![Dois repositórios de grupo](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importar os repositórios da equipa Microsoft TDSP

Nesta parte do tutorial, importa os conteúdos dos repositórios **projecttemplate** e **utilitários** geridos pela equipa da Microsoft TDSP para os seus repositórios **groupProjectTemplate** e **GroupUtilities.** 

Para importar os repositórios da equipa TDSP:

1. A partir da página inicial do projeto **GroupCommon,** selecione **Repos** na navegação à esquerda. Abre-se o repo padrão **do GroupProjectTemplate.** 
   
1. No Modelo de **Projeto de Grupo encontra-se a** página vazia, selecione **Import**. 
   
   ![Selecione Importar](./media/group-manager-tasks/import-repo.png)
   
1. No diálogo de **reparação De Importação de Git,** selecione **Git** como **tipo Fonte,** e *introduza https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* para o URL **Clone**. Em seguida, selecione **Import**. O conteúdo do repositório projecttemplate da equipa da Microsoft TDSP é importado para o seu repositório GroupProjectTemplate. 
   
   ![Importar repositório da equipa da Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. No topo da página **repos,** desça e selecione o repositório **GroupUtilities.**
   
1. Repita o processo de importação para importar o conteúdo do repositório da equipa Microsoft TDSP **Utilities,** *\/https: /github.com/Azure/Azure-TDSP-Utilities.git*, para o seu repositório **GroupUtilities.** 
   
Cada um dos seus dois repositórios de grupo agora contém todos os ficheiros, exceto os do diretório *.git,* do repositório correspondente da equipa da Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Personalize os conteúdos dos repositórios de grupo

Se quiser personalizar os conteúdos dos seus repositórios de grupo para atender às necessidades específicas do seu grupo, pode fazê-lo agora. Pode modificar os ficheiros, alterar a estrutura do diretório ou adicionar ficheiros que o seu grupo desenvolveu ou que sejam úteis para o seu grupo.

### <a name="make-changes-in-azure-repos"></a>Faça alterações em Azure Repos

Para personalizar conteúdos de repositório:

1. Na página **resumo** do projeto **GroupCommon,** selecione **Repos**. 
   
1. No topo da página, selecione o repositório que pretende personalizar.

1. Na estrutura do repo diretório, navegue para a pasta ou ficheiro que pretende alterar. 
   
   - Para criar novas pastas ou ficheiros, selecione a seta ao lado de **New**. 
     
     ![Criar novo ficheiro](./media/group-manager-tasks/new-file.png)
     
   - Para fazer upload de ficheiros, selecione **Ficheiros upload(s)**. 
     
     ![Carregar ficheiros](./media/group-manager-tasks/upload-files.png)
     
   - Para editar os ficheiros existentes, navegue para o ficheiro e, em seguida, **selecione Editar**. 
     
     ![Editar um ficheiro](./media/group-manager-tasks/edit-file.png)
     
1. Depois de adicionar ou editar ficheiros, selecione **'Cometer**'
   
   ![Comprometer alterações](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Faça alterações utilizando a sua máquina local ou DSVM

Se pretender fazer alterações utilizando a sua máquina local ou DSVM e empurrar as alterações para os repositórios de grupo, certifique-se de que tem os pré-requisitos para trabalhar com Git e DSVMs:

- Uma subscrição Azure, se quiser criar um DSVM.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as [Plataformas e ferramentas do apêndice](platforms-and-tools.md#appendix).
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criaram e configuraram em Azure. Para mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual da Ciência dos Dados .
- Para um DSVM windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **Descarregamento e Instalação** e selecione o **mais recente instalador**. Descarregue o instalador *.ex e* execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção de **chaves públicas Create SSH** nas Plataformas e no apêndice das [ferramentas](platforms-and-tools.md#appendix). 

Primeiro, copie ou *clone* o repositório para a sua máquina local. 
   
1. Na **página** resumo do projeto **GroupCommon,** selecione **Repos**, e no topo da página, selecione o repositório que pretende clonar.
   
1. Na página do repo, selecione **Clone** na parte superior direita.
   
1. No diálogo de **repositório Clone,** selecione **HTTPS** para uma ligação HTTP, ou **SSH** para uma ligação SSH, e copie o URL do clone sob **a linha de comando** para a sua área de pré-quadro.
   
   ![Repo clone](./media/group-manager-tasks/clone.png)
   
1. Na sua máquina local, crie os seguintes diretórios:
   
   - Para Windows: **C:\GitRepos\GroupCommon**
   - Para Linux, **$/GitRepos/GroupCommon** no seu diretório de casa 
   
1. Mude para o diretório que criou.
   
1. Em Git Bash, dirija o comando`git clone <clone URL>.`
   
   Por exemplo, qualquer um dos seguintes comandos clona o repositório **GroupUtilities** ao diretório *GroupCommon* na sua máquina local. 
   
   **Ligação HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Ligação SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Depois de fazer as alterações que quiser no clone local do seu repositório, pode empurrar as alterações para os repositórios comuns do grupo comum. 

Execute os seguintes comandos Git Bash do seu diretório local **do GroupProjectTemplate** ou **GroupUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Se esta for a primeira vez que se compromete com um repositório Git, poderá ter `git commit` de configurar parâmetros globais *user.name* e *user.email* antes de executar o comando. Executar os seguintes dois comandos:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Se está a comprometer-se com vários repositórios git, use o mesmo nome e endereço de e-mail para todos eles. Usar o mesmo nome e endereço de e-mail é conveniente ao construir dashboards Power BI para acompanhar as suas atividades git em vários repositórios.

## <a name="add-group-members-and-configure-permissions"></a>Adicione membros do grupo e configure permissões

Para adicionar membros ao grupo:

1. Em Azure DevOps, a partir da página inicial do projeto **GroupCommon,** selecione **as definições** do Projeto a partir da navegação à esquerda. 
   
1. A partir das **Definições** de Projeto à esquerda, selecione **Equipas**, em seguida, na página **equipas,** selecione a **Equipa Comum de Grupo**. 
   
   ![Equipas de Configuração](./media/group-manager-tasks/teams.png)
   
1. Na página perfil da **equipa,** selecione **Adicionar**.
   
   ![Adicionar à Equipa Comum de Grupo](./media/group-manager-tasks/add-to-team.png)
   
1. No diálogo **de utilizadores e grupos Adicionar,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Guardar alterações**. 
   
   ![Adicionar utilizadores e grupos](./media/group-manager-tasks/add-users.png)
   

Para configurar permissões para os membros:

1. A partir das **Definições** do Projeto à esquerda, selecione **Permissões**. 
   
1. Na página **Permissões,** selecione o grupo a que pretende adicionar membros. 
   
1. Na página desse grupo, selecione **Membros**, e, em seguida, selecione **Adicionar**. 
   
1. No popup dos **membros do Convidar,** procure e selecione membros para adicionar ao grupo e, em seguida, selecione **Save**. 
   
   ![Conceder permissões aos membros](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para descrições detalhadas das outras funções e tarefas no Processo de Ciência de Dados da Equipa:

- [Tarefas de Team Lead para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas do Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)
- [Tarefas do Project Individual Contributor para uma equipa de ciência de dados](project-ic-tasks.md)

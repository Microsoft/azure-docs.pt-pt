---
title: Tarefas do Gestor de grupo do Team Data Science Process
description: Uma descrição de tarefas de um Gestor de grupo num projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fb0482be1670a96befdd69a5356c9e21476d9f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305411"
---
# <a name="tasks-for-a-group-manager-on-a-data-science-team-project"></a>Tarefas do gerente de grupo num projeto de equipe de ciência de dados

Este contornos de tópico as tarefas que é esperado que um gerente de grupo são concluídas para hist / sua organização de ciência de dados. O objetivo é estabelecer o ambiente de colaboração de grupo que padroniza sobre o [Team Data Science Process](overview.md) (TDSP). Para uma descrição das funções pessoal e das respetivas tarefas associadas que são processadas por uma equipa de ciência de dados padronizar este processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md).

O **gerente do grupo** é o gerente da unidade de ciência de dados inteiro numa empresa. Uma unidade de ciência de dados pode ter várias equipes, cada um deles está trabalhando em vários projetos de ciência de dados em mercados verticais de negócios distintos. Gerente de grupo pode delegar suas tarefas, para um substituto, mas as tarefas associadas à função são os mesmos. Existem seis tarefas principais, conforme mostrado no diagrama seguinte:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


> [!NOTE] 
> Descrevemos os passos necessários para configurar um ambiente de grupo do TDSP usando os serviços de DevOps do Azure nas instruções que se seguem. Especificamos como realizar estas tarefas com os serviços de DevOps do Azure, uma vez que é saber como implementamos TDSP na Microsoft. Se outro código de plataforma de alojamento é utilizado para o seu grupo, não altere as tarefas que precisam ser preenchidos pelo Gestor de grupo em geral. Mas a forma de concluir estas tarefas vai ser diferente.

1. Configurar **serviços do Azure DevOps** para o grupo.
2. Criar uma **projeto do grupo** nos serviços de DevOps do Azure (para usuários de serviços de DevOps do Azure)
3. Criar a **GroupProjectTemplate** repositório
4. Criar a **GroupUtilities** repositório
5. Propagação de **GroupProjectTemplate** e **GroupUtilities** repositórios para os serviços de DevOps do Azure com o conteúdo a partir de repositórios do TDSP.
6. Configurar o **controlos de segurança** para os membros da Equipe acesso a repositórios do GroupProjectTemplate e GroupUtilities.

Cada um dos passos anteriores é descrita detalhadamente. Mas, em primeiro lugar, vamos familiarizá-lo com as abreviaturas e discutir os pré-requisitos para trabalhar com repositórios.

### <a name="abbreviations-for-repositories-and-directories"></a>Abreviações para repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Estas definições tornam mais fáceis de compreender as operações entre os repositórios e diretórios. Este notação é usada nas seções a seguir:

- **G1**: O repositório de modelos de projeto desenvolvidos e gerenciados pela equipe do TDSP da Microsoft.
- **G2**: O repositório de utilitários desenvolvidos e gerenciados pela equipe do TDSP da Microsoft.
- **R1**: O repositório de GroupProjectTemplate no Git configurar no seu servidor de grupo do Azure DevOps.
- **R2**: O repositório de GroupUtilities no Git configurar no seu servidor de grupo do Azure DevOps.
- **LG1** e **LG2**: Os diretórios locais na sua máquina que clona G1 e G2, respectivamente.
- **LR1** e **LR2**: Os diretórios locais na sua máquina que clona R1 e R2, respectivamente.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Pré-requisitos para a clonagem de repositórios e verificando códigos de entrada e saída

- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), o Git tiver sido previamente instalado e estiver pronto para começar. Caso contrário, consulte a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix).
- Se estiver a utilizar um **DSVM do Windows**, tem de ter [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo para o **transfira e instale** secção e clique nas *instalador mais recente*. Este passo orienta-o para a página de instalador mais recente. Transfira o instalador .exe aqui e executá-lo.
- Se estiver a utilizar **DSVM do Linux**, crie uma chave pública SSH na sua DSVM e adicioná-lo aos seus serviços de DevOps do Azure de grupo. Para obter mais informações sobre o SSH, consulte a **criar chave pública SSH** secção a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix).


## <a name="1-create-account-on-azure-devops-services"></a>1. Criar conta de serviços do Azure DevOps

Os serviços de DevOps do Azure aloja os repositórios do seguintes:

- **grupo repositórios comuns**: Repositórios para fins gerais que podem ser adotados por várias equipes dentro de um grupo de vários projetos de ciência de dados. Por exemplo, o *GroupProjectTemplate* e *GroupUtilities* repositórios.
- **repositórios do Team**:  Repositórios para equipas específicas dentro de um grupo. Estes repositórios são específicos para a necessidade de uma equipe e podem ser adotados por vários projetos executados por essa equipe, mas não suficientemente geral para ser útil para várias equipes dentro de um grupo de ciência de dados.
- **repositórios do projeto**: Repositórios disponíveis para projetos específicos. Esses repositórios podem não ser suficientemente gerais para ser útil para vários projetos realizados por uma equipa e para várias equipes de um grupo de ciência de dados.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Como configurar o início de sessão de serviços do Azure DevOps na sua conta Microsoft

Aceda a [Visual Studio online](https://www.visualstudio.com/), clique em **iniciar sessão** no canto superior direito e inicie sessão na sua conta Microsoft.

![1](./media/group-manager-tasks/login.PNG)

Se não tiver uma conta Microsoft, clique em **Inscreva-se agora** para criar uma conta Microsoft e, em seguida, inicie sessão com esta conta.

Se sua organização tiver uma subscrição do Visual Studio/MSDN, clique no verde **iniciar sessão com a sua conta escolar ou profissional** caixa e inicie sessão com as credenciais associadas esta subscrição.

![2](./media/group-manager-tasks/signin.PNG)



Depois de iniciar sessão, clique em **criar nova conta** no canto superior direito conforme mostrado na imagem seguinte:

![3](./media/group-manager-tasks/create-account-1.PNG)

Preencha as informações dos serviços de DevOps do Azure que pretende criar o **criar a sua conta** assistente com os seguintes valores:

- **URL do servidor**: Substitua *mysamplegroup* pelos seus próprios *nome do servidor*. O URL do seu servidor vai ser: *https://\<servername\>. visualstudio.com*.
- **Gerir com o código:** Selecione  **_Git_**.
- **Nome do projeto:** Introduza *GroupCommon*.
- **Organize o trabalho usando:** Escolher *Agile*.
- **Aloje os seus projetos no:** Escolha uma localização geográfica. Neste exemplo, escolhemos *Centro-Sul*.

![4](./media/group-manager-tasks/fill-in-account-information.png)

> [!NOTE] 
> Se vir a janela de pop-up seguinte depois de clicar em **criar nova conta de**, em seguida, tem de clicar em **alterar detalhes** para apresentar todos os campos discriminados.

![5](./media/group-manager-tasks/create-account-2.png)


Clique em **Continue** (Continuar).

## <a name="2-groupcommon-project"></a>2. Projeto de GroupCommon

O **GroupCommon** página (*https://\<servername\>.visualstudio.com/GroupCommon*) abre-se após a criação de seus serviços de DevOps do Azure.

![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Criar o repositório de GroupUtilities (R2)

Para criar o **GroupUtilities** repositório (R2) em serviços de DevOps do Azure:

- Para abrir o **criar um novo repositório** assistente, clique em **novo repositório** no **controle de versão** separador do seu projeto.

  ![7](./media/group-manager-tasks/create-grouputilities-repo-1.png)

- Selecione *Git* como o **tipo**e introduza *GroupUtilities* como o **nome**e, em seguida, clique em **criar**.

  ![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)

Agora verá dois repositórios do Git **GroupProjectTemplate** e **GroupUtilities** na coluna esquerda do **controle de versão** página:

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Criar o repositório de GroupProjectTemplate (R1)

A configuração dos repositórios para o servidor de grupo do Azure DevOps consiste em duas tarefas:

- Mudar o nome predefinido **GroupCommon** repositório***GroupProjectTemplate***.
- Criar a **GroupUtilities** repositório nos serviços de DevOps do Azure em projeto **GroupCommon**.

Instruções para a primeira tarefa estão contidas nesta secção após observações sobre as convenções de nomenclatura ou nossos repositórios e diretórios. As instruções para a segunda tarefa estão contidas na secção seguinte para o passo 4.

### <a name="rename-the-default-groupcommon-repository"></a>Mudar o nome do repositório de GroupCommon padrão

Para mudar o nome predefinido **GroupCommon** repositório como *GroupProjectTemplate* (chamado **R1** neste tutorial):

- Clique em **Colabore em código** sobre o **GroupCommon** página do projeto. Isto leva-o para a página de repositório de Git de padrão do projeto **GroupCommon**. Atualmente, este repositório de Git está vazio.

  ![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)

- Clique em **GroupCommon** no canto superior esquerdo (realçado com uma caixa vermelha na figura a seguir) na página de repositório de Git do **GroupCommon** e selecione **gerir repositórios**(realçado com uma caixa verde na figura a seguir). Este procedimento abre o **painel de controlo**.
- Selecione o **controle de versão** separador do seu projeto.

  ![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Clique no **...**  à direita do **GroupCommon** repositório no painel esquerdo e selecione **repositório de mudança de nome**.

  ![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)

- Na **mudar o nome do repositório de GroupCommon** assistente é exibido, introduza *GroupProjectTemplate* no **nome do repositório** caixa e, em seguida, clique em **mudar o nome** .

  ![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Efetuar o seeding os repositórios R1 & R2 nos serviços de DevOps do Azure

Nesse estágio do procedimento, efetuar o seeding da *GroupProjectTemplate* (R1) e *GroupUtilities* repositórios (R2) que configurou na secção anterior. Estes repositórios são implantados com o ***ProjectTemplate*** (**G1**) e ***utilitários*** (**G2**) repositórios que são geridos pelo Microsoft para o processo de ciência de dados de equipa. Quando é concluída esta propagação:

- o repositório de R1 vai ter o mesmo conjunto de diretórios e modelos de documentos que faz o G1
- o repositório de R2 vai conter o conjunto de utilitários de ciência de dados desenvolvidos pela Microsoft.

O procedimento de propagação utiliza os diretórios na sua DSVM local como sites intermediários de testes. Aqui estão as etapas percorridas nesta secção:

- G1 & G2 - clonado a -> LG1 & LG2
- R1 & R2 - clonado a -> LR1 & LR2
- LG1 & LG2 - -> arquivos sejam copiados na LR1 & LR2
- (Opcional) personalização do LR1 & LR2
- LR1 & LR2 - conteúdo Adicionar -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Repositórios de clone G1 & G2 à sua DSVM local

Neste passo, clone o repositório do Team Data Science Process (TDSP) ProjectTemplate (G1) e utilitários (G2) de repositórios do GitHub do TDSP para pastas em sua DSVM local como LG1 e LG2:

- Crie um diretório para servir como o diretório de raiz para alojar todos os seus clones dos repositórios.
  -  A DSVM do Windows, criar um diretório *C:\GitRepos\TDSPCommon*.
  -  A DSVM do Linux, criar um diretório *GitRepos\TDSPCommon* no seu diretório raiz.

- Execute o seguinte conjunto de comandos a partir da *GitRepos\TDSPCommon* diretório.

  `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
  `git clone https://github.com/Azure/Azure-TDSP-Utilities`

  ![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Nossos nomes abreviados do repositório a utilizar, este é o que esses scripts têm obtido:
    - G1 - clonado em -> LG1
    - G2 - clonado em -> LG2
- Depois da clonagem está concluída, deverá conseguir ver dois diretórios: _ProjectTemplate_ e _utilitários_, em **GitRepos\TDSPCommon** diretório.

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Repositórios de clone R1 & R2 à sua DSVM local

Neste passo, clone o repositório de GroupProjectTemplate (R1) e o repositório de GroupUtilities (R2) em diretórios locais (referido como LR1 e LR2, respectivamente) em **GitRepos\GroupCommon** em sua DSVM.

- Para obter os URLs dos repositórios do R1 e R2, vá para sua **GroupCommon** home page nos serviços de DevOps do Azure. Normalmente, está o URL *https://\<seu nome de serviços de DevOps do Azure\>.visualstudio.com/GroupCommon*.
- Clique em **código**.
- Escolha o **GroupProjectTemplate** e **GroupUtilities** repositórios. Copie e guarde a cada um dos URLs (HTTPS para Windows; SSH para Linux) do **URL de Clone** elemento, por sua vez, para utilização nos scripts do seguintes:

  ![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Mude para o **GitRepos\GroupCommon** diretório no seu Windows ou DSVM do Linux e execute um dos seguintes conjuntos de comandos para clonar R1 e R2 para esse diretório.

Seguem-se os scripts do Windows e Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

> [!NOTE] 
> Espera-se receber mensagens de aviso que LR1 e LR2 estão vazios.

- Nossos nomes abreviados do repositório a utilizar, este é o que esses scripts têm obtido:
    - R1 - clonado em -> LR1
    - R2 - clonado em -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Efetuar o seeding sua GroupProjectTemplate (LR1) e GroupUtilities (LR2)

Em seguida, no seu computador local, copie o conteúdo de diretórios ProjectTemplate e utilitários (exceto os metadados nos diretórios. Git) em GitRepos\TDSPCommon para os seus diretórios GroupProjectTemplate e GroupUtilities em **GitRepos\ GroupCommon**. Aqui estão as duas tarefas para concluir este passo:

- Copie os arquivos na GitRepos\TDSPCommon\ProjectTemplate (**LG1**) para GitRepos\GroupCommon\GroupProjectTemplate (**LR1**)
- Copie os arquivos na GitRepos\TDSPCommon\Utilities (**LG2** para GitRepos\GroupCommon\Utilities (**LR2**).

Para obter essas duas tarefas, execute os seguintes scripts na consola do PowerShell (Windows) ou a consola de script de Shell (Linux). Lhe for pedido para os caminhos de completos para LG1, LR1, LG2 e LR2 de entrada. Os caminhos que de entrada são validados. Se inserir um diretório que não existe, é-lhe perguntado para introduzi-la novamente.

    # Windows DSVM
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Agora pode ver que os arquivos em diretórios LG1 e LG1 (exceto os ficheiros no diretório do projeto) tenham sido copiados para LR1 e LR2, respectivamente.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)

Agora, pode ver que os ficheiros em duas pastas (exceto os ficheiros no diretório do projeto) são copiados para GroupProjectTemplate e GroupUtilities, respectivamente.

![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Nossos nomes abreviados do repositório a utilizar, este é o que esses scripts têm obtido:
    - LG1 - ficheiros copiados em LR1 ->
    - LG2 - ficheiros copiados em LR2 ->

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opção de personalizar o conteúdo do LR1 & LR2
    
Se pretender personalizar os conteúdos de LR1 e LR2 para satisfazer as necessidades específicas do seu grupo, esta é a fase do procedimento onde isso for adequado. Pode modificar os documentos de modelo, alterar a estrutura de diretórios e adicionar utilitários existentes que desenvolveu o grupo ou que são úteis para o grupo inteiro.

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Adicione o conteúdo no LR1 & LR2 R1 & R2 no servidor de grupo

Agora, precisa adicionar o conteúdo no LR1 e LR2 para repositórios R1 e R2. Seguem-se o git os comandos de bash que pode executar no Windows PowerShell ou no Linux.

Execute os seguintes comandos a partir do diretório GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

A opção -m permite-lhe definir uma mensagem para a consolidação de git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Pode ver o que nos serviços de DevOps do Azure do seu grupo, no repositório GroupProjectTemplate, os ficheiros são sincronizados de forma instantânea.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Por fim, altere para o **GitRepos\GroupCommon\GroupUtilities** comandos de bash de diretório e execute o mesmo conjunto de git:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

> [!NOTE] 
> Se esta for a primeira vez que se comprometer com um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os dois comandos seguintes:
>
>  `git config --global user.name <your name>`  
>  `git config --global user.email <your email address>`
>
> Se empenhados em vários repositórios de Git, utilize o mesmo nome e endereço de e-mail ao consolidar a cada um deles. Com o mesmo nome e endereço de e-mail prova conveniente posteriormente quando cria dashboards do Power BI para controlar as atividades de Git em vários repositórios.


- Nossos nomes abreviados do repositório a utilizar, este é o que esses scripts têm obtido:
    - Adicionar de conteúdo LR1 - a -> R1
    - Adicionar de conteúdo LR2 - a -> R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Adicionar membros de grupo para o servidor de grupo

A partir do seu grupo da home do Azure DevOps page dos serviços, clique nas **ícone de engrenagem** junto ao seu nome de utilizador no canto superior direito, em seguida, selecione a **segurança** separador. Pode adicionar membros ao seu grupo com várias permissões.

![24](./media/group-manager-tasks/add_member_to_group.PNG)


## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md)

---
title: Tarefas para a equipe de liderar em à equipa de processo de ciência de dados de equipa
description: Uma descrição das tarefas que uma equipe líder num projeto de equipe de ciência de dados deve concluir para a equipa de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812448"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Tarefas para a equipe de liderar em à equipa de processo de ciência de dados de equipa

Este tópico de contornos as tarefas que é um líder de equipe espera-se de executar para a equipa de ciência de dados. O objetivo é estabelecer o ambiente de equipa de colaboração que padroniza sobre o [Team Data Science Process](overview.md) (TDSP). TDSP é uma metodologia de ciência de dados ágil e iterativa para fornecer soluções de Análise Preditiva e aplicações inteligentes com eficiência. Foi concebido para ajudar a melhorar a colaboração e a aprendizagem de equipe. O processo é um distillation das melhores práticas e estruturas da Microsoft, bem como da indústria, necessários para a implementação bem-sucedida de iniciativas de ciência de dados para ajudar as empresas totalmente perceber os benefícios de seus programas de análise. Para uma descrição das funções pessoal e das respetivas tarefas associadas que são processadas por uma equipa de ciência de dados padronizar este processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md).

R **líder da equipe de** gere uma equipa na unidade de ciência de dados de uma empresa. Uma equipe é composta por vários cientistas de dados. Para a unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o **gerente do grupo** e o **líder da equipe de** pode ser a mesma pessoa ou poderiam delegar suas tarefas a um substituto. Mas, não altere as tasks em si. O fluxo de trabalho para as tarefas a serem concluídas pela líderes de equipe para configurar este ambiente estão representados na figura a seguir:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] As tarefas em blocos de 1 e 2 da figura são necessários se estiver a utilizar o Azure DevOps como a plataforma de alojamento de código e desejar ter um projeto de DevOps do Azure separado para a sua própria equipa. Assim que estas tarefas são concluídas, todos os repositórios de sua equipe podem ser criados sob este projeto. 

Depois de vários pré-requisitos a tarefas especificadas numa seção seguinte são satisfeitas pelo Gestor de grupo, existem cinco tarefas de principal (algumas opcional) que concluiu este tutorial. Estas tarefas correspondem principal numerados secções deste tópico:

1. Criar uma **projeto** nos serviços de DevOps do Azure do grupo do grupo e dois repositórios de equipe no projeto:
    - **Repositório de ProjectTemplate** 
    - **Repositório de TeamUtilities**
2. A equipe de efetuar o seeding **ProjectTemplate** repositório da **GroupProjectTemplate** repositório que configurou ao seu Gestor de grupo. 
3. Crie recursos de dados e análise da Equipe:
    - Adicionar os utilitários de específicas para a equipe para o **TeamUtilities** repositório. 
    - (Opcional) Criar uma **armazenamento de ficheiros do Azure** a ser utilizado para armazenar elementos de dados que podem ser útil para toda a equipe. 
4. (Opcional) Montar o armazenamento de ficheiros do Azure para o **máquina de Virtual de ciência de dados** (DSVM) da Equipe líder e adicionar recursos de dados no mesmo.
5. Configurar o **controlo de segurança** ao adicionar os membros da Equipe e configurar seus privilégios.

>[AZURE.NOTE] Descrevemos os passos necessários para configurar um ambiente de equipe do TDSP através do Azure DevOps nas instruções seguintes. Especificamos como realizar estas tarefas com o Azure DevOps, uma vez que é saber como implementamos TDSP na Microsoft. Se outro código de plataforma de alojamento é utilizado para o seu grupo, as tarefas que devem ser concluídas pelo líder da Equipe geralmente não são alterados. Mas a forma de concluir estas tarefas vai ser diferente.

## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tópico utiliza nomes abreviados para repositórios e diretórios. Esses nomes tornam mais fáceis de compreender as operações entre os repositórios e diretórios. Este notação (**R** repositórios do Git e **1!d** para diretórios locais na sua DSVM) é usado nas seções a seguir:

- **R1**: O **GroupProjectTemplate** repositório de Git que o seu Gestor de Grupo configurar no seu servidor de grupo do Azure DevOps.
- **R3**: A equipe **ProjectTemplate** repositório de Git que configurou.
- **R4**: O **TeamUtilities** repositório de Git que configurou.
- **D1**: O diretório de local clonar a partir R1 e copiado para D3.
- **D3**: O diretório de local clonar a partir R3, personalizar e copiados de volta para R3.
- **D4**: O diretório de local clonar a partir R4, personalizar e copiados de volta para R4.

Os nomes especificados para os repositórios e diretórios neste tutorial foram fornecidos no pressuposto de que o objetivo é estabelecer um projeto separado para a sua própria equipa dentro de um grupo de ciência de dados maior. Mas existem outras opções abertas a como líder de equipe:

- O grupo inteiro pode optar por criar um único projeto. Em seguida, todos os projetos de todas as equipas de ciência de dados seria sob esse projeto único. Para conseguir isso, pode designar um administrador de git de seguir estas instruções para criar um único projeto. Este cenário poderá ser válido, por exemplo, para:
    -  um grupo de ciência de dados pequeno que não tenha várias equipes de ciência de dados 
    -  um data science grupo maior com várias equipes de ciência de dados que deseja otimizar a colaboração entre as equipes com atividades como planejamento de sprints de nível de grupo entretanto. 
- As equipes podem optar por modelos de projeto de equipe específico ou utilitários específico de uma equipa em único projeto do grupo inteiro. Neste caso, os líderes de equipe devem criar repositórios de modelo de projeto e/ou de repositórios de utilitários de equipe no mesmo projeto. Dê um nome estes repositórios *< TeamName\>ProjectTemplate* e *< TeamName\>utilitários*, por exemplo, *TeamJohnProjectTemplate*e *TeamJohnUtilities*. 

De qualquer forma, os líderes de equipe precisam permitir que os membros da sua equipa saber quais repositórios de modelo e os utilitários a adotar quando eles são a configuração e a clonagem de repositórios do projeto e utilitários. Oportunidades potenciais do projeto devem seguir a [tarefas líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md) para criar repositórios de projeto, quer ao abrigo projetos separados ou num único projeto. 


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são cumpridos ao concluir as tarefas atribuídas para o seu Gestor de grupo descrita [tarefas de Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md). Para resumir aqui, os seguintes requisitos tem de cumprir antes de iniciar as tarefas de líder de equipe: 

- Sua **grupo de serviços do Azure DevOps** (ou conta de grupo em algum outro código de plataforma de alojamento) tiver sido configurado pelo seu Gestor de grupo.
- Sua **GroupProjectTemplate repositório** (R1) tiver sido configurada na sua conta de grupo ao seu Gestor de grupo no código que aloja a plataforma que pretende utilizar.
- A sua sessão foi **autorizado** na sua conta de grupo para criar repositórios para a sua equipa.
- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), o Git tiver sido previamente instalado e estiver pronto para começar. Caso contrário, consulte a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **DSVM do Windows**, tem de ter [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo para o **transfira e instale** secção e clique nas *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transfira o instalador .exe aqui e executá-lo. 
- Se estiver a utilizar **DSVM do Linux**, crie uma chave pública SSH na sua DSVM e adicioná-lo aos seus serviços de DevOps do Azure de grupo. Para obter mais informações sobre o SSH, consulte a **criar chave pública SSH** secção a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-project-and-repositories"></a>1. Criar um projeto e repositórios

Conclua este passo se estiver a utilizar o Azure DevOps como seu código de plataforma para colaboração e controlo de versões de alojamento. Esta secção tem que criar três artefactos nos serviços de DevOps do Azure do seu grupo:

- **MyTeam** projeto no Azure DevOps
- **MyProjectTemplate** repositório (**R3**) no Git
- **MyTeamUtilities** repositório (**R4**) no Git

### <a name="create-the-myteam-project"></a>Criar o projeto de MyTeam

- Vá para a home page de serviços de DevOps do Azure do seu grupo no URL `https://<Azure DevOps Services Name\>.visualstudio.com`. 
- Clique em **New** para criar um projeto. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Uma janela de projeto criar pede-lhe para introduzir o nome do projeto (**MyTeam** neste exemplo). Certifique-se de que seleciona **Agile** como o **modelo de processo** e **Git** como o **controle de versão**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Clique em **criar projeto**. Seu projeto **MyTeam** é criado em menos de 1 minuto. 

- Depois do projeto **MyTeam** é criado e clique em **Navigate ao projeto** botão, para ser direcionado para a home page do seu projeto. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Se vir um **Parabéns!** janela de pop-up, clique nas **adicione código** (botão na caixa vermelha). Caso contrário, clique em **código** (na caixa amarela). Isto direciona-o para a página de repositório de Git de seu projeto. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Criar o repositório de MyProjectTemplate (R3) no Git

- Na página de repositório de Git de seu projeto, clique na seta para baixo junto ao nome do repositório **MyTeam**e selecione **gerir repositórios...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Sobre o **controle de versão** separador do painel de controlo do seu projeto, clique em **MyTeam**, em seguida, selecione **repositório de mudança de nome...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Introduza um nome novo para o repositório no **mudar o nome do repositório de MyTeam** janela. Neste exemplo, *MyTeamProjectTemplate*. Pode escolher algo semelhante *< nome da sua equipe\>ProjectTemplate*. Clique em **mudar o nome** para continuar.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Criar o repositório de MyTeamUtilities (R4) no Git

- Para criar um novo repositório *< nome da sua equipe\>utilitários* em seu projeto, clique em **novo repositório de...**  sobre o **controle de versão** separador do painel de controlo do seu projeto.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Na **criar um novo repositório** janela que surge, forneça um nome para este repositório. Neste exemplo, Atribuímos nomes-o como *MyTeamUtilities*, que é **R4** na nossa notação. Escolha algo semelhante *< nome da sua equipe\>utilitários*. Certifique-se de que seleciona **Git** para **tipo**. Em seguida, clique em **criar** para continuar.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Confirme que vê dois repositórios Git novo criados em seu projeto **MyTeam**. Neste exemplo: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. Efetuar o seeding seus repositórios ProjectTemplate e TeamUtilities

O procedimento de propagação utiliza os diretórios na sua DSVM local como sites intermediários de testes. Se precisar de personalizar seu **ProjectTemplate** e **TeamUtilities** repositórios para atender a alguns específicos da Equipe às suas necessidades, fazê-lo na etapa a mais do procedimento a seguir. Aqui está um resumo dos passos utilizados para efetuar o seeding o conteúdo do **MyTeamProjectTemplate** e **MyTeamUtilities** repositórios para uma equipa de ciência de dados. Os passos individuais correspondem para as subsecções no procedimento propagação:

- Repositório de grupo do clone para diretório local: R1 - clonado a -> local D1 da Equipe
- Clone os repositórios de equipe para diretórios locais: R3 & R4 - clonado a -> local D3 & D4 da Equipe
- Copie o conteúdo de modelo de projeto de grupo para a pasta de equipe local:  D1 - conteúdo copiado a -> D3
- (Opcional) personalização do local D3 & D4
- Envie conteúdo de diretório local para repositórios de equipe: D3 & D4 - conteúdo Adicionar -> R3 & R4 da Equipe


### <a name="initialize-the-team-repositories"></a>Inicializar os repositórios de equipa

Neste passo, inicializa seu repositório de modelos de projeto do repositório de modelos de projeto do grupo:

- **MyTeamProjectTemplate** repositório (**R3**) do seu **GroupProjectTemplate** (**R1**) repositório


### <a name="clone-group-repositories-into-local-directories"></a>Clone repositórios de grupo para diretórios locais

Para iniciar este procedimento:

- Crie diretórios no seu computador local:
    - Para **Windows**: **C:\GitRepos\GroupCommon** e **C:\GitRepos\MyTeam**
    - Para **Linux**: **GitRepos\GroupCommon** e **GitRepos\MyTeam** no diretório raiz 
- Mude para diretório **GitRepos\GroupCommon**.
- Execute o comando seguinte, conforme apropriado, no sistema operativo do seu computador local.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Estes comandos clonagem seu **GroupProjectTemplate** repositório (R1) nos seus serviços de DevOps do Azure de grupo para um diretório local no **GitRepos\GroupCommon** no seu computador local. Após a clonagem, diretório **GroupProjectTemplate** (D1) é criado no diretório **GitRepos\GroupCommon**. Aqui, partimos do princípio de que o seu Gestor de grupo criado um projeto **GroupCommon**e o **GroupProjectTemplate** repositório está sob este projeto. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Clone os repositórios de equipe para diretórios locais

Estes comandos clonagem seu **MyTeamProjectTemplate** (R3) e **MyTeamUtilities** repositórios (R4) em seu projeto **MyTeam** nos seus serviços de DevOps do Azure de grupo para o **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** diretórios (D4) na **GitRepos\MyTeam** no seu computador local. 

- Mude para diretório **GitRepos\MyTeam**
- Execute os comandos seguintes, conforme apropriado, no sistema operativo do seu computador local. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Após a clonagem, dois diretórios **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4) são criados no diretório **GitRepos\MyTeam**. Pressupomos aqui chamado a seu projeto de modelo e utilitários repositórios **MyTeamProjectTemplate** e **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>Copie o conteúdo de modelo de projeto de grupo para o diretório de modelo de projeto local

Para copiar o conteúdo do local **GroupProjectTemplate** pasta (D1) local **MyTeamProjectTemplate** (D3), execute um dos seguintes scripts de shell: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Do PowerShell da linha de comandos para Windows       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>A partir da shell do Linux para o **DSVM do Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Os scripts de excluem o conteúdo do diretório do projeto. Os scripts de pedem-lhe para fornecer a **concluir caminhos** para o diretório de origem de D1 e para o diretório de destino D3.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>Personalizar o seu projeto modelo ou do team utilitários (opcionais)

Personalizar sua **MyTeamProjectTemplate** (D3) e **MyTeamUtilities** (D4), se necessário, nesta fase do processo de configuração. 

- Se desejar personalizar o conteúdo do D3 para satisfazer as necessidades específicas da sua equipa, pode modificar os documentos do modelo ou alterar a estrutura de diretórios.

- Se sua equipe desenvolveu alguns utilitários que pretende partilhar com toda a equipa, copie e cole esses utilitários no diretório D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Enviar conteúdo de diretório local para repositórios de equipa

Para adicionar o conteúdo nos diretórios locais (opcionalmente personalizados) D3 e D4 para os repositórios de equipe R3 e R4, execute o git seguinte comandos do bash a partir de uma consola do Windows PowerShell ou a partir da shell do Linux. Execute os comandos a partir da **GitRepos\MyTeam\MyTeamProjectTemplate** diretório.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Os ficheiros no repositório MyTeamProjectTemplate dos serviços de DevOps do Azure do seu grupo são sincronizados quase instantaneamente quando este script é executado.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Agora, execute o mesmo conjunto de quatro comandos git a partir da **GitRepos\MyTeam\MyTeamUtilities** diretório. 

> [AZURE.NOTE]Se esta for a primeira vez que se comprometer com um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os dois comandos seguintes:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se empenhados em vários repositórios de Git, utilize o mesmo nome e endereço de e-mail ao consolidar a cada um deles. Com o mesmo nome e endereço de e-mail prova conveniente posteriormente quando cria dashboards do Power BI para controlar as atividades de Git em vários repositórios.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Criar a equipe de recursos e análise de dados (opcional)

Compartilhamento de recursos de dados e análise com toda a equipa tem benefícios de desempenho e custo: os membros da Equipe podem executar seus projetos nos recursos partilhados, guardar no orçamentos e colaborar de forma mais eficiente. Nesta seção, fornecemos instruções sobre como criar o armazenamento de ficheiros do Azure. Na próxima seção, fornecemos instruções sobre como armazenamento de ficheiros do Azure de montagem no seu computador local. Para obter mais informações sobre o compartilhamento de outros recursos, tais como dados de ciência de máquinas virtuais do Azure, Azure HDInsight Spark Clusters, veja [as ferramentas e plataformas](platforms-and-tools.md). Este tópico fornece orientações de uma perspectiva de ciência de dados sobre a seleção de recursos que são adequados para as suas necessidades e links para páginas do produto e outros tutoriais de relevantes e útil que publicados.

>[AZURE.NOTE] Para evitar cruzada de transmissão de dados, centros de dados, que podem ser lenta e dispendioso, certifique-se de que o grupo de recursos, a conta de armazenamento e a VM do Azure (por exemplo, DSVM) estão no mesmo centro de dados do Azure. 

Execute os seguintes scripts para criar o armazenamento de ficheiros do Azure para a sua equipa. Armazenamento de ficheiros do Azure para a sua equipa pode ser utilizado para armazenar elementos de dados que são úteis para a equipe inteira. Os scripts solicitar-lhe para sua informação de conta e subscrição do Azure, por isso, ter estas credenciais prontos para introduzir. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Criar armazenamento de ficheiros do Azure com o PowerShell do Windows

Execute este script do PowerShell da linha de comandos:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Inicie sessão sua conta do Microsoft Azure quando lhe for pedido:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Selecione a subscrição do Azure que pretende utilizar:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Selecione a conta de armazenamento a utilizar ou criar um novo com a sua subscrição selecionada:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Introduza o nome do armazenamento de ficheiros do Azure para criar. Apenas carateres, números de casos e - são aceites:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Para facilitar a montagem e partilhar este armazenamento depois de criado, guarde as informações de armazenamento de ficheiros do Azure para um ficheiro de texto e tome nota do caminho para a localização. Em particular, precisa esse arquivo para montar o armazenamento de ficheiros do Azure para máquinas virtuais do Azure, na secção seguinte. 

É uma boa prática para verificar neste ficheiro de texto no seu repositório de ProjectTemplate. Recomendamos que coloque no diretório **Docs\DataDictionaries**. Por conseguinte, este recurso de dados pode ser acedido por todos os projetos da sua equipa. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Criar armazenamento de ficheiros do Azure com um script do Linux

Execute este script a partir da shell do Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Inicie sessão sua conta do Microsoft Azure seguindo as instruções neste ecrã:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Selecione a subscrição do Azure que pretende utilizar:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Selecione a conta de armazenamento a utilizar ou criar um novo com a sua subscrição selecionada:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Introduza o nome do armazenamento de ficheiros do Azure para criar, carateres de apenas letras minúsculas, números e - são aceites:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Para facilitar a aceder a este armazenamento depois de criado, guarde as informações de armazenamento de ficheiros do Azure para um ficheiro de texto e tome nota do caminho para a localização. Em particular, precisa esse arquivo para montar o armazenamento de ficheiros do Azure para máquinas virtuais do Azure, na secção seguinte.

É uma boa prática para verificar neste ficheiro de texto no seu repositório de ProjectTemplate. Recomendamos que coloque no diretório **Docs\DataDictionaries**. Por conseguinte, este recurso de dados pode ser acedido por todos os projetos da sua equipa. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Armazenamento de ficheiros do Azure de montagem (opcional)

Depois do armazenamento de ficheiros do Azure é criado com êxito, podem ser montada no seu computador local utilizando um dos seguintes scripts do PowerShell ou do Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Armazenamento de ficheiros do Azure de montagem com o PowerShell do Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
É-lhe perguntado para iniciar sessão em primeiro lugar, se não tiver sessão iniciada. 

Clique em **Enter** ou **y** continuar quando lhe for perguntado se tem uma informação de armazenamento de ficheiros do Azure de ficheiros e, em seguida, introduza o ***concluir o caminho e nome** do ficheiro que criar no passo anterior. As informações para montar um armazenamento de ficheiros do Azure são lidas diretamente a partir do que o ficheiro e está pronto para ir para o passo seguinte.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Se não tiver um ficheiro que contém as informações de armazenamento de ficheiros do Azure, os passos para introduzir as informações de teclado são fornecidos no final desta secção.

Em seguida, é-lhe perguntado para introduzir o nome da unidade a ser adicionado à sua máquina virtual. Uma lista de nomes de unidade existentes é impresso na tela. Deve fornecer um nome de unidade que ainda não existir na lista.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Certifique-se que uma nova unidade F foi montada com êxito para o seu computador.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Como introduzir manualmente as informações de armazenamento de ficheiros do Azure:** Se não tiver as informações de armazenamento de ficheiros do Azure num arquivo de texto, pode seguir as instruções no ecrã seguinte para o tipo na subscrição obrigatório, conta de armazenamento e informações de armazenamento de ficheiros do Azure:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Tipo de nome do seu subscrição do Azure, selecione a conta de armazenamento onde o armazenamento de ficheiros do Azure é criado, e escreva o nome de armazenamento de ficheiros do Azure:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Armazenamento de ficheiros do Azure de montagem com um script do Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

É-lhe perguntado para iniciar sessão em primeiro lugar, se não tiver sessão iniciada. 

Clique em **Enter** ou **y** continuar quando lhe for perguntado se tem uma informação de armazenamento de ficheiros do Azure de ficheiros e, em seguida, introduza o ***concluir o caminho e nome** do ficheiro que criar no passo anterior. As informações para montar um armazenamento de ficheiros do Azure são lidas diretamente a partir do que o ficheiro e está pronto para ir para o passo seguinte.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Em seguida, é-lhe perguntado para introduzir o nome da unidade a ser adicionado à sua máquina virtual. Uma lista de nomes de unidade existentes é impresso na tela. Deve fornecer um nome de unidade que ainda não existir na lista.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Certifique-se que uma nova unidade F foi montada com êxito para o seu computador.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Como introduzir manualmente as informações de armazenamento de ficheiros do Azure:** Se não tiver as informações de armazenamento de ficheiros do Azure num arquivo de texto, pode seguir as instruções no ecrã seguinte para o tipo na subscrição obrigatório, conta de armazenamento e informações de armazenamento de ficheiros do Azure:

- Entrada **n**.
- Selecione o índice do nome da subscrição em que o armazenamento de ficheiros do Azure foi criado no passo anterior:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Selecione a conta de armazenamento na sua subscrição e introduza o nome de armazenamento de ficheiros do Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Introduza o nome da unidade a ser adicionado à máquina, que deve ser diferente de qualquer já existentes:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configurar a política de controlo de segurança 

A partir do seu grupo da home do Azure DevOps page dos serviços, clique nas **ícone de engrenagem** junto ao seu nome de utilizador no canto superior direito, em seguida, selecione a **segurança** separador. Pode adicionar membros à sua equipe com várias permissões.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md)

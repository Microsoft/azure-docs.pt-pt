---
title: Tarefas para o projeto levam no Team Data Science Process
description: Espera-se uma descrição das tarefas que levam de um projeto num projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1ee409bc9e4ad60602ddffd3d1896baf09df52da
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854794"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Tarefas para o projeto levam no Team Data Science Process

Este tutorial descreve as tarefas que um líder de projeto é esperado concluir para sua equipe de projeto. O objetivo é estabelecer o ambiente de equipa de colaboração que padroniza sobre o [Team Data Science Process](overview.md) (TDSP). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar soluções de Análise Preditiva com base na cloud de forma eficiente. Para uma descrição das funções pessoal e das respetivas tarefas associadas que são processadas por uma equipa de ciência de dados padronizar este processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md).

R **líder de projeto** gerencia as atividades diárias de cientistas de dados individuais num projeto de ciência de dados específicos. O fluxo de trabalho para as tarefas a serem concluídas pela oportunidades potenciais de projeto para configurar este ambiente estão representados na figura a seguir:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tópico inclui atualmente tarefas 1,2 e 6, este fluxo de trabalho para oportunidades potenciais do projeto.

> [!NOTE]
> Descrevemos os passos necessários para configurar um ambiente de equipe do TDSP para um projeto de DevOps do Azure a utilizar nas seguintes instruções. Especificamos como realizar estas tarefas com o Azure DevOps, uma vez que é saber como implementamos TDSP na Microsoft. Se outra plataforma de alojamento de código é utilizada para o seu grupo, as tarefas que devem ser concluídas pelo líder da Equipe geralmente não são alterados. Mas a forma de concluir estas tarefas vai ser diferente.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Esses nomes tornam mais fáceis de compreender as operações entre os repositórios e diretórios. Este notação (R repositórios do Git) e D para diretórios locais na sua DSVM é usada nas seções a seguir:

- **R3**: A equipe **ProjectTemplate** repositório de Git configurou seu chefe de equipe.
- **R5**: O repositório do projeto no Git, configurar para o seu projeto.
- **D3**: O diretório de local clonar a partir R3.
- **D5**: O diretório de local clonar a partir R5.


## <a name="0-prerequisites"></a>0. Pré-requisitos

Os pré-requisitos são cumpridos ao concluir as tarefas atribuídas para o seu Gestor de grupo descrita [tarefas de Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md) e para da Equipe líder descrito no [tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md). 

Para resumir aqui, os seguintes requisitos tem de cumprir antes de iniciar as tarefas de líder de equipe: 

- Sua **grupo de serviços do Azure DevOps** (ou conta de grupo em outra plataforma de alojamento de código) tiver sido configurado pelo seu Gestor de grupo.
- Sua **TeamProjectTemplate repositório** (R3) tiver sido configurada na sua conta do grupo pelo seu chefe de equipe na plataforma de alojamento de código que pretende utilizar.
- A sua sessão foi **autorizado** pelo seu chefe de equipe para criar repositórios na sua conta de grupo para a sua equipa.
- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), o Git tiver sido previamente instalado e estiver pronto para começar. Caso contrário, consulte a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **DSVM do Windows**, tem de ter [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo para o **transfira e instale** secção e clique nas *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transfira o instalador .exe aqui e executá-lo. 
- Se estiver a utilizar **DSVM do Linux**, crie uma chave pública SSH na sua DSVM e adicioná-lo aos seus serviços de DevOps do Azure de grupo. Para obter mais informações sobre o SSH, consulte a **criar chave pública SSH** secção a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Criar um repositório de projeto (R5)

- Inicie sessão no seu grupo nos serviços de DevOps do Azure no *https://\<nome de serviços do Azure DevOps\>. visualstudio.com*. 
- Sob **projetos recentes & equipes**, clique em **procurar**. Uma janela que surge apresenta uma lista de todos os projetos nos serviços de DevOps do Azure. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Clique no nome do projeto em que pretende criar o seu repositório do projeto. Neste exemplo, clique em **MyTeam**. 
- Em seguida, clique em **Navigate** para ser direcionado para a home page do projeto **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Clique em **Colabore em código** para ser direcionado para a home page do git de seu projeto.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Clique na seta para baixo no canto superior esquerdo e selecione **+ novo repositório**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Na **criar um novo repositório** janela, introduza um nome para o repositório de git do projeto. Certifique-se de que seleciona **Git** como o tipo do repositório. Neste exemplo, utilizamos o nome *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Para criar sua ***DSProject1*** repositório de git do projeto, clique em **criar**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Efetuar o seeding o repositório do projeto DSProject1

A tarefa aqui é seed a **DSProject1** o repositório de projeto (R5) do seu repositório do modelo de projeto (R3). O procedimento de propagação utiliza os diretórios D3 e D5 em sua DSVM local como sites intermediários de testes. Em resumo, o caminho de propagação é: R3 -> D3 -> D5 -> R5.

Se precisar de personalizar seu **DSProject1** repositório de projeto para atender a alguns específicos do projeto às suas necessidades, fazê-lo na etapa a mais do procedimento a seguir. Aqui está um resumo dos passos utilizados para efetuar o seeding o conteúdo do **DSProject1** repositório do projeto. Os passos individuais correspondem para as subsecções no procedimento propagação:

- Repositório de modelos de projeto de clone para diretório local: R3 - clonado a -> local D3 da equipe.
- Repositório de DSProject1 clone para um diretório local: R5 - clonado a -> local D5 da equipe.
- Copie o conteúdo de modelo de projeto clonado para o clone local do repositório de DSProject1:  D3 - conteúdo copiado a -> D5.
- (Opcional) D5 local de personalização.
- Envie conteúdo de DSProject1 local para repositórios de equipe: D5 - adicionar conteúdo a -> equipa R5.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clone o repositório de modelos de projeto (R3) para um diretório (D3) no seu computador local.

No seu computador local, crie um diretório:

- *C:\GitRepos\MyTeamCommon* para Windows 
- *$home/GitRepos/MyTeamCommon* para Linux

Altere para esse diretório. Em seguida, execute o seguinte comando para clonar o repositório de modelos de projeto no seu computador local. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Se estiver a utilizar do Azure DevOps como a plataforma de alojamento de código, normalmente, o *URL de HTTPS do seu repositório de modelos de projeto* é:

 ***https://\<nome de serviços do Azure DevOps\>.visualstudio.com/\<o nome do projeto\>/_git/\<seu nome de repositório do modelo de projeto\>***. 

Neste exemplo, temos:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Se estiver a utilizar do Azure DevOps como a plataforma de alojamento de código, normalmente, o *SSH URL do repositório de modelos de projeto* é:

***o SSH: / /\<nome de serviços do Azure DevOps\>\@\<nome de serviços do Azure DevOps\>.visualstudio.com:22/\<nome do seu projeto > /_git/\<seu modelo de projeto nome do repositório\>.*** 

Neste exemplo, temos:

***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Clonar o repositório de DSProject1 (R5) para um diretório (D5) no seu computador local

Altere o diretório para **GitRepos**, e execute o seguinte comando para clonar o repositório de projeto no seu computador local. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Se estiver a utilizar do Azure DevOps como a plataforma de alojamento de código, normalmente, o _URL de HTTPS do repositório do projeto_ é ***https://\<nome de serviços do Azure DevOps\>.visualstudio.com/\<Nome do seu projeto > /_git/ < nome do seu projeto repositório\>***. Neste exemplo, temos ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Se estiver a utilizar do Azure DevOps como a plataforma de alojamento de código, normalmente, o _SSH URL do repositório do projeto_ é _ssh: / / < nome de serviços do Azure DevOps\>@< nome de serviços do Azure DevOps\>. visualstudio.com:22/<Your Project Name>/\_git / < nome do seu repositório de projeto\>. Neste exemplo, temos ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Copie o conteúdo do D3 para D5 

Agora no seu computador local, precisa copiar o conteúdo do _D3_ ao _D5_, exceto os metadados de git no diretório do projeto. Os seguintes scripts fará a tarefa. Certifique-se de digitar os caminhos corretos e completos para os diretórios. Pasta de origem é o para a sua equipa (_D3_); pasta de destino é a para seu projeto (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Agora pode ver na _DSProject1_ pasta, todos os ficheiros (excluindo o projeto) são copiados do _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Agora pode ver na _DSProject1_ pasta, todos os ficheiros (exceto os metadados em. Git) são copiados do _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personalizar o D5 se precisar de (opcional)

Se o projeto precise alguns diretórios específicos ou documentos, diferentes dos que obtém a partir do seu modelo de projeto (copiado para o seu diretório D5 no passo anterior), pode personalizar o conteúdo de D5 agora. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Adicionar conteúdo de DSProject1 no D5 para R5 nos seus serviços do grupo do Azure DevOps

Agora preciso de enviar por push o conteúdo **_DSProject1_** para _R5_ repositório no seu projeto nos serviços de DevOps do Azure do seu grupo. 


- Mude para diretório **D5**. 
- Utilize os seguintes comandos do git para adicionar o conteúdo na **D5** ao **R5**. Os comandos são os mesmos para sistemas Windows e Linux. 
    
    Adicione git de estado do Git.
    push de git do Git consolidação -m "push do win DSVM"
    
- Consolide a alteração e push. 

> [!NOTE]
> Se esta for a primeira vez que se comprometer com um repositório de Git, terá de configurar parâmetros globais *user.name* e *user.email* antes de executar o `git commit` comando. Execute os dois comandos seguintes:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Se empenhados em vários repositórios de Git, utilize o mesmo nome e endereço de e-mail em todos eles. Com o mesmo nome e endereço de e-mail prova conveniente posteriormente quando cria dashboards do Power BI para controlar as atividades de Git em vários repositórios.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Criar e montar o armazenamento de ficheiros do Azure como recursos de projeto (opcional)

Se pretende criar o armazenamento de ficheiros do Azure para partilhar dados, como o projeto de dados não processados ou as funcionalidades geradas para seu projeto, para que todos os membros do projeto tenham acesso aos conjuntos de dados mesmo de vários DSVMs, siga as instruções nas secções 3 e 4 de [ Tarefas de oportunidades potenciais para uma equipa de ciência de dados de equipa](team-lead-tasks.md). 


## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md)

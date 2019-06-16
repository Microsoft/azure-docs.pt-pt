---
title: Tarefas para um colaborador individual no Team Data Science Process
description: Uma descrição de tarefas para um colaborador individual num projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043354"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um colaborador individual no Team Data Science Process

Este tópico de contornos as tarefas que é um colaborador individual esperado concluir a sua equipa de ciência de dados. O objetivo é estabelecer o ambiente de equipa de colaboração que padroniza sobre o [Team Data Science Process](overview.md) (TDSP). Para uma descrição das funções pessoal e das respetivas tarefas associadas que são processadas por uma equipa de ciência de dados padronizar este processo, consulte [Team Data Science Process funções e tarefas](roles-tasks.md).

As tarefas de colaboradores individuais do projeto (cientistas de dados) para configurar o ambiente para o projeto de TDSP estão representadas da seguinte forma: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** é o repositório que mantém seu grupo de compartilhar utilitários útil entre o grupo inteiro. 
- **TeamUtilities** é o repositório que sua equipe mantém especificamente para a sua equipa. 

Para obter instruções sobre como executar um projeto de ciência de dados em TDSP, consulte [execução de projetos de ciência de dados](project-execution.md). 

>[AZURE.NOTE] Descrevemos os passos necessários para configurar um ambiente de equipe do TDSP através do Azure DevOps nas instruções seguintes. Especificamos como realizar estas tarefas com o Azure DevOps, uma vez que é saber como implementamos TDSP na Microsoft. Se outra plataforma de alojamento de código é utilizada para o seu grupo, as tarefas que devem ser concluídas pelo líder da Equipe geralmente não são alterados. Mas a forma de concluir estas tarefas vai ser diferente.


## <a name="repositories-and-directories"></a>Repositórios e diretórios

Este tutorial utiliza nomes abreviados para repositórios e diretórios. Esses nomes tornam mais fáceis de compreender as operações entre os repositórios e diretórios. Este notação (**R** repositórios do Git e **1!d** para diretórios locais na sua DSVM) é usado nas seções a seguir:

- **R2**: O repositório de GroupUtilities no Git que seu gerente de grupo tenha configurado no seu servidor de grupo do Azure DevOps.
- **R4**: O repositório de TeamUtilities no Git que configurou seu chefe de equipe.
- **R5**: O repositório do projeto no Git que foi configurado pelo seu líder de projeto.
- **D2**: O diretório de local clonado a partir do R2.
- **D4**: O diretório de local clonar a partir R4.
- **D5**: O diretório de local clonar a partir R5.


## <a name="step-0-prerequisites"></a>Passo-0: Pré-requisitos

Os pré-requisitos são cumpridos ao concluir as tarefas atribuídas para o seu Gestor de grupo descrita [tarefas de Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md). Para resumir aqui, os seguintes requisitos tem de ser cumpridos antes de iniciar as tarefas de líder de equipe: 
- O Gestor de grupo tiver configurado a **GroupUtilities** repositório (se houver). 
- Seu chefe de equipe tiver configurado a **TeamUtilities** repositório (se houver).
- O líder de projeto tiver configurado o repositório do projeto. 
- Foi adicionado ao seu repositório do projeto, o líder de projeto com o privilégio para clonar a partir dos e voltar a emitir para o repositório do projeto.

A segunda **TeamUtilities** repositório, pré-requisitos é opcional, dependendo se a sua equipa tem um repositório de específicas para a equipe de utilitário. Se qualquer um dos outros três pré-requisitos não foi concluída, contacte o seu chefe de equipe, o líder de projeto ou seus delegados a configurá-la ao seguir as instruções para [líder da equipe de tarefas para uma equipa de ciência de dados](team-lead-tasks.md) ou para [ Tarefas de oportunidades potenciais para uma equipa de ciência de dados do projeto](project-lead-tasks.md).

- Git tem de estar instalado no seu computador. Se estiver a utilizar uma Máquina Virtual de ciência de dados (DSVM), o Git tiver sido previamente instalado e estiver pronto para começar. Caso contrário, consulte a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix).  
- Se estiver a utilizar um **DSVM do Windows**, tem de ter [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado no seu computador. No ficheiro README.md, desloque para baixo para o **transfira e instale** secção e clique nas *instalador mais recente*. Isto leva-o para a página de instalador mais recente. Transfira o instalador .exe aqui e executá-lo. 
- Se estiver a utilizar **DSVM do Linux**, crie uma chave pública SSH na sua DSVM e adicioná-lo aos seus serviços de DevOps do Azure de grupo. Para obter mais informações sobre o SSH, consulte a **criar chave pública SSH** secção a [apêndice de ferramentas e plataformas](platforms-and-tools.md#appendix). 
- Se o líder de equipe e/ou projeto tiver criado algum armazenamento de ficheiros do Azure que precisa montar à sua DSVM, deve obter as informações de armazenamento de ficheiros do Azure dos mesmos. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Passo 1 a 3: Grupo de clone, a equipe e repositórios de projeto para o computador local

Esta secção fornece instruções sobre como concluir as três primeiras tarefas de colaboradores individuais do projeto: 

- Clone o **GroupUtilities** repositório R2 para D2
- Clone o **TeamUtilities** repositório R4 a D4 
- Clone o **projeto** repositório R5 para D5.

No seu computador local, crie um diretório ***C:\GitRepos*** (para Windows) ou ***$home/GitRepos*** (forLinux) e, em seguida, altere para esse diretório. 

Execute um dos seguintes comandos (conforme apropriado para seu sistema operacional) para clonar seu **GroupUtilities**, **TeamUtilities**, e **projeto** repositórios para os diretórios no seu computador local: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Confirme que vê as três pastas no seu diretório do projeto.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Confirme que vê as três pastas no seu diretório do projeto.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Passo 4-5: Armazenamento de ficheiros do Azure de montagem à sua DSVM (opcional)

Para armazenamento de ficheiros do Azure de montagem à sua DSVM, consulte as instruções na secção 4 do [tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md)


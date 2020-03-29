---
title: Tarefas para um colaborador individual no Processo de Ciência de Dados da Equipa
description: Uma passagem detalhada das tarefas para um colaborador individual num projeto da equipa de ciência de dados.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721256"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um colaborador individual no Processo de Ciência de Dados da Equipa

Este tópico descreve as tarefas que um *colaborador individual* completa para a criação de um projeto no Processo de Ciência de Dados da [Equipa](overview.md) (TDSP). O objetivo é trabalhar num ambiente de equipa colaborativo que normalize no TDSP. O TDSP foi concebido para ajudar a melhorar a colaboração e a aprendizagem em equipa. Para um esboço das funções de pessoal e das suas tarefas associadas que são tratadas por uma equipa de ciência de dados padronizada no TDSP, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa.

O diagrama seguinte mostra as tarefas que os contribuintes individuais (cientistas de dados) completam para configurar o seu ambiente de equipa. Para obter instruções sobre como executar um projeto de ciência de dados no âmbito do TDSP, consulte a [Execução de projetos de ciência de dados](project-execution.md). 

![Tarefas individuais dos contribuintes](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** é o repositório que a sua equipa de projeto mantém para partilhar modelos e ativos do projeto.
- **TeamUtilities** é o repositório de utilidades que a sua equipa mantém especificamente para a sua equipa. 
- **GroupUtilities** é o repositório que o seu grupo mantém para partilhar utilitários úteis em todo o grupo. 

> [!NOTE] 
> Este artigo utiliza o Azure Repos e uma Máquina Virtual de Ciência de Dados (DSVM) para criar um ambiente TDSP, porque é assim que se implementa o TDSP na Microsoft. Se a sua equipa utilizar outras plataformas de hospedagem ou desenvolvimento de códigos, as tarefas individuais dos contribuintes são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram criados pelo seu gestor de [grupo,](group-manager-tasks.md)líder de [equipa,](team-lead-tasks.md)e líder do [projeto:](project-lead-tasks.md)

- A **organização** Azure DevOps para a sua unidade de ciência de dados
- Um **repositório** de projeto criado pelo seu projeto leva a partilhar modelos e ativos de projeto
- **Repositórios de Utilitários** e **TeamUtilities** criados pelo gestor do grupo e liderança da equipa, se aplicável
- Armazenamento de **ficheiros** Azure configurado para ativos partilhados para a sua equipa ou projeto, se aplicável
- **Permissões** para que clone e empurre para o seu repositório de projeto 

Para clonar repositórios e modificar conteúdos na sua máquina local ou DSVM, ou montar o armazenamento de ficheiros Azure para o seu DSVM, você precisa considerar esta lista de verificação:

- Uma subscrição do Azure.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as [Plataformas e ferramentas do apêndice](platforms-and-tools.md#appendix).
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criaram e configuraram em Azure. Para mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual da Ciência dos Dados .
- Para um DSVM windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **Descarregamento e Instalação** e selecione o **mais recente instalador**. Descarregue o instalador *.ex e* execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção de **chaves públicas Create SSH** nas Plataformas e no apêndice das [ferramentas](platforms-and-tools.md#appendix). 
- As informações de armazenamento de ficheiros Azure para qualquer armazenamento de ficheiros Azure que você precisa montar para o seu DSVM. 

## <a name="clone-repositories"></a>Repositórios de clones

Para trabalhar com repositórios localmente e empurrar as suas mudanças para a equipa partilhada e repositórios de projeto, você primeiro copia ou *clona* os repositórios para a sua máquina local. 

1. No Azure DevOps, vá à página sumária do projeto da sua equipa em *\//\<https: nome do servidor>/\<nome de organização>/\<nome *de equipa>, por exemplo, **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Selecione **Repos** na navegação esquerda e, no topo da página, selecione o repositório que pretende clonar.
   
1. Na página do repo, selecione **Clone** na parte superior direita.
   
1. No diálogo de **repositório Clone,** selecione **HTTPS** para uma ligação HTTP, ou **SSH** para uma ligação SSH, e copie o URL do clone sob **a linha de comando** para a sua área de pré-quadro.
   
   ![Repo clone](./media/project-ic-tasks/clone.png)
   
1. Na sua máquina local ou DSVM, crie os seguintes diretórios:
   
   - Para Janelas: **C:\GitRepos**
   - Para Linux: **$home/GitRepos**
   
1. Mude para o diretório que criou.
   
1. Em Git Bash, `git clone <clone URL>` mande o comando para cada repositório que quiser clonar. 
   
   Por exemplo, os seguintes clones de comando o repositório **TeamUtilities** para o diretório *MyTeam* na sua máquina local. 
   
   **Ligação HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Ligação SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Confirme que pode ver as pastas dos repositórios clonados no seu diretório de projeto local.
   
   ![Três pastas de repositório local](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Armazenamento de ficheiros Mount Azure para o seu DSVM

Se a sua equipa ou projeto tiver ativos partilhados no armazenamento de ficheiros Azure, monte o armazenamento de ficheiros na sua máquina local ou DSVM. Siga as instruções no armazenamento de [ficheiros Mount Azure na sua máquina local ou DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados da Equipa:

- [Tarefas do Gestor de Grupos para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de Team Lead para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas do Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)


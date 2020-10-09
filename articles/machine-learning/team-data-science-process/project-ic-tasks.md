---
title: Tarefas para um contribuinte individual no Processo de Ciência de Dados da Equipa
description: Uma análise detalhada das tarefas para um colaborador individual num projeto de equipa de data science.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76721256"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Tarefas para um contribuinte individual no Processo de Ciência de Dados da Equipa

Este tópico descreve as tarefas que um *colaborador individual* completa para criar um projeto no Processo de Ciência de [Dados de Equipa](overview.md) (TDSP). O objetivo é trabalhar num ambiente de equipa colaborativo que normalize na TDSP. O TDSP foi concebido para ajudar a melhorar a colaboração e a aprendizagem em equipa. Para um esboço das funções de pessoal e das suas tarefas associadas que são tratadas por uma equipa de ciência de dados que normaliza no TDSP, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md)

O diagrama seguinte mostra as tarefas que projetam os contribuintes individuais (cientistas de dados) completos para configurar o seu ambiente de equipa. Para obter instruções sobre como executar um projeto de ciência de dados no âmbito da TDSP, consulte [execução de projetos de ciência de dados.](project-execution.md) 

![Tarefas individuais de contribuinte](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** é o repositório que a sua equipa de projeto mantém para partilhar modelos e ativos de projeto.
- **TeamUtilities** é o repositório de utilitários que a sua equipa mantém especificamente para a sua equipa. 
- **GroupUtilities** é o repositório que o seu grupo mantém para partilhar utilitários úteis em todo o grupo. 

> [!NOTE] 
> Este artigo utiliza o Azure Repos e uma Máquina Virtual de Ciência de Dados (DSVM) para criar um ambiente TDSP, porque é assim que se implementa a TDSP na Microsoft. Se a sua equipa utilizar outras plataformas de hospedagem ou desenvolvimento de códigos, as tarefas individuais dos contribuintes são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que os seguintes recursos e permissões foram criados pelo seu [gestor de grupo,](group-manager-tasks.md) [líder de equipa,](team-lead-tasks.md)e [líder do projeto:](project-lead-tasks.md)

- A **organização** Azure DevOps para a sua unidade de ciência de dados
- Um **repositório de projeto** criado pelo seu projeto leva a partilhar modelos e ativos de projeto
- Repositórios **groupUtilities** e **TeamUtilities** criados pelo gestor do grupo e pelo líder da equipa, se aplicável
- Armazenamento **de ficheiros** Azure configurado para ativos partilhados para a sua equipa ou projeto, se aplicável
- **Permissões** para clonar e empurrar de volta para o seu repositório de projeto 

Para clonar repositórios e modificar o conteúdo na sua máquina local ou DSVM, ou montar o armazenamento de ficheiros Azure no seu DSVM, tem de considerar esta lista de verificação:

- Uma subscrição do Azure.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as Plataformas e o [apêndice de ferramentas.](platforms-and-tools.md#appendix)
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criado e configurado em Azure. Para obter mais informações e instruções, consulte a [Documentação da Máquina Virtual da Ciência dos Dados.](/azure/machine-learning/data-science-virtual-machine/)
- Para um DSVM Do Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **de Descarregamento e Instalação** e selecione o **instalador mais recente**. Descarregue o instalador *.exe* da página do instalador e execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção **de chaves públicas Create SSH** no [apêndice de ferramentas e ferramentas.](platforms-and-tools.md#appendix) 
- As informações de armazenamento de ficheiros Azure para qualquer armazenamento de ficheiros Azure que necessite de montar no seu DSVM. 

## <a name="clone-repositories"></a>Repositórios de clones

Para trabalhar com repositórios localmente e empurrar as suas mudanças para a equipa partilhada e repositórios de projeto, você primeiro copiar ou *clonar* os repositórios para a sua máquina local. 

1. Em Azure DevOps, aceda à página de resumo do projeto da sua equipa em *https: \/ / \<server name> / \<organization name> / \<team name> *, por exemplo, **https: \/ /dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Selecione **Repos** na navegação à esquerda e, no topo da página, selecione o repositório que pretende clonar.
   
1. Na página de repo, selecione **Clone** na parte superior direita.
   
1. No diálogo **do repositório do Clone,** selecione **HTTPS** para uma ligação HTTP ou **SSH** para uma ligação SSH e copie o URL do clone sob **a linha de comando** para a sua área de transferência.
   
   ![Repo clone](./media/project-ic-tasks/clone.png)
   
1. Na sua máquina local ou DSVM, crie os seguintes diretórios:
   
   - Para janelas: **C:\GitRepos**
   - Para Linux: **$home/GitRepos**
   
1. Mude para o diretório que criou.
   
1. Em Git Bash, ordene `git clone <clone URL>` a cada repositório que queira clonar. 
   
   Por exemplo, o seguinte comando clona o repositório **TeamUtilities** para o diretório *MyTeam* na sua máquina local. 
   
   **Ligação HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Ligação SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Confirme que pode ver as pastas dos repositórios clonados no seu diretório de projetos local.
   
   ![Três pastas de repositório local](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Armazenamento de ficheiros Mount Azure para o seu DSVM

Se a sua equipa ou projeto tiver ativos partilhados no armazenamento de ficheiros Azure, monte o armazenamento de ficheiros na sua máquina local ou na DSVM. Siga as instruções no [armazenamento de ficheiros Mount Azure na sua máquina local ou na DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações com descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados de Equipa:

- [Tarefas do Gestor de Grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de liderança da equipa para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)


---
title: Tarefas para o projeto liderar no Processo de Ciência de Dados de Equipa
description: Uma passagem detalhada das tarefas para um projeto liderado por uma equipa de processo de ciência de dados de equipa
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321938"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Tarefas de liderança do projeto no Processo de Ciência de Dados de Equipa

Este artigo descreve tarefas que um *líder de projeto* completa para criar um repositório para a sua equipa de projeto no Processo de Ciência de [Dados de Equipa](overview.md) (TDSP). O TDSP é uma estrutura desenvolvida pela Microsoft que fornece uma sequência estruturada de atividades para executar eficientemente soluções de análise preditiva baseadas na nuvem. O TDSP foi concebido para ajudar a melhorar a colaboração e a aprendizagem em equipa. Para um esboço das funções de pessoal e das tarefas associadas para uma equipa de ciência de dados que esboça na TDSP, consulte [as funções e tarefas do Processo de Ciência de Dados de Equipa.](roles-tasks.md)

Um líder de projeto gere as atividades diárias de cientistas de dados individuais num projeto específico de ciência de dados na TDSP. O diagrama a seguir mostra o fluxo de trabalho para tarefas de chumbo do projeto:

![Fluxo de trabalho de tarefa de chumbo do projeto](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tutorial abrange o Passo 1: Criar repositório de projetos e repositório de projetos de semente da sua equipa ProjectTemplate. 

Para o passo 3: Criar item de trabalho de recurso para projeto, e passo 4: Adicionar Histórias para fases de projeto, ver [desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

Para o passo 5: Criar e personalizar os ativos de armazenamento/análise e partilhar, se necessário, ver [Criar dados de equipa e recursos de análise.](team-lead-tasks.md#create-team-data-and-analytics-resources)

Para o passo 6: Configure o controlo de segurança do repositório do projeto, consulte [adicionar os membros da equipa e configurar permissões](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Este artigo usa o Azure Repos para criar um projeto TDSP, porque é assim que se implementa a TDSP na Microsoft. Se a sua equipa utilizar outra plataforma de hospedagem de códigos, as tarefas de chumbo do projeto são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que o seu [gestor de grupo](group-manager-tasks.md) e líder de [equipa](team-lead-tasks.md) criaram os seguintes recursos e permissões:

- A **organização** Azure DevOps para a sua unidade de dados
- Um **projeto** de equipa para a sua equipa de ciência de dados
- Modelo de equipa e **repositórios** de utilidades
- **Permissões** na sua conta de organização para que crie e edite repositórios para o seu projeto

Para clonar repositórios e modificar conteúdos na sua máquina local ou na Máquina Virtual de Ciência de Dados (DSVM), ou configurar o armazenamento de ficheiros Azure e montá-lo no seu DSVM, também tem de considerar esta lista de verificação:

- Uma subscrição do Azure.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as Plataformas e o [apêndice de ferramentas.](platforms-and-tools.md#appendix)
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criado e configurado em Azure. Para obter mais informações e instruções, consulte a [Documentação da Máquina Virtual da Ciência dos Dados.](../data-science-virtual-machine/index.yml)
- Para um DSVM Do Windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **de Descarregamento e Instalação** e selecione o **instalador mais recente**. Descarregue o instalador *.exe* da página do instalador e execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção **de chaves públicas Create SSH** no [apêndice de ferramentas e ferramentas.](platforms-and-tools.md#appendix) 

## <a name="create-a-project-repository-in-your-team-project"></a>Crie um repositório de projeto no seu projeto de equipa

Para criar um repositório de projetos no projeto **MyTeam** da sua equipa:

1. Aceda ao projeto da sua equipa **Resumir** a página em *https: \/ / \<server name> / \<organization name> / \<team name>* , por exemplo, **https: \/ /dev.azure.com/DataScienceUnit/MyTeam** , e selecione **Repos** a partir da navegação à esquerda. 
   
1. Selecione o nome do repositório no topo da página e, em seguida, selecione **Novo repositório** a partir do dropdown.
   
   ![Selecione novo repositório](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. No Criar um novo diálogo **de repositório,** certifique-se de que **o Git** é selecionado por **tipo**. Introduza *o DSProject1* com **o nome repositório** e, em seguida, selecione **Criar**.
   
   ![Criar repositório](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Confirme que pode ver o novo repositório **DSProject1** na página de definições do seu projeto. 
   
   ![Repositório de projetos em Definições de Projeto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importe o modelo de equipa no seu repositório de projeto

Para preencher o seu repositório de projeto com o conteúdo do repositório do modelo da sua equipa:

1. Na **página** de resumo do projeto da sua equipa, selecione **Repos** na navegação à esquerda. 
   
1. Selecione o nome do repositório no topo da página e selecione **DSProject1** a partir do dropdown.
   
1. Na **página DSProject1 está a** página vazia, selecione **Import**. 
   
   ![Selecione Importar](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. No diálogo **repositório de repositório Import a Git,** selecione **Git** como o **tipo Origem** , e introduza o URL para o seu repositório **TeamTemplate** em **URL Clone**. O URL é *https: \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name>*. Por exemplo: **https: \/ /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Selecione **Import** (Importar). O conteúdo do repositório de modelos da sua equipa é importado para o seu repositório de projeto. 
   
   ![Repositório de modelo de equipa de importação](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se precisar de personalizar o conteúdo do seu repositório de projeto para atender às necessidades específicas do seu projeto, pode adicionar, eliminar ou modificar ficheiros e pastas de repositórios. Pode trabalhar diretamente em Azure Repos, ou clonar o repositório para a sua máquina local ou DSVM, fazer alterações e comprometer e empurrar as suas atualizações para o repositório de projeto partilhado. Siga as instruções da [Personalização do conteúdo dos repositórios da equipa](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações com descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados de Equipa:

- [Tarefas do Gestor de Grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de liderança da equipa para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas individuais de contribuinte para uma equipa de ciência de dados](project-ic-tasks.md)
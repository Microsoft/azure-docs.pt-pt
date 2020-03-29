---
title: Tarefas para o projeto liderar no Processo de Ciência de Dados da Equipa
description: Uma passagem detalhada das tarefas para um projeto liderado por uma equipa de Processo de Ciência de Dados de Equipa
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714423"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Tarefas de liderança do projeto no Processo de Ciência de Dados da Equipa

Este artigo descreve tarefas que um líder de *projeto* completa para criar um repositório para a sua equipa de projeto no Processo de Ciência de Dados da [Equipa](overview.md) (TDSP). O TDSP é um quadro desenvolvido pela Microsoft que fornece uma sequência estruturada de atividades para executar eficientemente soluções de análise preditivas baseadas na nuvem. O TDSP foi concebido para ajudar a melhorar a colaboração e a aprendizagem em equipa. Para um esboço das funções de pessoal e tarefas associadas para uma equipa de ciência de dados padronizada no TDSP, consulte as [funções e tarefas](roles-tasks.md)do Processo de Ciência de Dados da Equipa .

Um líder de projeto gere as atividades diárias de cientistas de dados individuais num projeto específico de ciência de dados no TDSP. O diagrama seguinte mostra o fluxo de trabalho para tarefas de chumbo do projeto:

![Fluxo de trabalho de tarefa de chumbo do projeto](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Este tutorial abrange a Etapa 1: Criar o repositório do projeto, e passo 2: repositório de projetos de sementes da sua equipa ProjectTemplate repositório. 

Para o Passo 3: Criar um item de trabalho de recurso para projeto, e Passo 4: Adicionar Histórias para fases de projeto, ver [desenvolvimento ágil de projetos de ciência de dados.](agile-development.md)

Para o Passo 5: Criar e personalizar ativos de armazenamento/análise e partilhar, se necessário, consulte [Criar dados da equipa e recursos de análise.](team-lead-tasks.md#create-team-data-and-analytics-resources)

Para o passo 6: Configurar o controlo de segurança do repositório do projeto, consulte [adicionar membros da equipa e configurar permissões](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Este artigo utiliza o Azure Repos para criar um projeto TDSP, porque é assim que se implementa o TDSP na Microsoft. Se a sua equipa utilizar outra plataforma de hospedagem de códigos, as tarefas principais do projeto são as mesmas, mas a forma de as completar pode ser diferente.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que o seu [gestor](group-manager-tasks.md) de grupo e [o líder da equipa](team-lead-tasks.md) criaram os seguintes recursos e permissões:

- A **organização** Azure DevOps para a sua unidade de dados
- Um **projeto** de equipa para a sua equipa de ciência de dados
- Modelo de equipa e **repositórios** de utilidades
- **Permissões** na sua conta de organização para que você crie e edite repositórios para o seu projeto

Para clonar repositórios e modificar conteúdos na sua máquina local ou máquina virtual de ciência de dados (DSVM), ou configurar o armazenamento de ficheiros Azure e montá-lo no seu DSVM, também precisa considerar esta lista de verificação:

- Uma subscrição do Azure.
- Git instalado na sua máquina. Se estiver a utilizar um DSVM, o Git está pré-instalado. Caso contrário, consulte as [Plataformas e ferramentas do apêndice](platforms-and-tools.md#appendix).
- Se pretender utilizar um DSVM, o Windows ou o Linux DSVM criaram e configuraram em Azure. Para mais informações e instruções, consulte a [Documentação](/azure/machine-learning/data-science-virtual-machine/)da Máquina Virtual da Ciência dos Dados .
- Para um DSVM windows, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado na sua máquina. No ficheiro *README.md,* desloque-se até à secção **Descarregamento e Instalação** e selecione o **mais recente instalador**. Descarregue o instalador *.ex e* execute-o. 
- Para um Linux DSVM, uma chave pública SSH configurada no seu DSVM e adicionada em Azure DevOps. Para obter mais informações e instruções, consulte a secção de **chaves públicas Create SSH** nas Plataformas e no apêndice das [ferramentas](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Crie um repositório de projetos no seu projeto de equipa

Para criar um repositório de projetos no projeto **MyTeam** da sua equipa:

1. Vá à página **resumo** do projeto da sua equipa em *https:\//\<nome do servidor>/\<nome de organização>/\<nome *da equipa>, por exemplo, **https:\//dev.azure.com/DataScienceUnit/MyTeam**, e selecione **Repos** da navegação à esquerda. 
   
1. Selecione o nome do repositório na parte superior da página e, em seguida, selecione **Novo repositório** a partir do dropdown.
   
   ![Selecione novo repositório](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. No Create um novo diálogo **de repositório,** certifique-se de que **git** é selecionado em **Tipo**. Introduza *o DSProject1* sob **o nome repositório**e, em seguida, selecione **Criar**.
   
   ![Criar repositório](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Confirme que pode ver o novo repositório **DSProject1** na página de definições do seu projeto. 
   
   ![Repositório de projetos em Definições de Projeto](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Importe o modelo de equipa para o seu repositório de projeto

Para povoar o seu repositório de projeto com o conteúdo do repositório do modelo de equipa:

1. A partir da **página** resumo do projeto da sua equipa, selecione **Repos** na navegação à esquerda. 
   
1. Selecione o nome do repositório na parte superior da página e selecione **DSProject1** a partir do dropdown.
   
1. No **DSProject1 encontra-se** a página vazia, selecione **Import**. 
   
   ![Selecione Importar](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. No diálogo de **reparação De Importação de Git,** selecione **Git** como **tipo Fonte**, e introduza o URL para o seu repositório **TeamTemplate** sob **o URL clone**. O URL é *https:\//\<\<nome do\<servidor>/ nome\<da organização>/ nome da equipa>/_git/ nome repositório de modelo *de equipa>. Por exemplo: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Selecione **Importar**. O conteúdo do repositório do modelo de equipa é importado para o seu repositório de projeto. 
   
   ![Repositório de modelo de equipa de importação](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Se precisar de personalizar o conteúdo do seu repositório de projeto para atender às necessidades específicas do seu projeto, pode adicionar, excluir ou modificar ficheiros e pastas de repositório. Pode trabalhar diretamente no Azure Repos, ou clonar o repositório para a sua máquina local ou DSVM, fazer alterações e comprometer e empurrar as suas atualizações para o repositório de projetopartilhado. Siga as instruções em [Personalizar o conteúdo dos repositórios da equipa](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para descrições detalhadas das outras funções e tarefas definidas pelo Processo de Ciência de Dados da Equipa:

- [Tarefas do Gestor de Grupos para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de Team Lead para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas individuais do Contribuinte para uma equipa de ciência de dados](project-ic-tasks.md)

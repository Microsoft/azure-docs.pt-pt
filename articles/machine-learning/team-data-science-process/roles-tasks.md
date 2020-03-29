---
title: Funções e tarefas do Processo de Ciência de Dados da Equipa
description: Um esboço dos componentes-chave, funções de pessoal e tarefas associadas a um grupo de ciência de dados.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720015"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Funções e tarefas do Processo de Ciência de Dados da Equipa

O Team Data Science Process (TDSP) é um quadro desenvolvido pela Microsoft que fornece uma metodologia estruturada para construir soluções de análise preditivas e aplicações inteligentes. Este artigo descreve as principais funções de pessoal e tarefas associadas para uma equipa de ciência de dados que normaliza este processo.

Este artigo introdutório liga-se a tutoriais sobre como criar o ambiente TDSP. Os tutoriais fornecem orientações detalhadas para a utilização de Projetos Azure DevOps, repositórios Azure Repos e Azure Boards.  O objetivo motivador é passar do conceito através da modelação e para a implantação.

Os tutoriais usam O Azure DevOps porque é assim que se implementa o TDSP na Microsoft. A Azure DevOps facilita a colaboração integrando a segurança baseada em papéis, gestão e rastreio de itens de trabalho, e hospedagem de códigos, partilha e controlo de fontes. Os tutoriais também usam uma [Máquina Virtual](https://aka.ms/dsvm) de Ciência de Dados Azure (DSVM) como o ambiente de trabalho de análise, que tem várias ferramentas populares de ciência de dados pré-configuradas e integradas com software microsoft e serviços Azure. 

Pode utilizar os tutoriais para implementar o TDSP utilizando outros códigos de alojamento, planeamento ágil e ferramentas e ambientes de desenvolvimento, mas algumas funcionalidades podem não estar disponíveis.

## <a name="structure-of-data-science-groups-and-teams"></a>Estrutura de grupos e equipas de ciência de dados

As funções de ciência de dados nas empresas são muitas vezes organizadas na seguinte hierarquia:

- Grupo de ciência de dados
  - Equipa de ciência de dados dentro do grupo

Nessa estrutura, há lideranças de grupo e lideranças de equipa. Tipicamente, um projeto de ciência de dados é feito por uma equipa de ciência de dados. As equipas de ciência de dados têm pistas de projeto para tarefas de gestão e governação de projetos, e cientistas e engenheiros de dados individuais para executar as partes da ciência dos dados e engenharia de dados do projeto. A configuração e governação do projeto inicial é feita pelo grupo, equipa ou projeto lidera.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definição e tarefas para as quatro funções TDSP
Com o pressuposto de que a unidade de ciência de dados é constituída por equipas dentro de um grupo, existem quatro funções distintas para o pessoal da TDSP:

1. **Gestor de Grupos**: Gere toda a unidade de ciência de dados numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada uma das quais está a trabalhar em vários projetos de ciência de dados em verticais de negócios distintos. Um Gestor de Grupo pode delegar as suas tarefas num substituto, mas as tarefas associadas ao papel não mudam.
   
2. **Team Lead**: Gere uma equipa na unidade de ciência de dados de uma empresa. Uma equipa é composta por vários cientistas de dados. Para uma pequena unidade de ciência de dados, o Gestor de Grupo e o Team Lead podem ser a mesma pessoa.
   
3. **Project Lead**: Gere as atividades diárias de cientistas de dados individuais num projeto específico de ciência de dados.
   
4. **Project Individual Contributors**: Data Scientists, Business Analysts, Data Engineers, Architects, e outros que executam um projeto de ciência de dados.

> [!NOTE]
> Dependendo da estrutura e dimensão de uma empresa, uma única pessoa pode desempenhar mais do que um papel, ou mais do que uma pessoa pode preencher um papel.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Tarefas a completar pelas quatro funções

O diagrama seguinte mostra as tarefas de alto nível para cada função do Processo de Ciência de Dados da Equipa. Este esquema e o seguinte esboço mais detalhado de tarefas para cada função TDSP pode ajudá-lo a escolher o tutorial de que precisa com base nas suas responsabilidades.

![Funções e tarefas visão geral](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Tarefas do Gestor de Grupos

O Gestor de Grupo ou um administrador designado do sistema TDSP completa as seguintes tarefas para adotar o TDSP:

- Cria uma **organização** Azure DevOps e um projeto de grupo dentro da organização. 
- Cria um **repositório** de modelos de projeto no projeto do grupo Azure DevOps, e semente-o a partir do repositório de modelos de projeto desenvolvido pela equipa da Microsoft TDSP. O repositório do modelo do projeto Microsoft TDSP fornece:
  - Uma **estrutura de diretório padronizada**, incluindo diretórios para dados, código e documentos.
  - Um conjunto de modelos de **documentos padronizados** para orientar um processo eficiente de ciência de dados.
- Cria um **repositório de utilidades**, e semente-o a partir do repositório de utilidade desenvolvido pela equipa da Microsoft TDSP. O repositório de utilitários TDSP da Microsoft fornece um conjunto de utilitários úteis para tornar o trabalho de um cientista de dados mais eficiente. O repositório de utilitários da Microsoft inclui utilitários para exploração interativa de dados, análise, relatório e modelação e reporte de base.
- Estabelece a **política** de controlo de segurança para a conta da organização.

Para obter instruções detalhadas, consulte [as tarefas do Gestor de Grupo para uma equipa](group-manager-tasks.md)de ciência de dados .

## <a name="team-lead-tasks"></a>Tarefas de Team Lead

O Team Lead ou um administrador de projeto designado completa as seguintes tarefas para adotar o TDSP:

- Cria um **projeto** de equipa na organização Azure DevOps do grupo.
- Cria o **repositório** de modelos de projeto no projeto, e semente-o a partir do repositório de modelo seletiva do grupo criado pelo Gestor de Grupo ou delegado.
- Cria o **repositório**de utilidade da equipa, semente-o do repositório de utilidade seletiva do grupo, e adiciona utilitários específicos da equipa ao repositório.
- Opcionalmente cria armazenamento de [ficheiros Azure](https://azure.microsoft.com/services/storage/files/) para armazenar ativos de dados úteis para a equipa. Outros membros da equipa podem montar esta loja de ficheiros em nuvem partilhada nos seus desktops de análise.
- Opcionalmente monta o armazenamento de ficheiros Azure no **DSVM** da equipa e adiciona-lhe os ativos da equipa.
- Configura o controlo de **segurança** adicionando membros da equipa e configurando as suas permissões.

Para obter instruções detalhadas, consulte [as tarefas team lead para uma equipa](team-lead-tasks.md)de ciência de dados .


## <a name="project-lead-tasks"></a>Tarefas de Project Lead

O Project Lead completa as seguintes tarefas para adotar o TDSP:

- Cria um **repositório** de projeto no projeto da equipa, e semente-o a partir do repositório do modelo de projeto.
- Cria opcionalmente armazenamento de **ficheiros Azure** para armazenar os ativos de dados do projeto.
- Opcionalmente monta o armazenamento de ficheiros Azure no **DSVM** e adiciona-lhe os ativos de dados do projeto.
- Configura **o controlo** de segurança adicionando membros do projeto e configurando as suas permissões.

Para obter instruções detalhadas, consulte [as tarefas do Project Lead para uma equipa](project-lead-tasks.md)de ciência de dados.

## <a name="project-individual-contributor-tasks"></a>Tarefas de Colaborador Individual do Projeto

O Project Individual Contributor, geralmente um Cientista de Dados, realiza as seguintes tarefas utilizando o TDSP:

- Clones o **repositório** do projeto criado pelo chumbo do projeto.
- Opcionalmente monta a equipa partilhada e projeta armazenamento **de ficheiros Azure** na sua Máquina Virtual de **Ciência de Dados** (DSVM).
- Executa o projeto.

Para obter instruções detalhadas para o embarque num projeto, consulte [as tarefas do Project Individual Contributor para uma equipa](project-ic-tasks.md)de ciência de dados.

## <a name="data-science-project-execution-workflow"></a>Fluxo de trabalho de execução de projetos de ciência de dados

Seguindo os tutoriais relevantes, os cientistas de dados, os líderes de projetoe os líderes de equipa podem criar itens de trabalho para acompanhar todas as tarefas e fases para o projeto do início ao fim. A utilização do Azure Repos promove a colaboração entre os cientistas de dados e garante que os artefactos gerados durante a execução do projeto são controlados e partilhados por todos os membros do projeto. A Azure DevOps permite-lhe ligar os seus itens de trabalho da Azure Boards com os seus balcões de repositório Azure Repos e rastrear facilmente o que foi feito para um item de trabalho.

O seguinte valor descreve o fluxo de trabalho da TDSP para a execução do projeto:

![Fluxo de trabalho típico do projeto de ciência de dados](./media/roles-tasks/overview-project-execute.png)

As etapas de fluxo de trabalho podem ser agrunadas em três atividades:

- Projeto Leads conduz em plano de sprint
- Cientistas de Dados `git` desenvolvem artefactos em ramos para abordar itens de trabalho
- Project Leads ou outros membros da equipa fazem revisões de código e fundem filiais de trabalho para o ramo principal

Para obter instruções detalhadas sobre o fluxo de trabalho de execução do projeto, consulte [o desenvolvimento ágil de projetos de ciência de dados.](agile-development.md)

## <a name="tdsp-project-template-repository"></a>Repositório de modelo de projeto TDSP

Utilize o [repositório](https://github.com/Azure/Azure-TDSP-ProjectTemplate) de modelos de projeto da equipa da Microsoft TDSP para apoiar a execução e colaboração eficientes do projeto. O repositório dá-lhe uma estrutura de diretório padronizada e modelos de documentoque pode usar para os seus próprios projetos De TDSP.

## <a name="next-steps"></a>Passos seguintes

Explore descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados da Equipa:

- [Tarefas do Gestor de Grupos para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de Team Lead para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas do Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)
- [Tarefas do Project Individual Contributor para uma equipa de ciência de dados](project-ic-tasks.md)

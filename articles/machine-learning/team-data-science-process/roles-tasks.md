---
title: Funções e tarefas do Processo de Ciência de Dados de Equipa
description: Um esboço dos componentes-chave, funções de pessoal e tarefas associadas para um grupo de ciência de dados.
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
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082754"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Funções e tarefas do Processo de Ciência de Dados de Equipa

O Team Data Science Process (TDSP) é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para construir eficientemente soluções de análise preditiva e aplicações inteligentes. Este artigo descreve as funções-chave do pessoal e as tarefas associadas para uma equipa de ciência de dados que normaliza este processo.

Este artigo introdutório liga-se a tutoriais sobre como configurar o ambiente TDSP. Os tutoriais fornecem orientações detalhadas para a utilização de projetos Azure DevOps, repositórios Azure Repos e Azure Boards.  O objetivo motivador é passar do conceito através da modelação e da implantação.

Os tutoriais usam Azure DevOps porque é assim que se implementa o TDSP na Microsoft. O Azure DevOps facilita a colaboração integrando a segurança baseada em funções, a gestão e rastreio de artigos de trabalho e o alojamento de códigos, partilha e controlo de fontes. Os tutoriais também usam uma [Máquina Virtual Azure Data Science](https://aka.ms/dsvm) (DSVM) como o ambiente de trabalho de análise, que tem várias ferramentas populares de ciência de dados pré-configuradas e integradas com software microsoft e serviços Azure. 

Você pode usar os tutoriais para implementar TDSP usando outros códigos de hospedagem, planeamento ágil e ferramentas de desenvolvimento e ambientes, mas algumas funcionalidades podem não estar disponíveis.

## <a name="structure-of-data-science-groups-and-teams"></a>Estrutura de grupos e equipas de ciência de dados

As funções de ciência de dados nas empresas são muitas vezes organizadas na seguinte hierarquia:

- Grupo de ciência de dados
  - Equipa/s de ciência de dados dentro do grupo

Nesta estrutura, há líderes de grupo e equipas líderes. Normalmente, um projeto de ciência de dados é feito por uma equipa de ciência de dados. As equipas de ciência de dados têm projetos líderes para tarefas de gestão e governação de projetos, e cientistas e engenheiros de dados individuais para executar as partes de ciência de dados e engenharia de dados do projeto. A configuração e governação do projeto inicial é feita pelo grupo, equipa ou projeto leads.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definição e tarefas para as quatro funções TDSP
Com o pressuposto de que a unidade de ciência de dados é constituída por equipas dentro de um grupo, existem quatro funções distintas para o pessoal da TDSP:

1. **Gestor do Grupo**: Gere toda a unidade de ciência de dados numa empresa. Uma unidade de ciência de dados pode ter várias equipas, cada uma das quais está a trabalhar em vários projetos de ciência de dados em verticais de negócios distintas. Um Gestor de Grupo pode delegar as suas tarefas num substituto, mas as tarefas associadas ao papel não mudam.
   
2. **Team Lead**: Gere uma equipa na unidade de ciência de dados de uma empresa. Uma equipa é composta por vários cientistas de dados. Para uma pequena unidade de ciência de dados, o Gerente de Grupo e o Team Lead podem ser a mesma pessoa.
   
3. **Project Lead**: Gere as atividades diárias de cientistas de dados individuais num projeto específico de ciência de dados.
   
4. **Project Individual Contributors**: Data Scientists, Business Analysts, Data Engineers, Architects, and others who executem um projeto de ciência de dados.

> [!NOTE]
> Dependendo da estrutura e tamanho de uma empresa, uma única pessoa pode desempenhar mais do que uma função, ou mais de uma pessoa pode preencher um papel.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Tarefas a completar pelas quatro funções

O diagrama seguinte mostra as tarefas de alto nível para cada papel de Processo de Ciência de Dados de Equipa. Este esquema e o seguinte esboço mais detalhado de tarefas para cada função TDSP podem ajudá-lo a escolher o tutorial de que precisa com base nas suas responsabilidades.

![Visão geral de papéis e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Tarefas do Gestor de Grupo

O Gestor do Grupo ou um administrador de sistema TDSP designado completa as seguintes tarefas para adotar o TDSP:

- Cria uma **organização** Azure DevOps e um projeto de grupo dentro da organização. 
- Cria um **repositório de modelo de projeto** no projeto do grupo Azure DevOps, e sementes-lo do repositório de modelo de projeto desenvolvido pela equipa da Microsoft TDSP. O repositório do modelo de projeto Microsoft TDSP fornece:
  - Uma **estrutura de diretório padronizada**, incluindo diretórios para dados, código e documentos.
  - Um conjunto de modelos de **documento padronizados** para orientar um processo eficiente de ciência de dados.
- Cria um **repositório de utilidades**, e sementes-lo a partir do repositório de utilidade desenvolvido pela equipa da Microsoft TDSP. O repositório de utilitários TDSP da Microsoft fornece um conjunto de utilitários úteis para tornar o trabalho de um cientista de dados mais eficiente. O repositório de utilitários da Microsoft inclui utilitários para exploração interativa de dados, análise, reporte e modelação e reporte de base.
- Estabelece a **política de controlo de segurança** para a conta da organização.

Para obter instruções detalhadas, consulte [as tarefas do Gestor do Grupo para uma equipa de ciência de dados.](group-manager-tasks.md)

## <a name="team-lead-tasks"></a>Tarefas de liderança da equipa

O Team Lead ou um administrador de projeto designado completa as seguintes tarefas para adotar o TDSP:

- Cria um **projeto** de equipa na organização Azure DevOps do grupo.
- Cria o **repositório do modelo** de projeto no projeto, e sementes-lo a partir do repositório de modelo de projeto de grupo criado pelo Gestor de Grupo ou delegado.
- Cria o **repositório de utilidade da equipa,** semente-o do repositório de utilidade do grupo e adiciona serviços de utilidade específicas da equipa ao repositório.
- Opcionalmente cria [o armazenamento de ficheiros Azure](https://azure.microsoft.com/services/storage/files/) para armazenar ativos de dados úteis para a equipa. Outros membros da equipa podem montar esta loja de ficheiros em nuvem partilhada nos seus ambientes de trabalho de análise.
- Opcionalmente monta o armazenamento de ficheiros Azure no **DSVM** da equipa e adiciona-lhe os ativos de dados da equipa.
- Cria **o controlo de segurança** adicionando membros da equipa e configurando as suas permissões.

Para obter instruções detalhadas, consulte [as tarefas de Team Lead para uma equipa de ciência de dados.](team-lead-tasks.md)


## <a name="project-lead-tasks"></a>Tarefas de chumbo do projeto

O Project Lead completa as seguintes tarefas para adotar o TDSP:

- Cria um **repositório de projeto** no projeto de equipa, e semente-o a partir do repositório do modelo do projeto.
- Opcionalmente cria **armazenamento de ficheiros Azure** para armazenar os ativos de dados do projeto.
- Opcionalmente monta o armazenamento de ficheiros Azure para o **DSVM** e adiciona-lhe os ativos de dados do projeto.
- Configura **o controlo de segurança** adicionando membros do projeto e configurando as suas permissões.

Para obter instruções detalhadas, consulte [as tarefas do Project Lead para uma equipa de ciência de dados.](project-lead-tasks.md)

## <a name="project-individual-contributor-tasks"></a>Projeto tarefas de contribuinte individual

O Contribuinte Individual do Projeto, geralmente um Cientista de Dados, realiza as seguintes tarefas utilizando o TDSP:

- Clones o **repositório do projeto** criado pelo líder do projeto.
- Opcionalmente monta a equipa partilhada e projeta o **armazenamento de ficheiros Azure** na sua **Máquina Virtual de Ciência de Dados** (DSVM).
- Executa o projeto.

Para obter instruções detalhadas para o embarque num projeto, consulte [as tarefas do Project Individual Contributor para uma equipa de ciência de dados.](project-ic-tasks.md)

## <a name="data-science-project-execution-workflow"></a>Fluxo de trabalho de execução de projetos de ciência de dados

Seguindo os tutoriais relevantes, os cientistas de dados, os líderes de projetos e os líderes de equipa podem criar itens de trabalho para acompanhar todas as tarefas e etapas para o projeto do início ao fim. A utilização do Azure Repos promove a colaboração entre os cientistas de dados e garante que os artefactos gerados durante a execução do projeto são controlados e partilhados por todos os membros do projeto. A Azure DevOps permite-lhe ligar os seus itens de trabalho da Azure Boards com os seus ramos de repositório Azure Repos e rastrear facilmente o que foi feito para um item de trabalho.

O seguinte número descreve o fluxo de trabalho da TDSP para a execução do projeto:

![Fluxo de trabalho típico de projeto de ciência de dados](./media/roles-tasks/overview-project-execute.png)

As etapas de fluxo de trabalho podem ser agrupadas em três atividades:

- Project Leads conduzem planeamento de sprint
- Cientistas de dados desenvolvem artefactos em `git` ramos para abordar itens de trabalho
- Project Leads ou outros membros da equipa fazem revisões de código e fundem ramos de trabalho para o ramo primário

Para obter instruções detalhadas sobre o fluxo de trabalho de execução do projeto, consulte [o desenvolvimento ágil de projetos de ciência de dados.](agile-development.md)

## <a name="tdsp-project-template-repository"></a>Repositório de modelo de projeto TDSP

Utilize o [repositório](https://github.com/Azure/Azure-TDSP-ProjectTemplate) de modelos de projeto da equipa da Microsoft TDSP para apoiar a execução e colaboração eficientes do projeto. O repositório dá-lhe uma estrutura de diretório padronizada e modelos de documentos que pode usar para os seus próprios projetos TDSP.

## <a name="next-steps"></a>Passos seguintes

Explore descrições mais detalhadas das funções e tarefas definidas pelo Processo de Ciência de Dados de Equipa:

- [Tarefas do Gestor de Grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de liderança da equipa para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de Project Lead para uma equipa de ciência de dados](project-lead-tasks.md)
- [Projetar tarefas individuais de contribuinte para uma equipa de ciência de dados](project-ic-tasks.md)

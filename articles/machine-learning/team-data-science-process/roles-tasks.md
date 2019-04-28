---
title: Tarefas e funções do Team Data Science Process
description: Uma descrição do componentes-chave, funções de equipe e tarefas associadas para um projeto de equipe de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 05fc742bba535ea3968e60cd0f40c80b812c09fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043073"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Team Data Science Process

O processo de ciência de dados de equipa é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para criar soluções de Análise Preditiva e aplicações inteligentes com eficiência. Este artigo descreve as funções de pessoal-chave e das respetivas tarefas associadas que são processadas por uma ciência de dados de equipa padronização sobre esse processo.

Este links de introdução para tutoriais que fornecem instruções sobre como configurar o ambiente do TDSP para o grupo de ciência de dados inteiro, as equipas de ciência de dados e projetos.
Ele fornece orientações detalhadas através do Azure DevOps nos tutoriais. DevOps do Azure fornece uma plataforma de alojamento de código e a ferramenta de planejamento agile para gerir as tarefas de equipe, controlar o acesso e gerenciar os repositórios.

Pode usar essas informações para implementar o TDSP na sua própria ferramenta de planejamento ágil e alojamento de código.

## <a name="structures-of-data-science-groups-and-teams"></a>Estruturas de equipes e grupos de ciência de dados

As funções de ciência de dados nas empresas, muitas vezes, podem ser organizadas na hierarquia do seguinte:

1. ***Grupo de ciência de dados/s***

2. ***Dados ciência equipe/s no grupo/s***

Numa estrutura, há oportunidades potenciais do grupo e a equipa. Normalmente, um projeto de ciência de dados é feito por uma equipa de ciência de dados, que pode ser composta de oportunidades potenciais de projeto (para tarefas de gestão e administração de projeto) e os cientistas de dados ou engenheiros (contribuidores individuais técnicos e de pessoal) que executará a ciência de dados e partes do projeto de engenharia de dados. Antes da execução, o programa de configuração e a governação é feito pelo grupo, a equipe ou o oportunidades potenciais do projeto.

## <a name="definition-of-four-tdsp-roles"></a>Definição de quatro funções TDSP
Com a suposição acima, existem quatro funções distintas para o pessoal da Equipe:

1. ***Gerente de grupo***. Gerente de grupo é o gerente da unidade de ciência de dados inteiro numa empresa. Uma unidade de ciência de dados pode ter várias equipes, cada um deles está trabalhando em vários projetos de ciência de dados em mercados verticais de negócios distintos. Gerente de grupo pode delegar suas tarefas, para um substituto, mas não altere as tarefas associadas à função.

2. ***Líder da Equipe***. Um líder de equipe está a gerir uma equipa na unidade de ciência de dados de uma empresa. Uma equipe é composta por vários cientistas de dados. Para a unidade de ciência de dados com apenas um pequeno número de cientistas de dados, o Gestor de grupo e o líder de equipe podem ser a mesma pessoa.

3. ***Líder de projeto***. Um líder de projeto gerencia as atividades diárias de cientistas de dados individuais num projeto de ciência de dados específicos.

4. ***Contribuidor Individual do projeto***. Cientista de dados, analista de negócios, engenheiro de dados, Arquiteto, etc. Um colaborador individual do projeto é executado um projeto de ciência de dados.


> [!NOTE]
> Consoante a estrutura numa empresa, uma única pessoa pode jogar mais de uma função ou poderá existir mais do que uma pessoa, trabalhando numa função. Com freqüência, isso pode ser o caso em que as pequenas empresas ou as empresas com um pequeno número de funcionários da organização de ciência de dados.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tarefas a serem concluídas por quatro pessoal

A imagem seguinte ilustra as tarefas de nível superior para o pessoal por função numa adotar e implementar o processo de ciência de dados de equipa como conceptualized pela Microsoft.

![Descrição geral de funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

Esse esquema e a estrutura seguinte, mais detalhada de tarefas que estão atribuídos a cada função no TDSP devem ajudar a escolher o tutorial adequado com base em suas responsabilidades na organização.

> [!NOTE]
> As instruções seguintes mostram as etapas de como configurar um ambiente de TDSP e concluir a outras tarefas de ciência de dados no Azure DevOps. Especificamos como realizar estas tarefas com o Azure DevOps, uma vez que é o que estamos a utilizar para implementar o TDSP na Microsoft. DevOps do Azure facilita a colaboração ao integrar a gestão de itens de trabalho que controlar as tarefas e um serviço de alojamento de código usado para compartilhar utilitários, versões de organizar e fornecer a segurança baseada em funções. É possível escolher outras plataformas, se preferir, para implementar as tarefas descritas pelo TDSP. Mas, dependendo da sua plataforma, algumas funcionalidades tirar partidas do Azure DevOps podem não estar disponíveis.
>
>Instruções aqui também utilizam o [Máquina Virtual de ciência de dados (DSVM)](https://aka.ms/dsvm) sobre o Azure como na nuvem a área de trabalho de análise com várias ferramentas de ciência de dados populares pré-configuradas e integradas em diversos softwares da Microsoft e o Azure serviços. Pode usar a DSVM ou qualquer outro ambiente de desenvolvimento para implementar o TDSP.


## <a name="group-manager-tasks"></a>Tarefas do Gestor de grupo

As seguintes tarefas são concluídas, o Gestor de grupo (ou um administrador de sistema do TDSP designado) adotar o TDSP:

- Criar uma **conta de grupo** num código de plataforma (como o GitHub, Git, Azure DevOps ou outras pessoas) de alojamento
- Criar uma **repositório de modelos de projeto** na conta de grupo e seed-lo a partir do repositório de modelos de projeto desenvolvido pela equipe do Microsoft TDSP. O repositório de modelos de projeto TDSP da Microsoft
    - Fornece uma **padronizados a estrutura de diretórios** incluindo diretórios de dados, código e documentos,
    - Fornece um conjunto de **padronizadas modelos de documentos** para orientar o processo de ciência de dados eficiente.
- Criar uma **repositório de utilitário**e efetuar o seeding do repositório de utilitário desenvolvido pela equipe do Microsoft TDSP. Fornece repositório de utilitário TDSP da Microsoft
    - um conjunto de utilitários útil para tornar o trabalho de um cientista de dados mais eficientes, incluindo utilitários para exploração interativa de dados, análises e relatórios e para a linha de base de modelagem e relatórios.
- Configurar o **política de controlo de segurança** destes dois repositórios na sua conta de grupo.

Para obter instruções passo a passo detalhadas, consulte [tarefas de Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md).


## <a name="team-lead-tasks"></a>Tarefas de líder de equipe

As seguintes tarefas são concluídas, o líder da Equipe (ou um administrador de projeto designado) adotar o TDSP:

- Se o DevOps do Azure está selecionado para ser a plataforma de alojamento de código para controle de versão e colaboração, crie uma **projeto** nos serviços de DevOps do Azure do grupo. Caso contrário, esta tarefa pode ser ignorada.
- Criar a **repositório de modelos de projeto** sob o projeto e seed-lo a partir do repositório de modelos de projeto de Grupo configurar ao seu Gestor de grupo ou o delegado do gestor.
- Criar a **repositório de utilitário de equipe**e adicione os utilitários de específicas para a equipe para o repositório.
- (Opcional) Crie **[file storage do Azure](https://azure.microsoft.com/services/storage/files/)** a ser utilizado para armazenar elementos de dados que podem ser útil para toda a equipe. Outros membros da Equipe podem montar este arquivo de ficheiros de cloud partilhado em suas áreas de trabalho de análise.
- (Opcional) Montar o armazenamento de ficheiros do Azure para o **máquina de Virtual de ciência de dados** (DSVM) da Equipe líder e adicionar recursos de dados no mesmo.
- Configurar o **controlo de segurança** ao adicionar os membros da Equipe e configurar seus privilégios.

Para obter instruções passo a passo detalhadas, consulte [líder da equipe de tarefas para uma equipa de ciência de dados](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Tarefas de líder do projeto

As seguintes tarefas são concluídas, o líder de projeto para adotar o TDSP:

- Criar uma **repositório projeto** sob o projeto e efetuar o seeding do repositório de modelos de projeto.
- (Opcional) Crie **armazenamento de ficheiros do Azure** a ser utilizado para armazenar elementos de dados do projeto.
- (Opcional) Montar o armazenamento de ficheiros do Azure para o **máquina de Virtual de ciência de dados** (DSVM) do projeto levar e adicionar recursos de dados do projeto no mesmo.
- Configurar o **controlo de segurança** ao adicionar membros do projeto e configurar seus privilégios.

Para obter instruções passo a passo detalhadas, consulte [tarefas líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Tarefas de Contribuidor Individual do projeto

As seguintes tarefas são concluídas por um colaborador Individual para conduzir o projeto de ciência de dados usando o TDSP projeto (normalmente um cientista de dados):

- Clone o **repositório projeto** configure, o líder de projeto.
- (Opcional) Montar partilhada **armazenamento de ficheiros do Azure** da Equipe e do projeto no respetivo **máquina de Virtual de ciência de dados** (DSVM).
- Execute o projeto.


Para obter instruções detalhadas passo a passo para a integração num projeto, consulte [contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md).


## <a name="data-science-project-execution"></a>Execução do projeto de ciência de dados

Ao seguir o conjunto relevante de instruções, cientistas de dados, líder de projeto e líderes de equipe podem criar itens de trabalho para controlar todas as tarefas e fases que precisa de um projeto desde o início para o respetivo fim. Com o git também promove a colaboração entre cientistas de dados e garante que os artefatos gerados durante a execução do projeto estão versão controlada e partilhados por todos os membros do projeto.

As instruções fornecidas para a execução do projeto foram desenvolvidas com base no pressuposto de que ambos os itens de trabalho e projeto git repositórios estão no Azure DevOps. Utilização do Azure DevOps para ambos permite-lhe ligar os seus itens de trabalho com os ramos do Git dos seus repositórios do projeto. Dessa forma, pode controlar facilmente o que foi feito para um item de trabalho.

A figura a seguir descreve o fluxo de trabalho para utilizar o TDSP de execução do projeto.

![Execução do projeto de ciência de dados típico](./media/roles-tasks/overview-project-execute.png)

O fluxo de trabalho inclui passos que podem ser agrupados em três atividades:

- (Líder de projeto) de planejamento de Sprint
- Desenvolvimento de artefactos no ramos do git para abordar os itens de trabalho (cientista de dados)
- Revisão de código e a mesclagem de ramificações com ramificações principais (líder de projeto ou outros membros da Equipe)

Para obter instruções detalhadas passo a passo no fluxo de trabalho de execução de projeto, consulte [execução de projetos de ciência de dados](project-execution.md).

## <a name="project-structure"></a>Estrutura do projeto

Utilize esta opção [repositório de modelos de projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para suportar a execução do projeto eficiente e colaboração. Este repositório dá-lhe um modelos de estrutura e o documento de diretório padronizados que pode utilizar para o seu próprio projeto TDSP.

## <a name="next-steps"></a>Passos Seguintes

Explore as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Contribuidores individuais do projeto para uma equipa de ciência de dados](project-ic-tasks.md)

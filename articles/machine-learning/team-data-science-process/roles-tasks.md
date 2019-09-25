---
title: Tarefas e funções do Team Data Science Process
description: Um contorno dos principais componentes, funções de pessoal e tarefas associadas para um grupo de ciência de dados.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/12/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bbc066f4b01a01a589849ad524b58a9b5d9dfae1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260641"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tarefas e funções do Team Data Science Process

O TDSP (processo de ciência de dados de equipe) é uma estrutura desenvolvida pela Microsoft que fornece uma metodologia estruturada para criar soluções de análise preditiva e aplicativos inteligentes com eficiência. Este artigo descreve as principais funções de pessoal e tarefas associadas para uma equipe de ciência de dados padronizando esse processo.

Este artigo introdutório fornece links para tutoriais sobre como configurar o ambiente TDSP para um grupo de ciência de dados inteiro, equipes de ciência de dados e projetos TDSP. Os tutoriais fornecem diretrizes detalhadas para o uso de repositórios Azure DevOps Projects, Azure Repos e Azure Boards ferramentas de planejamento Agile para controlar o acesso, hospedar e compartilhar o código e gerenciar tarefas da equipe.

Os tutoriais usam o DevOps do Azure porque é como implementar o TDSP na Microsoft. O Azure DevOps facilita a colaboração integrando a segurança baseada em funções, o gerenciamento e o controle de itens de trabalho, e a hospedagem de código, o compartilhamento e o recurso de origem. Os tutoriais também usam uma [máquina virtual de ciência de dados](https://aka.ms/dsvm) do Azure (DSVM) como a área de trabalho de análise, que tem várias ferramentas de ciência de dados populares pré-configuradas e integradas ao software da Microsoft e aos serviços do Azure. 

Você pode usar os tutoriais para implementar o TDSP usando outros ambientes e ferramentas de Hospedagem de código, planejamento ágil e desenvolvimento, mas alguns recursos podem não estar disponíveis.

## <a name="structure-of-data-science-groups-and-teams"></a>Estrutura de grupos e equipes de ciência de dados

As funções de ciência de dados em empresas geralmente são organizadas na seguinte hierarquia:

- Grupo de ciência de dados
  - Equipe/s de ciência de dados dentro do grupo

Nessa estrutura, há leads de grupo e líderes de equipe. Normalmente, um projeto de ciência de dados é feito por uma equipe de ciência de dados. As equipes de ciência de dados têm leads de projetos para tarefas de gerenciamento e governança de projetos e cientistas e engenheiros de dados individuais para executar as partes de ciência de dados e de engenharia de dados do projeto. A configuração e a governança do projeto inicial são feitas pelos leads Group, Team ou Project.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definição e tarefas para as quatro funções TDSP
Com a suposição de que a unidade de ciência de dados consiste em equipes dentro de um grupo, há quatro funções distintas para a equipe de TDSP:

1. **Gerente do grupo**: Gerencia toda a unidade de ciência de dados em uma empresa. Uma unidade de ciência de dados pode ter várias equipes, cada um deles está trabalhando em vários projetos de ciência de dados em mercados verticais de negócios distintos. Gerente de grupo pode delegar suas tarefas, para um substituto, mas não altere as tarefas associadas à função.
   
2. **Líder de equipe**: Gerencia uma equipe na unidade de ciência de dados de uma empresa. Uma equipe é composta por vários cientistas de dados. Para uma unidade de ciência de dados pequena, o gerente de grupo e o líder de equipe podem ser a mesma pessoa.
   
3. **Líder do projeto**: Gerencia as atividades diárias de cientistas de dados individuais em um projeto de ciência de dados específico.
   
4. **Colaboradores individuais do projeto**: Cientistas de dados, analistas de negócios, engenheiros de dados, arquitetos e outros que executam um projeto de ciência de dados.

> [!NOTE]
> Dependendo da estrutura e do tamanho de uma empresa, uma única pessoa pode executar mais de uma função ou mais de uma pessoa pode preencher uma função.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Tarefas a serem concluídas pelas quatro funções

O diagrama a seguir mostra as tarefas de nível superior para cada função de processo de ciência de dados de equipe. Este esquema e a seguinte estrutura de tópicos mais detalhada de tarefas para cada função de TDSP podem ajudá-lo a escolher o tutorial de que precisa com base em suas responsabilidades.

![Descrição geral de funções e tarefas](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Tarefas do Gestor de grupo

O gerente do grupo ou um administrador do sistema TDSP designado conclui as seguintes tarefas para adotar o TDSP:

- Cria uma **organização** do Azure DevOps e um projeto de grupo dentro da organização. 
- Cria um **repositório de modelos de projeto** no projeto do grupo DevOps do Azure e propaga-o do repositório de modelos do projeto desenvolvido pela equipe do Microsoft TDSP. O repositório de modelos de projeto do Microsoft TDSP fornece:
  - Uma **estrutura de diretório padronizada**, incluindo diretórios de dados, código e documentos.
  - Um conjunto de **modelos de documento padronizados** para guiar um processo eficiente de ciência de dados.
- Cria um **repositório do utilitário**e propaga-o do repositório do utilitário desenvolvido pela equipe do Microsoft TDSP. O repositório do utilitário TDSP da Microsoft fornece um conjunto de utilitários úteis para tornar o trabalho de um cientista de dados mais eficiente. O repositório do utilitário da Microsoft inclui utilitários para exploração de dados interativa, análise, relatórios e modelagem de linha de base e relatórios.
- Configura a **política de controle de segurança** para a conta da organização.

Para obter instruções detalhadas, consulte [tarefas do gerente de grupo para uma equipe de ciência de dados](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Tarefas de líder de equipe

O líder de equipe ou um administrador de projeto designado conclui as seguintes tarefas para adotar o TDSP:

- Cria um **projeto** de equipe na organização de DevOps do Azure do grupo.
- Cria o **repositório de modelos de projeto** no projeto e propaga-o do repositório de modelos de projeto de grupo configurado pelo gerente do grupo ou pelo delegado.
- Cria o **repositório do utilitário de equipe**, propaga-o do repositório do utilitário de grupo e adiciona utilitários específicos da equipe ao repositório.
- Opcionalmente, cria o [armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) para armazenar ativos de dados úteis para a equipe. Outros membros da Equipe podem montar este arquivo de ficheiros de cloud partilhado em suas áreas de trabalho de análise.
- Opcionalmente, monta o armazenamento de arquivos do Azure no **DSVM** da equipe e adiciona ativos de dados da equipe a ele.
- Configura o **controle de segurança** adicionando membros da equipe e configurando suas permissões.

Para obter instruções detalhadas, consulte [tarefas do líder de equipe para uma equipe de ciência de dados](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Tarefas de líder do projeto

O líder do projeto conclui as seguintes tarefas para adotar o TDSP:

- Cria um **repositório de projeto** no projeto de equipe e propaga-o do repositório de modelos de projeto.
- Opcionalmente, cria o **armazenamento de arquivos do Azure** para armazenar os ativos de dados do projeto.
- Opcionalmente, monta o armazenamento de arquivos do Azure para o **DSVM** e adiciona ativos de dados de projeto a ele.
- Configura o **controle de segurança** adicionando membros do projeto e configurando suas permissões.

Para obter instruções detalhadas, consulte [tarefas do líder de projeto para uma equipe de ciência de dados](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Tarefas de Contribuidor Individual do projeto

O colaborador individual do projeto, geralmente um cientista de dados, realiza as seguintes tarefas usando o TDSP:

- Clona o **repositório do projeto** configurado pelo líder do projeto.
- Opcionalmente, monta a equipe compartilhada e o **armazenamento de arquivos do Azure** no seu **máquina virtual de ciência de dados** (DSVM).
- Executa o projeto.

Para obter instruções detalhadas para integração em um projeto, consulte [tarefas de colaborador individuais do projeto para uma equipe de ciência de dados](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Fluxo de trabalho de execução do projeto de ciência de dados

Seguindo os tutoriais relevantes, os cientistas de dados, os leads de projeto e os líderes de equipe podem criar itens de trabalho para acompanhar todas as tarefas e estágios do projeto desde o início até o fim. O uso de Azure Repos promove a colaboração entre os cientistas de dados e garante que os artefatos gerados durante a execução do projeto sejam controlados por versão e compartilhados por todos os membros do projeto. O Azure DevOps permite que você vincule seus Azure Boards itens de trabalho com suas ramificações de repositório de Azure Repos e acompanhe facilmente o que foi feito para um item de trabalho.

A figura a seguir descreve o fluxo de trabalho do TDSP para a execução do projeto:

![Fluxo de trabalho típico do projeto de ciência de dados](./media/roles-tasks/overview-project-execute.png)

As etapas do fluxo de trabalho podem ser agrupadas em três atividades:

- Leads do projeto conduzem o planejamento do Sprint
- Cientistas de dados desenvolvem artefatos em `git` branches para endereçar itens de trabalho
- Clientes potenciais do projeto ou outros membros da equipe fazem revisões de código e mesclam branches de trabalho para o Branch mestre

Para obter instruções detalhadas sobre o fluxo de trabalho de execução do projeto, consulte [desenvolvimento ágil de projetos de ciência de dados](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repositório de modelos de projeto TDSP

Use o repositório de modelos de [projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) da equipe do Microsoft TDSP para dar suporte à colaboração e à execução eficiente do projeto. O repositório fornece uma estrutura de diretórios padronizada e modelos de documentos que você pode usar para seus próprios projetos TDSP.

## <a name="next-steps"></a>Passos seguintes

Explore as descrições mais detalhadas das funções e tarefas definidas pelo processo de ciência de dados de equipa:

- [Tarefas do Gestor de grupo para uma equipa de ciência de dados](group-manager-tasks.md)
- [Tarefas de líder de equipe para uma equipa de ciência de dados](team-lead-tasks.md)
- [Tarefas de líder de projeto para uma equipa de ciência de dados](project-lead-tasks.md)
- [Projetar tarefas de colaborador individuais para uma equipe de ciência de dados](project-ic-tasks.md)

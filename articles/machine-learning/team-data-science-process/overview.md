---
title: O que é o Processo de Ciência de Dados da Equipa?
description: Fornece uma metodologia de ciência de dados para fornecer soluções de análise preditiva e aplicações inteligentes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088074"
---
# <a name="what-is-the-team-data-science-process"></a>O que é o Processo de Ciência de Dados da Equipa?

O Team Data Science Process (TDSP) é uma metodologia ágil e iterativa de ciência de dados para fornecer soluções de análise preditiva e aplicações inteligentes de forma eficiente. A TDSP ajuda a melhorar a colaboração e a aprendizagem da equipa, sugerindo como os papéis da equipa funcionam melhor em conjunto. A TDSP inclui as melhores práticas e estruturas da Microsoft e de outros líderes do setor para ajudar a implementar com sucesso iniciativas de ciência de dados. O objetivo é ajudar as empresas a atingir todo o potencial dos benefícios dos respetivos programas de análise.

Este artigo fornece uma visão geral da TDSP e dos seus principais componentes. Fornecemos uma descrição genérica do processo aqui que pode ser implementado com diferentes tipos de ferramentas. Uma descrição mais detalhada das tarefas e funções do projeto envolvidas no ciclo de vida do processo é fornecida em tópicos ligados adicionais. São também fornecidas orientações sobre como implementar o TDSP utilizando um conjunto específico de ferramentas e infraestruturas da Microsoft que utilizamos para implementar o TDSP nas nossas equipas.

## <a name="key-components-of-the-tdsp"></a>Componentes-chave do TDSP

A TDSP compreende os seguintes componentes-chave:

- Uma definição de ciclo de **vida da ciência dos dados**
- Uma **estrutura de projeto padronizada**
- **Infraestruturas e recursos** para projetos de ciência de dados
- **Ferramentas e utilidades** para a execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida da ciência dos dados

O Team Data Science Process (TDSP) fornece um ciclo de vida para estruturar o desenvolvimento dos seus projetos de ciência de dados. O ciclo de vida descreve todos os passos que os projetos de sucesso seguem.

Se estiver a utilizar outro ciclo de vida de ciência de dados, como [CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD ou](https://wikipedia.org/wiki/Data_mining#Process) o próprio processo personalizado da sua organização, ainda pode utilizar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. A um nível elevado, estas diferentes metodologias têm muito em comum. 

Este ciclo de vida foi concebido para projetos de ciência de dados que enviam como parte de aplicações inteligentes. Estas aplicações implementam modelos de machine learning ou inteligência artificial para análiseprevetiva. Projetos exploratórios de ciência de dados ou projetos de análise improvisados também podem beneficiar da utilização deste processo. Mas, nestes casos, alguns dos passos descritos podem não ser necessários.    

O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

* **Compreensão de Negócios**
* **Aquisição e Compreensão de Dados**
* **Modelação**
* **Implantação**
* **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do Processo de Ciência de Dados da **Equipa.** 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Os objetivos, tarefas e artefactos de documentação para cada fase do ciclo de vida em TDSP são descritos no tópico do ciclo de vida do Processo de Ciência de Dados da [Equipa.](lifecycle.md) Estas tarefas e artefactos estão associados a funções de projeto:

- Arquiteto de solução
- Gestor do projeto
- Cientista de dados
- Líder de projeto 

O diagrama seguinte proporciona uma visão da grelha das tarefas (em azul) e artefactos (em verde) associados a cada fase do ciclo de vida (no eixo horizontal) para estas funções (no eixo vertical). 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Estrutura de projeto padronizada

Ter todos os projetos compartilham uma estrutura de diretório e usam modelos para documentos de projeto, facilitando aos membros da equipa encontrar informações sobre os seus projetos. Todos os códigos e documentos são armazenados num sistema de controlo de versão (VCS) como Git, TFS ou Subversão para permitir a colaboração da equipa. O rastreio de tarefas e funcionalidades num sistema ágil de rastreio de projetos como Jira, Rally e Azure DevOps permite um rastreio mais próximo do código para funcionalidades individuais. Este rastreio também permite que as equipas obtenham melhores estimativas de custos. A TDSP recomenda a criação de um repositório separado para cada projeto no VCS para versão, segurança de informação e colaboração. A estrutura padronizada para todos os projetos ajuda a construir conhecimentos institucionais em toda a organização.

Fornecemos modelos para a estrutura da pasta e documentos necessários em locais padrão. Esta estrutura de pasta organiza os ficheiros que contêm código para a exploração de dados e extração de recursos, e que registam iterações de modelos. Estes modelos facilitam aos membros da equipa compreender o trabalho feito por outros e adicionar novos membros às equipas. É fácil visualizar e atualizar os modelos de documentos em formato de marcação. Utilize modelos para fornecer listas de verificação com questões-chave para cada projeto para garantir que o problema está bem definido e que os resultados cumprem a qualidade esperada. Os exemplos incluem:

- uma carta de projeto para documentar o problema de negócios e âmbito do projeto
- relatórios de dados para documentar a estrutura e as estatísticas dos dados brutos
- relatórios de modelo para documentar as características derivadas
- métricas de desempenho do modelo, tais como curvas ROC ou MSE


[![TDSP-directórios](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A estrutura do diretório pode ser clonada a partir do [GitHub.](https://github.com/Azure/Azure-TDSP-ProjectTemplate)

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestruturas e recursos para projetos de ciência de dados  

A TDSP fornece recomendações para a gestão de infraestruturas de análise partilhada e armazenamento tais como:

- sistemas de ficheiros em nuvem para armazenar conjuntos de dados 
- bases de dados
- clusters de big data (Hadoop ou Spark) 
- serviço de aprendizagem automática 

A infraestrutura de análise e armazenamento, onde os conjuntos de dados crus e processados são armazenados, pode estar na nuvem ou no local. Esta infraestrutura permite uma análise reprodutível. Evita igualmente duplicações, o que pode levar a incoerências e custos de infraestruturas desnecessários. São fornecidas ferramentas para fornecer os recursos partilhados, rastreá-los e permitir que cada membro da equipa se conectem a esses recursos de forma segura. É também uma boa prática ter membros do projeto a criar um ambiente de computação consistente. Diferentes membros da equipa podem então replicar e validar experiências.

Aqui está um exemplo de uma equipa que trabalha em vários projetos e partilha vários componentes de infraestruturas de análise em nuvem.

[![Infraestrutura TDSP](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilidades para a execução do projeto

Introduzir processos na maioria das organizações é um desafio. Ferramentas fornecidas para implementar o processo de ciência de dados e ciclo de vida ajudam a reduzir as barreiras e aumentar a consistência da sua adoção. A TDSP fornece um conjunto inicial de ferramentas e scripts para iniciar a adoção de TDSP dentro de uma equipa. Também ajuda a automatizar algumas das tarefas comuns no ciclo de vida da ciência dos dados, como a exploração de dados e modelação de base. Existe uma estrutura bem definida para que os indivíduos contribuam com ferramentas e utilidades partilhadas para o repositório de código partilhado da sua equipa. Estes recursos podem então ser alavancados por outros projetos dentro da equipa ou da organização. A TDSP também planeia permitir as contribuições de ferramentas e utilidades para toda a comunidade. Os utilitários TDSP podem ser clonados a partir do [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Passos seguintes

Processo de Ciência de [Dados da Equipa: Funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Delineia as principais funções de pessoal e as suas tarefas associadas para uma equipa de ciência de dados que normaliza este processo. 

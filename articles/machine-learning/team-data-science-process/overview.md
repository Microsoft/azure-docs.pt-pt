---
title: O que é o Processo de Ciência de Dados de Equipa?
description: Fornece uma metodologia de ciência de dados para fornecer soluções de análise preditiva e aplicações inteligentes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94738004"
---
# <a name="what-is-the-team-data-science-process"></a>O que é o Processo de Ciência de Dados de Equipa?

O Processo de Ciência de Dados de Equipa (TDSP) é uma metodologia ágil e iterativa da ciência dos dados para fornecer soluções de análise preditivas e aplicações inteligentes de forma eficiente. A TDSP ajuda a melhorar a colaboração e a aprendizagem da equipa, sugerindo como os papéis de equipa funcionam melhor em conjunto. A TDSP inclui as melhores práticas e estruturas da Microsoft e de outros líderes do setor para ajudar a implementar com sucesso as iniciativas de ciência de dados. O objetivo é ajudar as empresas a atingir todo o potencial dos benefícios dos respetivos programas de análise.

Este artigo fornece uma visão geral da TDSP e dos seus principais componentes. Fornecemos uma descrição genérica do processo aqui que pode ser implementado com diferentes tipos de ferramentas. Uma descrição mais detalhada das tarefas e funções do projeto envolvidas no ciclo de vida do processo é fornecida em tópicos ligados adicionais. Também são fornecidas orientações sobre como implementar o TDSP utilizando um conjunto específico de ferramentas e infraestruturas da Microsoft que utilizamos para implementar o TDSP nas nossas equipas.

## <a name="key-components-of-the-tdsp"></a>Componentes-chave do TDSP

A TDSP tem os seguintes componentes-chave:

- Uma definição **de ciclo de vida da ciência dos dados**
- Uma **estrutura de projeto padronizada**
- **Infraestruturas e recursos recomendados** para projetos de ciência de dados
- **Ferramentas e utilidades recomendadas** para a execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida da ciência dos dados

O Processo de Ciência de Dados de Equipa (TDSP) fornece um ciclo de vida para estruturar o desenvolvimento dos seus projetos de ciência de dados. O ciclo de vida descreve todos os passos que os projetos de sucesso seguem.

Se estiver a utilizar outro ciclo de vida da ciência dos dados, como [o CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [o KDD ou](https://wikipedia.org/wiki/Data_mining#Process) o processo personalizado da sua própria organização, ainda pode utilizar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. A um nível elevado, estas metodologias diferentes têm muito em comum. 

Este ciclo de vida foi concebido para projetos de ciência de dados que enviam como parte de aplicações inteligentes. Estas aplicações implementam modelos de aprendizagem automática ou inteligência artificial para análise preditiva. Projetos exploratórios de ciência de dados ou projetos de análise improvisada também podem beneficiar da utilização deste processo. Mas, nesses casos, alguns dos passos descritos podem não ser necessários.    

O ciclo de vida descreve as principais fases que os projetos normalmente executam, muitas vezes iterativamente:

* **Compreensão empresarial**
* **Aquisição e Compreensão de Dados**
* **Modelação**
* **Implementação**

Aqui está uma representação visual do ciclo de vida do **Processo de Ciência de Dados da Equipa.** 

![O diagrama mostra o ciclo de vida da ciência dos dados, incluindo a compreensão do negócio, a aquisição/compreensão de dados, a modelação e a implantação.](./media/overview/tdsp-lifecycle2.png) 

Os objetivos, tarefas e artefactos de documentação para cada fase do ciclo de vida em TDSP são descritos no tópico do ciclo de vida do [Processo de Ciência de Dados da Equipa.](lifecycle.md) Estas tarefas e artefactos estão associados a funções de projeto:

- Arquiteto de solução
- Gestor do projeto
- Engenheiro de dados
- Cientista de dados
- Desenvolvedor de aplicações
- Líder de projeto 

O diagrama seguinte proporciona uma visão da grelha das tarefas (em azul) e artefactos (em verde) associados a cada fase do ciclo de vida (no eixo horizontal) para estas funções (no eixo vertical). 

[![Funções e tarefas TDSP](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Estrutura de projeto padronizada

Ter todos os projetos compartilhando uma estrutura de diretório e modelos de uso para documentos de projeto torna fácil para os membros da equipa encontrar informações sobre seus projetos. Todos os códigos e documentos são armazenados num sistema de controlo de versão (VCS) como Git, TFS ou Subversão para permitir a colaboração da equipa. O rastreio de tarefas e funcionalidades num sistema ágil de rastreio de projetos como Jira, Rally e Azure DevOps permite um rastreio mais próximo do código para funcionalidades individuais. Este rastreio também permite que as equipas obtenham melhores estimativas de custos. A TDSP recomenda a criação de um repositório separado para cada projeto no VCS para a versão, segurança da informação e colaboração. A estrutura padronizada para todos os projetos ajuda a construir conhecimento institucional em toda a organização.

Fornecemos modelos para a estrutura da pasta e documentos necessários em locais padrão. Esta estrutura de pasta organiza os ficheiros que contêm código para a exploração de dados e extração de recursos, e que registam iterações de modelos. Estes modelos facilitam aos membros da equipa a compreensão do trabalho feito pelos outros e a adição de novos membros às equipas. É fácil visualizar e atualizar modelos de documentos em formato de marcação. Use modelos para fornecer listas de verificação com questões-chave para cada projeto para garantir que o problema está bem definido e que os resultados vão de encontro à qualidade esperada. Os exemplos incluem:

- uma carta de projeto para documentar o problema de negócios e âmbito do projeto
- relatórios de dados para documentar a estrutura e as estatísticas dos dados brutos
- relatórios de modelo para documentar as características derivadas
- Métricas de desempenho do modelo, tais como curvas ROC ou MSE


[![TDSP-directórios](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A estrutura do diretório pode ser clonada do [GitHub.](https://github.com/Azure/Azure-TDSP-ProjectTemplate)

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestruturas e recursos para projetos de ciência de dados  

A TDSP fornece recomendações para a gestão de infraestruturas de análise e armazenamento partilhadas, tais como:

- sistemas de ficheiros em nuvem para armazenar conjuntos de dados 
- Bases de dados
- clusters de big data (SQL ou Spark) 
- serviço de aprendizagem automática 

A infraestrutura de análise e armazenamento, onde os conjuntos de dados brutos e processados são armazenados, pode estar na nuvem ou no local. Esta infraestrutura permite uma análise reprodutível. Evita igualmente duplicações, o que pode conduzir a inconsistências e custos desnecessários de infraestruturas. As ferramentas são fornecidas para fornecer os recursos partilhados, rastreá-los e permitir que cada membro da equipa se conecte a esses recursos de forma segura. É também uma boa prática fazer com que os membros do projeto criem um ambiente computacional consistente. Diferentes membros da equipa podem então replicar e validar experiências.

Aqui está um exemplo de uma equipa que trabalha em vários projetos e partilha de vários componentes de infraestruturas de análise de nuvem.

[![TDSP-infraestrutura](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilidades para a execução do projeto

Introduzir processos na maioria das organizações é um desafio. As ferramentas fornecidas para implementar o processo de ciência dos dados e o ciclo de vida ajudam a reduzir as barreiras e aumentar a consistência da sua adoção. A TDSP fornece um conjunto inicial de ferramentas e scripts para iniciar a adoção de TDSP dentro de uma equipa. Também ajuda a automatizar algumas das tarefas comuns no ciclo de vida da ciência dos dados, como a exploração de dados e modelação de base. Existe uma estrutura bem definida, fornecida para que os indivíduos contribuam com ferramentas e utilidades partilhadas no repositório de código partilhado da sua equipa. Estes recursos podem então ser alavancados por outros projetos dentro da equipa ou da organização.  A Microsoft fornece uma extensa ferramenta dentro do [Azure Machine Learning](../index.yml) suportando tanto a open-source (Python, R, ONNX e quadros comuns de deep learning) como também a própria ferramenta da Microsoft (AutoML).


## <a name="next-steps"></a>Passos seguintes

[Processo de Ciência de Dados da Equipa: Funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Delineia as funções-chave do pessoal e as suas tarefas associadas para uma equipa de ciência de dados que normaliza este processo. 

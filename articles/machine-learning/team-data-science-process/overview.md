---
title: O que é o Processo de Ciência de Dados de Equipa?
description: Fornece uma metodologia de ciência de dados para fornecer soluções de Análise Preditiva e aplicações inteligentes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 10/20/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b4e8c78d7402c254c91c3e100814e1f3eafc41b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867206"
---
# <a name="what-is-the-team-data-science-process"></a>O que é o Processo de Ciência de Dados de Equipa?

O Team Data Science Process (TDSP) é uma metodologia de ciência de dados ágil e iterativa para fornecer soluções de Análise Preditiva e aplicações inteligentes com eficiência. O TDSP ajuda a melhorar a colaboração e a aprendizagem em equipa. Contém uma seleção das melhores práticas e estruturas da Microsoft e de outras empresas do setor para facilitar a implementação de iniciativas de ciência de dados. O objetivo é ajudar as empresas a atingir todo o potencial dos benefícios dos respetivos programas de análise.

Este artigo fornece uma visão geral do TDSP e seus principais componentes. Fornecemos uma descrição genérica do processo aqui que pode ser implementada com uma variedade de ferramentas. Uma descrição mais detalhada das tarefas de projetos e funções envolvidas no ciclo de vida do processo é fornecida no adicionais tópicos ligados. Também são disponibilizadas orientações sobre como implementar o TDSP usando um conjunto específico de ferramentas da Microsoft e a infraestrutura que usamos para implementar o TDSP em nossas equipas.

## <a name="key-components-of-the-tdsp"></a>Os principais componentes do TDSP

TDSP inclui os seguintes componentes principais:

- R **ciclo de vida de ciência de dados** definição
- A **padronizados a estrutura do projeto**
- **Recursos de infraestrutura e** para projetos de ciência de dados
- **Ferramentas e utilitários** para execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida de ciência de dados

O Team Data Science Process (TDSP) fornece um ciclo de vida para estruturar o desenvolvimento dos seus projetos de ciência de dados. O ciclo de vida descreve os passos, do início ao fim, que projetos seguem, normalmente, quando forem executados.

Se estiver a utilizar outro lifecycle de ciência de dados, tal como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou seu processo de personalizados da organização, pode continuar a utilizar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. Num alto nível, essas metodologias diferentes têm muito em comum. 

Este ciclo de vida foi projetado para projetos de ciência de dados que são fornecidos como parte de aplicações inteligentes. Esses aplicativos implementarem modelos de aprendizagem ou inteligência artificial de automática para Análise Preditiva. Projetos de ciência de dados exploratório ou os projetos de análise ad hoc, também podem beneficiar da utilização deste processo. Mas em tais casos alguns dos passos descritos podem não ser necessária.    

O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

* **Compreensão empresarial**
* **Aquisição de dados e compreensão**
* **Modelagem**
* **Implementação**
* **Aceitação do cliente**

Aqui está uma representação visual do **ciclo de vida do processo de ciência de dados de equipa**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

As metas, tarefas e artefactos de documentação para cada fase do ciclo de vida no TDSP descritos a [ciclo de vida do processo de ciência de dados de equipa](lifecycle.md) tópico. Estas tarefas e os artefactos estão associados a funções do projeto:

- Arquiteto de soluções
- Gerente de projeto
- Cientista de dados
- Líder de projeto 

O diagrama seguinte fornece uma exibição de grade das tarefas (a azul) e artefatos (em verde) associados a cada fase do ciclo de vida (no eixo horizontal) para estas funções (no eixo vertical). 

[![TDSP-funções-e-tarefas](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Estrutura do projeto padronizado

Ter todos os projetos Compartilhar uma estrutura de diretório e usar modelos para documentos de projeto torna mais fácil para os membros da Equipe obter informações sobre seus projetos. Todos os código e os documentos são armazenados num sistema de controle de versão (VCS) como o Git, TFS ou o Subversion para permitir a colaboração em equipe. Controlo de tarefas e recursos num sistema, como no Jira, Rally e DevOps do Azure de acompanhamento de projetos agile permite que mais perto de controlo de código para recursos individuais. Esse controle também permite às equipes obter estimativas de custo melhor. TDSP recomenda a criação de um repositório separado para cada projeto no VCS para controle de versão, a segurança de informações e colaboração. A estrutura padronizada para todos os projetos ajuda a criar o conhecimento institucional toda a organização.

Fornecemos modelos para a estrutura de pastas e os documentos necessários em locais padrão. Esta estrutura de pastas organiza os ficheiros que contêm o código de exploração de dados e a extração de recurso e que registe iterações do modelo. Estes modelos tornam mais fácil para os membros da Equipe para compreender o trabalho realizado por terceiros e para adicionar novos membros de equipes. É fácil ver e atualizar os modelos de documento no formato de markdown. Utilize modelos para fornecer a listas de verificação principais perguntas para cada projeto assegurar que o problema é bem definido e que os resultados finais cumprem a qualidade esperada. Os exemplos incluem:

- um estatuto de projeto para documentar o problema de negócios e o escopo do projeto
- relatórios de dados para a estrutura do documento e as estatísticas dos dados não processados
- relatórios de modelo para documentar os recursos derivados
- métricas de desempenho de modelo como curvas ROC ou MSE


[![Diretórios de TDSP](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A estrutura de diretório pode ser clonada partir [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestrutura e os recursos para projetos de ciência de dados  

TDSP fornece recomendações para a gestão de análise partilhado e a infraestrutura de armazenamento, tais como:

- sistemas de ficheiros de cloud para armazenar conjuntos de dados 
- bases de dados
- clusters de grandes volumes de dados (Hadoop ou Spark) 
- serviço de Machine learning 

É possível a infraestrutura de armazenamento e análise na cloud ou no local. Isso é onde os conjuntos de dados não processados e processados são armazenados. Essa infra-estrutura permite que a análise reproduzível. Ele também evita a duplicação, que pode levar a inconsistências e custos de infraestrutura desnecessários. São fornecidas ferramentas para aprovisionar os recursos partilhados, controlá-las e permitir que cada membro da Equipe ligar a esses recursos em segurança. Também é recomendável ter membros de projeto, criar um ambiente de computação consistentes. Os membros da Equipe diferentes, em seguida, podem replicar e validar experimentações.

Eis um exemplo de uma equipe de trabalhar em vários projetos e partilha vários componentes de infraestrutura de análise na cloud.

[![Infraestrutura de TDSP](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilitários para execução do projeto

Introduzir processos na maioria das organizações é um desafio. Ferramentas fornecidas para implementar a ajuda de processo e o ciclo de vida da ciência de dados mais baixo, as barreiras para e aumentar a consistência da adoção. TDSP fornece um conjunto inicial de ferramentas e scripts para promover a adoção do TDSP dentro de uma equipe. Ele também ajuda a automatizar algumas das tarefas comuns do ciclo de vida de ciência de dados, como a exploração e modelação de linha de base dados. Existe uma estrutura bem definida fornecida para indivíduos contribuir partilhadas ferramentas e utilitários para o repositório de código compartilhado de sua equipe. Estes recursos, em seguida, podem ser aproveitados por outros projetos dentro da Equipe ou a organização. Também planeja TDSP ativar as contribuições de ferramentas e utilitários para a Comunidade inteira. Os utilitários do TDSP podem clonar a partir [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Passos Seguintes

[Processo de ciência de dados de equipa: Funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) descreve as funções de pessoal-chave e das respetivas tarefas associadas para uma equipa de ciência de dados que padroniza sobre esse processo. 

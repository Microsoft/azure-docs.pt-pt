---
title: Conceitos de sessões de depurar (pré-visualização)
titleSuffix: Azure Cognitive Search
description: As sessões de depuração, acedidas através do portal Azure, proporcionam um ambiente semelhante ao IDE onde pode identificar e corrigir erros, validar alterações e impulsionar alterações nas competências no pipeline de enriquecimento de IA. As sessões de Debug estão em pré-visualização.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92057625"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Sessões de Debug em Pesquisa Cognitiva Azure

A Debug Sessions é uma editora visual que trabalha com uma habilidade existente no portal Azure. Numa sessão de depuração pode identificar e resolver erros, validar alterações e impulsionar alterações a uma habilidade de produção no pipeline de enriquecimento de IA.

> [!Important]
> As sessões de depuração são uma funcionalidade de pré-visualização fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Usando sessões de depuro

Quando inicia uma sessão, o serviço cria uma cópia do skillset, indexador e índice onde um único documento é usado para testar o skillset. As alterações efetuadas no âmbito da sessão são guardadas na sessão. As alterações introduzidas no âmbito da sessão de depurar não afetarão o skillset de produção a menos que as alterações estejam comprometidas com ela. A commissão de alterações substituirá o skillset de produção.

Durante uma sessão de depuração pode editar um skillset, inspecionar e validar cada nó na árvore de enriquecimento no contexto de um documento específico. Uma vez resolvidas as questões do gasoduto de enriquecimento, as alterações podem ser guardadas na sessão e comprometidas com o skillset na produção. 

Se o gasoduto de enriquecimento não tiver erros, pode ser utilizada uma sessão de depuração para enriquecer incrementalmente um documento, testar e validar cada alteração antes de cometer as alterações.

## <a name="creating-a-debug-session"></a>Criar uma sessão de depurar

Para iniciar uma sessão de depuração deve ter um gasoduto de enriquecimento de IA existente, incluindo; uma fonte de dados, um skillset, um indexante e um índice. Para configurar uma sessão de depuração, você precisa nomear a sessão, e fornecer uma conta de armazenamento de propósito geral que será usada para cache as execuções de habilidades durante a execução do indexante. Também terá de selecionar o indexante que estará em execução. O indexante tem referências armazenadas na fonte de dados, skillset e índice. A sessão de depuração será padrão para o primeiro documento na fonte de dados ou pode especificar um documento na fonte de dados para passar.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Criar uma sessão de depurar":::

## <a name="debug-session-features"></a>Características da sessão de Debug

A sessão de depuramento começa executando o skillset no documento selecionado. A sessão de depurar registará metadados adicionais associados a cada operação no skillset. Os metadados criados pelas execuções de habilidades do pipeline, informam o seguinte conjunto de funcionalidades que são depois usadas para ajudar a identificar e corrigir problemas com o skillset.

## <a name="ai-enrichments"></a>Enriquecimentos de IA

À medida que as habilidades executam uma árvore de enriquecimentos, representando o documento, cresce. A utilização de uma árvore para visualizar as saídas de habilidades ou enriquecimentos proporciona um olhar abrangente sobre todos os enriquecimentos realizados. Você pode olhar em todo o documento e inspecionar cada nó da árvore de enriquecimento. Esta perspetiva facilita a formação de objetos. Este formato também fornece pistas visuais para o tipo, caminho e conteúdo de cada nó na árvore.

## <a name="skill-graph"></a>Gráfico de habilidade

A **visão Skill Graph** proporciona uma representação visuais hierárquica do skillset. O gráfico é uma representação de cima para baixo da ordem em que as habilidades são executadas. As competências que dependem da produção de outras competências serão mostradas mais baixas no gráfico. Competências ao mesmo nível da hierarquia podem ser executadas em paralelo. 

A seleção de uma habilidade no gráfico irá destacar as habilidades ligadas a ele, os nós que criam as suas entradas e os nós que aceitam as suas saídas. Cada nó de habilidade apresenta o seu tipo, erros ou avisos, e a execução conta. O **Gráfico de Habilidade** é onde irá selecionar qual a habilidade para depurar ou melhorar. Quando selecionar uma habilidade, os seus detalhes serão apresentados no painel de detalhes de habilidades à direita do gráfico.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Gráfico de habilidade":::

## <a name="skill-details"></a>Detalhes de habilidades

O painel de detalhes de habilidades exibe um conjunto de áreas para trabalhar com uma habilidade específica, quando essa habilidade é realçada no **Gráfico de Habilidades.** Pode rever e editar os detalhes das definições da habilidade. A definição JSON da habilidade é fornecida. Os detalhes da execução da habilidade e os erros e avisos também são exibidos. O separador **Skill Settings** & **Skill JSON Editor** permitem edições diretas à habilidade. Abre [`</>`](#expression-evaluator) uma janela para visualizar e editar as expressões das entradas e saídas de competências.

Os controlos de entrada aninhados na janela de definições de habilidades podem ser usados para construir formas complexas para projeções, mapeamentos de campo de saída para um campo de tipo complexo, ou uma entrada para uma habilidade. Quando utilizado com o **avaliador de expressão,** as entradas aninhadas proporcionam um teste fácil e validam o construtor de expressão.

## <a name="skill-execution-history"></a>Histórico de execução de habilidades

Uma habilidade pode executar várias vezes numa habilidade para um único documento. Por exemplo, a habilidade OCR executará uma vez por cada imagem extraída de cada documento. Nos detalhes de habilidades, o separador **Execuções** exibe o histórico de execução da habilidade, proporcionando um olhar mais profundo sobre cada invocação da habilidade. 

O histórico de execução permite rastrear um enriquecimento específico de volta à habilidade que o gerou. Clicar numa entrada de habilidade navega para a habilidade que gerou essa entrada. Isto permite identificar a causa principal de um problema que pode manifestar-se numa habilidade a jusante. 

Quando um problema potencial é identificado, o histórico de execução apresenta ligações às habilidades que geraram as entradas específicas, fornecendo um traço de stack como recurso. Clicando na habilidade associada a uma entrada, navega para a habilidade para corrigir quaisquer erros ou continuar a rastrear a questão específica.

Ao construir uma habilidade personalizada ou depurar um erro com uma habilidade personalizada, existe a opção de gerar um pedido de invocação de habilidades no histórico de execução.

## <a name="enriched-data-structure"></a>Estrutura de Dados Enriquecida

O painel **da Estrutura de Dados Enriquecido** mostra os enriquecimentos do documento através do skillset, detalhando o contexto para cada enriquecimento e a habilidade de origem. O **avaliador de expressão** também pode ser usado para visualizar o conteúdo para cada enriquecimento.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Estrutura de Dados Enriquecida":::

## <a name="expression-evaluator"></a>Avaliador de expressão

**O avaliador de expressão** dá uma espreitadela rápida ao valor de qualquer caminho. Permite editar o percurso e testar os resultados antes de atualizar qualquer uma das entradas ou contexto para uma habilidade ou projeção.

## <a name="errorswarnings"></a>Erros/Avisos

Esta janela exibe todos os erros e avisos que o skillset produz à medida que é executado contra o documento na sessão de depurar.

## <a name="limitations"></a>Limitações

Os sesis de depurg funcionam com todas as fontes de dados geralmente disponíveis amd a maioria das fontes de dados de pré-visualização. A API mongoDB (pré-visualização) e a Cassandra API (pré-visualização) da Cosmos DB não são atualmente suportadas.

## <a name="next-steps"></a>Passos seguintes

Agora que compreende os elementos das sessões de Debug, experimente o tutorial para uma experiência prática.

> [!div class="nextstepaction"]
> [Explore o tutorial das sessões de Debug](./cognitive-search-tutorial-debug-sessions.md)
---
title: Depurar uma habilidade de enriquecimento de IA (pré-visualização)
titleSuffix: Azure Cognitive Search
description: As sessões de depuração, acessadas através do portal Azure, proporcionam um ambiente semelhante ao IDE onde pode identificar e corrigir erros, validar alterações e impulsionar alterações nas competências no pipeline de enriquecimento de IA. As sessões de depuração estão em pré-visualização.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 04b221d772abf923d7aabfe767a6424b72ed8fb2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664068"
---
# <a name="debug-sessions"></a>Sessões de depuração

A Debug sessions é um editor visual que trabalha com uma habilidade existente no portal Azure. Dentro de uma sessão de depuração pode identificar e resolver erros, validar alterações e empurrar alterações para um conjunto de habilidades de produção no gasoduto de enriquecimento de IA.

> [!Important]
> O suporte para sessões de depuração para A Pesquisa Cognitiva Azure está disponível [mediante pedido](https://aka.ms/DebugSessions) como pré-visualização de acesso limitado. As funcionalidades de pré-visualização são fornecidas sem um acordo de nível de serviço, e não são recomendadas para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> Assim que lhe for concedido o acesso à pré-visualização, poderá aceder e utilizar sessões de Debug para o serviço utilizando o portal Azure.

## <a name="using-debug-sessions"></a>Usando sessões de depuração

Quando inicia uma sessão, o serviço cria uma cópia do skillset, indexador e índice onde um único documento é usado para testar a habilidade. As alterações efetuadas dentro da sessão são guardadas para a sessão. As alterações efetuadas na sessão de depuração não afetarão a capacidade de produção a menos que as alterações estejam comprometidas com o mesmo. Comprometer alterações substituirá a habilidade de produção.

Durante uma sessão de depuração pode editar uma habilidade, inspecionar e validar cada nó na árvore de enriquecimento no contexto de um documento específico. Uma vez resolvidas as questões do gasoduto de enriquecimento, as alterações podem ser guardadas na sessão e comprometidas com a competência na produção. 

Se o gasoduto de enriquecimento não tiver erros, uma sessão de depuração pode ser usada para enriquecer incrementalmente um documento, testar e validar cada alteração antes de cometer as alterações.

## <a name="creating-a-debug-session"></a>Criar uma sessão de depuração

Para iniciar uma sessão de depuração, deve ter um oleoduto de enriquecimento de IA existente, incluindo; uma fonte de dados, um skillset, um indexante, e um índice. Para configurar uma sessão de depuração, é necessário nomear a sessão e fornecer uma conta de armazenamento de uso geral que será usada para cache as execuções de habilidades durante a execução do indexador. Também terá de selecionar o indexador que estará em funcionamento. O indexante tem referências armazenadas à fonte de dados, skillset e índice. A sessão de depuração será predefinida para o primeiro documento na fonte de dados ou pode especificar um documento na fonte de dados para passar.

> [!div class="mx-imgBorder"]
> ![Criar uma sessão de depuração](media/cognitive-search-debug/debug-session-new.png)

## <a name="debug-session-features"></a>Funcionalidades da sessão de depuração

A sessão de depuração começa executando a habilidade no documento selecionado. A sessão de depuração registará metadados adicionais associados a cada operação no skillset. Os metadados criados pelas execuções de habilidades do oleoduto, informam o seguinte conjunto de funcionalidades que são então usadas para ajudar a identificar e corrigir problemas com o skillset.

## <a name="ai-enrichments"></a>Enriquecimentos de IA

À medida que as habilidades executam uma árvore de enriquecimentos, representando o documento, cresce. A utilização de uma árvore para visualizar as saídas de competências ou enriquecimentos proporciona um olhar abrangente sobre todos os enriquecimentos realizados. Você pode olhar através de todo o documento e inspecionar cada nó da árvore de enriquecimento. Esta perspetiva facilita a formação de objetos. Este formato também fornece pistas visuais para o tipo, caminho e conteúdo de cada nó na árvore.

## <a name="skill-graph"></a>Gráfico de habilidades

A visão **do Skill Graph** proporciona uma representação hierárquica e visual do skillset. O gráfico é uma representação de cima a baixo da ordem em que as habilidades são executadas. As competências que dependem da saída de outras competências serão mostradas mais baixas no gráfico. As competências ao mesmo nível da hierarquia podem ser executadas em paralelo. 

A seleção de uma habilidade no gráfico irá destacar as habilidades que lhe estão ligadas, os nós que criam as suas inputs e os nós que aceitam as suas saídas. Cada nó de habilidade exibe o seu tipo, erros ou avisos, e conta a execução. O **Skill Graph** é onde irá selecionar qual a habilidade para depurar ou melhorar. Quando selecionar uma habilidade, os seus detalhes serão apresentados no painel de detalhes de habilidade à direita do gráfico.

> [!div class="mx-imgBorder"]
> ![Gráfico de habilidades](media/cognitive-search-debug/skills-graph.png)

## <a name="skill-details"></a>Detalhes de habilidades

O painel de detalhes de habilidade sonante exibe um conjunto de áreas para trabalhar com uma habilidade específica, quando essa habilidade é realçada no **Skill Graph**. Pode rever e editar os detalhes das definições da habilidade. A definição JSON da habilidade é fornecida. Os detalhes da execução da habilidade e os erros e avisos também são apresentados. O separador **Definições de Habilidade&** **Skill JSON Editor** permitem edificações diretas para a habilidade. A [`</>`](#expression-evaluator) abre uma janela para visualizar e editar as expressões das inputs e saídas de competências.

Os controlos de entrada aninhados na janela de definições de habilidade podem ser usados para construir formas complexas para projeções, mapeamentos de campo de saída para um campo de tipo complexo, ou uma entrada para uma habilidade. Quando utilizadas com o **avaliador de expressão,** as inputs aninhadas proporcionam um teste fácil e validam o construtor de expressão.

## <a name="skill-execution-history"></a>Histórico de execução de habilidades

Uma habilidade pode executar várias vezes numa habilidade para um único documento. Por exemplo, a habilidade OCR executará uma vez por cada imagem extraída de cada documento. Nos detalhes de habilidade saem o separador **Execuções** mostra o histórico de execução da habilidade, proporcionando um olhar mais profundo sobre cada invocação da habilidade. 

O histórico de execução permite rastrear um enriquecimento específico de volta à habilidade que o gerou. Clicar numa entrada de habilidade navega para a habilidade que gerou essa entrada. Isto permite identificar a causa principal de um problema que pode manifestar-se numa habilidade a jusante. 

Quando um problema potencial é identificado, o histórico de execução mostra ligações às habilidades que geraram as inputs específicas, fornecendo um traço de pilha como característica. Clicando na habilidade associada a uma entrada, navega para a habilidade para corrigir quaisquer bugs ou continuar a rastrear o problema específico.

Ao construir uma habilidade personalizada ou depurar um erro com uma habilidade personalizada, existe a opção de gerar um pedido de invocação de habilidades no histórico de execução.

## <a name="enriched-data-structure"></a>Estrutura de Dados Enriquecidos

O painel de estrutura de **dados enriquecido** mostra os enriquecimentos do documento através da habilidade, detalhando o contexto para cada enriquecimento e a habilidade de origem. O **avaliador de expressão** também pode ser usado para visualizar o conteúdo para cada enriquecimento.

> [!div class="mx-imgBorder"]
> ![Estrutura de Dados Enriquecidos](media/cognitive-search-debug/enriched-data-structure-display.png)

## <a name="expression-evaluator"></a>Avaliador de expressão

**Avaliador** de expressão dá uma rápida olhada no valor de qualquer caminho. Permite editar o caminho e testar os resultados antes de atualizar qualquer uma das inputs ou contexto para uma habilidade ou projeção.

## <a name="errorswarnings"></a>Erros/Advertências

Esta janela exibe todos os erros e avisos que a habilidade produz à medida que é executada contra o documento na sessão de depuração.

## <a name="next-steps"></a>Passos seguintes

Agora que compreende os elementos das sessões de Debug, experimente o tutorial para uma experiência prática.

> [!div class="nextstepaction"]
> [Explore o tutorial de sessões de Debug](https://docs.microsoft.com/azure/search/cognitive-search-tutorial-debug-sessions)
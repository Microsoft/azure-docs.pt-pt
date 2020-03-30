---
title: Introdução ao enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: A extração de conteúdo, o processamento de linguagem natural (NLP) e o processamento de imagem são usados para criar conteúdo pesquisável em índices de Pesquisa Cognitiva Azure com habilidades cognitivas pré-definidas e algoritmos de IA personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283026"
---
# <a name="getting-started-with-ai-enrichment"></a>Começar com enriquecimento de IA

O enriquecimento de IA é uma capacidade da indexação de Pesquisa Cognitiva Azure usada para extrair texto de imagens, bolhas e outras fontes de dados não estruturadas. O enriquecimento e a extração tornam o seu conteúdo mais pesquisável numa [loja de índices](search-what-is-an-index.md) ou [conhecimentos.](knowledge-store-concept-intro.md) A extração e o enriquecimento são implementados utilizando *competências cognitivas ligadas* ao gasoduto de indexação. As competências cognitivas incorporadas no serviço enquadram-se nestas categorias: 

+ **As** competências de processamento de linguagem natural incluem reconhecimento de [entidades,](cognitive-search-skill-entity-recognition.md) [deteção de linguagem,](cognitive-search-skill-language-detection.md)extração de [frases-chave,](cognitive-search-skill-keyphrases.md)manipulação de texto, [deteção de sentimentos](cognitive-search-skill-sentiment.md)e [deteção de PII.](cognitive-search-skill-pii-detection.md) Com estas habilidades, o texto não estruturado é mapeado como campos pesquisáveis e filtrados num índice.

+ **As** habilidades de processamento de imagem incluem reconhecimento ótico de [caracteres (OCR)](cognitive-search-skill-ocr.md) e identificação de [características visuais](cognitive-search-skill-image-analysis.md), tais como deteção facial, interpretação de imagem, reconhecimento de imagem (pessoas famosas e marcos) ou atributos como orientação de imagem. Estas habilidades criam representações de texto de conteúdo de imagem, tornando-o pesquisável usando as capacidades de consulta da Pesquisa Cognitiva Azure.

![Diagrama de gasoduto de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "visão geral do gasoduto de enriquecimento")

As competências cognitivas em Pesquisa Cognitiva Azure baseiam-se em modelos de aprendizagem automática pré-treinados em APIs de Serviços Cognitivos: [Visão Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e [Análise de Texto.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 

O processamento de linguagem natural e imagem é aplicado durante a fase de ingestão de dados, com os resultados a tornarem-se parte da composição de um documento num índice pesquisável na Pesquisa Cognitiva Azure. Os dados são obtidos como um conjunto de dados Azure e depois empurrados através de um pipeline de indexação usando as [habilidades incorporadas](cognitive-search-predefined-skills.md) que você precisa. A arquitetura é extensível por isso, se as habilidades incorporadas não forem suficientes, você pode criar e anexar [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) para integrar o processamento personalizado. Exemplos podem ser um módulo de entidade personalizada ou um classificador de documento sintetizado que visa um domínio específico, como finanças, publicações científicas ou medicina.

## <a name="when-to-use-ai-enrichment"></a>Quando usar o enriquecimento de IA

Deve considerar a utilização de habilidades cognitivas incorporadas se o seu conteúdo bruto for texto não estruturado, conteúdo de imagem ou conteúdo que necessite de deteção e tradução linguística. Aplicar IA através das habilidades cognitivas incorporadas pode desbloquear este conteúdo, aumentando o seu valor e utilidade nas suas aplicações de pesquisa e ciência de dados. 

Além disso, pode considerar adicionar uma habilidade personalizada se tiver código de código de código aberto, de terceiros ou de primeira parte que gostaria de integrar no pipeline. Os modelos de classificação que identificam características salientes de vários tipos de documentos enquadram-se nesta categoria, mas qualquer pacote que acrescente valor ao seu conteúdo pode ser utilizado.

### <a name="more-about-built-in-skills"></a>Mais sobre habilidades incorporadas

Um conjunto de [habilidades](cognitive-search-defining-skillset.md) que é montado usando habilidades incorporadas é bem adequado para os seguintes cenários de aplicação:

+ Documentos digitalizados (JPEG) que pretende tornar pesquisáveis por texto completo. Pode anexar uma habilidade de reconhecimento ótico de caracteres (OCR) para identificar, extrair e ingerir texto a partir de ficheiros JPEG.

+ PDFs com imagem e texto combinados. O texto em PDFs pode ser extraído durante a indexação sem a utilização de passos de enriquecimento, mas a adição de imagem e processamento de linguagem natural pode muitas vezes produzir um resultado melhor do que uma indexação padrão fornece.

+ Conteúdo multilingual contra o qual pretende aplicar a deteção de linguagem e, possivelmente, a tradução de texto.

+ Documentos não estruturados ou semi-estruturados que contenham conteúdo que tenha significado ou contexto inerente soais escondidos no documento maior. 

  As bolhas, em particular, contêm frequentemente um grande corpo de conteúdo que é embalado num "campo" singled. Ao anexar habilidades de processamento de imagem e linguagem natural a um indexador, pode criar novas informações que estão existentes no conteúdo bruto, mas que não surgem como campos distintos. Algumas habilidades cognitivas prontas a usar que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as competências incorporadas também podem ser usadas através de operações de divisão de texto, fusão e forma.

### <a name="more-about-custom-skills"></a>Mais sobre habilidades personalizadas

As competências personalizadas podem suportar cenários mais complexos, como o reconhecimento de formulários ou a deteção personalizada de entidades utilizando um modelo que fornece e envolve na [interface web de habilidades personalizadas.](cognitive-search-custom-skill-interface.md) Vários exemplos de competências personalizadas incluem O Reconhecimento de [Formulários,](/azure/cognitive-services/form-recognizer/overview)integração da API de Pesquisa de [Entidades Bing,](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)e reconhecimento personalizado de [entidades.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)


## <a name="steps-in-an-enrichment-pipeline"></a>Passos em um oleoduto de enriquecimento

Um gasoduto de enriquecimento baseia-se em [*indexadores.*](search-indexer-overview.md) Os indexadores povoam um índice baseado em mapeamentos de campo-campo entre o índice e a sua fonte de dados para a quebra de documentos. As competências, agora anexadas aos indexadores, intercetam e enriquecem documentos de acordo com as competências que define. Uma vez indexado, pode aceder ao conteúdo através de pedidos de pesquisa através de todos os tipos de [consulta suportados pela Pesquisa Cognitiva Azure](search-query-overview.md).  Se você é novo para os indexadores, esta secção leva você através dos degraus.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passo 1: Fase de ligação e quebra de documentos

No início do pipeline, tem texto não estruturado ou conteúdo não-texto (como imagens, documentos digitalizados ou ficheiros JPEG). Os dados devem existir num serviço de armazenamento de dados Azure que possa ser acedido por um indexante. Os indexadores podem "decifrar" documentos de origem para extrair texto a partir de dados de origem. A cifra de documentos é o processo de extração ou criação de conteúdo de texto a partir de fontes não-texto durante a indexação.

![Fase de rachadura de documentos](./media/cognitive-search-intro/document-cracking-phase-blowup.png "rachadura de documento")

 Fontes suportadas incluem armazenamento de blob Azure, armazenamento de mesa Azure, Base de Dados Azure SQL e Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de ficheiros: PDFs, Word, PowerPoint, CSV. Para a lista completa, consulte [formatos suportados](search-howto-indexing-azure-blob-storage.md#supported-document-formats). A indexação leva tempo para começar com um pequeno conjunto de dados representativo e, em seguida, construí-lo incrementalmente à medida que a sua solução amadurece.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passo 2: Competências cognitivas e fase de enriquecimento

O enriquecimento é realizado com *habilidades cognitivas* que realizam operações atómicas. Por exemplo, uma vez que tenha rachado um PDF, pode aplicar o reconhecimento da entidade, a deteção de linguagem ou a extração de frases-chave para produzir novos campos no seu índice que não estão disponíveis de forma nativa na fonte. No total, a coleção de competências utilizadas no seu oleoduto chama-se *skillset*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Uma habilidade é baseada em [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) ou [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta-se ao skillset. Um skillset pode ser mínimo ou altamente complexo, e determina não só o tipo de processamento, mas também a ordem de operações. Um skillset mais os mapeamentos de campo definidos como parte de um indexante especifica totalmente o gasoduto de enriquecimento. Para obter mais informações sobre a união de todas estas peças, consulte [definir uma habilidade](cognitive-search-defining-skillset.md).

Internamente, o oleoduto gera uma coleção de documentos enriquecidos. Pode decidir quais as partes dos documentos enriquecidos que devem ser mapeadas para campos indexáveis no seu índice de pesquisa. Por exemplo, se aplicasse a chave da extração da frase e as competências de reconhecimento de entidades, esses novos campos tornar-se-iam parte do documento enriquecido e poderiam ser mapeados em campos no seu índice. Consulte [As Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicione um elemento knowledgeStore para salvar enriquecimentos

[Search REST api-version=2019-05-06-Preview](search-api-preview.md) alarga as `knowledgeStore` competências com uma definição que fornece uma ligação de armazenamento Azure e projeções que descrevem como os enriquecimentos são armazenados. Isto para além do seu índice. Num oleoduto normal de IA, os documentos enriquecidos são transitórios, utilizados apenas durante a indexação e depois descartados. Com a loja de conhecimento, os documentos enriquecidos são preservados. Para mais informações, consulte a [Knowledge store (pré-visualização)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Passo 3: Índice de pesquisa e acesso baseado em consultas

Quando o processamento estiver concluído, tem um índice de pesquisa composto por documentos enriquecidos, totalmente pesquisáveis em Pesquisa Cognitiva Azure. [Consultar o índice](search-query-overview.md) é a forma como os desenvolvedores e os utilizadores acedem aos conteúdos enriquecidos gerados pelo pipeline. 

![Índice com ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com ícone de pesquisa")

O índice é como qualquer outro que possa criar para a Pesquisa Cognitiva Azure: pode complementar com analisadores personalizados, invocar consultas de pesquisa difusas, adicionar pesquisa filtrada ou experimentar perfis de pontuação para reformular os resultados da pesquisa.

Os índices são gerados a partir de um esquema de índice que define os campos, atributos e outras construções anexadas a um índice específico, tais como perfis de pontuação e mapas de sinónimo. Uma vez definido e povoado um índice, pode indexar-se incrementalmente para recolher novos e atualizados documentos de origem. Certas modificações requerem uma reconstrução completa. Deve utilizar um pequeno conjunto de dados até que o design do esquema seja estável. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

**Lista de verificação: Um fluxo de trabalho típico**

1. Subponha os dados de origem Azure numa amostra representativa. A indexação leva tempo para começar com um pequeno conjunto de dados representativo e, em seguida, construí-lo incrementalmente à medida que a sua solução amadurece.

1. Crie um [objeto de origem](https://docs.microsoft.com/rest/api/searchservice/create-data-source) de dados na Pesquisa Cognitiva Azure para fornecer uma cadeia de ligação para a recuperação de dados.

1. Crie uma [habilidade](https://docs.microsoft.com/rest/api/searchservice/create-skillset) com passos de enriquecimento.

1. Defina o [esquema do índice.](https://docs.microsoft.com/rest/api/searchservice/create-index) A coleção *Fields* inclui campos a partir de dados de origem. Também deve eliminar campos adicionais para manter valores gerados para conteúdos criados durante o enriquecimento.

1. Defina o [indexador](https://docs.microsoft.com/rest/api/searchservice/create-skillset) que refere a fonte de dados, a skillset e o índice.

1. Dentro do indexante, adicione *outputFieldMappings*. Esta secção mapeia a saída do skillset (no passo 3) para os campos de entrada no esquema de índice (no passo 4).

1. Envie o pedido *do Indexer Create* que acabou de criar (um pedido post com uma definição de indexante no organismo de pedido) para expressar o indexador em Pesquisa Cognitiva Azure. Este passo é como se dirige o indexador, invocando o oleoduto.

1. Executar consultas para avaliar resultados e modificar código para atualizar habilidades, esquema ou configuração indexante.

1. [Redefinir o indexante](search-howto-reindex.md) antes de reconstruir o gasoduto.

## <a name="next-steps"></a>Passos seguintes

+ [Ligações de documentação de enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Exemplo: Criação de uma habilidade personalizada para enriquecimento de IA (C#)](cognitive-search-create-custom-skill-example.md)
+ [Quickstart: Experimente o enriquecimento de IA num portal walk-through](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de enriquecimento de IA](cognitive-search-tutorial-blob.md)
+ [Arquivo de dados de conhecimento (pré-visualização)](knowledge-store-concept-intro.md)
+ [Criar uma loja de conhecimentos em REST](knowledge-store-create-rest.md)
+ [Sugestões de resolução de problemas](cognitive-search-concept-troubleshooting.md)

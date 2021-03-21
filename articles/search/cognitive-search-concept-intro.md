---
title: Conceitos de enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: A extração de conteúdos, o processamento de linguagem natural (NLP) e o processamento de imagem são usados para criar conteúdo pes pescável em índices de Pesquisa Cognitiva Azure com competências cognitivas pré-definidas e algoritmos de IA personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037256"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Enriquecimento de IA em Pesquisa Cognitiva Azure

O enriquecimento de IA é uma extensão de [indexadores](search-indexer-overview.md) que podem ser usados para extrair texto de imagens, bolhas e outras fontes de dados não estruturadas. O enriquecimento e extração tornam o seu conteúdo mais pescêvel em objetos de saída indexante, seja num [índice de pesquisa](search-what-is-an-index.md) ou numa loja de [conhecimento.](knowledge-store-concept-intro.md) 

A extração e o enriquecimento são implementados utilizando *competências cognitivas ligadas* ao gasoduto orientado pelo indexante. Pode utilizar habilidades incorporadas da Microsoft ou incorporar o processamento externo numa [*habilidade personalizada*](cognitive-search-create-custom-skill-example.md) que cria. Exemplos de uma habilidade personalizada podem ser um módulo de entidade personalizada ou classificador de documentos direcionado para um domínio específico, como finanças, publicações científicas ou medicina.

As competências incorporadas enquadram-se nestas categorias: 

+ **As** competências de processamento de linguagem natural incluem reconhecimento de [entidades,](cognitive-search-skill-entity-recognition.md) [deteção de linguagens,](cognitive-search-skill-language-detection.md) [extração de frases-chave,](cognitive-search-skill-keyphrases.md)manipulação de texto, [deteção de sentimentos](cognitive-search-skill-sentiment.md)e [deteção de PII.](cognitive-search-skill-pii-detection.md) Com estas habilidades, o texto não estruturado é mapeado como campos pescandáveis e filtrados num índice.

+ **As** competências de processamento de imagem incluem [reconhecimento de caracteres óticos (OCR)](cognitive-search-skill-ocr.md) e identificação de [características visuais,](cognitive-search-skill-image-analysis.md)tais como deteção facial, interpretação de imagem, reconhecimento de imagem (pessoas famosas e marcos) ou atributos como orientação de imagem. Estas habilidades criam representações de texto de conteúdo de imagem, tornando-o pesmável usando as capacidades de consulta da Azure Cognitive Search.

![Diagrama do gasoduto de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "Visão geral do gasoduto de enriquecimento")

As competências incorporadas na Pesquisa Cognitiva Azure baseiam-se em modelos pré-treinados de aprendizagem automática em APIs de Serviços Cognitivos: [Visão computacional](../cognitive-services/computer-vision/index.yml) e [Análise de Texto.](../cognitive-services/text-analytics/overview.md) Pode anexar um recurso de Serviços Cognitivos se quiser aproveitar estes recursos durante o processamento de conteúdos.

O processamento de linguagem natural e de imagem é aplicado durante a fase de ingestão de dados, com os resultados a tornarem-se parte da composição de um documento num índice pesquisável na Pesquisa Cognitiva do Azure. Os dados são obtidos como um conjunto de dados Azure e, em seguida, empurrados através de um pipeline de indexação usando [as habilidades incorporadas](cognitive-search-predefined-skills.md) que você precisa.  

## <a name="feature-availability"></a>Disponibilidade de funcionalidades

O enriquecimento de IA está disponível em regiões onde os Serviços Cognitivos Azure também estão disponíveis.  Pode verificar a disponibilidade atual de enriquecimento de IA nos [produtos Azure disponíveis por página da região.](https://azure.microsoft.com/global-infrastructure/services/?products=search)  O enriquecimento de IA está disponível em todas as regiões apoiadas, exceto:

+ Austrália Sudeste
+ China Norte 2
+ Leste da Noruega
+ Alemanha Centro-Oeste

Se o seu serviço de pesquisa estiver localizado numa destas regiões, não será capaz de criar e utilizar habilidades, mas todas as outras funcionalidades do serviço de pesquisa estão disponíveis e totalmente suportadas.

## <a name="when-to-use-ai-enrichment"></a>Quando usar o enriquecimento de IA

Deve considerar a utilização de habilidades cognitivas incorporadas se o seu conteúdo bruto for texto, conteúdo de imagem ou conteúdo não estruturado que precise de deteção e tradução de linguagem. Aplicar IA através das competências cognitivas incorporadas pode desbloquear este conteúdo, aumentando o seu valor e utilidade nas suas aplicações de pesquisa e ciência de dados. 

Além disso, pode considerar adicionar uma habilidade personalizada se tiver código de código aberto, de terceiros ou de primeira parte que gostaria de integrar no pipeline. Os modelos de classificação que identificam características salientes de vários tipos de documentos enquadram-se nesta categoria, mas qualquer pacote que acrescente valor ao seu conteúdo poderia ser utilizado.

### <a name="more-about-built-in-skills"></a>Mais sobre habilidades incorporadas

Um [skillset](cognitive-search-defining-skillset.md) que é montado usando habilidades incorporadas é bem adequado para os seguintes cenários de aplicação:

+ Documentos digitalizados (JPEG) que pretende tornar o texto completo pescável. Pode anexar uma habilidade de reconhecimento de caracteres óticos (OCR) para identificar, extrair e ingerir texto a partir de ficheiros JPEG.

+ PDFs com imagem combinada e texto. O texto em PDFs pode ser extraído durante a indexação sem a utilização de etapas de enriquecimento, mas a adição de imagem e processamento de linguagem natural pode muitas vezes produzir um resultado melhor do que um indexante padrão fornece.

+ Conteúdo multilingue contra o qual pretende aplicar a deteção de idiomas e possivelmente a tradução de texto.

+ Documentos não estruturados ou semi-estruturados que contenham conteúdo que tenha significado ou contexto inerente que esteja escondido no documento maior. 

  As bolhas, em particular, contêm frequentemente um grande corpo de conteúdo que é embalado num único "campo". Ao anexar habilidades de processamento de imagem e linguagem natural a um indexante, pode criar novas informações que estão existentes no conteúdo bruto, mas que não surgiram de outra forma como campos distintos. Algumas habilidades cognitivas incorporadas prontas a usar que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as competências incorporadas também podem ser usadas para reestruturar o conteúdo através de operações de divisão de texto, fusão e forma.

### <a name="more-about-custom-skills"></a>Mais sobre habilidades personalizadas

As habilidades personalizadas podem suportar cenários mais complexos, tais como reconhecer formulários ou deteção de entidades personalizadas usando um modelo que fornece e embrulha na [interface web de habilidades personalizadas.](cognitive-search-custom-skill-interface.md) Vários exemplos de competências personalizadas incluem [Formas Recogniser,](../cognitive-services/form-recognizer/overview.md)integração da [API de Pesquisa de Entidade Bing,](./cognitive-search-create-custom-skill-example.md)e [reconhecimento de entidades personalizadas.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

## <a name="steps-in-an-enrichment-pipeline"></a>Passos num oleoduto de enriquecimento <a name="enrichment-steps"></a>

Um gasoduto de enriquecimento baseia-se em [*indexadores.*](search-indexer-overview.md) Os indexantes preenchem um índice baseado em mapeamentos campo-a-campo entre o índice e a sua fonte de dados para a quebra de documentos. Competências, agora ligadas a indexantes, intercetam e enriquecem documentos de acordo com as competências que define. Uma vez indexado, pode aceder ao conteúdo através de pedidos de pesquisa através de todos os [tipos de consulta suportados pela Azure Cognitive Search](search-query-overview.md).  Se você é novo em indexadores, esta secção leva você através dos degraus.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passo 1: Fase de ligação e de fissura de documentos

No início do pipeline, tem texto ou conteúdo não estruturado (como imagens, documentos digitalizados ou ficheiros JPEG). Os dados devem existir num serviço de armazenamento de dados Azure que possa ser acedido por um indexante. Os indexantes podem "quebrar" documentos de origem para extrair texto de dados de origem. A fissuração de documentos é o processo de extração ou criação de conteúdos de texto a partir de fontes não-texto durante a indexação.

![Fase de fissuração de documentos](./media/cognitive-search-intro/document-cracking-phase-blowup.png "rachadura documental")

 As fontes suportadas incluem o armazenamento de blob Azure, o armazenamento de mesaS Azure, a Base de Dados Azure SQL e a DB Azure Cosmos. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de ficheiros: PDFs, Word, PowerPoint, ficheiros CSV. Para obter a lista completa, consulte [os formatos suportados.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) A indexação leva tempo, por isso comece com um pequeno conjunto de dados representativo e, em seguida, acumuça-o incrementalmente à medida que a sua solução amadurece.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passo 2: Competências cognitivas e fase de enriquecimento

O enriquecimento é realizado com *habilidades cognitivas* que realizam operações atómicas. Por exemplo, uma vez que tenha decifrado um PDF, pode aplicar reconhecimento de entidades, deteção de linguagem ou extração de frases-chave para produzir novos campos no seu índice que não estão disponíveis na fonte. No total, a recolha de competências utilizadas no seu oleoduto *chama-se skillset*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um skillset é baseado [em habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) ou [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta-se ao skillset. Um skillset pode ser mínimo ou altamente complexo, e determina não só o tipo de processamento, mas também a ordem de operações. Um skillset mais os mapeamentos de campo definidos como parte de um indexante especifica completamente o gasoduto de enriquecimento. Para obter mais informações sobre a junção de todas estas peças, consulte [Definir uma habilidade.](cognitive-search-defining-skillset.md)

Internamente, o gasoduto gera uma coleção de documentos enriquecidos. Pode decidir quais as partes dos documentos enriquecidos que devem ser mapeadas para campos indexáveis no seu índice de pesquisa. Por exemplo, se aplicasse a extração da frase-chave e as competências de reconhecimento da entidade, esses novos campos tornar-se-iam parte do documento enriquecido, podendo ser mapeados para campos no seu índice. Consulte [anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicione um elemento knowledgeStore para salvar enriquecimentos

[Search REST api-version=2020-06-30](/rest/api/searchservice/) estende habilidades com uma `knowledgeStore` definição que fornece uma conexão de armazenamento Azure e projeções que descrevem como os enriquecimentos são armazenados. Isto é além do seu índice. Num gasoduto de IA padrão, os documentos enriquecidos são transitórios, utilizados apenas durante a indexação e depois descartados. Com a loja de conhecimento, os documentos enriquecidos são preservados. Para mais informações, consulte [a Loja de Conhecimento.](knowledge-store-concept-intro.md)

### <a name="step-3-search-index-and-query-based-access"></a>Passo 3: Índice de pesquisa e acesso baseado em consultas

Quando o processamento estiver terminado, tem um índice de pesquisa composto por documentos enriquecidos, totalmente pescável por texto na Pesquisa Cognitiva Azure. [A consulta do índice](search-query-overview.md) é como os desenvolvedores e os utilizadores acedem ao conteúdo enriquecido gerado pelo pipeline. 

![Índice com ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com ícone de pesquisa")

O índice é como qualquer outro que possa criar para a Azure Cognitive Search: pode complementar com analisadores personalizados, invocar consultas de pesquisa difusas, adicionar pesquisa filtrada ou experimentar perfis de pontuação para reformular os resultados da pesquisa.

Os índices são gerados a partir de um esquema de índice que define os campos, atributos e outras construções anexadas a um índice específico, tais como perfis de pontuação e mapas de sinónimo. Uma vez definido e povoado um índice, pode indexar-se incrementalmente para recolher novos documentos de origem atualizados. Certas modificações requerem uma reconstrução completa. Deve utilizar um pequeno conjunto de dados até que o design do esquema esteja estável. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

**Lista de verificação: Um fluxo de trabalho típico**

1. Subestraça os seus dados de origem Azure numa amostra representativa. A indexação leva tempo, por isso comece com um pequeno conjunto de dados representativo e, em seguida, acumuça-o incrementalmente à medida que a sua solução amadurece.

1. Crie um [objeto de origem de dados](/rest/api/searchservice/create-data-source) na Pesquisa Cognitiva Azure para fornecer uma cadeia de ligação para a recuperação de dados.

1. Crie um [skillset](/rest/api/searchservice/create-skillset) com passos de enriquecimento.

1. Defina o [esquema de índice.](/rest/api/searchservice/create-index) A recolha *fields* inclui campos a partir de dados de origem. Também deve retirar campos adicionais para manter valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexante](/rest/api/searchservice/create-indexer) referindo a fonte de dados, skillset e índice.

1. Dentro do indexador, adicione *outputFieldMappings*. Esta secção mapeia a saída do skillset (no passo 3) para os campos de entradas no esquema de índice (no passo 4).

1. Envie o pedido *de Create Indexer* que acabou de criar (um pedido DEM com uma definição de indexante no organismo de pedido) para expressar o indexante na Pesquisa Cognitiva Azure. Este passo é como se gere o indexante, invocando o oleoduto.

1. Executar consultas para avaliar resultados e modificar código para atualizar habilidades, esquemas ou configuração do indexante.

1. [Reinicie o indexante](search-howto-reindex.md) antes de reconstruir o gasoduto.

## <a name="next-steps"></a>Passos seguintes

+ [Ligações de documentação de enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Exemplo: Criar uma habilidade personalizada para enriquecimento de IA (C#)](cognitive-search-create-custom-skill-example.md)
+ [Quickstart: Experimente o enriquecimento de IA num portal walk-through](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de enriquecimento de IA](cognitive-search-tutorial-blob.md)
+ [Arquivo de dados de conhecimento](knowledge-store-concept-intro.md)
+ [Criar uma loja de conhecimento em REST](knowledge-store-create-rest.md)
+ [Dicas de resolução de problemas](cognitive-search-concept-troubleshooting.md)

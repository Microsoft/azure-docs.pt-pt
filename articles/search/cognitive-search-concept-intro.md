---
title: Introdução ao enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Extração de conteúdo, processamento de linguagem natural (NLP) e processamento de imagem para criar conteúdo pesquisável na indexação da Pesquisa Cognitiva Azure utilizando habilidades cognitivas e algoritmos de IA.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/04/2019
ms.openlocfilehash: 6d75e48443fd8622ca2ae7ff05fe81184c4b2b16
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472439"
---
# <a name="introduction-to-ai-in-azure-cognitive-search"></a>Introdução à IA na Pesquisa Cognitiva Azure

O enriquecimento de IA é uma capacidade da indexação da Pesquisa Cognitiva Azure usada para extrair texto de imagens, bolhas e outras fontes de dados não estruturadas - enriquecendo o conteúdo para torná-lo mais pesquisável em um índice ou loja de conhecimento. A extração e o enriquecimento são implementados através *de competências cognitivas ligadas* a um gasoduto de indexação. As competências cognitivas incorporadas no serviço enquadram-se nestas categorias: 

+ **As** competências de processamento de linguagem natural incluem reconhecimento de [entidades,](cognitive-search-skill-entity-recognition.md) [deteção de linguagem,](cognitive-search-skill-language-detection.md)extração de [frases-chave,](cognitive-search-skill-keyphrases.md)manipulação de texto, [deteção de sentimentos](cognitive-search-skill-sentiment.md)e [deteção de PII.](cognitive-search-skill-pii-detection.md) Com estas habilidades, o texto não estruturado pode assumir novas formas, mapeadas como campos pesquisáveis e filtrantes num índice.

+ **As** habilidades de processamento de imagem incluem reconhecimento ótico de [caracteres (OCR)](cognitive-search-skill-ocr.md) e identificação de [características visuais](cognitive-search-skill-image-analysis.md), tais como deteção facial, interpretação de imagem, reconhecimento de imagem (pessoas famosas e marcos) ou atributos como cores ou orientação de imagem. Pode criar representações de texto de conteúdos de imagem, pesquisáveis utilizando todas as capacidades de consulta da Pesquisa Cognitiva Azure.

![Diagrama de gasoduto de enriquecimento](./media/cognitive-search-intro/cogsearch-architecture.png "visão geral do gasoduto de enriquecimento")

As competências cognitivas em Pesquisa Cognitiva Azure baseiam-se em modelos de aprendizagem automática pré-treinados em APIs de Serviços Cognitivos: [Visão Computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e [Análise de Texto.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) 

O processamento de linguagem natural e imagem é aplicado durante a fase de ingestão de dados, com os resultados a tornarem-se parte da composição de um documento num índice pesquisável na Pesquisa Cognitiva Azure. Os dados são obtidos como um conjunto de dados Azure e depois empurrados através de um pipeline de indexação usando as [habilidades incorporadas](cognitive-search-predefined-skills.md) que você precisa. A arquitetura é extensível por isso, se as habilidades incorporadas não forem suficientes, você pode criar e anexar [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) para integrar o processamento personalizado. Exemplos podem ser um módulo de entidade personalizada ou um classificador de documento sintetizado que visa um domínio específico, como finanças, publicações científicas ou medicina.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-skills"></a>Quando usar habilidades cognitivas

Deve considerar a utilização de habilidades cognitivas incorporadas se o seu conteúdo bruto for texto não estruturado, conteúdo de imagem ou conteúdo que necessite de deteção e tradução linguística. Aplicar IA através das habilidades cognitivas incorporadas pode desbloquear este conteúdo, aumentando o seu valor e utilidade nas suas aplicações de pesquisa e ciência de dados. 

Além disso, pode considerar adicionar uma habilidade personalizada se tiver código de código de código aberto, de terceiros ou de primeira parte que gostaria de integrar no pipeline. Os modelos de classificação que identificam características salientes de vários tipos de documentos enquadram-se nesta categoria, mas qualquer pacote que acrescente valor ao seu conteúdo também pode ser utilizado.

### <a name="more-about-built-in-skills"></a>Mais sobre habilidades incorporadas

Um conjunto de habilidades que é montado usando habilidades incorporadas é bem adequado para os seguintes cenários de aplicação:

+ Documentos digitalizados (JPEG) que pretende tornar pesquisáveis por texto completo. Pode anexar uma habilidade de reconhecimento ótico de caracteres (OCR) para identificar, extrair e ingerir texto a partir de ficheiros JPEG.

+ PDFs com imagem e texto combinados. O texto em PDFs pode ser extraído durante a indexação sem a utilização de passos de enriquecimento, mas a adição de imagem e processamento de linguagem natural pode muitas vezes produzir um resultado melhor do que uma indexação padrão fornece.

+ Conteúdo multilingual contra o qual pretende aplicar a deteção de linguagem e, possivelmente, a tradução de texto.

+ Documentos não estruturados ou semi-estruturados que contenham conteúdo que tenha significado ou contexto inerente soais escondidos no documento maior. 

  As bolhas, em particular, contêm frequentemente um grande corpo de conteúdo que é embalado num "campo" singled. Ao anexar habilidades de processamento de imagem e linguagem natural a um indexador, pode criar novas informações que estão existentes no conteúdo bruto, mas que não surgem como campos distintos. Algumas habilidades cognitivas prontas a usar que podem ajudar: extração de frases-chave, análise de sentimentos e reconhecimento de entidades (pessoas, organizações e locais).

  Além disso, as competências incorporadas também podem ser usadas através de operações de divisão de texto, fusão e forma.

### <a name="more-about-custom-skills"></a>Mais sobre habilidades personalizadas

As competências personalizadas podem suportar cenários mais complexos, como o reconhecimento de formulários ou a deteção personalizada de entidades utilizando um modelo que fornece e envolve na [interface web de habilidades personalizadas.](cognitive-search-custom-skill-interface.md) Vários exemplos de competências personalizadas incluem O Reconhecimento de [Formulários,](/azure/cognitive-services/form-recognizer/overview)integração da API de Pesquisa de [Entidades Bing,](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)e reconhecimento personalizado de [entidades.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)


## <a name="components-of-an-enrichment-pipeline"></a>Componentes de um gasoduto de enriquecimento

Um gasoduto de enriquecimento baseia-se em [*indexadores*](search-indexer-overview.md) que rastejam fontes de dados e fornecem o processamento de índices de ponta a ponta. As competências estão agora ligadas aos indexadores, intercetando e enriquecendo documentos de acordo com a habilidade que define. Uma vez indexado, pode aceder ao conteúdo através de pedidos de pesquisa através de todos os tipos de [consulta suportados pela Pesquisa Cognitiva Azure](search-query-overview.md).  Se você é novo para os indexadores, esta secção leva você através dos degraus.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passo 1: Fase de ligação e quebra de documentos

No início do pipeline, tem texto não estruturado ou conteúdo não-texto (como ficheiros JPEG de imagem e documento digitalizado). Os dados devem existir num serviço de armazenamento de dados Azure que possa ser acedido por um indexante. Os indexadores podem "decifrar" documentos de origem para extrair texto a partir de dados de origem.

![Fase de rachadura de documentos](./media/cognitive-search-intro/document-cracking-phase-blowup.png "rachadura de documento")

 Fontes suportadas incluem armazenamento de blob Azure, armazenamento de mesa Azure, Base de Dados Azure SQL e Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de ficheiros: PDFs, Word, PowerPoint, CSV. Para a lista completa, consulte [formatos suportados](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passo 2: Competências cognitivas e fase de enriquecimento

O enriquecimento é através *de habilidades cognitivas* que realizam operações atómicas. Por exemplo, uma vez que tenha conteúdo de texto a partir de um PDF, pode aplicar a deteção de linguagem de reconhecimento de entidades ou a extração de frases-chave para produzir novos campos no seu índice que não estão disponíveis de forma nativa na fonte. No total, a coleção de competências utilizadas no seu oleoduto chama-se *skillset*.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Uma habilidade é baseada em [habilidades cognitivas incorporadas](cognitive-search-predefined-skills.md) ou [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) que você fornece e conecta-se ao skillset. Um skillset pode ser mínimo ou altamente complexo, e determina não só o tipo de processamento, mas também a ordem de operações. Um skillset mais os mapeamentos de campo definidos como parte de um indexante especifica totalmente o gasoduto de enriquecimento. Para obter mais informações sobre a união de todas estas peças, consulte [definir uma habilidade](cognitive-search-defining-skillset.md).

Internamente, o oleoduto gera uma coleção de documentos enriquecidos. Pode decidir quais as partes dos documentos enriquecidos que devem ser mapeadas para campos indexáveis no seu índice de pesquisa. Por exemplo, se aplicasse as frases-chave de extração e as competências de reconhecimento de entidades, esses novos campos tornar-se-iam parte do documento enriquecido, e podem ser mapeados em campos no seu índice. Consulte [As Anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre formações de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicione um elemento knowledgeStore para salvar enriquecimentos

[Search REST api-version=2019-05-06-Preview](search-api-preview.md) alarga as competências com uma definição `knowledgeStore` que fornece uma ligação de armazenamento Azure e projeções que descrevem como os enriquecimentos são armazenados. 

Adicionar uma loja de conhecimentos a um skillset dá-lhe a capacidade de projetar uma representação dos seus enriquecimentos para outros cenários que não a procura completa de texto. Para mais informações, consulte a [Knowledge store (pré-visualização)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Passo 3: Índice de pesquisa e acesso baseado em consultas

Quando o processamento estiver concluído, tem um índice de pesquisa composto por documentos enriquecidos, totalmente pesquisáveis em Pesquisa Cognitiva Azure. [Consultar o índice](search-query-overview.md) é a forma como os desenvolvedores e os utilizadores acedem aos conteúdos enriquecidos gerados pelo pipeline. 

![Índice com ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com ícone de pesquisa")

O índice é como qualquer outro que possa criar para a Pesquisa Cognitiva Azure: pode complementar com analisadores personalizados, invocar consultas de pesquisa difusas, adicionar pesquisa filtrada ou experimentar perfis de pontuação para reformular os resultados da pesquisa.

Os índices são gerados a partir de um esquema de índice que define os campos, atributos e outras construções anexadas a um índice específico, tais como perfis de pontuação e mapas de sinónimo. Uma vez definido e povoado um índice, pode indexar-se incrementalmente para recolher novos e atualizados documentos de origem. Certas modificações requerem uma reconstrução completa. Deve utilizar um pequeno conjunto de dados até que o design do esquema seja estável. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Funcionalidades e conceitos principais

| Conceito | Descrição| Ligações |
|---------|------------|-------|
| Skillset | Um recurso de alto nível que contém uma coleção de habilidades. Uma habilidade é o oleoduto de enriquecimento. É invocado durante a indexação por um indexante. | Ver [Definir uma habilidade](cognitive-search-defining-skillset.md) |
| Habilidade cognitiva | Uma transformação atómica num oleoduto de enriquecimento. Muitas vezes, é um componente que extrai ou infere a estrutura, e por isso aumenta a sua compreensão dos dados de entrada. Quase sempre, a saída é baseada em texto e o processamento é processamento de linguagem natural ou processamento de imagem que extrai ou gera texto a partir de inputs de imagem. A saída de uma habilidade pode ser mapeada para um campo num índice, ou usada como uma entrada para um enriquecimento a jusante. Uma habilidade é predefinida e fornecida pela Microsoft, ou personalizada: criada e implementada por si. | [Competências cognitivas incorporadas](cognitive-search-predefined-skills.md) |
| Extração de dados | Abrange uma ampla gama de processamento, mas relacionada com o enriquecimento de IA, a habilidade de reconhecimento da entidade é mais tipicamente usada para extrair dados (uma entidade) de uma fonte que não fornece essa informação de forma nativa. | Ver Habilidade de Reconhecimento de [Entidades](cognitive-search-skill-entity-recognition.md) e Habilidade de Extração de [Documentos (pré-visualização)](cognitive-search-skill-document-extraction.md)| 
| Processamento de imagens | Infere texto a partir de uma imagem, como a capacidade de reconhecer um marco, ou extrai texto de uma imagem. Exemplos comuns incluem OCR para levantar caracteres de um ficheiro de documento digitalizado (JPEG) ou reconhecer um nome de rua numa fotografia que contenha um sinal de rua. | Ver [Habilidade de Análise](cognitive-search-skill-image-analysis.md) de Imagem ou Habilidade [OCR](cognitive-search-skill-ocr.md)
| Processamento de linguagem natural | Processamento de texto para insights e informações sobre inputs de texto. Deteção de linguagem, análise de sentimentos e extração de frases-chave são competências que se enquadram no processamento de linguagem natural.  | Ver Habilidade de extração de [frases-chave,](cognitive-search-skill-keyphrases.md) [habilidade de deteção](cognitive-search-skill-language-detection.md)de linguagem, [habilidade de tradução](cognitive-search-skill-text-translation.md)de texto, habilidade de análise de [sentimentos,](cognitive-search-skill-sentiment.md)habilidade [de deteção de PII (pré-visualização)](cognitive-search-skill-pii-detection.md) |
| Rachadura de documento | O processo de extração ou criação de conteúdo de texto a partir de fontes não-texto durante a indexação. O reconhecimento ótico de caracteres (OCR) é um exemplo, mas geralmente refere-se à funcionalidade do indexante central, uma vez que o indexador extrai conteúdo dos ficheiros de aplicação. A fonte de dados que fornece a localização do ficheiro de origem, e a definição do indexante que fornece mapeamentos de campo, são ambos fatores-chave na quebra de documentos. | Ver [visão geral dos Indexers](search-indexer-overview.md) |
| Moldar | Consolidar fragmentos de texto numa estrutura maior ou, inversamente, decompor pedaços de texto maiores num tamanho manejável para um processamento a jusante. | Ver [Habilidade shaper,](cognitive-search-skill-shaper.md) [habilidade de fusão de texto,](cognitive-search-skill-textmerger.md)habilidade de divisão de [texto](cognitive-search-skill-textsplit.md) |
| Documentos enriquecidos | Uma estrutura interna transitória, gerada durante o processamento, com a saída final refletida num índice de pesquisa. Um skillset determina quais os enriquecimentos realizados. Os mapeamentos de campo determinam quais os elementos de dados adicionados ao índice. Opcionalmente, pode criar uma loja de conhecimentos para persistir e explorar documentos enriquecidos usando ferramentas como Storage Explorer, Power BI ou qualquer outra ferramenta que se ligue ao armazenamento do Blob Azure. | Ver [loja de conhecimento (pré-visualização)](knowledge-store-concept-intro.md) |
| Indexante |  Um crawler que extrai dados e metadados pesquisáveis de uma fonte externa de dados e povoa um índice baseado em mapeamentos de campo para campo entre o índice e a sua fonte de dados para a cisão de documentos. Para enriquecimentos de IA, o indexante invoca um conjunto de habilidades, e contém os mapeamentos de campo que associam a saída de enriquecimento aos campos-alvo no índice. A definição do indexante contém todas as instruções e referências para as operações do gasoduto, e o gasoduto é invocado quando executa o indexante. Com uma configuração adicional, pode reutilizar o conteúdo processado existente e executar apenas os passos e competências que são alterados. | Consulte [indexadores](search-indexer-overview.md) e [enriquecimento incremental (pré-visualização)](cognitive-search-incremental-indexing-conceptual.md). |
| Origem de Dados  | Um objeto utilizado por um indexante para se ligar a uma fonte externa de dados de tipos suportados no Azure. | Ver [visão geral dos Indexers](search-indexer-overview.md) |
| Índice | Um índice de pesquisa persistente na Pesquisa Cognitiva Azure, construído a partir de um esquema de índice que define a estrutura de campo e o uso. | Ver [Criar um índice básico](search-what-is-an-index.md) | 
| Arquivo de dados de conhecimento | Uma conta de armazenamento onde os documentos enriquecidos podem ser moldados e projetados para além do índice de pesquisa | Ver [Introdução à loja de conhecimento](knowledge-store-concept-intro.md) | 
| Cache | Uma conta de armazenamento que contém saída em cache criada por um oleoduto de enriquecimento. Permitir que o cache preserve a saída existente que não é afetada por alterações a um skillset ou a outros componentes do gasoduto de enriquecimento. | Ver [enriquecimento incremental](cognitive-search-incremental-indexing-conceptual.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Por onde devo começar?

**Passo 1: [Criar um recurso de pesquisa cognitiva Azure](search-create-service-portal.md)** 

**Passo 2: Experimente alguns quickstarts e exemplos para a experiência prática**

+ [Arranque rápido (portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (pedidos HTTP)](cognitive-search-tutorial-blob.md)
+ [Exemplo: Criar uma habilidade personalizadaC#para enriquecimento de IA ( )](cognitive-search-create-custom-skill-example.md)

Recomendamos o serviço Gratuito para fins de aprendizagem, no entanto o número de transações gratuitas é limitado a 20 documentos por dia. Para executar aulas várias vezes, apague e recrie o indexante para redefinir o contador a zero.

**Passo 3: Rever a API**

Pode utilizar `api-version=2019-05-06` REST em pedidos ou no .NET SDK. Se estiver a explorar a loja de conhecimentos, utilize a pré-visualização REST API (`api-version=2019-05-06-Preview`).

Este passo utiliza as APIs rest para construir uma solução de enriquecimento de IA. Apenas duas APIs são adicionadas ou estendidas para enriquecimento de IA. Outras APIs têm a mesma sintaxe que as versões geralmente disponíveis.

| API REST | Descrição |
|-----|-------------|
| [Criar Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externo que fornece dados de origem utilizados para criar documentos enriquecidos.  |
| [Criar Skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Esta API é específica para o enriquecimento de IA. É um recurso que coordena o uso de [competências incorporadas](cognitive-search-predefined-skills.md) e [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) usadas num pipeline de enriquecimento durante a indexação. |
| [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de pesquisa cognitiva Azure. Campos no mapa de índices para campos em dados de origem ou para campos fabricados durante a fase de enriquecimento (por exemplo, um campo para nomes de organização criados pelo reconhecimento de entidades). |
| [Criar Indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define componentes utilizados durante a indexação: incluindo uma fonte de dados, um conjunto de competências, associações de campo de fonte e estruturas de dados intermediárias para o índice-alvo, e o próprio índice. Executar o indexador é o gatilho para a ingestão de dados e enriquecimento. A saída é um índice de pesquisa baseado no esquema de índice, povoado com dados de origem, enriquecido através de skillsets. Esta API existente é estendida para cenários de pesquisa cognitiva com a inclusão de uma propriedade skillset. |

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

Para obter mais informações sobre questões ou problemas específicos, consulte dicas de [resolução de problemas.](cognitive-search-concept-troubleshooting.md)

## <a name="next-steps"></a>Passos seguintes

+ [Ligações de documentação de enriquecimento de IA](cognitive-search-resources-documentation.md)
+ [Quickstart: Experimente o enriquecimento de IA num portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de enriquecimento de IA](cognitive-search-tutorial-blob.md)
+ [Loja de conhecimento (pré-visualização)](knowledge-store-concept-intro.md)
+ [Criar uma loja de conhecimentos em REST](knowledge-store-create-rest.md)

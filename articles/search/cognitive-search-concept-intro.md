---
title: Introdução à pesquisa cognitiva e enriquecimento de ia-Azure Search
description: Extração de conteúdo, NLP (processamento de linguagem natural) e processamento de imagem para criar conteúdo pesquisável na indexação de Azure Search usando habilidades cognitivas e algoritmos de ia.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: overview
ms.date: 05/28/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 524ab33fc1d6a88620077a28ec70f09d55b06106
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015785"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>O que é "pesquisa cognitiva" em Azure Search?

A pesquisa cognitiva é um recurso de ia em Azure Search, usado para extrair texto de imagens, BLOBs e outras fontes de dados não estruturadas – enriquecendo o conteúdo para torná-lo mais pesquisável em um índice de Azure Search. Extração e enriquecimento são implementados por meio de *habilidades cognitivas* anexadas a um pipeline de indexação. Os aprimoramentos de ia têm suporte das seguintes maneiras: 

+ As habilidades de **processamento de linguagem natural** incluem [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [detecção de idioma](cognitive-search-skill-language-detection.md), extração de frases- [chave](cognitive-search-skill-keyphrases.md), manipulação de texto e [detecção de sentimentos](cognitive-search-skill-sentiment.md). Com essas habilidades, o texto não estruturado pode assumir novos formulários, mapeados como campos pesquisáveis e filtráveis em um índice.

+ As habilidades de **processamento de imagem** incluem [OCR (reconhecimento óptico de caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md), como detecção facial, interpretação de imagem, reconhecimento de imagem (famosas pessoas e pontos de referência) ou atributos como cor ou orientação da imagem. Você pode criar representações de texto de conteúdo de imagem, pesquisáveis usando todos os recursos de consulta do Azure Search.

![Diagrama de pipeline de pesquisa cognitiva](./media/cognitive-search-intro/cogsearch-architecture.png "Visão geral do pipeline de pesquisa cognitiva")

As habilidades cognitivas no Azure Search são baseadas em modelos de aprendizado de máquina no API de Serviços Cognitivos: [Pesquisa Visual computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) e [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

O processamento de imagem e linguagem natural é aplicado durante a fase de ingestão de dados, com os resultados que se tornam parte da composição de um documento em um índice pesquisável no Azure Search. Os dados são originados como um conjunto de dados do Azure e enviados por Push por meio de um pipeline de indexação usando as [habilidades internas](cognitive-search-predefined-skills.md) necessárias. A arquitetura é extensível, portanto, se as habilidades internas não forem suficientes, você poderá criar e anexar [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) para integrar o processamento personalizado. Os exemplos podem ser um módulo de entidade personalizado ou classificador de documento direcionado a um domínio específico, como finanças, publicações científicas ou medicina.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="components-of-cognitive-search"></a>Componentes de pesquisa cognitiva

Um pipeline de pesquisa cognitiva é baseado em [Azure Search *indexadores* ](search-indexer-overview.md) que rastreiam fontes de dados e fornecem processamento de índice de ponta a ponta. As habilidades agora estão conectadas a indexadores, interceptando e enriquecendo documentos de acordo com o configurador que você definir. Depois de indexar, você pode acessar o conteúdo por meio de solicitações de pesquisa por meio de todos os [tipos de consulta com suporte pelo Azure Search](search-query-overview.md)  Se você for novo em indexadores, esta seção o guiará pelas etapas.

### <a name="step-1-connection-and-document-cracking-phase"></a>Passo 1: Fase de violação de conexão e documento

No início do pipeline, você tem texto não estruturado ou conteúdo de não texto (como imagens e arquivos JPEG de documentos digitalizados). Os dados devem existir em um serviço de armazenamento de dados do Azure que possa ser acessado por um indexador. Os indexadores podem "decifrar" documentos de origem para extrair texto de dados de origem.

![Fase de violação de documento](./media/cognitive-search-intro/document-cracking-phase-blowup.png "quebra de documento")

 As fontes com suporte incluem armazenamento de BLOBs do Azure, armazenamento de tabelas do Azure, banco de dados SQL do Azure e Azure Cosmos DB. O conteúdo baseado em texto pode ser extraído dos seguintes tipos de arquivo: Arquivos PDF, Word, PowerPoint e CSV. Para obter a lista completa, consulte [formatos com suporte](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Passo 2: Fase de enriquecimento e habilidades cognitivas

O enriquecimento é por meio de *habilidades cognitivas* executando operações atômicas. Por exemplo, depois de ter conteúdo de texto de um PDF, você pode aplicar a detecção de idioma de reconhecimento de entidade ou a extração de frases-chave para produzir novos campos no índice que não estão disponíveis nativamente na origem. Totalmente, a coleção de habilidades usadas em seu pipeline é chamada deconjunto de qualificações.  

![Fase de enriquecimento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimento")

Um contratador é baseado em [habilidades cognitivas](cognitive-search-predefined-skills.md) predefinidas ou [habilidades personalizadas](cognitive-search-create-custom-skill-example.md) fornecidas por você e conectadas ao contratador de habilidades. Um tipo de habilidade pode ser mínimo ou altamente complexo, e determina não apenas os tipos de processamento, mas também a ordem das operações. Um qualificable mais os mapeamentos de campo definidos como parte de um indexador especificam totalmente o pipeline de enriquecimento. Para obter mais informações sobre como reunir todas essas partes, consulte [definir um](cognitive-search-defining-skillset.md)conjunto de qualificações.

Internamente, o pipeline gera uma coleção de documentos aprimorados. Você pode decidir quais partes dos documentos aprimorados devem ser mapeados para campos indexáveis no índice de pesquisa. Por exemplo, se você aplicou a extração de frases-chave e as habilidades de reconhecimento de entidade, esses novos campos se tornarão parte do documento aprimorado e poderão ser mapeados para campos em seu índice. Consulte [anotações](cognitive-search-concept-annotations-syntax.md) para saber mais sobre as formas de entrada/saída.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Adicionar um elemento knowledgeStore para salvar os aprimoramentos

[API REST do serviço de pesquisa-Version = 2019-05-06](search-api-preview.md) estende habilidades com uma definição de knowledgeStore que fornece uma conexão de armazenamento do Azure e projeções que descrevem como os aprimoramentos são armazenados. 

A adição de uma loja de conhecimento a um confere a capacidade de projetar uma representação de seus aprimoramentos para cenários diferentes da pesquisa de texto completo. Para obter mais informações, consulte [o que é o Knowledge Store](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Passo 3: Índice de pesquisa e acesso baseado em consulta

Quando o processamento é concluído, você tem um índice de pesquisa que consiste em documentos aprimorados, com pesquisa de texto completo em Azure Search. [Consultar o índice](search-query-overview.md) é como os desenvolvedores e usuários acessam o conteúdo aprimorado gerado pelo pipeline. 

![Índice com o ícone de pesquisa](./media/cognitive-search-intro/search-phase-blowup.png "Índice com o ícone de pesquisa")

O índice é como qualquer outro que você possa criar para Azure Search: você pode complementar com analisadores personalizados, invocar consultas de pesquisa difusa, adicionar pesquisa filtrada ou experimentar perfis de Pontuação para remodelar os resultados da pesquisa.

Os índices são gerados de um esquema de índice que define os campos, atributos e outras construções anexados a um índice específico, como perfis de Pontuação e mapas de sinônimos. Depois que um índice é definido e preenchido, você pode indexar incrementalmente para obter documentos de origem novos e atualizados. Determinadas modificações exigem uma recompilação completa. Você deve usar um pequeno conjunto de dados até que o design do esquema seja estável. Para obter mais informações, veja [Como reconstruir um índice](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Funcionalidades e conceitos principais

| Conceito | Descrição| Hiperligações |
|---------|------------|-------|
| Qualificações | Um recurso nomeado de nível superior que contém uma coleção de habilidades. Um contratador de habilidades é o pipeline de enriquecimento. Ele é invocado durante a indexação por um indexador. | [Definir um habilidades](cognitive-search-defining-skillset.md) |
| Habilidade cognitiva | Uma transformação atômica em um pipeline de enriquecimento. Geralmente, é um componente que extrai ou infere a estrutura e, portanto, aumenta sua compreensão dos dados de entrada. Quase sempre, a saída é baseada em texto e o processamento é processamento de linguagem natural ou processamento de imagem que extrai ou gera texto de entradas de imagem. A saída de uma habilidade pode ser mapeada para um campo em um índice ou usada como uma entrada para um enriquecimento de downstream. Uma habilidade é predefinida e fornecida pela Microsoft, ou personalizada: criada e implantada por você. | [Habilidades predefinidas](cognitive-search-predefined-skills.md) |
| Extração de dados | Aborda uma ampla gama de processamento, mas pertence à pesquisa cognitiva, a habilidade de reconhecimento de entidade geralmente é usada para extrair dados (uma entidade) de uma fonte que não fornece essas informações nativamente. | [Habilidade de reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)| 
| Processamento de imagem | Infere o texto de uma imagem, como a capacidade de reconhecer um ponto de referência ou extrair texto de uma imagem. Exemplos comuns incluem o OCR para levantar caracteres de um arquivo de documento digitalizado (JPEG) ou reconhecer um nome de rua em uma fotografia que contém um sinal de rua. | [Habilidade de análise de imagem](cognitive-search-skill-image-analysis.md) ou [habilidade de OCR](cognitive-search-skill-ocr.md)
| Processamento de linguagem natural | Processamento de texto para insights e informações sobre entradas de texto. Detecção de idioma, análise de sentimentos e extração de frases-chave são habilidades que se enquadram no processamento de idioma natural.  | [Extração de frases-chave habilidades](cognitive-search-skill-keyphrases.md), [Detecção de Idioma habilidade](cognitive-search-skill-language-detection.md), [análise de sentimento habilidade](cognitive-search-skill-sentiment.md) |
| Quebra de documento | O processo de extrair ou criar conteúdo de texto de fontes que não sejam de texto durante a indexação. O OCR (reconhecimento óptico de caracteres) é um exemplo, mas geralmente se refere à funcionalidade principal do indexador, pois o indexador extrai o conteúdo dos arquivos de aplicativo. A fonte de dados que fornece o local do arquivo de origem e a definição do indexador fornecendo mapeamentos de campo são os dois fatores principais na quebra de documento. | Consulte [indexadores](search-indexer-overview.md) |
| Dela | Consolide fragmentos de texto em uma estrutura maior ou inversamente divida partes de texto maiores em um tamanho gerenciável para um processamento mais longo do downstream. | [Habilidade](cognitive-search-skill-shaper.md)do modelador, [habilidade de fusão de texto](cognitive-search-skill-textmerger.md), habilidade de divisão de [texto](cognitive-search-skill-textsplit.md) |
| Documentos aprimorados | Uma estrutura interna transitória, gerada durante o processamento, com a saída final refletida em um índice de pesquisa. Um configurador de qualificações determina quais aprimoramentos são executados. Os mapeamentos de campo determinam quais elementos de dados são adicionados ao índice. Opcionalmente, você pode criar um repositório de conhecimento para persistir e explorar documentos aprimorados usando ferramentas como Gerenciador de Armazenamento, Power BI ou qualquer outra ferramenta que se conecte ao armazenamento de BLOBs do Azure. | Consulte [repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md). |
| Indexador |  Um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa e popula um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados para decifração de documentos. Para aprimoramentos de pesquisa cognitiva, o indexador invoca um qualificable e contém os mapeamentos de campo que associam a saída de enriquecimento aos campos de destino no índice. A definição do indexador contém todas as instruções e referências para operações de pipeline e o pipeline é invocado quando você executa o indexador. | [Indexadores](search-indexer-overview.md) |
| Origem de Dados  | Um objeto usado por um indexador para se conectar a uma fonte de dados externa de tipos com suporte no Azure. | Consulte [indexadores](search-indexer-overview.md) |
| Índice | Um índice de pesquisa persistente no Azure Search, criado com base em um esquema de índice que define a estrutura e o uso do campo. | [Índices no Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Por onde devo começar?

**Etapa 1: [Criar um recurso de Azure Search](search-create-service-portal.md)** 

**Etapa 2: Experimente alguns guias de início rápido e exemplos para experiência prática**

+ [Início rápido (Portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (solicitações HTTP)](cognitive-search-tutorial-blob.md)
+ [Exemplo: Criando uma habilidade personalizada para pesquisa cognitiva (C#)](cognitive-search-create-custom-skill-example.md)

Recomendamos o serviço gratuito para fins de aprendizado, mas lembre-se de que o número de transações gratuitas é limitado a 20 documentos por dia. Para executar o início rápido e o tutorial em um dia, use um conjunto de arquivos menor (10 documentos) para que você possa se ajustar a ambos os exercícios.

**Etapa 3: Examinar a API**

Você pode usar REST `api-version=2019-05-06` em solicitações ou o SDK do .net. 

Esta etapa usa as APIs REST para criar uma solução de pesquisa cognitiva. Somente duas APIs são adicionadas ou estendidas para pesquisa cognitiva. Outras APIs têm a mesma sintaxe que as versões disponíveis para o público geral.

| API REST | Descrição |
|-----|-------------|
| [Criar Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa que fornece dados de origem usados para criar documentos aprimorados.  |
| [Criar Qualificable (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades](cognitive-search-predefined-skills.md) predefinidas e [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) usadas em um pipeline de enriquecimento durante a indexação. |
| [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de Azure Search. Os campos no índice são mapeados para campos em dados de origem ou para campos fabricados durante a fase de enriquecimento (por exemplo, um campo para nomes de organização criados pelo reconhecimento de entidade). |
| [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, uma habilidade, associações de campo das estruturas de dados de origem e intermediária para o índice de destino e o próprio índice. A execução do indexador é o gatilho para a ingestão e o enriquecimento de dados. A saída é um índice de pesquisa baseado no esquema de índice, preenchido com dados de origem, aprimorado por meio de habilidades.  |

**Verificação Um fluxo de trabalho típico**

1. Subdivida seus dados de origem do Azure em um exemplo representativo. A indexação leva tempo para começar com um pequeno conjunto de dados representativos e, em seguida, compilá-lo incrementalmente à medida que sua solução amadurece.

1. Crie um [objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) no Azure Search para fornecer uma cadeia de conexão para a recuperação de dados.

1. Crie um [conconhecimento](https://docs.microsoft.com/rest/api/searchservice/create-skillset) com etapas de enriquecimento.

1. Defina o [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index). A coleção Fields inclui campos de dados de origem. Você também deve criar um stub de campos adicionais para manter os valores gerados para o conteúdo criado durante o enriquecimento.

1. Defina o [indexador](https://docs.microsoft.com/rest/api/searchservice/create-skillset) que referencia a fonte de dados, o skillset e o índice.

1. No indexador, adicione *outputFieldMappings*. Esta seção mapeia a saída do skillset (na etapa 3) para os campos de entrada no esquema de índice (na etapa 4).

1. Enviar solicitação *criar indexador* que você acabou de criar (uma solicitação post com uma definição de indexador no corpo da solicitação) para expressar o indexador no Azure Search. Esta etapa é como você executa o indexador, invocando o pipeline.

1. Execute consultas para avaliar os resultados e modificar o código para atualizar habilidades, esquema ou configuração do indexador.

1. [Redefina o indexador](search-howto-reindex.md) antes de recompilar o pipeline.

Para obter mais informações sobre perguntas ou problemas específicos, consulte [dicas de solução de problemas](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Passos Seguintes

+ [Documentação da pesquisa cognitiva](cognitive-search-resources-documentation.md)
+ [Quickstart: Instruções de pesquisa cognitiva em um portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Visão geral da loja de conhecimento](knowledge-store-concept-intro.md)
+ [Explicação do Knowledge Store](knowledge-store-howto.md)

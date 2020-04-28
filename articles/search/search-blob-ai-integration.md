---
title: Use AI para entender os dados de armazenamento blob
titleSuffix: Azure Cognitive Search
description: Adicione o processamento de linguagem natural e a análise de imagem às bolhas Azure utilizando um oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73496436"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Use AI para entender os dados de armazenamento blob

Os dados no armazenamento da Blob Azure são frequentemente uma variedade de conteúdos não estruturados, tais como imagens, texto longo, PDFs e documentos do Office. Ao utilizar as capacidades de IA em Pesquisa Cognitiva Azure, pode compreender e extrair informações valiosas de blobs de várias maneiras. Exemplos de aplicação de IA ao conteúdo blob incluem:

+ Extrair texto de imagens utilizando reconhecimento ótico de caracteres (OCR)
+ Produzir uma descrição de cena ou etiquetas de uma foto
+ Detetar linguagem e traduzir texto em diferentes línguas
+ Processar texto com reconhecimento de entidades nomeadas (NER) para encontrar referências a pessoas, datas, lugares ou organizações 

Embora possa precisar apenas de uma destas capacidades de IA, é comum combinar várias delas no mesmo pipeline (por exemplo, extrair texto de uma imagem digitalizada e, em seguida, encontrar todas as datas e lugares referenciados nele). 

O enriquecimento de IA cria novas informações, captadas como texto, armazenadas em campos. Após o enriquecimento, pode aceder a esta informação a partir de um índice de pesquisa através de pesquisa completa de texto, ou enviar documentos enriquecidos de volta para o armazenamento do Azure para alimentar novas experiências de aplicação que incluem explorar dados para cenários de descoberta ou análise. 

Neste artigo, vemos o enriquecimento de IA através de uma lente larga para que possa rapidamente compreender todo o processo, desde a transformação de dados brutos em blobs, até informações que possam consultar num índice de pesquisa ou numa loja de conhecimento.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>O que significa "enriquecer" os dados blob com IA

O enriquecimento de *IA* faz parte da arquitetura indexante da Pesquisa Cognitiva Azure que integra IA incorporada da Microsoft ou IA personalizada que fornece. Ajuda-o a implementar cenários de ponta a ponta onde precisa de processar bolhas (tanto existentes como novas à medida que entram ou são atualizadas), abrir todos os formatos de ficheiros para extrair imagens e texto, extrair as informações desejadas utilizando várias capacidades de IA, e indexá-las num índice de pesquisa para pesquisa rápida, recuperação e exploração. 

As inputs são as suas bolhas, num único recipiente, no armazenamento azure Blob. As bolhas podem ser quase qualquer tipo de texto ou dados de imagem. 

A saída é sempre um índice de pesquisa, usado para pesquisa rápida de texto, recuperação e exploração em aplicações de clientes. Além disso, a produção também pode ser uma loja de *conhecimento* que projeta documentos enriquecidos em blobs Azure ou tabelas Azure para análise a jusante em ferramentas como Power BI ou em cargas de trabalho de ciência de dados.

Pelo meio está a arquitetura do oleoduto em si. O gasoduto baseia-se na funcionalidade *indexante,* à qual pode atribuir um conjunto de *habilidades,* que é composto por uma ou mais *competências* que fornecem a IA. O objetivo do gasoduto é produzir *documentos enriquecidos* que entrem como conteúdo bruto, mas que recolham estrutura, contexto e informação adicionais ao mesmo tempo que se movem através do oleoduto. Os documentos enriquecidos são consumidos durante a indexação para criar índices invertidos e outras estruturas utilizadas em plena pesquisa ou exploração e análise de texto.

## <a name="start-with-services"></a>Comece com os serviços

Precisa de pesquisa cognitiva azure e armazenamento azure blob. No armazenamento blob, você precisa de um recipiente que forneça conteúdo de origem.

Pode começar diretamente na página do portal da sua conta de Armazenamento. Na página de navegação à esquerda, sob o **serviço Blob** clique em **Adicionar Pesquisa Cognitiva Azure** para criar um novo serviço ou selecione um existente. 

Assim que adicionar a Pesquisa Cognitiva Azure à sua conta de armazenamento, pode seguir o processo padrão para enriquecer dados em qualquer fonte de dados do Azure. Recomendamos o assistente de **dados de importação** em Pesquisa Cognitiva Azure para uma introdução inicial fácil ao enriquecimento de IA. Este quickstart acompanha-o através dos degraus: Criar um oleoduto de enriquecimento de [IA no portal](cognitive-search-quickstart-blob.md). 

Nas seguintes secções, vamos explorar mais componentes e conceitos.

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

O enriquecimento de IA é um complemento a um oleoduto indexante, e na Pesquisa Cognitiva Azure, esses oleodutos são construídos em cima de um *indexante.* Um indexante é um subserviço consciente de dados equipado com lógica interna para recolher dados, ler dados de metadados, recuperar dados e serializar dados de formatos nativos em documentos JSON para posterior importação. Os indexadores são frequentemente usados por si próprios para importar, separados da IA, mas se você quiser construir um oleoduto de enriquecimento de IA, você precisará de um indexador e um skillset para acompanhar. Esta secção destaca o indexante; a próxima secção centra-se em habilidades.

As bolhas no Armazenamento Azure são indexadas utilizando o indexante de [armazenamento De pesquisa cognitiva Azure Blob](search-howto-indexing-azure-blob-storage.md). Pode invocar este indexante utilizando o assistente de **dados de importação,** uma API REST ou o .NET SDK. Em código, utiliza este indexador definindo o tipo e fornecendo informações de ligação que incluam uma conta de Armazenamento Azure juntamente com um recipiente de bolhas. Pode subdefinir as suas bolhas criando um diretório virtual, que pode passar como parâmetro, ou filtrando uma extensão do tipo de ficheiro.

Um indexante faz a "rachadura de documentos", abrindo uma bolha para inspecionar o conteúdo. Depois de ligar à fonte de dados, é o primeiro passo no oleoduto. Para os dados blob, é aqui que são detetados pdf, docs de escritório, imagem e outros tipos de conteúdo. A rachadura de documentos com extração de texto não é custo. A rachadura de documentos com a extração de imagem é cobrada a taxas que pode encontrar na [página de preços](https://azure.microsoft.com/pricing/details/search/).

Apesar de todos os documentos serem desvendados, o enriquecimento só ocorre se fornecer explicitamente as competências para o fazer. Por exemplo, se o seu pipeline consistir exclusivamente em análise de imagem, o texto no seu recipiente ou documentos é ignorado.

O indexante Blob vem com parâmetros de configuração e suporta o rastreio de alterações se os dados subjacentes fornecerem informações suficientes. Pode saber mais sobre a funcionalidade principal no indexante de [armazenamento De Pesquisa Cognitiva Azure](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Adicionar componentes de IA

O enriquecimento de IA refere-se a módulos que procuram padrões ou características e, em seguida, executam uma operação em conformidade. Reconhecimento facial em fotos, descrições de texto de fotos, deteção de frases-chave num documento, e OCR (ou reconhecer texto impresso ou manuscrito em ficheiros binários) são exemplos ilustrativos.

Na Pesquisa Cognitiva Azure, *as competências* são os componentes individuais do processamento de IA que pode utilizar autónomo, ou em combinação com outras habilidades. 

+ As competências incorporadas são apoiadas por Serviços Cognitivos, com análise de imagem baseada na Visão Computacional, e processamento de linguagem natural com base no Text Analytics. Para a lista completa, consulte [as competências incorporadas para enriquecimento de conteúdo](cognitive-search-predefined-skills.md).

+ As competências personalizadas são código personalizado, envoltos numa [definição](cognitive-search-custom-skill-interface.md) de interface que permite a integração no pipeline. Nas soluções de cliente, é prática comum utilizar ambos, com competências personalizadas que fornecem módulos de IA de código aberto, de terceiros ou de primeira parte.

Um *skillset* é a coleção de habilidades usadas num oleoduto, e é invocado após a fase de rachadura do documento disponibilizar conteúdo. Um indexante pode consumir exatamente uma habilidade, mas essa habilidade existe independentemente de um indexante para que possa reutilizá-lo em outros cenários.

As habilidades personalizadas podem parecer complexas, mas podem ser simples e simples em termos de implementação. Se tiver pacotes existentes que forneçam modelos de correspondência de padrões ou classificação, o conteúdo que extrai de bolhas pode ser passado para estes modelos para processamento. Uma vez que o enriquecimento de IA é baseado em Azure, o seu modelo também deve estar no Azure. Algumas metodologias comuns de hospedagem incluem a utilização de [Funções Azure](cognitive-search-create-custom-skill-example.md) ou [Contentores.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

As competências incorporadas apoiadas pelos Serviços Cognitivos requerem uma chave de subscrição all-in-one [dos Serviços Cognitivos que](cognitive-search-attach-cognitive-services.md) lhe dá acesso ao recurso. Uma chave tudo-em-um dá-lhe análise de imagem, deteção de linguagem, tradução de texto e análise de texto. Outras habilidades incorporadas são características da Pesquisa Cognitiva Azure e não requerem nenhum serviço ou chave adicional. O formador de texto, o divisor e a fusão são exemplos de competências de ajudante que são por vezes necessárias na conceção do gasoduto.

Se utilizar apenas competências personalizadas e habilidades de utilidade incorporadas, não há dependência ou custos relacionados com os Serviços Cognitivos.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumir produção enriquecida em IA em soluções a jusante

A produção do enriquecimento de IA é ou um índice de pesquisa sobre pesquisa cognitiva azure, ou uma loja de [conhecimento](knowledge-store-concept-intro.md) no Armazenamento Azure.

Na Pesquisa Cognitiva Azure, um índice de pesquisa é usado para exploração interativa usando texto gratuito e consultas filtradas numa aplicação de cliente. Os documentos enriquecidos criados através da IA são formatados em JSON e indexados da mesma forma que todos os documentos estão indexados na Pesquisa Cognitiva Azure, alavancando todos os benefícios que um indexante proporciona. Por exemplo, durante a indexação, o indexante blob refere-se a parâmetros de configuração e configurações para utilizar quaisquer mapeamentos de campo ou alterar lógica de deteção. Tais configurações estão totalmente disponíveis para indexação regular e cargas de trabalho enriquecidas em IA. Após a indexação, quando o conteúdo é armazenado na Pesquisa Cognitiva Azure, pode construir ricas consultas e expressões de filtro para entender o seu conteúdo.

No Armazenamento Azure, uma loja de conhecimentos tem duas manifestações: um recipiente de bolha, ou mesas no armazenamento de mesas. 

+ Um recipiente de bolha captura documentos enriquecidos na sua totalidade, o que é útil se quiser alimentar-se em outros processos. 

+ Em contraste, o armazenamento de mesa pode acomodar projeções físicas de documentos enriquecidos. Pode criar fatias ou camadas de documentos enriquecidos que incluam ou excluem peças específicas. Para análise no Power BI, as tabelas no armazenamento da Tabela Azure tornam-se a fonte de dados para posterior visualização e exploração.

Um documento enriquecido no final do gasoduto difere da sua versão inicial de entrada pela presença de campos adicionais que contenham novas informações que tenham sido extraídas ou geradas durante o enriquecimento. Como tal, pode trabalhar com uma combinação de conteúdo original e criado, independentemente da estrutura de saída que utilizar.

## <a name="next-steps"></a>Passos seguintes

Há muito mais que pode fazer com o enriquecimento de IA para tirar o máximo partido dos seus dados no Armazenamento Azure, incluindo combinar Serviços Cognitivos de diferentes maneiras, e autoria habilidades personalizadas para casos em que não existe serviço cognitivo existente para o cenário. Pode saber mais seguindo os links abaixo.

+ [Upload, download e lista de bolhas com o portal Azure (armazenamento Azure Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurar um indexador de bolhas (Pesquisa Cognitiva Azure)](search-howto-indexing-azure-blob-storage.md) 
+ [Visão geral do enriquecimento da IA (Pesquisa Cognitiva Azure)](cognitive-search-concept-intro.md) 
+ [Criar um skillset (Pesquisa Cognitiva Azure)](cognitive-search-defining-skillset.md)
+ [Map nodes em uma árvore de anotação (Pesquisa Cognitiva Azure)](cognitive-search-output-field-mapping.md)

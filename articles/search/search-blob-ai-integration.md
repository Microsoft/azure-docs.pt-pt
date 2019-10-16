---
title: Usar o ia para entender os dados do blob
titleSuffix: Azure Search
description: Adicione processamento semântico, de linguagem natural e de imagem a BLOBs do Azure usando um pipeline de enriquecimento de ia no Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 5dc81f6e35f86c6dee77d44ff5c59c2657434a37
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376272"
---
# <a name="use-ai-to-understand-blob-data"></a>Usar o ia para entender os dados do blob

Os dados no armazenamento de BLOBs do Azure geralmente são uma variedade de conteúdo não estruturado, como imagens, texto longo, PDFs e documentos do Office. Usando os recursos de ia no Azure Search, você pode entender e extrair informações valiosas de blobs de várias maneiras. Os exemplos de aplicação de AI ao conteúdo de blob incluem:

+ Extrair texto de imagens usando reconhecimento óptico de caracteres (OCR)
+ Produzir uma descrição de cena ou marcas de uma foto
+ Detectar o idioma e traduzir o texto em idiomas diferentes
+ Processar texto com reconhecimento de entidade nomeada (NER) para encontrar referências a pessoas, datas, lugares ou organizações 

Embora você possa precisar apenas de um desses recursos de ia, é comum combinar vários deles no mesmo pipeline (por exemplo, extrair texto de uma imagem digitalizada e, em seguida, localizar todas as datas e locais referenciados). 

O enriquecimento de ia cria novas informações, capturadas como texto, armazenadas em campos. Após o enriquecimento, você pode acessar essas informações de um índice de pesquisa por meio da pesquisa de texto completo ou enviar documentos aprimorados de volta ao armazenamento do Azure para poder obter novas experiências de aplicativos que incluem a exploração de dados para cenários de descoberta ou análise. 

Neste artigo, veremos a sofisticação de AI por meio de uma ampla lente para que você possa entender rapidamente todo o processo, desde transformar dados brutos em BLOBs, até informações consultáveis em um índice de pesquisa ou em um repositório de conhecimento.

## <a name="what-it-means-to-enrich-blob-data"></a>O que significa "enriquecer" os dados de BLOB

O *enriquecimento de ia* faz parte da arquitetura de indexação do Azure Search que integra o ia interno da Microsoft ou do ia personalizado que você fornece. Ele ajuda a implementar cenários de ponta a ponta em que você precisa processar BLOBs (tanto os existentes como os novos quando eles chegam ou são atualizados), desaparecendo abrir todos os formatos de arquivo para extrair imagens e texto, extrair as informações desejadas usando vários recursos de ia e indexá-los em um índice de Azure Search para pesquisa rápida, recuperação e exploração. 

As entradas são seus BLOBs, em um único contêiner, no armazenamento de BLOBs do Azure. Os BLOBs podem ser praticamente qualquer tipo de dados de texto ou imagem. 

A saída é sempre um índice Azure Search, usado para pesquisa rápida de texto, recuperação e exploração em aplicativos cliente. Além disso, a saída também pode ser uma *loja de conhecimento* que projeta documentos em BLOBs do Azure ou tabelas do Azure para análise downstream em ferramentas como Power bi ou em cargas de trabalho de ciência de dados.

No, between é a própria arquitetura de pipeline. O pipeline é baseado no recurso de *indexador* , ao qual você pode atribuir um *skillset*, que é composto por uma ou mais *habilidades* que fornecem o ia. A finalidade do pipeline é produzir *documentos aprimorados* que entram como conteúdo bruto, mas coletam estrutura, contexto e informações adicionais ao passar pelo pipeline. Os documentos aprimorados são consumidos durante a indexação para criar índices invertidos e outras estruturas usadas em pesquisa de texto completo ou exploração e análise.

## <a name="how-to-get-started"></a>Como começar

Você pode iniciar diretamente em sua página do portal da conta de armazenamento. Clique em **adicionar Azure Search** e crie um novo serviço de Azure Search ou selecione um existente. Se você já tiver um serviço de pesquisa existente na mesma assinatura, clicar em **adicionar Azure Search** abrirá o assistente de importação de dados para que você possa percorrer imediatamente a indexação, o enriquecimento e a definição de índice.

Depois de adicionar Azure Search à sua conta de armazenamento, você pode seguir o processo padrão para enriquecer dados em qualquer fonte de dados do Azure. Supondo que você já tenha conteúdo de BLOB, você pode usar o assistente de importação de dados no Azure Search para obter uma introdução inicial fácil ao enriquecimento de ia. Este guia de início rápido explica as etapas: [criar um pipeline de enriquecimento de ia no portal](cognitive-search-quickstart-blob.md). 

Nas seções a seguir, exploraremos mais componentes e conceitos.

## <a name="begin-with-blob-indexers"></a>Começar com indexadores de BLOB

O enriquecimento de ia é um complemento em um pipeline de indexação e, em Azure Search, esses pipelines são criados sobre um *indexador*. Um indexador é um subserviço com reconhecimento de fonte de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. Os indexadores geralmente são usados por si próprios para importação, separados do ia, mas se você quiser criar um pipeline de enriquecimento de ia, precisará de um indexador e de um configurador para seguir. Nesta seção, vamos nos concentrar no próprio indexador.

Os BLOBs no armazenamento do Azure são indexados usando o [indexador de armazenamento de blobs Azure Search](search-howto-indexing-azure-blob-storage.md). Você invoca esse indexador definindo o tipo e fornecendo informações de conexão que incluem uma conta de armazenamento do Azure junto com um contêiner de BLOB. A menos que você tenha organizado BLOBs anteriormente em um diretório virtual, que você pode passar como um parâmetro, o indexador de blob efetua pull de todo o contêiner.

Um indexador faz a "quebra de documento" e, depois de se conectar à fonte de dados, é a primeira etapa no pipeline. Para dados de BLOB, é o local em que os documentos PDF, Office docs, imagem e outros tipos de conteúdo são detectados. A quebra de documentos com a extração de texto não é cobrada. A quebra de documento com extração de imagem é cobrada a taxas que você pode encontrar na [página de preços](https://azure.microsoft.com/pricing/details/search/)de Azure Search.

Embora todos os documentos sejam rachados, o enriquecimento só ocorrerá se você fornecer explicitamente as habilidades para fazer isso. Por exemplo, se o pipeline consistir exclusivamente na análise de texto, todas as imagens em seu contêiner ou documentos serão ignoradas.

O indexador de blob vem com parâmetros de configuração e oferece suporte ao controle de alterações se os dados subjacentes fornecerem informações suficientes. Você pode saber mais sobre a funcionalidade básica no [indexador de armazenamento de blobs Azure Search](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai"></a>Adicionar ia

As *habilidades* são os componentes individuais do processamento de ia que você pode usar autônomo ou em combinação com outras habilidades para o processamento sequencial. 

+ As habilidades internas são apoiadas por serviços cognitivas, com análise de imagem baseada em Pesquisa Visual Computacional e processamento de linguagem natural com base em Análise de Texto. Alguns exemplos são [OCR](cognitive-search-skill-ocr.md), [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md)e [análise de imagem](cognitive-search-skill-image-analysis.md). Você pode examinar a lista completa de habilidades internas em [habilidades predefinidas para o enriquecimento de conteúdo](cognitive-search-predefined-skills.md).

+ Habilidades personalizadas são código personalizado, encapsulado em uma definição de interface que permite a integração no pipeline. Nas soluções de clientes, é uma prática comum usar ambos, com habilidades personalizadas que fornecem módulos de ia de software livre, de terceiros ou de terceiros.

Um conjunto *de qualificações é a* coleção de habilidades usadas em um pipeline e é invocado depois que a fase de quebra de documento disponibiliza o conteúdo. Um indexador pode consumir exatamente um qualificable, mas esse qualificable existe independentemente de um indexador para que você possa reutilizá-lo em outros cenários.

As habilidades personalizadas podem parecer complexas, mas podem ser simples e diretas em termos de implementação. Se você tiver pacotes existentes que fornecem modelos de classificação ou correspondência de padrões, o conteúdo extraído dos BLOBs poderá ser passado para esses modelos para processamento. Como a enriquecimento do ia é baseada no Azure, seu modelo também deve estar no Azure. Algumas metodologias de hospedagem comuns incluem o uso de [Azure Functions](cognitive-search-create-custom-skill-example.md) ou [contêineres](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

As habilidades internas apoiadas por serviços cognitivas exigem uma chave de assinatura All-in-One de [Serviços cognitivas anexada](cognitive-search-attach-cognitive-services.md) que fornece acesso ao recurso. Uma chave All-in-One fornece análise de imagem, detecção de idioma, tradução de texto e análise de texto. Outras habilidades internas são recursos de Azure Search e não exigem serviço ou chave adicional. A forma de texto, o divisor e a fusão são exemplos de habilidades auxiliares que às vezes são necessárias ao projetar o pipeline.

Se você usar apenas habilidades personalizadas e habilidades internas do utilitário, não haverá dependência ou custos relacionados a serviços cognitivas.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="how-to-use-ai-enriched-content"></a>Como usar o conteúdo aprimorado do ia

A saída do enriquecimento de AI é um índice de pesquisa em Azure Search ou uma loja de conhecimento no armazenamento do Azure.

No Azure Search, um índice de pesquisa é usado para exploração interativa usando texto livre e consultas filtradas em um aplicativo cliente. Os documentos aprimorados criados por meio do ia são formatados em JSON e indexados da mesma forma que todos os documentos são indexados em Azure Search, aproveitando todos os benefícios que um indexador fornece. Por exemplo, durante a indexação, o indexador de blob refere-se aos parâmetros de configuração e configurações para utilizar qualquer mapeamento de campo ou lógica de detecção de alteração. Essas configurações estão totalmente disponíveis para indexação regular e cargas de trabalho de ia aprimoradas. Após a indexação, quando o conteúdo é armazenado no Azure Search, você pode criar consultas avançadas e expressões de filtro para entender o conteúdo.

No armazenamento do Azure, uma loja de conhecimento tem duas manifestações: um contêiner de BLOB ou tabelas no armazenamento de tabelas. 

+ Um contêiner de blob captura documentos aprimorados em sua totalidade, o que é útil se você quiser alimentar outros processos. 

+ Por outro lado, o armazenamento de tabelas pode acomodar projeções físicas de documentos aprimorados. Você pode criar fatias ou camadas de documentos aprimorados que incluem ou excluem partes específicas. Para análise no Power BI, as tabelas no armazenamento de tabelas do Azure tornam-se a fonte de dados para visualização e exploração adicionais.

Um documento aprimorado no final do pipeline difere de sua versão de entrada original pela presença de campos adicionais que contêm novas informações que foram extraídas ou geradas durante o enriquecimento. Dessa forma, você pode trabalhar com uma combinação de conteúdo original e criado, independentemente da estrutura de saída usada.

## <a name="next-steps"></a>Passos seguintes

Há muito mais que você pode fazer com a enriquecimento de ia para tirar o máximo proveito de seus dados no armazenamento do Azure, incluindo a combinação de serviços cognitivas de maneiras diferentes e a criação de habilidades personalizadas para casos em que não há nenhum serviço de cognitiva existente para o cenário. Você pode aprender mais seguindo os links abaixo.

> [!div class="nextstepaction"]
> [Visão geral de enriquecimento de ia](cognitive-search-concept-intro.md) 
>  criar um dos nós de mapa de[qualificações](cognitive-search-defining-skillset.md)
> [em uma árvore de anotação](cognitive-search-output-field-mapping.md)

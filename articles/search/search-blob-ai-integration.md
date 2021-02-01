---
title: Use AI para enriquecer o conteúdo da bolha
titleSuffix: Azure Cognitive Search
description: Conheça as capacidades naturais de análise de linguagem e imagem na Pesquisa Cognitiva do Azure, e como esses processos se aplicam aos conteúdos armazenados em blobs Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222053"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Use AI para processar e analisar conteúdo blob em Pesquisa Cognitiva Azure

Os conteúdos no armazenamento Azure Blob que são compostos por imagens ou textos indiferenciados longos podem ser submetidos a análises de aprendizagem profunda para revelar e extrair informações valiosas úteis para aplicações a jusante. Ao utilizar [o enriquecimento de IA,](cognitive-search-concept-intro.md)pode:

+ Extrair texto de imagens utilizando reconhecimento de caracteres óticos (OCR)
+ Produzir uma descrição de cena ou etiquetas de uma foto
+ Detetar a linguagem e traduzir texto em diferentes línguas
+ Inferir estrutura através do reconhecimento de entidades, encontrando referências a pessoas, datas, locais ou organizações

Embora possa precisar apenas de uma destas capacidades de IA, é comum combinar vários deles no mesmo oleoduto (por exemplo, extrair texto de uma imagem digitalizada e, em seguida, encontrar todas as datas e locais referenciados nele). Também é comum incluir um processamento personalizado de IA ou machine learning sob a forma de pacotes externos de ponta ou modelos internos adaptados aos seus dados e requisitos.

Embora possa aplicar o enriquecimento de IA a qualquer fonte de dados suportada por um indexante de pesquisa, as bolhas são as estruturas mais utilizadas num oleoduto de enriquecimento. Os resultados são puxados para um índice de pesquisa para pesquisa completa de texto, ou reencaminhados de volta para O Azure Storage para alimentar novas experiências de aplicação que incluem explorar dados para cenários de descoberta ou analíticos. 

Neste artigo, vemos o enriquecimento de IA através de uma lente larga para que você possa rapidamente compreender todo o processo, desde a transformação de dados brutos em bolhas, até informações consultadas em um índice de pesquisa ou uma loja de conhecimento.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>O que significa "enriquecer" dados blob com IA

*O enriquecimento* de IA faz parte da arquitetura de indexação da Azure Cognitive Search que integra modelos de aprendizagem automática da Microsoft ou modelos de aprendizagem personalizados que fornece. Ajuda-o a implementar cenários de ponta a ponta onde é necessário processar bolhas (tanto as existentes como as novas à medida que entram ou são atualizadas), abre todos os formatos de ficheiros para extrair imagens e texto, extrair as informações desejadas utilizando várias capacidades de IA e indexá-las num índice de pesquisa para pesquisa rápida, recuperação e exploração. 

As entradas são as suas bolhas, num único recipiente, no armazenamento de Azure Blob. As bolhas podem ser quase qualquer tipo de texto ou dados de imagem. 

A saída é sempre um índice de pesquisa, usado para pesquisa rápida de texto, recuperação e exploração em aplicações de clientes. Além disso, a produção também pode ser uma [*loja de conhecimento*](knowledge-store-concept-intro.md) que projeta documentos enriquecidos em bolhas Azure ou mesas Azure para análise a jusante em ferramentas como o Power BI ou em cargas de trabalho de ciência de dados.

Pelo meio está a arquitetura do gasoduto em si. O pipeline baseia-se nos [*indexantes*](search-indexer-overview.md), aos quais pode atribuir um [*skillset,*](cognitive-search-working-with-skillsets.md)que é composto por uma ou mais *competências* que fornecem a IA. O objetivo do gasoduto é produzir *documentos enriquecidos* que entrem no gasoduto como conteúdo bruto, mas recolham estrutura, contexto e informação adicionais enquanto se deslocam através do oleoduto. Os documentos enriquecidos são consumidos durante a indexação para criar índices invertidos e outras estruturas utilizadas na pesquisa ou exploração e análise de texto completos.

## <a name="required-resources"></a>Recursos necessários

Além do armazenamento Azure Blob e da Pesquisa Cognitiva Azure, precisa de um terceiro serviço ou mecanismo que forneça a IA:

+ Para IA incorporada, a Pesquisa Cognitiva integra-se com a visão dos Serviços Cognitivos Azure e as APIs de processamento de linguagem natural. Pode [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) para adicionar Reconhecimento Ótico de Caracteres (OCR), análise de imagem ou processamento de linguagem natural (deteção de linguagem, tradução de texto, reconhecimento de entidades, extração de frases-chave). 

+ Para a IA personalizada utilizando recursos Azure, pode definir uma habilidade personalizada que envolve a função externa ou modelo que pretende utilizar. [As habilidades personalizadas](cognitive-search-custom-skill-interface.md) podem utilizar código fornecido por Azure Machine Learning, Azure Machine Learning, Azure Form Recogniser ou outro recurso que seja acessível através do HTTPS.

+ Para a IA não-Azure personalizada, o seu modelo ou módulo tem de estar acessível a um indexante sobre HTTP.

Se não tiver todos os serviços prontamente disponíveis, comece diretamente na página do portal da conta de Armazenamento. Na página de navegação esquerda, em **serviço Blob** clique em **Adicionar Azure Cognitive Search** para criar um novo serviço ou selecionar um existente. 

Assim que adicionar a Azure Cognitive Search à sua conta de armazenamento, pode seguir o processo padrão para enriquecer dados em qualquer fonte de dados do Azure. Recomendamos o assistente **de dados de importação** em Azure Cognitive Search para uma introdução inicial fácil ao enriquecimento de IA. Pode anexar um recurso de Serviços Cognitivos durante o fluxo de trabalho. Este quickstart acompanha-o através dos degraus: [Criar um gasoduto de enriquecimento de IA no portal](cognitive-search-quickstart-blob.md). 

As seguintes secções analisam mais de perto os componentes e o fluxo de trabalho.

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

O enriquecimento de IA é um complemento a um gasoduto de indexação, e na Pesquisa Cognitiva Azure, esses oleodutos são construídos em cima de um *indexante.* Um indexante é um subserviço consciente de dados equipado com lógica interna para amostragem de dados, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para posterior importação. Os indexantes são frequentemente usados por si mesmos para importar, separados da IA, mas se você quiser construir um pipeline de enriquecimento de IA, você precisará de um indexante e um skillset para acompanhar. Esta secção destaca o indexante; a próxima secção centra-se em skillsets.

As bolhas no Azure Storage são indexadas utilizando o [indexante blob](search-howto-indexing-azure-blob-storage.md). Pode invocar este indexante utilizando o assistente **de dados De importação,** uma API REST ou um SDK. Um indexante blob é invocado quando a fonte de dados utilizada pelo indexante é um recipiente Azure Blob. Pode indexar um subconjunto das suas bolhas criando um diretório virtual, que pode passar como parâmetro, ou filtrando uma extensão do tipo de ficheiro.

Um indexante faz o "documento rachar", abrindo uma bolha para inspecionar o conteúdo. Depois de ligar à fonte de dados, é o primeiro passo no oleoduto. Para os dados blob, é aqui que são detetados PDF, docs de escritório, imagem e outros tipos de conteúdo. A rachadura de documento com a extração de texto não é cobrado. O documento que quebra com a extração de imagem é cobrado a preços que pode encontrar na [página de preços](https://azure.microsoft.com/pricing/details/search/).

Embora todos os documentos sejam rachados, o enriquecimento só ocorre se fornecer explicitamente as habilidades para o fazer. Por exemplo, se o seu pipeline consistir exclusivamente em análise de imagem, o texto no seu recipiente ou documentos é ignorado.

O indexante blob vem com parâmetros de configuração e suporta o rastreio de alterações se os dados subjacentes fornecerem informações suficientes. Pode aprender mais em [Como configurar um indexante blob](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Adicionar componentes de IA

O enriquecimento de IA refere-se a módulos que procuram padrões ou características, e depois executa uma operação em conformidade. O reconhecimento facial em fotos, descrições de texto de fotografias, deteção de frases-chave num documento, e OCR (ou reconhecer texto impresso ou manuscrito em ficheiros binários) são exemplos ilustrativos.

Na Azure Cognitive Search, *as habilidades* são os componentes individuais do processamento de IA que você pode usar autónomo, ou em combinação com outras habilidades. 

+ As competências incorporadas são apoiadas pelos Serviços Cognitivos, com análise de imagem baseada na Visão Computacional, e processamento de linguagem natural com base em Text Analytics. Para obter a lista completa, consulte [as competências incorporadas para enriquecimento de conteúdos.](cognitive-search-predefined-skills.md)

+ As habilidades personalizadas são código personalizado, envolto numa [definição de interface](cognitive-search-custom-skill-interface.md) que permite a integração no pipeline. Nas soluções de cliente, é prática comum usar ambos, com habilidades personalizadas que fornecem módulos de IA de código aberto, de terceiros ou de primeira parte.

Um *skillset* é a coleção de habilidades usadas num oleoduto, e é invocada após a fase de cracking documental disponibilizar conteúdo. Um indexante pode consumir exatamente uma habilidade, mas essa habilidade existe independentemente de um indexante para que você possa reutilizá-lo em outros cenários.

As habilidades personalizadas podem parecer complexas, mas podem ser simples e simples em termos de implementação. Se tiver pacotes existentes que forneçam modelos de correspondência ou classificação de padrões, o conteúdo que extrai de bolhas pode ser passado para estes modelos para processamento. Uma vez que o enriquecimento de IA é baseado em Azure, o seu modelo também deve estar no Azure. Algumas metodologias comuns de hospedagem incluem a utilização de [Funções Azure](cognitive-search-create-custom-skill-example.md) ou [Contentores.](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)

As competências incorporadas apoiadas pelos Serviços Cognitivos requerem uma chave de subscrição de [Serviços Cognitivos anexada](cognitive-search-attach-cognitive-services.md) que lhe dá acesso ao recurso. Uma chave tudo-em-um dá-lhe análise de imagem, deteção de linguagem, tradução de texto e análise de texto. Outras habilidades incorporadas são características da Azure Cognitive Search e não requerem nenhum serviço ou chave adicional. O shaper de texto, o splitter e a fusão são exemplos de habilidades de ajuda que por vezes são necessárias ao conceber o pipeline.

Se utilizar apenas habilidades personalizadas e competências de utilidade incorporadas, não existe dependência ou custos relacionados com os Serviços Cognitivos.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumir produção enriquecida em IA em soluções a jusante

A produção do enriquecimento de IA é um índice de pesquisa na Azure Cognitive Search, ou uma [loja de conhecimento](knowledge-store-concept-intro.md) no Azure Storage.

Na Pesquisa Cognitiva Azure, um índice de pesquisa é usado para exploração interativa usando texto gratuito e consultas filtradas em uma aplicação do cliente. Os documentos enriquecidos criados através da IA são formatados em JSON e indexados da mesma forma que todos os documentos são indexados na Pesquisa Cognitiva Azure, aproveitando todos os benefícios que um indexante proporciona. Por exemplo, durante a indexação, o indexante blob refere-se a parâmetros de configuração e definições para utilizar quaisquer mapeamentos de campo ou alterar a lógica de deteção. Tais configurações estão totalmente disponíveis para indexação regular e cargas de trabalho enriquecidas em IA. A pós-indexação, quando o conteúdo é armazenado na Azure Cognitive Search, pode construir consultas ricas e expressões de filtro para entender o seu conteúdo.

No Azure Storage, uma loja de conhecimento tem duas manifestações: um recipiente blob, ou mesas em armazenamento de mesa. 

+ Um recipiente blob captura documentos enriquecidos na sua totalidade, o que é útil se quiser alimentar-se de outros processos. 

+ Em contraste, o armazenamento de mesa pode acomodar projeções físicas de documentos enriquecidos. Pode criar fatias ou camadas de documentos enriquecidos que incluam ou excluam peças específicas. Para análise em Power BI, as tabelas no armazenamento da Tabela Azure tornam-se a fonte de dados para posterior visualização e exploração.

Um documento enriquecido no final do gasoduto difere da sua versão inicial de entrada pela presença de campos adicionais que contenham novas informações que foram extraídas ou geradas durante o enriquecimento. Como tal, pode trabalhar com uma combinação de conteúdo original e criado, independentemente da estrutura de saída que utilizar.

## <a name="next-steps"></a>Passos seguintes

Há muito mais que você pode fazer com o enriquecimento de IA para tirar o máximo partido dos seus dados no Azure Storage, incluindo combinar Serviços Cognitivos de diferentes maneiras, e autoria de habilidades personalizadas para casos em que não existe serviço cognitivo existente para o cenário. Pode saber mais seguindo os links abaixo.

+ [Upload, download e liste blobs com o portal Azure (armazenamento Azure Blob)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configurar um indexante blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Visão geral do enriquecimento de IA (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Criar um skillset (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Nós de mapa em uma árvore de anotação (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
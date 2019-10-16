---
title: Adicionar pesquisa de texto completo ao armazenamento de BLOBs do Azure
titleSuffix: Azure Search
description: Extraia o conteúdo e adicione a estrutura aos BLOBs do Azure ao criar um índice de pesquisa de texto completo no Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331283"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Adicionar pesquisa de texto completo aos dados de blob do Azure usando Azure Search

Pesquisar na variedade de tipos de conteúdo armazenados no armazenamento de BLOBs do Azure pode ser um problema difícil de resolver. No entanto, você pode indexar e pesquisar o conteúdo de seus BLOBs em apenas alguns cliques usando [Azure Search](search-what-is-azure-search.md). Azure Search tem integração interna para indexação fora do armazenamento de BLOBs por meio de um [*indexador de blob*](search-howto-indexing-azure-blob-storage.md) que adiciona recursos de reconhecimento de fonte de dados à indexação.

## <a name="supported-content-types"></a>Tipos de conteúdo com suporte

Ao executar um indexador de BLOB em um contêiner, você pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Opcionalmente, você pode anexar o [enriquecimento de ai](search-blob-ai-integration.md) na forma de um *qualificador* para criar novas informações e estrutura de BLOBs, incluindo representações de texto de arquivos de imagem. A adição do enriquecimento de ia expande a lista tipo de conteúdo para incluir JPEG e PNG. Ele adiciona habilidades de processamento de imagens, como [OCR (reconhecimento óptico de caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md) , que possibilitam a indexação do conteúdo Visual encontrado em cada imagem.

## <a name="search-through-your-blob-metadata"></a>Pesquisar seus metadados de BLOB
Um cenário comum que torna mais fácil a classificação por meio de blobs de qualquer tipo de conteúdo é indexar metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações de todos os BLOBs são indexadas independentemente do tipo de documento, armazenados em um índice em Azure Search. Usando seu novo índice, você pode continuar a classificar, filtrar e facetar em todo o conteúdo do armazenamento de BLOBs.

### <a name="indexing-json-blobs"></a>Indexando BLOBs JSON
Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado em BLOBs que contêm JSON. Azure Search pode ler BLOBs JSON e analisar o conteúdo estruturado nos campos apropriados de um documento Azure Search. Azure Search também pode pegar BLOBs que contêm uma matriz de objetos JSON e mapear cada elemento para um documento Azure Search separado. Você pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexador.

## <a name="how-to-get-started"></a>Como começar

Você pode iniciar diretamente em sua página do portal da conta de armazenamento. Clique em **adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço de Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será navegado para fora da experiência do portal da sua conta de armazenamento. Você pode navegar de volta para a página do portal de armazenamento e selecionar novamente a opção **adicionar Azure Search** , na qual você pode selecionar o serviço existente. 

![](./media/search-blob-storage-integration/blob-blade.png)

Se você já tiver um serviço de pesquisa existente na mesma assinatura, clicar em **adicionar Azure Search** abrirá o assistente de importação de dados para que você possa percorrer imediatamente a indexação, o enriquecimento e a definição de índice.

Você também pode [criar um serviço de Azure Search](search-create-index-portal.md) e definir indexadores de BLOB que efetuam pull de conteúdo de contêineres de BLOB.

Os seguintes guias de início rápido e tutoriais usam dados de blob:

+ [Indexar BLOBs semiestruturados usando APIs REST](search-semi-structured-data.md)
+ [Criar um pipeline de enriquecimento de ia no portal](cognitive-search-quickstart-blob.md)
+ [Criar um pipeline de enriquecimento de ia emC#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar um indexador de BLOB](search-howto-indexing-azure-blob-storage.md) 

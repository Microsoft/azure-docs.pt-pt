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
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302905"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Adicionar pesquisa de texto completo aos dados de blob do Azure usando Azure Search

Pesquisar na variedade de tipos de conteúdo armazenados no armazenamento de BLOBs do Azure pode ser um problema difícil de resolver. No entanto, você pode indexar e pesquisar o conteúdo de seus BLOBs em apenas alguns cliques usando Azure Search. Pesquisar por meio do armazenamento de BLOBs requer o provisionamento de um serviço de Azure Search. Os vários limites de serviço e os tipos de preço do Azure Search podem ser encontrados na [página de preços](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>O que é o Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) é um serviço de pesquisa que facilita para os desenvolvedores a adição de experiências robustas de pesquisa de texto completo a aplicativos Web e móveis. Como um serviço, Azure Search remove a necessidade de gerenciar qualquer infraestrutura de pesquisa, oferecendo um [SLA de tempo de atividade de 99,9%](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indexar e Pesquisar formatos de documentos empresariais
Com suporte para [extração de documentos](https://aka.ms/azsblobindexer) no armazenamento de BLOBs do Azure, você pode indexar o seguinte conteúdo:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Ao extrair o texto e os metadados desses tipos de arquivo, você pode pesquisar em vários formatos de arquivo com uma única consulta. 

## <a name="search-through-your-blob-metadata"></a>Pesquisar seus metadados de BLOB
Um cenário comum que torna mais fácil a classificação por meio de blobs de qualquer tipo de conteúdo é indexar metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações de todos os BLOBs são indexadas independentemente do tipo de documento. Em seguida, você pode continuar a classificar, filtrar e facetar em todo o conteúdo do armazenamento de BLOBs.

[Saiba mais sobre como indexar metadados de BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Pesquisa de imagem
Os recursos de pesquisa de texto completo Azure Search, navegação facetada e classificação agora podem ser aplicados aos metadados de imagens armazenadas em BLOBs.

A pesquisa cognitiva inclui habilidades de processamento de imagem, como [OCR (reconhecimento óptico de caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md) que possibilitam a indexação do conteúdo Visual encontrado em cada imagem.

## <a name="index-and-search-through-json-blobs"></a>Indexar e Pesquisar por BLOBs JSON
Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado em BLOBs que contêm JSON. Azure Search pode ler BLOBs JSON e analisar o conteúdo estruturado nos campos apropriados de um documento Azure Search. Azure Search também pode pegar BLOBs que contêm uma matriz de objetos JSON e mapear cada elemento para um documento Azure Search separado.

A análise JSON não é configurável no momento por meio do Portal. [Saiba mais sobre a análise de JSON no Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Início Rápido
Azure Search pode ser adicionado aos BLOBs diretamente da página do portal de armazenamento de BLOBs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço de Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será navegado para fora da experiência do portal da sua conta de armazenamento. Você pode navegar de volta para a página do portal de armazenamento e selecionar novamente a opção **adicionar Azure Search** , na qual você pode selecionar o serviço existente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o indexador de blob Azure Search na [documentação](https://aka.ms/azsblobindexer)completa.

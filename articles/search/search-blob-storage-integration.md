---
title: Adicionar pesquisa de texto completo ao armazenamento de BLOBs do Azure-Azure Search
description: Rastreie o conteúdo de texto no armazenamento de BLOBs do Azure para Azure Search indexação, no código usando a API REST HTTP.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650092"
---
# <a name="searching-blob-storage-with-azure-search"></a>Pesquisar o armazenamento de Blobs com o Azure Search

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

## <a name="quick-start"></a>Início rápido
Azure Search pode ser adicionado aos BLOBs diretamente da página do portal de armazenamento de BLOBs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço de Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será navegado para fora da experiência do portal da sua conta de armazenamento. Você pode navegar de volta para a página do portal de armazenamento e selecionar novamente a opção **adicionar Azure Search** , na qual você pode selecionar o serviço existente.

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre o indexador de blob Azure Search na [documentação](https://aka.ms/azsblobindexer)completa.

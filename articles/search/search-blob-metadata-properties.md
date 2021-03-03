---
title: Propriedades de metadados de conteúdo
titleSuffix: Azure Cognitive Search
description: As propriedades dos metadados dos documentos podem fornecer conteúdo a campos num índice de pesquisa, ou informações que informam o comportamento de indexação no tempo de execução. Este artigo lista propriedades de metadados suportadas na Pesquisa Cognitiva Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668423"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Propriedades de metadados de conteúdo utilizadas na Pesquisa Cognitiva Azure

O armazenamento de blob Do SharePoint Online e Azure pode conter vários conteúdos, e muitos desses tipos de conteúdo têm propriedades de metadados que podem ser úteis para indexar. Assim como pode criar campos de pesquisa para propriedades blob padrão **`metadata_storage_name`** como, você pode criar campos para propriedades de metadados que são específicos de um formato de documento.

## <a name="supported-document-formats"></a>Formatos de documento suportados

A Cognitive Search suporta a indexação de blob e a indexação de documentos Do SharePoint Online para os seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Propriedades por formato de documento

A tabela seguinte resume o processamento feito para cada formato de documento, e descreve as propriedades de metadados extraídas por um indexante blob e o indexante SharePoint Online.

| Formato documental / tipo de conteúdo | Metadados extraídos | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Strip HTML marcação e texto de extrato |
| PDF (aplicação/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados (excluindo imagens) |
| DOCX (aplicação/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| DOC (aplicação/palavra) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| DOCM (aplicação/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| WORD XML (aplicação/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Strip XML marcação e texto de extrato |
| PALAVRA 2003 XML (aplicação/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Strip XML marcação e texto de extrato |
| XLSX (aplicação/vnd.openxmlformats-officedocument.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| XLS (aplicação/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| XLSM (aplicação/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| PPTX (aplicação/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| PPT (aplicação/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| PPTM (aplicação/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Texto de extração, incluindo documentos incorporados |
| MSG (aplicação/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo texto extraído de anexos. `metadata_message_to_email`, `metadata_message_cc_email` e `metadata_message_bcc_email` são coleções de cordas, o resto dos campos são cordas.|
| ODT (aplicação/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Texto de extração, incluindo documentos incorporados |
| ODS (aplicação/vnd.oasis.opendocument.sheet de cálculo) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Texto de extração, incluindo documentos incorporados |
| ODP (aplicação/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Texto de extração, incluindo documentos incorporados |
| ZIP (aplicação/fecho) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| GZ (aplicação/gzip) |`metadata_content_type` |Extrair texto de todos os documentos do arquivo |
| EPUB (aplicação/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extrair texto de todos os documentos do arquivo |
| XML (aplicação/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Strip XML marcação e texto de extrato |
| JSON (aplicação/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>NOTA: Se necessitar de extrair vários campos de documentos de uma bolha JSON, consulte [as bolhas JSON indexantes](search-howto-index-json-blobs.md) para obter mais detalhes |
| EML (mensagem/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Texto de extração, incluindo anexos |
| RTF (aplicação/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto simples (texto/planície) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|

## <a name="see-also"></a>Ver também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Compreender bolhas usando IA](search-blob-ai-integration.md)
* [Visão geral da indexação da bolha](search-blob-storage-integration.md)
* [Indexação online sharePoint](search-howto-index-sharepoint-online.md)
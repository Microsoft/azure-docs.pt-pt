---
title: Pesquisa sobre Azure Data Lake Storage Gen2 (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como indexar conteúdo e metadados na Azure Data Lake Storage Gen2. Esta funcionalidade está atualmente em pré-visualização pública
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545569"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Documentos de indexação em Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> O suporte da Azure Data Lake Storage Gen2 está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pode solicitar o acesso às pré-visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [versão REST API 2020-06-30-Preview](search-api-preview.md) e portal fornecem esta funcionalidade. Atualmente não existe suporte .NET SDK.


Ao configurar uma conta de armazenamento Azure, tem a opção de ativar o [espaço hierárquico](../storage/blobs/data-lake-storage-namespace.md)de nomes . Isto permite que a recolha de conteúdos numa conta seja organizada numa hierarquia de diretórios e subdiretórios aninhados. Ao ativar o espaço hierárquico de nomes, você ativa [a Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Este artigo descreve como começar com documentos de indexação que estão no Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurar o indexador Azure Data Lake Storage Gen2

Existem alguns passos que precisará de completar para indexar o conteúdo da Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Passo 1: Inscreva-se para a pré-visualização

Inscreva-se para a pré-visualização do indexante Gen2 de armazenamento de dados preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Receberá um e-mail de confirmação assim que tiver sido aceite na pré-visualização.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Passo 2: Siga os passos de configuração de indexação de armazenamento Azure Blob

Assim que tiver recebido a confirmação de que a sua pré-visualização foi bem sucedida, está pronto para criar o pipeline de indexação.

Pode indexar conteúdo e metadados a partir da Data Lake Storage Gen2 utilizando a [versão REST API 2020-06-30-Preview](search-api-preview.md) ou o portal. Não existe suporte .NET SDK neste momento.

A indexação do conteúdo na Data Lake Storage Gen2 é idêntica à indexação do conteúdo no armazenamento de Azure Blob. Assim, para entender como configurar a fonte de dados, índice e indexante do Data Lake Storage Gen2, consulte como [indexar documentos no Azure Blob Storage com Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). O artigo de armazenamento Blob também fornece informações sobre quais os formatos de documentos suportados, quais as propriedades de metadados blob extraídas, indexação incremental, e muito mais. Esta informação será a mesma para data lake storage gen2.

## <a name="access-control"></a>Controlo de acesso

A Azure Data Lake Storage Gen2 implementa um modelo de controlo de [acesso](../storage/blobs/data-lake-storage-access-control.md) que suporta tanto o controlo de acesso baseado em funções Azure (Azure RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Ao indexar o conteúdo da Data Lake Storage Gen2, a Azure Cognitive Search não extrairá do conteúdo a informação do Azure RBAC e da ACL. Como resultado, esta informação não será incluída no seu índice de Pesquisa Cognitiva Azure.

Se a manutenção do controlo de acesso em cada documento do índice for importante, cabe ao desenvolvedor da aplicação implementar [aparas de segurança](./search-security-trimming-for-azure-search.md).

## <a name="change-detection"></a>Deteção de Alterações

O indexante da Gen2 de armazenamento de dados suporta a deteção de alterações. Isto significa que quando o indexante executa, apenas reindexiza as bolhas alteradas como determinado pela estada de tempo da `LastModified` bolha.

> [!NOTE] 
> Data Lake Storage Gen2 permite que os diretórios sejam renomeados. Quando um diretório é renomeado os timetamps para as bolhas nesse diretório não são atualizados. Como resultado, o indexante não reindexuais essas bolhas. Se precisar que as bolhas de um diretório sejam reexexuadas após um rebatizador de diretório porque agora têm novos URLs, terá de atualizar a `LastModified` data de pontuação para todas as bolhas no diretório para que o indexante saiba reindexá-las durante uma corrida futura.
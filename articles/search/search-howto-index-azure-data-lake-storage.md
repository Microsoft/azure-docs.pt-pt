---
title: Pesquisa sobre Azure Data Lake Storage Gen2 (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Saiba como indexar conteúdo e metadados em Azure Data Lake Storage Gen2. Esta funcionalidade encontra-se atualmente em pré-visualização pública
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905654"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Documentos de indexação em Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> O suporte do Azure Data Lake Storage Gen2 está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . Pode solicitar acesso às pré-visualizações preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece esta funcionalidade. Atualmente não existe nenhum suporte de Portal ou .NET SDK.


Ao configurar uma conta de armazenamento Azure, tem a opção de ativar o espaço de [nome hierárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Isto permite que a recolha de conteúdos numa conta seja organizada numa hierarquia de diretórios e subdiretórios aninhados. Ao permitir espaço de nome hierárquico, permite o [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Este artigo descreve como começar com documentos indexantes que estão em Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurar o indexador de armazenamento de lagos Azure Data Gen2

Há alguns passos que terá de completar para indexar o conteúdo do Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Passo 1: Inscreva-se para a pré-visualização

Inscreva-se na pré-visualização do indexer de armazenamento de data Lake Gen2 preenchendo [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Receberá um e-mail de confirmação assim que tiver sido aceite na pré-visualização.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Passo 2: Siga os passos de configuração de armazenamento de Blob Azure

Assim que tiver recebido a confirmação de que a sua inscrição de pré-visualização foi bem sucedida, está pronto para criar o pipeline de indexação.

Pode indexar conteúdo e metadados a partir de Data Lake Storage Gen2 utilizando a [versão REST API 2019-05-06-Preview](search-api-preview.md). Não existe nenhum suporte de Portal ou .NET SDK neste momento.

O conteúdo indexante no Data Lake Storage Gen2 é idêntico ao conteúdo indexante no armazenamento de Blob Azure. Para entender como configurar a fonte de dados, índice e indexador de dados do Data Lake Storage Gen2, consulte [como indexar documentos no Armazenamento de Blob Azure com pesquisa cognitiva Azure](search-howto-indexing-azure-blob-storage.md). O artigo de armazenamento blob também fornece informações sobre quais os formatos de documentos suportados, que propriedades de metadados blob são extraídas, indexação incremental, e muito mais. Esta informação será a mesma para data lake storage Gen2.

## <a name="access-control"></a>Controlo de acesso

O Azure Data Lake Storage Gen2 implementa um modelo de controlo de [acesso](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) que suporta tanto as listas de controlo de acesso baseados em funções azure (RBAC) como as listas de controlo de acesso semelhantes a POSIX (ACLs). Ao indexar o conteúdo do Data Lake Storage Gen2, a Pesquisa Cognitiva Azure não extrairá as informações rBAC e ACL do conteúdo. Como resultado, esta informação não será incluída no seu índice de Pesquisa Cognitiva Azure.

Se manter o controlo de acesso em cada documento no índice é importante, cabe ao desenvolvedor de aplicações implementar o [aparador de segurança](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Deteção de Alterações

O indexante de armazenamento de data lake Gen2 suporta a deteção de alterações. Isto significa que quando o indexante funciona, apenas reindexe as bolhas `LastModified` alteradas, tal como determinado si, pelo carimbo temporal da bolha.

> [!NOTE] 
> Data Lake Storage Gen2 permite que os diretórios sejam renomeados. Quando um diretório é renomeado os carimbos temporais para as bolhas nesse diretório não são atualizados. Como resultado, o indexante não irá reindexar essas bolhas. Se precisa que as bolhas de um diretório sejam reindexadas após um renome de diretório porque `LastModified` agora têm novos URLs, terá de atualizar a marca de tempo para todas as bolhas do diretório para que o indexante saiba reindexá-las durante uma futura execução.

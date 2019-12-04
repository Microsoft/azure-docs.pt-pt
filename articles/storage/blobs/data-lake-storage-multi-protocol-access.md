---
title: Acesso de vários protocolos em Azure Data Lake Storage | Microsoft Docs
description: Use APIs de BLOB e aplicativos que usam APIs de blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: aebd7b58a2107ac109a03d3ce12f27d65ba0ab90
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769817"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acesso de vários protocolos no Azure Data Lake Storage

As APIs de blob agora funcionam com contas que têm um namespace hierárquico. Isso desbloqueia o ecossistema de ferramentas, aplicativos e serviços, bem como vários recursos de armazenamento de BLOBs para contas que têm um namespace hierárquico.

Até recentemente, talvez você precise manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isso ocorre porque Azure Data Lake Storage Gen2 tinha suporte limitado a ecossistema. Ele também tinha acesso limitado aos recursos do serviço BLOB, como o log de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter, pois você precisa mover dados entre contas para realizar vários cenários. Você não precisa mais fazer isso.

Com o acesso de vários protocolos no Data Lake Storage, você pode trabalhar com seus dados usando o ecossistema de ferramentas, aplicativos e serviços. Isso também inclui ferramentas e aplicativos de terceiros. Você pode apontar para as contas que têm um namespace hierárquico sem precisar modificá-las. Esses aplicativos funcionam *como estão* , mesmo se chamarem APIs de BLOB, porque as APIs de blob podem agora operar em dados em contas que têm um namespace hierárquico.

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) , agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento de BLOBs sem perder o acesso a esses recursos importantes. 

> [!NOTE]
> O acesso de vários protocolos no Data Lake Storage está geralmente disponível e está disponível em todas as regiões. Alguns serviços do Azure ou recursos de armazenamento de BLOBs habilitados pelo acesso de vários protocolos permanecem em versão prévia. Consulte as tabelas em cada seção deste artigo para obter mais informações. 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso de vários protocolos no armazenamento do data Lake

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados em contas de armazenamento que têm um namespace hierárquico. Data Lake Storage Gen2 roteia as APIs de blob por meio do namespace hierárquico para que você possa obter os benefícios das operações de diretório de primeira classe e das ACLs (listas de controle de acesso) compatíveis com POSIX. 

![Acesso de vários protocolos em Data Lake Storage conceitual](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e os aplicativos existentes que usam a API de blob obterão esses benefícios automaticamente. Os desenvolvedores não precisarão modificá-los. Data Lake Storage Gen2 aplica de forma consistente ACLs de nível de arquivo e de diretório, independentemente do protocolo que as ferramentas e os aplicativos usam para acessar os dados. 

## <a name="blob-storage-feature-support"></a>Suporte ao recurso de armazenamento de BLOBs

O acesso de vários protocolos no Data Lake Storage permite que você use mais recursos de armazenamento de BLOBs com seu Data Lake Storage. Esta tabela lista os recursos que são habilitados pelo acesso de vários protocolos no Data Lake Storage. 

Os itens que aparecem nesta tabela serão alterados ao longo do tempo, pois o suporte para recursos de armazenamento de BLOBs continua a expandir. 

> [!NOTE]
> Embora o acesso de vários protocolos no Data Lake Storage esteja geralmente disponível, o suporte para alguns desses recursos permanece em versão prévia. 

|Recurso de armazenamento de BLOBs | Nível de suporte |
|---|---|
|[Camada de acesso fria](storage-blob-storage-tiers.md)|Disponível para o público geral|
|APIs REST de BLOB|Disponível para o público geral|
|SDKs de BLOB |Disponível para o público geral|
|[PowerShell (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |Disponível para o público geral|
|[CLI (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |Disponível para o público geral|
|SDKs de blob com semântica do sistema de arquivos ([.net](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|Pré-visualização|
|[PowerShell com semântica de sistema de arquivos](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|Pré-visualização|
|[CLI com semântica do sistema de arquivos](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|Pré-visualização|
|[Registos de diagnóstico](../common/storage-analytics-logging.md)| Pré-visualização|
|[Políticas de gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md)| Pré-visualização|
|[Notificações por meio da grade de eventos do Azure](data-lake-storage-events.md)|Pré-visualização|
|[Camada de acesso de arquivo](storage-blob-storage-tiers.md)| Pré-visualização|
|[blobfuse](storage-how-to-mount-container-linux.md)|Ainda não tem suporte|
|[Armazenamento imutável](storage-blob-immutable-storage.md)|Ainda não tem suporte|
|[Instantâneos](storage-blob-snapshots.md)|Ainda não tem suporte|
|[Exclusão reversível](storage-blob-soft-delete.md)|Ainda não tem suporte|
|[Sites estáticos](storage-blob-static-website.md)|Ainda não tem suporte|

Para saber mais sobre problemas conhecidos e limitações gerais com o Azure Data Lake Storage Gen2, consulte [problemas conhecidos](data-lake-storage-known-issues.md).

## <a name="azure-ecosystem-support"></a>Suporte a ecossistema do Azure

O acesso multiprotocolo no Data Lake Storage também permite que você se conecte a mais serviços do Azure com seu Data Lake Storage. Esta tabela lista os serviços que são habilitados pelo acesso de vários protocolos no Data Lake Storage. 

Assim como a lista de recursos de armazenamento de BLOBs com suporte, os itens que aparecem nessa tabela serão alterados ao longo do tempo, pois o suporte aos serviços do Azure continuará a ser expandido. 

> [!NOTE]
> Embora o acesso de vários protocolos no Data Lake Storage esteja geralmente disponível, o suporte para alguns desses serviços permanece em versão prévia. 

|Serviço do Azure | Nível de suporte |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|Disponível para o público geral|
|[Captura de hubs de eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|Disponível para o público geral|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|Disponível para o público geral|
|[Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|Disponível para o público geral|
|[Aplicativos lógicos](https://azure.microsoft.com/services/logic-apps/)|Disponível para o público geral|
|[Pesquisa Cognitiva do Azure](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|Pré-visualização|

Para obter a lista completa de suporte a ecossistema do Azure para Data Lake Storage Gen2, consulte [integrar Azure data Lake Storage com os serviços do Azure](data-lake-storage-integrate-with-azure-services.md).

Para saber mais sobre problemas conhecidos e limitações gerais com o Azure Data Lake Storage Gen2, consulte [problemas conhecidos](data-lake-storage-known-issues.md).

## <a name="next-steps"></a>Passos seguintes

Consulte [problemas conhecidos](data-lake-storage-known-issues.md)





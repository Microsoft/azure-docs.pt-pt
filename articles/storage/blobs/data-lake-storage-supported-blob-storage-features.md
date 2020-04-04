---
title: Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Saiba quais as funcionalidades de armazenamento blob que pode utilizar com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637219"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2

As funcionalidades de armazenamento blob, tais como [a exploração madeireira de diagnóstico,](../common/storage-analytics-logging.md)os níveis de [acesso,](storage-blob-storage-tiers.md)e as políticas de gestão do ciclo de [vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço de nome hierárquico. Por isso, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder acesso a estas funcionalidades.

Esta tabela lista as funcionalidades de armazenamento Blob que pode utilizar com o Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas vão mudar ao longo do tempo à medida que o apoio continua a expandir-se.

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento Blob suportados

> [!NOTE]
> O nível de suporte refere-se apenas à forma como a funcionalidade é suportada com data lake storage Gen2.

|Recurso de armazenamento blob |Nível de apoio |Artigos relacionados |
|---------------|-------------------|---|
|Escalão de acesso frequente|Disponível em Geral|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Escalão de acesso esporádico|Disponível em Geral|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível em Geral|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível em Geral|[Métricas de análise de armazenamento azure (Clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível em Geral|[Métricas do Armazenamento do Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento blob|Disponível em Geral|[Quickstart: Upload, download e lista de blobs com PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos Azure CLI de armazenamento blob|Disponível em Geral|[Quickstart: Criar, descarregar e listar bolhas com o Azure CLI](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de blob|Disponível em Geral|[Quickstart: Biblioteca de clientes de armazenamento Azure Blob v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerir bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerir bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Nível de acesso de arquivo|Pré-visualização|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gestão do ciclo de vida|Pré-visualização|[Gerir o ciclo de vida do Armazenamento de blobs do Azure](storage-lifecycle-management-concepts.md)|
|Registos de diagnósticos|Disponível em Geral|[Registo da análise de Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed de alterações|Ainda não apoiado|[Alterar suporte para alimentação no armazenamento de Blob Azure](storage-blob-change-feed.md)|
|Falha na conta|Ainda não apoiado|[Recuperação de desastres e falha na conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Contentor blob ACL|Ainda não apoiado|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não apoiado|[Mapear um domínio personalizado para um ponto final de armazenamento De Blob Azure](storage-custom-domain-name.md)|
|Armazenamento imutável|Ainda não apoiado|[Armazenar dados de blob críticos de negócios com armazenamento imutável](storage-blob-immutable-storage.md)|
|Instantâneos|Ainda não apoiado|[Crie e gerencie um instantâneo blob em .NET](storage-blob-snapshots.md)|
|Eliminação de Forma Recuperável|Ainda não apoiado|[Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](storage-blob-soft-delete.md)|
|Web sites estáticos|Ainda não apoiado|[Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)|
|Exploração madeireira no Monitor Azure|Ainda não apoiado|Ainda não está disponível|
|Bolhas de bloco premium|Ainda não apoiado|[Criar uma conta BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Consulte também

- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código aberto que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure](data-lake-storage-multi-protocol-access.md)
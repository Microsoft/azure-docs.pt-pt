---
title: Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2 Microsoft Docs
description: Saiba quais as funcionalidades de armazenamento blob que pode usar com a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 15e6cf7adfda995148a75ec21b8d8e5d8a5cab2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559062"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2

As funcionalidades de Armazenamento blob, tais como [o registo de diagnóstico,](../common/storage-analytics-logging.md) [os níveis de acesso](storage-blob-storage-tiers.md)e as políticas de [gestão do ciclo de vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço hierárquico de nomes. Portanto, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder o acesso a estas funcionalidades.

Esta tabela lista as funcionalidades de armazenamento Blob que pode utilizar com a Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas mudarão ao longo do tempo à medida que o suporte continua a expandir-se. Para saber mais sobre questões específicas associadas ao estado de pré-visualização de uma funcionalidade, consulte o artigo [questões conhecidas.](data-lake-storage-known-issues.md)

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

> [!NOTE]
> O nível de suporte refere-se apenas à forma como a funcionalidade é suportada com a Data Lake Storage Gen2. 
>
> [As contas de BlockBlobStorage de desempenho premium](storage-blob-create-account-block-blob.md) para data lake storage gen2 estão atualmente em pré-visualização pública. Os níveis de suporte para este tipo de contas aparecem na coluna **BlockBlobStorage (Premium).**

|Recurso blob armazenamento |V2 de uso geral |BlockBlobStorage (Premium) |Artigos relacionados |
|---------------|-------------------|---|
|Escalão de acesso frequente|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Escalão de acesso esporádico|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível em Geral|Pré-visualizar|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível em Geral|Não suportado|[Métricas de análise de armazenamento Azure (Clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível em Geral|Pré-visualizar|[Métricas de armazenamento Azure em Monitor Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento de blob|Disponível em Geral|Pré-visualizar|[Quickstart: Upload, download e list blobs com PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob armazenamento Comandos Azure CLI|Disponível em Geral|Pré-visualizar|[Quickstart: Criar, transferir e listar bolhas com Azure CLI](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de blob|Disponível em Geral|Pré-visualizar|[Quickstart: Azure Blob storage client library v12 for .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerir bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerir bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Registos de diagnósticos|Disponível em Geral|Pré-visualizar <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure Storage analytics logging](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Registo de análise do Armazenamento do Azure)|
|Nível de acesso ao arquivo|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Início de sessão no Monitor Azure|Pré-visualizar |Ainda não suportado|[Monitorização do Armazenamento Azure](../common/monitor-storage.md)|
|Instantâneos|Pré-visualizar|Ainda não suportado|[Snapshots blob](snapshots-overview.md)|
|Web sites estáticos|Pré-visualizar|Ainda não suportado|[Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)|
|Armazenamento imutável|Pré-visualizar|Ainda não suportado|[Armazenar dados de blob críticos de negócio com armazenamento imutável](storage-blob-immutable-storage.md)|
|Políticas de gestão do ciclo de vida|Pré-visualizar|Ainda não suportado|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Rio Blobfuse|Pré-visualizar|Ainda não suportado|[Como montar o armazenamento Blob como um sistema de ficheiros com blobfuse](storage-how-to-mount-container-linux.md)|
|Feed de alterações|Ainda não suportado|Ainda não suportado|[Alterar suporte de alimentação no armazenamento Azure Blob](storage-blob-change-feed.md)|
|Falha na conta|Ainda não suportado|Ainda não suportado|[Recuperação de desastres e falha na conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Recipiente blob ACL|Ainda não suportado|Ainda não suportado|[Conjunto de recipiente ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não suportado|Ainda não suportado|[Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md)|
|Eliminação suave|Ainda não suportado|Ainda não suportado|[Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](storage-blob-soft-delete.md)|

<div id="diagnostic-logging"><sup>1</sup> Para contas de armazenamento de blocos premium, os registos de diagnóstico (clássico) não podem ser ativados utilizando o portal Azure. Ative-os utilizando o PowerShell.</div>

## <a name="see-also"></a>Veja também

- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas open source que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multi-protocolo no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
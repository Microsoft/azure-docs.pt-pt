---
title: Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2 [ Microsoft Docs
description: Saiba quais as funcionalidades de armazenamento blob que pode utilizar com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: ab704f53e608e7021f53a5d4b0a3e06c2ab807a1
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007457"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2

As funcionalidades de armazenamento blob, tais como [a exploração madeireira de diagnóstico,](../common/storage-analytics-logging.md)os níveis de [acesso,](storage-blob-storage-tiers.md)e as políticas de gestão do ciclo de [vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço de nome hierárquico. Por isso, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder acesso a estas funcionalidades.

Esta tabela lista as funcionalidades de armazenamento Blob que pode utilizar com o Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas vão mudar ao longo do tempo à medida que o apoio continua a expandir-se. Para saber mais sobre questões específicas associadas ao estado de pré-visualização de uma funcionalidade, consulte o artigo [sobre questões conhecidas.](data-lake-storage-known-issues.md)

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

> [!NOTE]
> O nível de suporte refere-se apenas à forma como a funcionalidade é suportada com data lake storage Gen2. 
>
> As contas de armazenamento de [blocos de desempenho premium](storage-blob-create-account-block-blob.md) para data lake storage Gen2 estão atualmente em pré-visualização pública. Os níveis de suporte para este tipo de contas aparecem na coluna de contas de **armazenamento de blocos Premium.**

|Recurso de armazenamento blob |Contas de armazenamento V2 de uso geral|Contas de armazenamento de blobs de blocos Premium |Artigos relacionados |
|---------------|-------------------|---|
|Escalão de acesso frequente|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Escalão de acesso esporádico|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível em Geral|Pré-visualização|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível em Geral|Não suportado|[Métricas de análise de armazenamento azure (Clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível em Geral|Pré-visualização|[Métricas do Armazenamento do Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento blob|Disponível em Geral|Pré-visualização|[Quickstart: Upload, download e lista de blobs com PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos Azure CLI de armazenamento blob|Disponível em Geral|Pré-visualização|[Quickstart: Criar, descarregar e listar bolhas com o Azure CLI](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de blob|Disponível em Geral|Pré-visualização|[Quickstart: Biblioteca de clientes de armazenamento Azure Blob v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerir bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerir bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Registos de diagnósticos|Disponível em Geral|Pré-visualização <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure Storage analytics logging](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Registo de análise do Armazenamento do Azure)|
|Nível de acesso de arquivo|Pré-visualização|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gestão do ciclo de vida|Pré-visualização|Ainda não apoiado|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Feed de alterações|Ainda não apoiado|Ainda não apoiado|[Alterar suporte para alimentação no armazenamento de Blob Azure](storage-blob-change-feed.md)|
|Falha na conta|Ainda não apoiado|Ainda não apoiado|[Recuperação de desastres e falha na conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Contentor blob ACL|Ainda não apoiado|Ainda não apoiado|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não apoiado|Ainda não apoiado|[Mapear um domínio personalizado para um ponto final de armazenamento De Blob Azure](storage-custom-domain-name.md)|
|Armazenamento imutável|Ainda não apoiado|Ainda não apoiado|[Armazenar dados de blob críticos de negócios com armazenamento imutável](storage-blob-immutable-storage.md)|
|Instantâneos|Ainda não apoiado|Ainda não apoiado|[Crie e gerencie um instantâneo blob em .NET](storage-blob-snapshots.md)|
|Eliminação suave|Ainda não apoiado|Ainda não apoiado|[Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](storage-blob-soft-delete.md)|
|Web sites estáticos|Ainda não apoiado|Ainda não apoiado|[Alojamento de site estático no Armazenamento do Azure](storage-blob-static-website.md)|
|Exploração madeireira no Monitor Azure|Ainda não apoiado|Ainda não apoiado|Ainda não está disponível|

<div id="diagnostic-logging"><sup>1</sup> Para contas de armazenamento de blocos premium, os registos de diagnóstico (clássico) não podem ser ativados utilizando o portal Azure. Ative-os utilizando o PowerShell.</div>

## <a name="see-also"></a>Consulte também

- [Questões conhecidas com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de código aberto que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multiprotocolo ao Armazenamento de Lagos De Dados Azure](data-lake-storage-multi-protocol-access.md)
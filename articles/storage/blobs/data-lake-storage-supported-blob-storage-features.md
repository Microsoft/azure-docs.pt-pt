---
title: Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2 Microsoft Docs
description: Saiba quais as funcionalidades de armazenamento blob que pode usar com a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 87e0fe7baaf3d83c18b12227e9ecb5bf1d2fb361
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869461"
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
|Camada de armazenamento frequente|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Camada de armazenamento esporádico|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível em Geral|Pré-visualizar|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível em Geral|Não suportado|[Métricas de análise de armazenamento Azure (Clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível em Geral|Pré-visualizar|[Métricas do Armazenamento do Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento de blob|Disponível em Geral|Pré-visualizar|[Quickstart: Upload, download e list blobs com PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob armazenamento Comandos Azure CLI|Disponível em Geral|Pré-visualizar|[Quickstart: Criar, transferir e listar bolhas com Azure CLI](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de blob|Disponível em Geral|Pré-visualizar|[Início Rápido: biblioteca de cliente do armazenamento de blobs do Azure v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerir bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerir bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Registos de diagnósticos|Disponível em Geral|Pré-visualizar <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Registo da análise de Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Nível de acesso ao arquivo|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gestão do ciclo de vida|Disponível em Geral|Ainda não suportado|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Início de sessão no Monitor Azure|Pré-visualizar |Ainda não suportado|[Monitorização do Armazenamento Azure](../common/monitor-storage.md)|
|Instantâneos|Pré-visualizar|Ainda não suportado|[Snapshots blob](snapshots-overview.md)|
|Web sites estáticos|Pré-visualizar|Ainda não suportado|[Static website hosting in Azure Storage](storage-blob-static-website.md) (Alojamento de sites estáticos no Armazenamento do Microsoft Azure)|
|Armazenamento imutável|Pré-visualizar|Ainda não suportado|[Armazenar dados de blob críticos de negócio com armazenamento imutável](storage-blob-immutable-storage.md)|
|Políticas de gestão do ciclo de vida|Pré-visualizar|Ainda não suportado|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Eliminação macia do recipiente|Pré-visualizar|Pré-visualizar|[Excluir suave para recipientes (pré-visualização)](soft-delete-container-overview.md)|
|Blob soft delete|Ainda não suportado|Ainda não suportado|[Eliminação recuperável para blobs](storage-blob-soft-delete.md)|
|Rio Blobfuse|Pré-visualizar|Ainda não suportado|[Como montar o armazenamento Blob como um sistema de ficheiros com blobfuse](storage-how-to-mount-container-linux.md)|
|Feed de alterações|Ainda não suportado|Ainda não suportado|[Alterar suporte de alimentação no armazenamento Azure Blob](storage-blob-change-feed.md)|
|Falha na conta|Ainda não suportado|Ainda não suportado|[Recuperação de desastres e falha na conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Recipiente blob ACL|Ainda não suportado<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Ainda não suportado<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Conjunto de recipiente ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não suportado|Ainda não suportado|[Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md)|
|Âmbitos de encriptação|Ainda não suportado|Ainda não suportado|[Criar e gerir os âmbitos de encriptação (pré-visualização)](encryption-scope-manage.md)|

<div id="diagnostic-logging"><sup>1</sup> Para contas de armazenamento de blocos premium, os registos de diagnóstico (clássico) não podem ser ativados utilizando o portal Azure. Ative-os utilizando o PowerShell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> Pode colocar ACLs na pasta raiz do recipiente, mas não no próprio recipiente.</div><br>

<div id="preview-form"><sup>3</sup> Para utilizar instantâneos, armazenamento imutável ou websites estáticos com Data Lake Storage Gen2, você precisa inscrever-se na pré-visualização completando este <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulário</a>.  </div>

## <a name="see-also"></a>Ver também

- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas open source que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multi-protocolo no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)

---
title: Funcionalidades de armazenamento blob disponíveis no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais as funcionalidades de armazenamento blob que pode usar com a Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516283"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2

As funcionalidades de Armazenamento blob, tais como [o registo de diagnóstico,](../common/storage-analytics-logging.md) [os níveis de acesso](storage-blob-storage-tiers.md)e as políticas de [gestão do ciclo de vida blob Storage](storage-lifecycle-management-concepts.md) funcionam agora com contas que têm um espaço hierárquico de nomes. Portanto, pode ativar espaços hierárquicos nas suas contas de armazenamento Blob sem perder o acesso a estas funcionalidades.

Esta tabela lista as funcionalidades de armazenamento Blob que pode utilizar com a Azure Data Lake Storage Gen2. Os itens que aparecem nestas tabelas mudarão ao longo do tempo à medida que o suporte continua a expandir-se. Para saber mais sobre questões específicas associadas ao estado de pré-visualização de uma funcionalidade, consulte o artigo [questões conhecidas.](data-lake-storage-known-issues.md)

## <a name="supported-blob-storage-features"></a>Funcionalidades suportadas do Armazenamento de Blobs

A tabela seguinte mostra como cada recurso de armazenamento Blob é suportado com Data Lake Storage Gen2. Há uma coluna para níveis de desempenho padrão e [premium.](premium-tier-for-data-lake-storage.md) 

|Recurso blob armazenamento |Standard |Premium |Artigos relacionados |
|---------------|-------------------|---|
|Camada de armazenamento frequente|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Camada de armazenamento esporádico|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível em Geral|Disponível em Geral|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (Clássico)|Disponível em Geral|Disponível em Geral|[Métricas de análise de armazenamento Azure (Clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível em Geral|Pré-visualizar|[Métricas do Armazenamento do Azure no Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos PowerShell de armazenamento de blob|Disponível em Geral|Disponível em Geral|[Quickstart: Upload, download e list blobs com PowerShell](storage-quickstart-blobs-powershell.md)|
|Blob armazenamento Comandos Azure CLI|Disponível em Geral|Disponível em Geral|[Quickstart: Criar, transferir e listar bolhas com Azure CLI](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de blob|Disponível em Geral|Disponível em Geral|[Início Rápido: biblioteca de cliente do armazenamento de blobs do Azure v12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Gerir bolhas com Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Gerir bolhas com Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Gerir bolhas com JavaScript v12 SDK em Node.js](storage-quickstart-blobs-nodejs.md)|
|Registos de diagnósticos|Disponível em Geral|Pré-visualizar |[Azure Storage analytics logging](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Registo de análise do Armazenamento do Azure)|
|Nível de acesso ao arquivo|Disponível em Geral|Não suportado|[Armazenamento de blobs do Azure: camadas de armazenamento frequente, esporádico e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gestão do ciclo de vida (tiering)|Disponível em Geral|Ainda não suportado|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Políticas de gestão do ciclo de vida (eliminar bolha)|Disponível em Geral|Disponível em Geral|[Gerir o ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md)|
|Início de sessão no Monitor Azure|Pré-visualizar |Pré-visualizar|[Monitorização do Armazenamento Azure](./monitor-blob-storage.md)|
|Instantâneos|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Snapshots blob](snapshots-overview.md)|
|Web sites estáticos|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Static website hosting in Azure Storage](storage-blob-static-website.md) (Alojamento de sites estáticos no Armazenamento do Microsoft Azure)|
|Armazenamento imutável|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Pré-visualizar<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Armazenar dados de blobs críticos para a empresa com o armazenamento imutável](storage-blob-immutable-storage.md)|
|Eliminação macia do recipiente|Pré-visualizar|Pré-visualizar|[Excluir suave para recipientes (pré-visualização)](soft-delete-container-overview.md)|
|Inventário de armazenamento Azure|Pré-visualizar|Pré-visualizar|[Utilize o inventário de armazenamento Azure para gerir dados blob (pré-visualização)](blob-inventory.md)|
|Domínios personalizados|Pré-visualizar<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Pré-visualizar<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md)|
|Blob soft delete|Ainda não suportado|Ainda não suportado|[Eliminação recuperável para blobs](./soft-delete-blob-overview.md)|
|Rio Blobfuse|Disponível em Geral|Disponível em Geral|[Como montar o armazenamento Blob como um sistema de ficheiros com blobfuse](storage-how-to-mount-container-linux.md)|
|Acesso público anónimo |Disponível em Geral|Disponível em Geral| Consulte [o público anónimo Configure acesso a contentores e bolhas](anonymous-read-access-configure.md).|
|Falha na conta gerida pelo cliente|Ainda não suportado|Ainda não suportado|[Recuperação de desastres e falha na conta](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Chaves fornecidas pelo cliente|Ainda não suportado|Ainda não suportado|[Fornecer uma chave de encriptação sobre um pedido para o armazenamento Blob](encryption-customer-provided-keys.md)|
|Âmbitos de encriptação|Ainda não suportado|Ainda não suportado|[Criar e gerir os âmbitos de encriptação (pré-visualização)](encryption-scope-manage.md)|
|Feed de alterações|Ainda não suportado|Ainda não suportado|[Alterar suporte de alimentação no armazenamento Azure Blob](storage-blob-change-feed.md)|
|Replicação de objetos|Ainda não suportado|Ainda não suportado|[Configurar a replicação do objeto para bolhas de bloco](object-replication-configure.md)|
|Versão blob|Ainda não suportado|Ainda não suportado|[Ativar e gerir a versão blob](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Para utilizar instantâneos, armazenamento imutável ou websites estáticos com Data Lake Storage Gen2, você precisa inscrever-se na pré-visualização completando este <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulário</a>.  </div>
<div id="preview-form-2"><sup>2</sup> Um nome de domínio personalizado só pode mapear para o serviço blob ou ponto final estático do site. O ponto final de armazenamento do Data Lake não é </a> suportado.  </div>

## <a name="see-also"></a>Ver também

- [Problemas conhecidos com Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Serviços Azure que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas open source que suportam Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso multi-protocolo no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
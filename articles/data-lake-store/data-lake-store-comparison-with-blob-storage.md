---
title: Comparação de Azure Data Lake Storage Gen1 com Azure Storage Blob | Microsoft Docs
description: Comparação de Azure Data Lake Storage Gen1 com Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438727"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Comparando Azure Data Lake Storage Gen1 e o armazenamento de BLOBs do Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A tabela neste artigo resume as diferenças entre Azure Data Lake Storage Gen1 e o armazenamento de BLOBs do Azure ao longo de alguns aspectos importantes do processamento de Big Data. O armazenamento de BLOBs do Azure é um repositório de objetos escalonável, de uso geral, projetado para uma ampla variedade de cenários de armazenamento. Azure Data Lake Storage Gen1 é um repositório de hiperescala otimizado para cargas de trabalho de Big Data Analytics.

|  | Armazenamento Ger1 do Azure Data Lake | Armazenamento de Blobs do Azure |
| --- | --- | --- |
| Finalidade |Armazenamento otimizado para cargas de trabalho de análise de Big Data |Armazenamento de objetos de uso geral para uma ampla variedade de cenários de armazenamento, incluindo Big Data Analytics |
| Casos de Utilização |Lote, interativo, análise de streaming e dados de aprendizado de máquina, como arquivos de log, dados de IoT, fluxos de cliques, conjuntos de grandes volumes |Qualquer tipo de dados de texto ou binários, como back-end de aplicativo, dados de backup, armazenamento de mídia para streaming e dados de uso geral. Além disso, suporte completo para cargas de trabalho de análise; lote, interativo, análise de streaming e dados de aprendizado de máquina, como arquivos de log, dados de IoT, fluxos de cliques, conjuntos de grandes volumes |
| Conceitos-chave |Data Lake Storage Gen1 conta contém pastas que, por sua vez, contêm dados armazenados como arquivos |A conta de armazenamento tem contêineres que, por sua vez, têm dados na forma de BLOBs |
| Estrutura |Sistema de arquivos hierárquico |Repositório de objetos com namespace simples |
| API |API de REST através de HTTPS |API de REST através de HTTP/HTTPS |
| API do lado do servidor |[API REST compatíveis com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[API REST do armazenamento de BLOBs do Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Cliente de sistema de ficheiro do Hadoop |Sim |Sim |
| Operações de dados-autenticação |Com base em [identidades de Azure Active Directory](../active-directory/develop/authentication-scenarios.md) |Com base em segredos compartilhados- [chaves de acesso da conta](../storage/common/storage-account-keys-manage.md) e chaves de assinatura de [acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operações de dados-protocolo de autenticação |OAuth 2.0. As chamadas devem conter um JWT (token Web JSON) válido emitido por Azure Active Directory |Message Authentication Code com base em hash (HMAC). As chamadas devem conter um hash SHA-256 codificado na base64 em uma parte da solicitação HTTP. |
| Operações de dados-autorização |ACLs (listas de controle de acesso) do POSIX.  ACLs com base em Azure Active Directory identidades podem ser definidas no nível de arquivo e pasta. |Para autorização em nível de conta – usar [chaves de acesso da conta](../storage/common/storage-account-keys-manage.md)<br>Para autorização de conta, contêiner ou BLOB-use [chaves de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de dados – auditoria |Há. Consulte [aqui](data-lake-store-diagnostic-logs.md) para obter informações. |Disponível |
| Dados de encriptação em repouso |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves gerenciadas pelo serviço</li><li>Com chaves gerenciadas pelo cliente no Azure keyvault</li></ul></ul> |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves gerenciadas pelo serviço</li><li>Com chaves gerenciadas pelo cliente no Azure keyvault (visualização)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão (por exemplo, criar conta) |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta |
| SDKs de programador |.NET, node. js de Java, Python, |.Net, Java, Python, Node. js, C++, Ruby, PHP, go, Android, Ios |
| Desempenho da carga de trabalho de análise |Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Sem limites de tamanhos de conta, tamanhos de ficheiro ou número de ficheiros |Para limites específicos, consulte [metas de escalabilidade para contas de armazenamento Standard](../storage/common/scalability-targets-standard-account.md) e [escalabilidade e metas de desempenho para o armazenamento de BLOBs](../storage/blobs/scalability-targets.md). Limites de conta maiores disponíveis entrando em contato com o [suporte do Azure](https://azure.microsoft.com/support/faq/) |
| Redundância geográfica |Com redundância local (várias cópias de dados em uma região do Azure) |Localmente redundante (LRS), com redundância de zona (ZRS), globalmente redundante (GRS), acesso de leitura globalmente redundante (RA-GRS). Consulte [aqui](../storage/common/storage-redundancy.md) para obter mais informações |
| Estado do serviço |Disponível em geral |Disponível em geral |
| Disponibilidade regional |Consulte [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Consulte [preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Consulte [preços](https://azure.microsoft.com/pricing/details/storage/) |



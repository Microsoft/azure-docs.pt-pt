---
title: Comparação de Azure Data Lake Storage Gen1 com armazenamento blob
description: Conheça as diferenças entre o Azure Data Lake Storage Gen1 e o Azure Blob Storage relativamente a alguns aspetos fundamentais do processamento de big data.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 77ac3c0809c08719d77457c59ef311ad43ef99cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108342"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Comparando o armazenamento de Azure Data Lake Gen1 e o armazenamento de blob Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A tabela deste artigo resume as diferenças entre o Azure Data Lake Storage Gen1 e o Azure Blob Storage ao longo de alguns aspetos fundamentais do processamento de big data. O Azure Blob Storage é uma loja de objetos escaláveis para fins gerais que é projetada para uma grande variedade de cenários de armazenamento. Azure Data Lake Storage Gen1 é um repositório de hiperescala que é otimizado para grandes cargas de trabalho de análise de dados.

| Categoria | Armazenamento do Azure Data Lake Ger1 | Armazenamento de Blobs do Azure |
| -------- | ---------------------------- | ------------------ |
| Objetivo |Armazenamento otimizado para grandes cargas de trabalho de análise de dados |Loja de objetos de finalidade geral para uma grande variedade de cenários de armazenamento, incluindo análise de big data |
| Casos de Utilização |Dados de lote, análise interativa, streaming e aprendizagem automática, tais como ficheiros de registo, dados de IoT, streams de cliques, grandes conjuntos de dados |Qualquer tipo de texto ou dados binários, tais como final de aplicação, dados de backup, armazenamento de mídia para streaming e dados de finalidade geral. Adicionalmente, apoio total para cargas de trabalho analíticas; batch, interactive, streaming analytics e dados de aprendizagem automática, tais como ficheiros de registo, dados IoT, streams de cliques, grandes conjuntos de dados |
| Conceitos-chave |A conta Gen1 de armazenamento de dados contém pastas, que por sua vez contêm dados armazenados como ficheiros |A conta de armazenamento tem contentores, que por sua vez têm dados sob a forma de bolhas |
| Estrutura |Sistema de ficheiros hierárquicos |Loja de objetos com espaço de nome plano |
| API |REST API sobre HTTPS |REST API EM HTTP/HTTPS |
| API do lado do servidor |[API DE REPOUSO compatível com WebHDFS](/rest/api/datalakestore/) |[Azure Blob Storage REST API](/rest/api/storageservices/Blob-Service-REST-API) |
| Cliente do sistema de ficheiros Hadoop |Yes |Yes |
| Operações de Dados - Autenticação |Baseado em [Identidades do Diretório Ativo Azure](../active-directory/develop/authentication-vs-authorization.md) |Baseado em segredos partilhados - [Chaves de acesso à conta](../storage/common/storage-account-keys-manage.md) e chaves de assinatura de acesso [partilhado](../storage/common/storage-sas-overview.md). |
| Operações de Dados - Protocolo de Autenticação |[Ligação OpenID](https://openid.net/connect/). As chamadas devem conter um JWT válido (token web JSON) emitido pela Azure Ative Directory.|Código de autenticação de mensagem baseado em haxixe (HMAC). As chamadas devem conter um haxixe SHA-256 codificado pela Base64 sobre uma parte do pedido HTTP. |
| Operações de Dados - Autorização |Listas de Controlo de Acesso POSIX (ACLs).  Os ACLs baseados em Identidades do Diretório Ativo Azure podem ser definidos ao nível do ficheiro e da pasta. |Para autorização ao nível da conta - Utilize chaves de [acesso à conta](../storage/common/storage-account-keys-manage.md)<br>Para autorização de conta, contentor ou blob - Utilize [chaves de assinatura de acesso partilhado](../storage/common/storage-sas-overview.md) |
| Operações de Dados - Auditoria |Disponível. Consulte [aqui](data-lake-store-diagnostic-logs.md) para obter informações. |Disponível |
| Dados de encriptação em repouso |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves geridas por serviço</li><li>Com chaves geridas pelo cliente em Azure KeyVault</li></ul></ul> |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves geridas por serviço</li><li>Com chaves geridas pelo cliente em Azure KeyVault (pré-visualização)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão (por exemplo, Criação de Conta) |[Controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) para gestão de conta |[Controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) para gestão de conta |
| Desenvolvedores de SDKs |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Desempenho da carga de trabalho de Analytics |Desempenho otimizado para cargas de trabalho de análise paralela. High Throughput e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Sem limites em tamanhos de conta, tamanhos de ficheiros ou número de ficheiros |Para limites específicos, consulte [os objetivos de escalabilidade para contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md) e [metas de escalabilidade e desempenho para armazenamento blob](../storage/blobs/scalability-targets.md). Limites de conta maiores disponíveis contactando o [Suporte Azure](https://azure.microsoft.com/support/faq/) |
| Georredundância |Localmente redundante (múltiplas cópias de dados numa região de Azure) |Localmente redundante (LRS), zona redundante (ZRS), globalmente redundante (GRS), acesso à leitura globalmente redundante (RA-GRS). Consulte [aqui](../storage/common/storage-redundancy.md) mais informações |
| Estado do serviço |Disponível em Geral |Disponível em Geral |
| Disponibilidade regional |Ver [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Ver [Preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Ver [Preços](https://azure.microsoft.com/pricing/details/storage/) |
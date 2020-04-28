---
title: Azure Data Lake Storage Gen1 comparação com Azure Storage Blob [ Microsoft Docs
description: Azure Data Lake Storage Gen1 comparação com Azure Storage Blob
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438727"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Comparando o armazenamento de dados azure Gen1 e o armazenamento de Blob Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A tabela deste artigo resume as diferenças entre o Armazenamento de Lagos De Dados Azure Gen1 e o Armazenamento de Blob Azure ao longo de alguns aspetos fundamentais do processamento de big data. O Armazenamento Azure Blob é um propósito geral, loja de objetos escalável que é projetado para uma grande variedade de cenários de armazenamento. Azure Data Lake Storage Gen1 é um repositório de hiperescala que está otimizado para grandes cargas de trabalho de análise de dados.

|  | Armazenamento do Azure Data Lake Ger1 | Armazenamento de Blobs do Azure |
| --- | --- | --- |
| Objetivo |Armazenamento otimizado para grandes cargas de trabalho de análise de dados |Loja de objetos de propósito geral para uma grande variedade de cenários de armazenamento, incluindo análise de big data |
| Casos de Utilização |Lote, análise interativa, streaming de análise e dados de aprendizagem automática, tais como ficheiros de registo, dados IoT, fluxos de cliques, grandes conjuntos de dados |Any type of text or binary data, such as application back end, backup data, media storage for streaming and general purpose data. Adicionalmente, apoio total para cargas de trabalho analíticas; lote, análise interativa, streaming e dados de aprendizagem automática, tais como ficheiros de registo, dados IoT, fluxos de cliques, grandes conjuntos de dados |
| Conceitos-chave |Data Lake Storage Gen1 conta contém pastas, que por sua vez contêm dados armazenados como ficheiros |A conta de armazenamento tem contentores, que por sua vez têm dados sob a forma de bolhas |
| Estrutura |Sistema hierárquico de ficheiros |Loja de objetos com espaço de nome plano |
| API |REST API sobre HTTPS |REST API sobre HTTP/HTTPS |
| API do lado do servidor |[API REST compatível com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[API DE ARMAZENAMENTO De Caixa De Depósito Azure Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Cliente do sistema de ficheiros Hadoop |Sim |Sim |
| Operações de Dados - Autenticação |Baseado em [identidades de diretório ativo azure](../active-directory/develop/authentication-scenarios.md) |Baseado em segredos partilhados - [Chaves de acesso](../storage/common/storage-account-keys-manage.md) à conta e chaves de assinatura de acesso [partilhado.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de Dados - Protocolo de Autenticação |OAuth 2.0. As chamadas devem conter um JWT válido (JSON Web Token) emitido pelo Azure Ative Directory |Código de Autenticação de Mensagens Baseada em Hash (HMAC) . As chamadas devem conter um hash SHA-256 codificado pela Base64 sobre uma parte do pedido HTTP. |
| Operações de Dados - Autorização |Listas de Controlo de Acesso POSIX (ACLs).  Os ACLs baseados em identidades de diretório ativo azure podem ser definidos ao nível do ficheiro e da pasta. |Para autorização ao nível da conta - Utilizar chaves de acesso à [conta](../storage/common/storage-account-keys-manage.md)<br>Para a conta, contentor ou autorização de bolha - Utilize [chaves de assinatura](../storage/common/storage-dotnet-shared-access-signature-part-1.md) de acesso partilhado |
| Operações de Dados - Auditoria |Disponível. Consulte [aqui](data-lake-store-diagnostic-logs.md) informações. |Disponível |
| Dados de encriptação em repouso |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves geridas pelo serviço</li><li>Com chaves geridas pelo cliente no Azure KeyVault</li></ul></ul> |<ul><li>Transparente, lado do servidor</li> <ul><li>Com chaves geridas pelo serviço</li><li>Com chaves geridas pelo cliente no Azure KeyVault (pré-visualização)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão (por exemplo, Criação de Conta) |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecido pelo Azure para gestão de contas |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecido pelo Azure para gestão de contas |
| SDKs de desenvolvimento |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Desempenho da carga de trabalho analítica |Desempenho otimizado para cargas de trabalho de análise paralela. Alta Entrada e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Sem limites no tamanho da conta, tamanho de ficheiroou número de ficheiros |Para limites específicos, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../storage/common/scalability-targets-standard-account.md) e para a [escalabilidade e os objetivos](../storage/blobs/scalability-targets.md)de desempenho para o armazenamento de Blob . Limites de conta maiores disponíveis contactando [o Suporte Azure](https://azure.microsoft.com/support/faq/) |
| Geo-redundância |Redundante localmente (múltiplas cópias de dados numa região de Azure) |Redundante localmente (LRS), zona redundante (ZRS), globalmente redundante (GRS), acesso de leitura globalmente redundante (RA-GRS). Veja [aqui](../storage/common/storage-redundancy.md) mais informações |
| Estado do serviço |Disponível em Geral |Disponível em Geral |
| Disponibilidade regional |Ver [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Ver [Preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Ver [Preços](https://azure.microsoft.com/pricing/details/storage/) |



---
title: Comparação de geração 1 do Data Lake Storage do Azure com o Azure Storage Blob | Documentos da Microsoft
description: Comparação de geração 1 do Data Lake Storage do Azure com o Azure Storage Blob
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
ms.openlocfilehash: 478c261bb909cbc931a7dbbaa9cb6c61152970e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878975"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Comparar o Azure Data Lake Storage Gen1 e o armazenamento de Blobs do Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

A tabela este artigo resume as diferenças entre a geração 1 de armazenamento do Azure Data Lake e o armazenamento de Blobs do Azure ao longo de alguns aspetos-chave de processamento de macrodados. Armazenamento de Blobs do Azure é uma finalidade geral, o armazenamento de objetos dimensionável que foi concebido para uma grande variedade de cenários de armazenamento. Geração de armazenamento 1 do Azure Data Lake é um repositório de hiperescala que está otimizado para cargas de trabalho de análise de macrodados.

|  | Armazenamento do Azure Data Lake Ger1 | Armazenamento de Blobs do Azure |
| --- | --- | --- |
| Objetivo |Armazenamento otimizado para cargas de trabalho de análise de macrodados |Arquivo de objetos de fins gerais para uma grande variedade de cenários de armazenamento, incluindo a análise de macrodados |
| Casos de Utilização |Lote, interativas, transmissão em fluxo de análise e machine learning de dados, tais como ficheiros de registo, dados de IoT, clique em fluxos de grandes conjuntos de dados |Qualquer tipo de dados de texto ou binários, como o aplicativo faça uma cópia de ponto, dados de cópia de segurança e armazenamento de suportes de dados para dados de finalidade de transmissão em fluxo e geral. Além disso, total suporte para cargas de trabalho de análise; lote, interativas, transmissão em fluxo de análise e machine learning de dados, tais como ficheiros de registo, dados de IoT, clique em fluxos de grandes conjuntos de dados |
| Conceitos-chave |Conta de armazenamento geração-1 do Data Lake contém pastas, que por sua vez contém os dados armazenados como arquivos |Conta de armazenamento tem contentores, que por sua vez tem dados na forma de blobs |
| estrutura |Sistema de ficheiros hierárquico |Arquivo de objetos com o espaço de nomes simples |
| API |API de REST através de HTTPS |API de REST através de HTTP/HTTPS |
| API do lado do servidor |[API REST compatíveis com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[REST API do armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Cliente de sistema de ficheiro do Hadoop |Sim |Sim |
| Operações de dados - autenticação |Com base em [identidades do Active Directory do Azure](../active-directory/develop/authentication-scenarios.md) |Com base em segredos compartilhados - [chaves de acesso da conta](../storage/common/storage-account-manage.md#access-keys) e [chaves de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Operações de dados - protocolo de autenticação |OAuth 2.0. Chamadas tem de conter um JWT válido (JSON Web Token) emitido pelo Azure Active Directory |Com base em hash Message Authentication Code (HMAC). Chamadas tem de conter um hash SHA-256 codificada em Base64 através de uma parte da solicitação HTTP. |
| Operações de dados - autorização |Listas de controlo de acesso POSIX (ACLs).  As ACLs com base em identidades do Active Directory do Azure podem ser definidas ao nível do ficheiro e pasta. |Para a autorização ao nível da conta – utilize [chaves de acesso de conta](../storage/common/storage-account-manage.md#access-keys)<br>Para a conta, contentor ou blob autorização - utilize [chaves de assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Operações de dados - auditoria |Está disponível. Ver [aqui](data-lake-store-diagnostic-logs.md) para obter informações. |Disponível |
| Dados de encriptação em repouso |<ul><li>Transparente, do lado do servidor</li> <ul><li>Com chaves geridas pelo serviço</li><li>Com chaves geridas pelo cliente no Azure KeyVault</li></ul></ul> |<ul><li>Transparente, do lado do servidor</li> <ul><li>Com chaves geridas pelo serviço</li><li>Com chaves geridas pelo cliente no Cofre de chaves do Azure (pré-visualização)</li></ul><li>Encriptação do lado do cliente</li></ul> |
| Operações de gestão (por exemplo, criar conta) |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta |[Controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) fornecida pelo Azure para a gestão de conta |
| SDKs de programador |.NET, node. js de Java, Python, |.NET, Java, Python, node. js, C++, Ruby, PHP, Go, Android, iOS |
| Desempenho da carga de trabalho de análise |Desempenho otimizado para cargas de trabalho de análise paralela. Alto débito e IOPS. |Desempenho otimizado para cargas de trabalho de análise paralela. |
| Limites de tamanho |Sem limites de tamanhos de conta, tamanhos de ficheiro ou número de ficheiros |Limites específicos documentados [aqui](../storage/common/storage-scalability-targets.md). Conta maior limitação disponível contactando [suporte do Azure](https://azure.microsoft.com/support/faq/) |
| Redundância geográfica |Localmente redundante (várias cópias dos dados numa única região do Azure) |Localmente redundantes (LRS), de zona (ZRS) redundante, globalmente georredundante (GRS), acesso de leitura globalmente redundantes (RA-GRS). Ver [aqui](../storage/common/storage-redundancy.md) para obter mais informações |
| Estado do serviço |Disponível em geral |Disponível em geral |
| Disponibilidade regional |Consulte [aqui](https://azure.microsoft.com/regions/#services) |Disponível em todas as regiões do Azure |
| Preço |Consulte [preços](https://azure.microsoft.com/pricing/details/data-lake-store/) |Consulte [preços](https://azure.microsoft.com/pricing/details/storage/) |



---
title: Autorizar operações de dados
titleSuffix: Azure Storage
description: Conheça as diferentes formas de autorizar o acesso ao Azure Storage, incluindo o Azure Ative Directory, a autorização de Chave Partilhada ou assinaturas de acesso partilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 806222fc522c548fd58935812d705e12c9b3cee1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714417"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Autorizar o acesso aos dados no Azure Storage

Sempre que acede aos dados na sua conta de armazenamento, o seu cliente faz um pedido em HTTP/HTTPS para Azure Storage. Cada pedido a um recurso seguro tem de ser autorizado, para que o serviço garanta que o cliente tem as permissões necessárias para aceder aos dados.

A tabela a seguir descreve as opções que o Azure Storage oferece para autorizar o acesso aos recursos:

| Artefacto azul | Chave partilhada (chave da conta de armazenamento) | Assinatura de acesso partilhado (SAS) | Azure Active Directory (Azure AD) | Serviços de Domínio de Diretório Ativo no local (pré-visualização) | Público anónimo lê acesso |
| -------------- | -------------------------------- | ----------------------------- | --------------------------------- | ------------------------------------------------------ | ---------------------------- |
|Blobs do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |[Suportado](storage-auth-aad.md)         |Não suportado|[Suportado](../blobs/storage-manage-access-to-resources.md)         |
|Ficheiros Azure (SMB)     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |Não suportado         |[Suportado, apenas com serviços de domínio AAD](../files/storage-files-active-directory-overview.md)         |[Suportadas, as credenciais devem ser sincronizadas com a Azure AD](../files/storage-files-active-directory-overview.md)|Não suportado         |
|Ficheiros Azure (REST)     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |Não suportado         |Não suportado |Não suportado         |
|Filas do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |[Suportado](storage-auth-aad.md)         |Não suportado | Não suportado         |
|Tabelas do Azure     |[Suportado](/rest/api/storageservices/authorize-with-shared-key/)         |[Suportado](storage-sas-overview.md)         |Não suportado         |Não suportado| Não suportado         |

Cada opção de autorização é brevemente descrita abaixo:

- **Integração do Azure Ative Directory (Azure AD)** para bolhas e filas. O Azure fornece o controlo de acesso baseado em funções (Azure RBAC) para controlo sobre o acesso de um cliente aos recursos numa conta de armazenamento. Para obter mais informações sobre a integração da AD Azure para bolhas e filas, consulte [acesso autorizado a bolhas e filas Azure utilizando o Azure Ative Directory](storage-auth-aad.md).

- **Autenticação dos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS)** para ficheiros Azure. O Azure Files suporta uma autorização baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) através do Azure AD DS. Você pode usar O RBAC Azure para um controlo fino sobre o acesso de um cliente aos recursos do Azure Files numa conta de armazenamento. Para obter mais informações sobre a autenticação dos Ficheiros Azure utilizando os serviços de domínio, consulte a [visão geral](../files/storage-files-active-directory-overview.md).

- **No local, serviços de domínio de diretório ativo (DS AD, ou no local AD DS) autenticação (pré-visualização)** para Ficheiros Azure. A Azure Files suporta a autorização baseada na identidade sobre o SMB através da DS AD. O seu ambiente AD DS pode ser hospedado em máquinas no local ou em VMs Azure. O acesso SMB a Ficheiros é suportado utilizando credenciais AD DS de máquinas de ligação de domínio, quer no local, quer em Azure. Você pode usar uma combinação de Azure RBAC para controlo de acesso ao nível de ações e NTFS DACLs para aplicação de permissão de nível de diretório/arquivo. Para obter mais informações sobre a autenticação dos Ficheiros Azure utilizando os serviços de domínio, consulte a [visão geral](../files/storage-files-active-directory-overview.md).

- **Autorização de chave partilhada** para bolhas, ficheiros, filas e mesas. Um cliente que usa a Chave Partilhada passa um cabeçalho com cada pedido assinado usando a chave de acesso à conta de armazenamento. Para mais informações, consulte [Autorizar com chave partilhada.](/rest/api/storageservices/authorize-with-shared-key/)
- **Assinaturas de acesso partilhadas** para bolhas, ficheiros, filas e mesas. As assinaturas de acesso partilhado (SAS) proporcionam acesso limitado e delegado aos recursos numa conta de armazenamento. A adição de restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões que concede proporciona flexibilidade na gestão do acesso. Para obter mais informações, consulte [utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).
- **O público anónimo leu acesso** a contentores e bolhas. A autorização não é necessária. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).  

Por predefinição, todos os recursos no Azure Storage estão seguros e estão disponíveis apenas para o proprietário da conta. Embora possa utilizar qualquer uma das estratégias de autorização descritas acima para garantir aos clientes o acesso aos recursos na sua conta de armazenamento, a Microsoft recomenda a utilização do Azure AD sempre que possível para máxima segurança e facilidade de utilização.

## <a name="next-steps"></a>Passos seguintes

- [Autorizar o acesso a blobs e filas Azure usando o Azure Ative Directory](storage-auth-aad.md)
- [Autorizar com a Chave Partilhada](/rest/api/storageservices/authorize-with-shared-key/)
- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md)

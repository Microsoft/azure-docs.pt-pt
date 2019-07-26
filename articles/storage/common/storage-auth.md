---
title: Autorizando o acesso ao armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre as diferentes maneiras de autorizar o acesso ao armazenamento do Azure, incluindo Azure Active Directory, autenticação de chave compartilhada ou assinaturas de acesso compartilhado.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a0717785f4f9c1c21a18d081d157a6cdc8c12f18
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371197"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizando o acesso ao armazenamento do Azure

Sempre que você acessa dados em sua conta de armazenamento, o cliente faz uma solicitação sobre HTTP/HTTPS para o armazenamento do Azure. Cada solicitação para um recurso seguro deve ser autorizada, para que o serviço garanta que o cliente tenha as permissões necessárias para acessar os dados.

A tabela a seguir descreve as opções que o armazenamento do Azure oferece para autorizar o acesso a recursos:

|  |Chave compartilhada (chave da conta de armazenamento)  |Assinatura de acesso partilhado (SAS)  |Azure Active Directory (Azure AD)  |Acesso de leitura público anônimo  |
|---------|---------|---------|---------|---------|
|BLOBs do Azure     |[Suportado](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Suportado](storage-dotnet-shared-access-signature-part-1.md)         |[Suportado](storage-auth-aad.md)         |[Suportado](../blobs/storage-manage-access-to-resources.md)         |
|Arquivos do Azure (SMB)     |[Suportado](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |Não suportado         |[Com suporte, somente com os serviços de domínio do AAD](../files/storage-files-active-directory-overview.md)         |Não suportado         |
|Arquivos do Azure (REST)     |[Suportado](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Suportado](storage-dotnet-shared-access-signature-part-1.md)         |Não suportado         |Não suportado         |
|Filas do Azure     |[Suportado](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Suportado](storage-dotnet-shared-access-signature-part-1.md)         |[Suportado](storage-auth-aad.md)         |Não suportado         |
|Tabelas do Azure     |[Suportado](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[Suportado](storage-dotnet-shared-access-signature-part-1.md)         |Não suportado         |Não suportado         |

Cada opção de autorização é descrita brevemente abaixo:

- **Integração do Azure Active Directory (Azure AD)** para BLOBs e filas. O Azure AD fornece o RBAC (controle de acesso baseado em função) para um controle refinado sobre o acesso de um cliente aos recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do Azure AD para BLOBs e filas, consulte [Autenticando solicitações para o armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).

- **Integração de Azure AD Domain Services (DS) (versão prévia)** para arquivos. Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB por meio do Azure AD DS. Isso fornece ao RBAC um controle refinado sobre o acesso de um cliente aos recursos em uma conta de armazenamento. Para obter mais informações sobre a integração do AD do Azure para arquivos usando os serviços de domínio, consulte [visão geral do suporte à autenticação do AAD DS (serviço de domínio Azure Active Directory de arquivos do Azure) para acesso SMB (versão prévia)](../files/storage-files-active-directory-overview.md).

- **Autorização de chave compartilhada** para BLOBs, arquivos, filas e tabelas. Um cliente que usa a chave compartilhada passa um cabeçalho com cada solicitação que é assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [autorizar com chave compartilhada](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Assinaturas de acesso compartilhado** para BLOBs, arquivos, filas e tabelas. As assinaturas de acesso compartilhado (SAS) fornecem acesso delegado limitado aos recursos em uma conta de armazenamento. Adicionar restrições no intervalo de tempo para o qual a assinatura é válida ou nas permissões que ela concede fornece flexibilidade no gerenciamento de acesso. Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](storage-dotnet-shared-access-signature-part-1.md).
- **Acesso de leitura público anônimo** para contêineres e blobs. A autorização não é necessária. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).  

Por padrão, todos os recursos no armazenamento do Azure são protegidos e estão disponíveis somente para o proprietário da conta. Embora você possa usar qualquer uma das estratégias de autorização descritas acima para conceder aos clientes acesso aos recursos em sua conta de armazenamento, a Microsoft recomenda usar o Azure AD quando possível para segurança máxima e facilidade de uso. 

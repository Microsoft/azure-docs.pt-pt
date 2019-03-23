---
title: Autorizar o acesso ao armazenamento do Azure | Documentos da Microsoft
description: Saiba mais sobre as diferentes formas de autorizar o acesso ao armazenamento do Azure, incluindo o Azure Active Directory, a autenticação de chave partilhada ou assinaturas de acesso partilhado.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ba4d83d620a597c9a59f1a3c7f2f9d6722ba42d8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368369"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorizar o acesso ao armazenamento do Azure

Sempre que aceder a dados na sua conta de armazenamento, o cliente faz um pedido através de HTTP/HTTPS para o armazenamento do Azure. Cada solicitação a um recurso seguro tem de estar autorizada, para que o serviço garante que o cliente tem as permissões necessárias para aceder aos dados. Armazenamento do Azure disponibiliza estas opções para autorizar o acesso para proteger os recursos:

- **Integração do Azure Active Directory (Azure AD)** para blobs e filas. O Azure AD fornece o controlo de acesso baseado em funções (RBAC) para um controle refinado sobre o acesso de um cliente para recursos numa conta de armazenamento. Para obter mais informações, consulte [autenticar pedidos no armazenamento do Azure com o Azure Active Directory](storage-auth-aad.md).
- **Partilhado autorização de chave** para blobs, ficheiros, filas e tabelas. Um cliente usando a chave partilhada passa um cabeçalho com cada solicitação que esteja assinada utilizando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [autorizar com a chave partilhada](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Assinaturas de acesso partilhado** para blobs, ficheiros, filas e tabelas. Assinaturas de acesso partilhado (SAS) fornecem acesso delegado limitado aos recursos numa conta de armazenamento. A adicionar restrições sobre o intervalo de tempo para que a assinatura é válida ou nas permissões que concede acesso fornece flexibilidade para gerir o acesso. Para obter mais informações, consulte [Using partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Acesso de leitura anónimo público** para contentores e blobs. Não é necessária a autorização. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).  

Por predefinição, todos os recursos no armazenamento do Azure estão protegidos e só estão disponíveis para o proprietário da conta. Apesar de poder utilizar qualquer uma das estratégias de autorização descritas acima, para conceder acesso a recursos na sua conta de armazenamento de clientes, a Microsoft recomenda a utilização do Azure AD sempre que possível para o máximo de segurança e facilidade de utilização. 




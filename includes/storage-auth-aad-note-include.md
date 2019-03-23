---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372670"
---
> [!NOTE]
> - Atribuições de funções do RBAC podem demorar até dois minutos para propagar.
>
> Para autorizar as operações de BLOBs e filas com um token de OAuth, tem de utilizar HTTPS.
>
> - O portal do Azure agora suporta a utilização de credenciais do Azure AD para ler e escrever os BLOBs e dados de fila, como parte da pré-visualização da versão. Para acessar dados de BLOBs e filas no portal do Azure, um utilizador tem de ser atribuído o Azure Resource Manager **leitor** função, além da função adequada para acesso de BLOBs ou filas. Para obter mais informações, consulte [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) atualmente utiliza a chave de conta de armazenamento para aceder a dados de BLOBs e filas. Se a chave não estiver disponível, a autorização do Azure AD é utilizada para aceder a blobs. Autorização do Azure AD não é atualmente suportada para filas. 
>
> - Os ficheiros do Azure suporta a autenticação com o Azure AD através de SMB para associados a um domínio apenas VMs (pré-visualização). Para saber mais sobre como utilizar o Azure AD através de SMB para ficheiros do Azure, veja [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](../articles/storage/files/storage-files-active-directory-overview.md).
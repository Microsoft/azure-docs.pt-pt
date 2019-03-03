---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251976"
---
> [!NOTE]
> - A pré-visualização da autenticação do Azure AD para blobs e filas destina-se apenas a utilização de não produção. Contratos de nível de serviço de produção (SLAs) não estão atualmente disponíveis. Se a autenticação do Azure AD ainda não é suportada para o seu cenário, continue a usar a autorização de chave partilhada ou SAS tokens em seus aplicativos.
>
> - Durante a pré-visualização, as atribuições de funções do RBAC podem demorar até cinco minutos para propagar.
>
> - Para autorizar as operações de BLOBs e filas com um token de OAuth, tem de utilizar HTTPS.
>
> - O portal do Azure agora suporta a utilização de credenciais do Azure AD para ler e escrever os BLOBs e dados de fila, como parte da pré-visualização da versão. Para acessar dados de BLOBs e filas no portal do Azure, um utilizador tem de ser atribuído a função RBAC de leitor de Gestor de recursos do Azure, além da função de pré-visualização apropriado para o acesso de fila ou blob. Para obter mais informações, consulte [conceder acesso a contentores do Azure e filas com RBAC no portal do Azure (pré-visualização)](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) atualmente utiliza a chave de conta de armazenamento para aceder a dados de BLOBs e filas. Acesso de OAuth é suportado para blobs.
>
> - Os ficheiros do Azure suporta a autenticação com o Azure AD através de SMB para associados a um domínio apenas VMs (pré-visualização). Para saber mais sobre como utilizar o Azure AD através de SMB para ficheiros do Azure, veja [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](../articles/storage/files/storage-files-active-directory-overview.md).
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
ms.openlocfilehash: 694491f3bec9fd1e62e65504968be9f06dda0c11
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824275"
---
> [!NOTE]
> - A pré-visualização da autenticação do Azure AD para blobs e filas destina-se apenas a utilização de não produção. Contratos de nível de serviço de produção (SLAs) não estão atualmente disponíveis. Se a autenticação do Azure AD ainda não é suportada para o seu cenário, continue a usar a autorização de chave partilhada ou SAS tokens em seus aplicativos.
>
> - Durante a pré-visualização, as atribuições de funções do RBAC podem demorar até cinco minutos para propagar.
>
> - Para autorizar as operações de BLOBs e filas com um token de OAuth, tem de utilizar HTTPS.
>
> - O portal do Azure agora suporta a utilização de credenciais do Azure AD para ler e escrever os BLOBs e dados de fila, como parte da pré-visualização da versão. 
> 
> - [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) atualmente utiliza a chave de conta de armazenamento para aceder a dados de BLOBs e filas. Acesso de OAuth é suportado para blobs.
>
> - Os ficheiros do Azure suporta a autenticação com o Azure AD através de SMB para associados a um domínio apenas VMs (pré-visualização). Para saber mais sobre como utilizar o Azure AD através de SMB para ficheiros do Azure, veja [autenticação de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](../articles/storage/files/storage-files-active-directory-overview.md).
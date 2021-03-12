---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603241"
---
O SMB Multicanal para ações de ficheiros Azure tem atualmente as seguintes restrições:
- Só pode ser usado com contas de arquivos redundantes locais.
- Suportado apenas para clientes Windows. 
- O número máximo de canais é de quatro.
- SMB Direct não é suportado.
- Os pontos finais privados para contas de armazenamento não são suportados.
- Para contas de armazenamento com serviços de domínio de diretório ativo (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) Azure AD DS habilitada para ficheiros Azure, os clientes SMB não seriam capazes de usar o Windows File Explorer para configurar permissões NTFS em diretórios e ficheiros.
    - Utilize a ferramenta [ICACLs](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) para configurar permissões.


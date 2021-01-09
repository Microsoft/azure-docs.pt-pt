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
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052768"
---
O SMB Multicanal para ações de ficheiros Azure tem atualmente as seguintes restrições:
- Só pode ser usado com contas de arquivos redundantes locais.
- Suportado apenas para clientes Windows. 
- O número máximo de canais é de quatro.
- SMB Direct não é suportado.
- Os pontos finais privados para contas de armazenamento não são suportados.
- Para contas de armazenamento com serviços de domínio de diretório ativo (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) Azure AD DS habilitada para ficheiros Azure, os clientes SMB não seriam capazes de usar o Windows File Explorer para configurar permissões NTFS em diretórios e ficheiros.
    - Utilize a ferramenta [ICACLs](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) para configurar permissões.


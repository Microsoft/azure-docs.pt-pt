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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995498"
---
O SMB Multicanal para ações de ficheiros Azure tem atualmente as seguintes restrições:
- Só pode ser usado com contas de arquivos redundantes locais.
- Suportado apenas para clientes Windows. 
- O número máximo de canais é de quatro.
- SMB Direct não é suportado.
- Para contas de armazenamento com serviços de domínio de diretório ativo (AD DS) ou [autenticação baseada em identidade](../articles/storage/files/storage-files-active-directory-overview.md) Azure AD DS habilitada para ficheiros Azure, os clientes SMB não seriam capazes de usar o Windows File Explorer para configurar permissões NTFS em diretórios e ficheiros.
    - Utilize a ferramenta [ICACLs](/windows-server/administration/windows-commands/icacls) do Windows ou o comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) para configurar permissões.


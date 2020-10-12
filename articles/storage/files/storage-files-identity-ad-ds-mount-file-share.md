---
title: Partilha de ficheiros mount Azure para um VM aderido a DS AD
description: Saiba como montar uma partilha de ficheiros nas suas máquinas de serviços de domínio de diretório ativo no local.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87535064"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Parte quatro: montar uma partilha de ficheiros a partir de um VM de domínio

Antes de iniciar este artigo, certifique-se de que completa o artigo anterior, [configurar o diretório e as permissões de nível de ficheiros em SMB](storage-files-identity-ad-ds-configure-permissions.md).

O processo descrito neste artigo verifica que as permissões de partilha de ficheiros e acessos estão corretamente configuradas e que pode aceder a uma partilha de FicheiroS Azure a partir de um VM unido a domínio. A atribuição de funções Azure de nível de partilha pode levar algum tempo a fazer efeito. 

Inscreva-se no cliente utilizando as credenciais a que concedeu permissões, como mostra a imagem seguinte.

![Screenshot mostrando ecrã de inscrição AD AD para autenticação do utilizador](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Pré-requisitos de montagem

Antes de poder montar a partilha de ficheiros, certifique-se de que passou pelos seguintes pré-requisitos:

- Se estiver a montar a parte do ficheiro de um cliente que já montou a parte do ficheiro utilizando a chave da sua conta de armazenamento, certifique-se de que desligou a partilha, removeu as credenciais persistentes da chave da conta de armazenamento e está atualmente a utilizar credenciais de DS AD para autenticação.
- O seu cliente deve ter uma linha de visão para o seu DS AD. Se a sua máquina ou VM estiver fora da rede gerida pelo seu DS AD, terá de ativar a VPN para chegar a DS AD para autenticação.

Substitua os valores do espaço reservado pelos seus próprios valores e, em seguida, use o seguinte comando para montar a partilha de ficheiros Azure:

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se encontrar problemas de montagem com credenciais de DS AD, consulte [Não conseguir montar Ficheiros Azure com credenciais de AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) para orientação.

Se a montagem da sua partilha de ficheiros tiver sido bem sucedida, então ativou e configurado com sucesso a autenticação AD DS no local para as suas ações de ficheiroS Azure.

## <a name="next-steps"></a>Passos seguintes

Se a identidade que criou em DS AD para representar a conta de armazenamento estiver num domínio ou OU que aplica a rotação da palavra-passe, continue até ao próximo artigo para instruções sobre a atualização da sua palavra-passe:

[Atualize a palavra-passe da sua identidade de conta de armazenamento em DS AD](storage-files-identity-ad-ds-update-password.md)

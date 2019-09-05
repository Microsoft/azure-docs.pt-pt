---
title: Manter arquivos em Azure Cloud Shell | Microsoft Docs
description: Explicação de como Azure Cloud Shell persiste arquivos.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: b2823c935d11ae99ab1d87ae708945721820ad8c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306737"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Como funciona Cloud Shell armazenamento 
Cloud Shell persiste arquivos por meio dos dois métodos a seguir: 
* Criar uma imagem de disco do `$Home` seu diretório para manter todo o conteúdo dentro do diretório. A imagem de disco é salva no compartilhamento de arquivos especificado `acc_<User>.img` como `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`em e sincroniza as alterações automaticamente. 
* Montando o compartilhamento de arquivos `clouddrive` especificado como `$Home` no diretório para uma interação direta de compartilhamento de arquivos. `/Home/<User>/clouddrive`está mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os arquivos em `$Home` seu diretório, como chaves SSH, são persistidos na imagem de disco do usuário, que é armazenada em seu compartilhamento de arquivos montado. Aplique as práticas recomendadas ao manter as informações `$Home` em seu diretório e no compartilhamento de arquivos montado.

## <a name="clouddrive-commands"></a>comandos CloudDrive

### <a name="use-the-clouddrive-command"></a>Usar o `clouddrive` comando
No Cloud Shell, você pode executar um comando chamado `clouddrive`, que permite atualizar manualmente o compartilhamento de arquivos que é montado no Cloud Shell.
![Executando o comando "CloudDrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista`clouddrive`
Para descobrir qual compartilhamento de arquivos está montado `clouddrive`como, execute `df` o comando. 

O caminho do arquivo para CloudDrive mostra o nome da conta de armazenamento e o compartilhamento de arquivos na URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Montar um novo CloudDrive

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para a montagem manual
Você pode atualizar o compartilhamento de arquivos que está associado com Cloud shell usando o `clouddrive mount` comando.

Se você montar um compartilhamento de arquivos existente, as contas de armazenamento deverão estar localizadas em sua região Select Cloud Shell. Recupere o local executando `env` e verificando o. `ACC_LOCATION`

#### <a name="the-clouddrive-mount-command"></a>O `clouddrive mount` comando

> [!NOTE]
> Se você estiver montando um novo compartilhamento de arquivos, uma nova imagem de usuário será `$Home` criada para seu diretório. A imagem `$Home` anterior é mantida no compartilhamento de arquivos anterior.

Execute o `clouddrive mount` comando com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para exibir mais detalhes, execute `clouddrive mount -h`, conforme mostrado aqui:

![Executando o ' CloudDrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmontar CloudDrive
Você pode desmontar um compartilhamento de arquivos que é montado em Cloud Shell a qualquer momento. Como Cloud Shell requer que um compartilhamento de arquivos montado seja usado, você será solicitado a criar e montar outro compartilhamento de arquivos na próxima sessão.

1. Execute `clouddrive unmount`.
2. Reconheça e confirme as solicitações.

O compartilhamento de arquivos continuará a existir, a menos que você o exclua manualmente. Cloud Shell não procurará mais este compartilhamento de arquivos em sessões subsequentes. Para exibir mais detalhes, execute `clouddrive unmount -h`, conforme mostrado aqui:

![Executando o ' CloudDrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora a execução desse comando não exclua nenhum recurso, excluir manualmente um grupo de recursos, uma conta de armazenamento ou um compartilhamento de arquivos mapeado para Cloud Shell `$Home` apaga a imagem de disco do diretório e todos os arquivos em seu compartilhamento de arquivos. Esta ação não pode ser desfeita.
## <a name="powershell-specific-commands"></a>Comandos específicos do PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Listar `clouddrive` compartilhamentos de arquivos do Azure
O `Get-CloudDrive` cmdlet recupera as informações de compartilhamento de arquivos do Azure atualmente `clouddrive` montadas pelo no Cloud Shell. <br>
![Executando Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar`clouddrive`
Você pode desmontar um compartilhamento de arquivos do Azure que é montado em Cloud Shell a qualquer momento. Se o compartilhamento de arquivos do Azure tiver sido removido, você será solicitado a criar e montar um novo compartilhamento de arquivos do Azure na próxima sessão.

O `Dismount-CloudDrive` cmdlet desmonta um compartilhamento de arquivos do Azure da conta de armazenamento atual. Desmontar o `clouddrive` encerra a sessão atual. O usuário será solicitado a criar e montar um novo compartilhamento de arquivos do Azure durante a próxima sessão.
![Executando Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Nota: Se você precisar definir uma função em um arquivo e chamá-la dos cmdlets do PowerShell, o operador de ponto deverá ser incluído. Por exemplo:. .\MyFunctions.ps1

## <a name="next-steps"></a>Passos Seguintes
[Guia de início rápido Cloud Shell](quickstart.md) <br>
[Saiba mais sobre o armazenamento de arquivos de Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

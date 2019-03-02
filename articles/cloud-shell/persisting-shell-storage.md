---
title: Manter os ficheiros para o Bash no Azure Cloud Shell | Documentos da Microsoft
description: Instruções sobre como o Bash no Azure Cloud Shell persistir os ficheiros.
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
ms.openlocfilehash: 0aa00af543a3d21db9b8ad0ed808a8bff0b534e1
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246275"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Como funciona o armazenamento de Cloud Shell 
Cloud Shell persistir ficheiros através de ambos dos seguintes métodos: 
* Criar uma imagem de disco do seu `$Home` diretório para manter todo o conteúdo dentro do diretório. A imagem de disco é guardada na partilha de ficheiros especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, e ele sincroniza automaticamente as alterações. 
* Montar a partilha de ficheiros especificado como `clouddrive` no seu `$Home` diretório para interação direta de partilha de ficheiros. `/Home/<User>/clouddrive` é mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os ficheiros na sua `$Home` diretório, como chaves SSH são mantidas em sua imagem de disco do utilizador, que é armazenada na sua partilha de ficheiros instalado. Aplicar as práticas recomendadas ao manter informações na sua `$Home` directory e a partilha de ficheiros instalado.

## <a name="bash-specific-commands"></a>Comandos de bash específicas

### <a name="use-the-clouddrive-command"></a>Utilize o `clouddrive` comando
Com o Bash no Cloud Shell, pode executar um comando chamado `clouddrive`, que permite-lhe atualizar manualmente a partilha de ficheiros que está montada para a Cloud Shell.
![Executar o comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Montar um novo clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para a montagem manual
Pode atualizar a partilha de ficheiros que está associada à Cloud Shell, utilizando o `clouddrive mount` comando.

Se montar uma partilha de ficheiros existentes, as contas de armazenamento tem de estar localizadas na sua região do Cloud Shell selecione. Obter a localização, executando `env` partir do Bash e verificando o `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>O `clouddrive mount` comando

> [!NOTE]
> Se estiver a montar uma nova partilha de ficheiros, uma nova imagem de utilizador é criada para seu `$Home` diretório. O anterior `$Home` imagem é mantida na partilha de ficheiros anteriores.

Execute o `clouddrive mount` comando com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver mais detalhes, execute `clouddrive mount -h`, como mostrado aqui:

![A executar o "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmontar clouddrive
Poderá desmontar a partilha de ficheiros que está montada para Cloud Shell em qualquer altura. Uma vez que o Cloud Shell requer uma partilha de ficheiros instalado para ser utilizado, será solicitado para criar e montar outra partilha de ficheiros na próxima sessão.

1. Execute `clouddrive unmount`.
2. Reconhecer e confirme prompts.

A partilha de ficheiros continuarão a existir, a menos que elimine manualmente. Cloud Shell já não irá procurar esta partilha de ficheiros em sessões subsequentes. Para ver mais detalhes, execute `clouddrive unmount -h`, como mostrado aqui:

![A executar o "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Apesar de executar este comando não irá eliminar todos os recursos, manualmente ao eliminar um grupo de recursos, a conta de armazenamento ou a partilha de ficheiros que está mapeada para o Cloud Shell apaga sua `$Home` imagem de disco de diretório e todos os ficheiros na partilha de ficheiros. Esta ação não pode ser anulada.

### <a name="list-clouddrive"></a>Lista `clouddrive`
Para descobrir qual partilha de ficheiros está montada como `clouddrive`, execute o `df` comando. 

O caminho de ficheiro para clouddrive mostra que o nome da conta de armazenamento e o ficheiro partilham no URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

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
## <a name="powershell-specific-commands"></a>Comandos do PowerShell específicos

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` partilhas de ficheiros do Azure
O `Get-CloudDrive` cmdlet obtém as informações de partilha de ficheiros do Azure atualmente montadas pelo `clouddrive` no Cloud Shell. <br>
![Get-CloudDrive em execução](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar `clouddrive`
Poderá desmontar uma partilha de ficheiros do Azure está montada para Cloud Shell em qualquer altura. Se a partilha de ficheiros do Azure tiver sido removida, será solicitado a criar e montar uma partilha de ficheiros do Azure nova da sessão seguinte.

O `Dismount-CloudDrive` cmdlet desmonta uma partilha de ficheiros do Azure da conta de armazenamento atual. Desmontar o `clouddrive` termina a sessão atual. O usuário será solicitado a criar e montar uma nova partilha de ficheiros do Azure durante a próxima sessão.
![Dismount-CloudDrive em execução](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Nota: Se tiver de definir uma função num arquivo e chamá-lo a partir de cmdlets do PowerShell, em seguida, o operador de ponto tem de ser incluído. Por exemplo:. .\MyFunctions.ps1

## <a name="next-steps"></a>Passos Seguintes
[Guia de introdução do Cloud Shell de bash](quickstart.md) <br>
[PowerShell no guia de introdução do Cloud Shell](quickstart-powershell.md) <br>
[Saiba mais sobre armazenamento de ficheiros do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre as etiquetas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

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
ms.openlocfilehash: ee68400d000ca823816c8efc6bcbc224d1388832
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082986"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Manter arquivos em Azure Cloud Shell
O Cloud Shell utiliza o armazenamento de arquivos do Azure para manter arquivos entre sessões. Na inicialização inicial, Cloud Shell solicita que você associe um compartilhamento de arquivos novo ou existente para manter os arquivos entre as sessões.

> [!NOTE]
> O bash e o PowerShell compartilham o mesmo compartilhamento de arquivos. Somente um compartilhamento de arquivos pode ser associado à montagem automática no Cloud Shell.

## <a name="create-new-storage"></a>Criar novo armazenamento

Quando você usa configurações básicas e seleciona apenas uma assinatura, Cloud Shell cria três recursos em seu nome na região com suporte mais próxima a você:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Compartilhamento de arquivos: `cs-<user>-<domain>-com-<uniqueGuid>`

![A configuração de assinatura](media/persisting-shell-storage/basic-storage.png)

O compartilhamento de arquivos é montado como `clouddrive` em seu diretório `$Home`. Essa é uma ação única e o compartilhamento de arquivos é montado automaticamente em sessões subsequentes. 

> [!NOTE]
> Para segurança, cada usuário deve provisionar sua própria conta de armazenamento.  Para o RBAC (controle de acesso baseado em função), os usuários devem ter acesso de colaborador ou acima no nível da conta de armazenamento.

O compartilhamento de arquivos também contém uma imagem de 5 GB que é criada para você, que persiste automaticamente os dados em seu diretório `$Home`. Isso se aplica tanto ao bash quanto ao PowerShell.

## <a name="use-existing-resources"></a>Usar recursos existentes

Usando a opção avançado, você pode associar os recursos existentes. Ao selecionar uma região de Cloud Shell, você deve selecionar uma conta de armazenamento de backup colocalizada na mesma região. Por exemplo, se sua região atribuída for oeste dos EUA do que você deve associar um compartilhamento de arquivos que reside dentro do oeste dos EUA também.

Quando o prompt de configuração de armazenamento for exibido, selecione **Mostrar configurações avançadas** para exibir opções adicionais. O filtro opções de armazenamento populadas para LRS (armazenamento com redundância local), GRS (armazenamento com redundância geográfica) e contas de ZRS (armazenamento com redundância de zona). 

> [!NOTE]
> O uso de contas de armazenamento GRS ou ZRS é recomendado para resiliência adicional para o compartilhamento de arquivos de backup. Qual tipo de redundância depende de suas metas e da preferência de preço. [Saiba mais sobre as opções de replicação para contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![A configuração do grupo de recursos](media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
As contas de armazenamento do Azure associadas devem residir na mesma região que o computador Cloud Shell no qual você está montando. Para localizar sua região atual, você pode executar `env` em bash e localizar a variável `ACC_LOCATION`. Os compartilhamentos de arquivos recebem uma imagem de 5 GB criada para que você persista o diretório `$Home`.

As máquinas Cloud Shell existem nas seguintes regiões:

|Área|Região|
|---|---|
|Américas|Leste dos EUA, Sul EUA Central, oeste dos EUA|
|Europa|Europa do Norte, Europa Ocidental|
|Ásia-Pacífico|Índia central, Sudeste Asiático|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento que você cria no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se você quiser impedir que os usuários criem contas de armazenamento no Cloud Shell, crie uma [política de recursos do Azure para marcas](../azure-policy/json-samples.md) disparadas por essa marca específica.



## <a name="how-cloud-shell-storage-works"></a>Como funciona Cloud Shell armazenamento 
Cloud Shell persiste arquivos por meio dos dois métodos a seguir: 
* Criar uma imagem de disco do seu diretório `$Home` para manter todo o conteúdo dentro do diretório. A imagem de disco é salva no compartilhamento de arquivos especificado como `acc_<User>.img` em `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`e sincroniza as alterações automaticamente. 
* Montar o compartilhamento de arquivos especificado como `clouddrive` em seu diretório `$Home` para a interação direta de compartilhamento de arquivos. `/Home/<User>/clouddrive` está mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os arquivos em seu diretório de `$Home`, como chaves SSH, são persistidos na imagem de disco do usuário, que é armazenada em seu compartilhamento de arquivos montado. Aplique as práticas recomendadas ao manter as informações no diretório `$Home` e no compartilhamento de arquivos montado.

## <a name="clouddrive-commands"></a>comandos CloudDrive

### <a name="use-the-clouddrive-command"></a>Usar o comando `clouddrive`
No Cloud Shell, você pode executar um comando chamado `clouddrive`, que permite atualizar manualmente o compartilhamento de arquivos que é montado no Cloud Shell.
![executando o comando "CloudDrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Listar `clouddrive`
Para descobrir qual compartilhamento de arquivos está montado como `clouddrive`, execute o comando `df`. 

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
Você pode atualizar o compartilhamento de arquivos associado a Cloud Shell usando o comando `clouddrive mount`.

Se você montar um compartilhamento de arquivos existente, as contas de armazenamento deverão estar localizadas em sua região Select Cloud Shell. Recupere o local executando `env` e verificando o `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>O comando `clouddrive mount`

> [!NOTE]
> Se você estiver montando um novo compartilhamento de arquivos, uma nova imagem de usuário será criada para seu diretório `$Home`. A imagem de `$Home` anterior é mantida no compartilhamento de arquivos anterior.

Execute o comando `clouddrive mount` com os seguintes parâmetros:

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
> Embora a execução desse comando não exclua nenhum recurso, excluir manualmente um grupo de recursos, uma conta de armazenamento ou um compartilhamento de arquivos que está mapeado para Cloud Shell apaga a imagem de disco do `$Home` Directory e quaisquer arquivos em seu compartilhamento de arquivos. Esta ação não pode ser desfeita.
## <a name="powershell-specific-commands"></a>Comandos específicos do PowerShell

### <a name="list-clouddrive-azure-file-shares"></a>Listar `clouddrive` compartilhamentos de arquivos do Azure
O cmdlet `Get-CloudDrive` recupera as informações de compartilhamento de arquivos do Azure atualmente montadas pelo `clouddrive` no Cloud Shell. <br>
![execução de Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar `clouddrive`
Você pode desmontar um compartilhamento de arquivos do Azure que é montado em Cloud Shell a qualquer momento. Se o compartilhamento de arquivos do Azure tiver sido removido, você será solicitado a criar e montar um novo compartilhamento de arquivos do Azure na próxima sessão.

O cmdlet `Dismount-CloudDrive` desmonta um compartilhamento de arquivos do Azure da conta de armazenamento atual. Desmontar o `clouddrive` encerra a sessão atual. O usuário será solicitado a criar e montar um novo compartilhamento de arquivos do Azure durante a próxima sessão.
![executando o Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Observação: se você precisar definir uma função em um arquivo e chamá-la dos cmdlets do PowerShell, o operador de ponto deverá ser incluído. Por exemplo:. .\MyFunctions.ps1

## <a name="next-steps"></a>Passos seguintes
[Guia de início rápido Cloud Shell](quickstart.md) <br>
[Saiba mais sobre o armazenamento de arquivos de Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre marcas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

---
title: Persista ficheiros em Azure Cloud Shell Microsoft Docs
description: Adida em como a Azure Cloud Shell persiste nos ficheiros.
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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152263"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Persistir ficheiros em Azure Cloud Shell
A Cloud Shell utiliza o armazenamento de ficheiros Azure para persistir ficheiros em todas as sessões. No início inicial, a Cloud Shell pede-lhe que associe uma nova ou existente partilha de ficheiros para persistir ficheiros em todas as sessões.

> [!NOTE]
> A Bash e a PowerShell partilham a mesma partilha de ficheiros. Apenas uma partilha de ficheiros pode ser associada à montagem automática na Cloud Shell.

> [!NOTE]
> A firewall de armazenamento Azure não é suportada para contas de armazenamento de conchas em nuvem.

## <a name="create-new-storage"></a>Criar novo armazenamento

Quando utiliza definições básicas e seleciona apenas uma subscrição, a Cloud Shell cria três recursos em seu nome na região suportada que está mais próxima de si:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Partilha de ficheiros: `cs-<user>-<domain>-com-<uniqueGuid>`

![A definição de subscrição](media/persisting-shell-storage/basic-storage.png)

A partilha de ficheiros está `clouddrive` no seu `$Home` diretório. Esta é uma ação única, e a partilha de ficheiros monta automaticamente em sessões subsequentes. 

A partilha de ficheiros também contém uma imagem de 5 GB que é criada para si, que persiste automaticamente nos dados do seu `$Home` diretório. Isto aplica-se tanto a Bash como a PowerShell.

## <a name="use-existing-resources"></a>Utilizar os recursos existentes

Ao utilizar a opção avançada, pode associar os recursos existentes. Ao selecionar uma região cloud Shell, deve selecionar uma conta de armazenamento de suporte co-localizada na mesma região. Por exemplo, se a sua região designada for a West US, então deve associar uma parte de ficheiro que também reside nos EUA Ocidentais.

Quando aparecer o pedido de configuração de armazenamento, **selecione Mostrar definições avançadas** para visualizar opções adicionais. O filtro de opções de armazenamento povoado para armazenamento localmente redundante (LRS), armazenamento geo-redundante (GRS) e contas de armazenamento redundante de zona (ZRS). 

> [!NOTE]
> A utilização de contas de armazenamento GRS ou ZRS é recomendada para uma resiliência adicional para a sua partilha de ficheiros de suporte. Que tipo de redundância depende dos seus objetivos e preferência de preço. [Saiba mais sobre as opções de replicação das contas de Armazenamento Azure](../storage/common/storage-redundancy.md).

![A definição do grupo de recursos](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Garantir o acesso ao armazenamento
Para a segurança, cada utilizador deve providenciar a sua própria conta de armazenamento.  Para o controlo de acesso baseado em funções Azure (Azure RBAC), os utilizadores devem ter acesso ao contribuinte ou acima ao nível da conta de armazenamento.

A Cloud Shell utiliza uma Partilha de Ficheiros Azure numa conta de armazenamento, dentro de uma subscrição especificada. Devido às permissões herdadas, os utilizadores com direitos de acesso suficientes à subscrição poderão aceder a todas as contas de armazenamento e às ações de ficheiros contidas na subscrição.

Os utilizadores devem bloquear o acesso aos seus ficheiros definindo as permissões na conta de armazenamento ou no nível de subscrição.

## <a name="supported-storage-regions"></a>Regiões de armazenamento apoiadas
Para encontrar a sua região atual pode correr `env` em Bash e localizar a variável , ou a partir da corrida `ACC_LOCATION` PowerShell `$env:ACC_LOCATION` . As ações de ficheiros recebem uma imagem de 5 GB criada para que possa persistir no seu `$Home` diretório.

As máquinas Cloud Shell existem nas seguintes regiões:

|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro Sul dos EUA, Oeste dos EUA|
|Europa|Europa do Norte, Europa Ocidental|
|Ásia-Pacífico|Índia Central, Sudeste Asiático|

Os clientes devem escolher uma região primária, a menos que tenham a obrigação de que os seus dados em repouso sejam armazenados numa determinada região. Se tiverem esse requisito, deve utilizar-se uma região de armazenagem secundária.

### <a name="secondary-storage-regions"></a>Regiões secundárias de armazenamento
Se for utilizada uma região de armazenamento secundário, a conta de armazenamento Azure associada reside numa região diferente da máquina Cloud Shell a que está a montá-las. Por exemplo, Jane pode definir a sua conta de armazenamento para ser localizada no Canadá Leste, uma região secundária, mas a máquina a que ela está montada ainda está localizada numa região primária. Os seus dados em repouso estão localizados no Canadá, mas são processados nos Estados Unidos.

> [!NOTE]
> Se for utilizada uma região secundária, o acesso ao ficheiro e o tempo de arranque para cloud Shell podem ser mais lentos.

Um utilizador pode correr `(Get-CloudDrive | Get-AzStorageAccount).Location` no PowerShell para ver a localização da sua Partilha de Ficheiros.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos Azure
As contas de armazenamento que cria na Cloud Shell estão marcadas com `ms-resource-usage:azure-cloud-shell` . Se pretender proibir os utilizadores de criarem contas de armazenamento na Cloud Shell, crie uma [política de recursos Azure para tags](../governance/policy/samples/index.md) que são desencadeadas por esta etiqueta específica.

## <a name="how-cloud-shell-storage-works"></a>Como funciona o armazenamento da Cloud Shell 
A Cloud Shell persiste nos ficheiros através de ambos os seguintes métodos: 
* Criar uma imagem em disco do seu `$Home` diretório para persistir todos os conteúdos dentro do diretório. A imagem do disco é guardada na sua partilha de ficheiros especificada `acc_<User>.img` como em , e sincroniza automaticamente as `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` alterações. 
* Montagem da sua partilha de ficheiros especificada como `clouddrive` no seu `$Home` diretório para interação direta com partilha de ficheiros. `/Home/<User>/clouddrive` está mapeado para `fileshare.storage.windows.net/fileshare` .
 
> [!NOTE]
> Todos os ficheiros do seu `$Home` diretório, como as teclas SSH, são persistidos na imagem do seu disco de utilizador, que é armazenado na sua partilha de ficheiros montada. Aplique as melhores práticas quando persistir em informação no seu `$Home` diretório e partilha de ficheiros montado.

## <a name="clouddrive-commands"></a>comandos clouddrive

### <a name="use-the-clouddrive-command"></a>Use o `clouddrive` comando
Na Cloud Shell, pode executar um comando chamado `clouddrive` , que lhe permite atualizar manualmente a partilha de ficheiros que é montada na Cloud Shell.

![Executando o comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista `clouddrive`
Para descobrir qual a partilha de ficheiros que é montada `clouddrive` como , executar o `df` comando. 

O caminho do ficheiro para clouddrive mostra o nome da sua conta de armazenamento e a sua partilha de ficheiros no URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Monte uma nova nuvem

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
Pode atualizar a partilha de ficheiros que está associada à Cloud Shell utilizando o `clouddrive mount` comando.

Se montar uma partilha de ficheiros existente, as contas de armazenamento devem estar localizadas na sua região de Cloud Shell selecionada. Recupere o local correndo `env` e verificando o `ACC_LOCATION` .

#### <a name="the-clouddrive-mount-command"></a>O `clouddrive mount` comando

> [!NOTE]
> Se estiver a montar uma nova partilha de ficheiros, é criada uma nova imagem de utilizador para o seu `$Home` diretório. A sua imagem anterior `$Home` é mantida na sua parte de ficheiro anterior.

Executar o `clouddrive mount` comando com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver mais detalhes, `clouddrive mount -h` corra, como mostrado aqui:

![Executando o comando 'clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmontar a nuvem
Pode desmontar uma partilha de ficheiros montada na Cloud Shell a qualquer momento. Uma vez que a Cloud Shell requer uma partilha de ficheiros montada para ser usada, você será solicitado para criar e montar outra partilha de ficheiros na próxima sessão.

1. Execute `clouddrive unmount`.
2. Reconhecer e confirmar as solicitações.

A sua partilha de ficheiros continuará a existir a menos que a elimine manualmente. A Cloud Shell deixará de procurar esta partilha de ficheiros nas sessões seguintes. Para ver mais detalhes, `clouddrive unmount -h` corra, como mostrado aqui:

![Executando o comando 'clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora executar este comando não eliminará quaisquer recursos, eliminando manualmente um grupo de recursos, uma conta de armazenamento ou uma partilha de ficheiros que está mapeada para a Cloud Shell apaga a imagem do disco de `$Home` diretório e quaisquer ficheiros na sua partilha de ficheiros. Esta ação não pode ser desfeita.
## <a name="powershell-specific-commands"></a>Comandos específicos powerShell

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` ações de ficheiros Azure
O `Get-CloudDrive` cmdlet recupera a informação de partilha de ficheiros Azure atualmente montada pela `clouddrive` Cloud Shell. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar `clouddrive`
Pode desmontar uma partilha de ficheiros Azure que está montada na Cloud Shell a qualquer momento. Se a partilha de ficheiros Azure tiver sido removida, será solicitado a criar e montar uma nova partilha de ficheiros Azure na próxima sessão.

O `Dismount-CloudDrive` cmdlet desmonta uma parte de ficheiro Azure da conta de armazenamento corrente. Desmontar o `clouddrive` termina a sessão atual. O utilizador será solicitado a criar e montar uma nova partilha de ficheiros Azure durante a próxima sessão.
![Executando Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Nota: Se precisar de definir uma função num ficheiro e chamá-la a partir dos cmdlets PowerShell, então o operador de pontos deve ser incluído. Por exemplo:. .\MyFunctions.ps1

## <a name="next-steps"></a>Passos seguintes
[Cloud Shell Quickstart](quickstart.md) <br>
[Saiba mais sobre o armazenamento de Ficheiros Microsoft Azure](../storage/files/storage-files-introduction.md) <br>
[Saiba mais sobre etiquetas de armazenamento](../azure-resource-manager/management/tag-resources.md) <br>
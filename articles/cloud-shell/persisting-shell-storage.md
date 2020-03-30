---
title: Persista ficheiros em Azure Cloud Shell [ Microsoft Docs
description: Walkthrough de como azure Cloud Shell persiste ficheiros.
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
ms.openlocfilehash: d4d59642f638e7b1221c35a4bb281923571d5066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297592"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Persistem ficheiros em Azure Cloud Shell
A Cloud Shell utiliza o armazenamento de ficheiros Azure para persistir ficheiros em sessões. No início inicial, a Cloud Shell pede-lhe que associe uma nova ou existente partilha de ficheiros para persistir ficheiros em sessões.

> [!NOTE]
> A Bash e a PowerShell partilham a mesma parte do ficheiro. Apenas uma partilha de ficheiros pode ser associada à montagem automática na Cloud Shell.

> [!NOTE]
> A firewall de armazenamento Azure não é suportada para contas de armazenamento de conchas em nuvem.

## <a name="create-new-storage"></a>Criar novo armazenamento

Quando utiliza configurações básicas e seleciona apenas uma subscrição, a Cloud Shell cria três recursos em seu nome na região apoiada que lhe é mais próxima:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento:`cs<uniqueGuid>`
* Partilha de ficheiros:`cs-<user>-<domain>-com-<uniqueGuid>`

![A definição de subscrição](media/persisting-shell-storage/basic-storage.png)

A partilha de `clouddrive` ficheiros `$Home` é montada como no seu diretório. Esta é uma ação única, e a partilha de ficheiros aumenta automaticamente nas sessões subsequentes. 

A partilha de ficheiros também contém uma imagem de 5 GB `$Home` que é criada para si, que automaticamente persiste dados no seu diretório. Isto aplica-se tanto à Bash como à PowerShell.

## <a name="use-existing-resources"></a>Utilizar os recursos existentes

Ao utilizar a opção avançada, pode associar os recursos existentes. Ao selecionar uma região cloud Shell, deve selecionar uma conta de armazenamento de apoio co-localizada na mesma região. Por exemplo, se a sua região atribuída for a Oeste dos EUA, então deve associar uma parte de ficheiro que reside também no Oeste dos EUA.

Quando aparecer o pedido de configuração de armazenamento, selecione **Configurações avançadas** para visualizar opções adicionais. As opções de armazenamento povoadas filtram para armazenamento localmente redundante (LRS), armazenamento geo-redundante (GRS) e contas de armazenamento redundante em zona (ZRS). 

> [!NOTE]
> Recomenda-se a utilização de contas de armazenamento GRS ou ZRS para uma resiliência adicional para a sua parte de ficheiro de apoio. Que tipo de redundância depende dos seus objetivos e preferência de preço. [Saiba mais sobre as opções](https://docs.microsoft.com/azure/storage/common/storage-redundancy)de replicação para as contas de Armazenamento Azure.

![A definição do grupo de recursos](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Garantir o acesso ao armazenamento
Por segurança, cada utilizador deve fornecer a sua própria conta de armazenamento.  Para o controlo de acesso baseado em funções (RBAC), os utilizadores devem ter acesso ao contribuinte ou superior ao nível da conta de armazenamento.

A Cloud Shell utiliza uma Partilha de Ficheiros Azure numa conta de armazenamento, dentro de uma subscrição especificada. Devido a permissões herdadas, os utilizadores com direitos de acesso suficientes à subscrição poderão aceder a todas as contas de armazenamento e a arquivar ações contidas na subscrição.

Os utilizadores devem bloquear o acesso aos seus ficheiros, definindo as permissões na conta de armazenamento ou no nível de subscrição.

## <a name="supported-storage-regions"></a>Regiões de armazenamento apoiadas
Para encontrar a sua `env` região atual, `ACC_LOCATION`pode correr em `$env:ACC_LOCATION`Bash e localizar a variável , ou da corrida powerShell . As partilhas de ficheiros recebem uma `$Home` imagem de 5 GB criada para que persista o seu diretório.

Existem máquinas Cloud Shell nas seguintes regiões:

|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Europa do Norte, Europa Ocidental|
|Ásia-Pacífico|Índia Central, Sudeste Asiático|

Os clientes devem escolher uma região primária, a menos que tenham a obrigação de que os seus dados em repouso sejam armazenados numa determinada região. Se tiverem tal exigência, deve utilizar-se uma região de armazenamento secundária.

### <a name="secondary-storage-regions"></a>Regiões secundárias de armazenamento
Se for utilizada uma região de armazenamento secundário, a conta de armazenamento azure associada reside numa região diferente da máquina Cloud Shell a que está a montá-las. Por exemplo, Jane pode definir a sua conta de armazenamento para ser localizada no Canadá Leste, uma região secundária, mas a máquina onde está montada ainda está localizada numa região primária. Os seus dados em repouso estão localizados no Canadá, mas são processados nos Estados Unidos.

> [!NOTE]
> Se for utilizada uma região secundária, o acesso ao ficheiro e o tempo de arranque da Cloud Shell podem ser mais lentos.

Um utilizador `(Get-CloudDrive | Get-AzStorageAccount).Location` pode correr no PowerShell para ver a localização da sua Partilha de Ficheiros.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos Azure
As contas de armazenamento que cria na `ms-resource-usage:azure-cloud-shell`Cloud Shell estão marcadas com . Se pretender proibir os utilizadores de criarem contas de armazenamento na Cloud Shell, crie uma política de [recursos Azure para etiquetas](../azure-policy/json-samples.md) que sejam desencadeadas por esta etiqueta específica.

## <a name="how-cloud-shell-storage-works"></a>Como funciona o armazenamento da Cloud Shell 
Cloud Shell persiste ficheiros através de ambos os seguintes métodos: 
* Criar uma imagem `$Home` em disco do seu diretório para persistir todos os conteúdos dentro do diretório. A imagem do disco é guardada `acc_<User>.img` `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`na sua parte de ficheiro especificada como em , e sincroniza automaticamente as alterações. 
* Montagem da sua parte `clouddrive` de `$Home` ficheiro especificada como no seu diretório para interação direta de partilha de ficheiros. `/Home/<User>/clouddrive`está mapeado para `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Todos os `$Home` ficheiros do seu diretório, como as teclas SSH, são persistentes na sua imagem de disco de utilizador, que está armazenada na sua partilha de ficheiros montada. Aplique as melhores práticas `$Home` quando persistir informação no seu diretório e partilha de ficheiros montado.

## <a name="clouddrive-commands"></a>comandos clouddrive

### <a name="use-the-clouddrive-command"></a>Use `clouddrive` o comando
Na Cloud Shell, pode executar `clouddrive`um comando chamado , que lhe permite atualizar manualmente a partilha de ficheiros que é montada na Cloud Shell.

![Executando o comando "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista`clouddrive`
Para descobrir que partilha de `clouddrive`ficheiros `df` é montada como , executar o comando. 

O caminho do ficheiro para clouddrive mostra o nome da sua conta de armazenamento e a partilha de ficheiros no URL. Por exemplo, `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Monte uma nova clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Pré-requisitos para montagem manual
Pode atualizar a partilha de ficheiros associada à `clouddrive mount` Cloud Shell utilizando o comando.

Se montar uma parte de ficheiro existente, as contas de armazenamento devem estar localizadas na sua região de Cloud Shell selecionada. Recupere a `env` localização correndo `ACC_LOCATION`e verificando o .

#### <a name="the-clouddrive-mount-command"></a>O `clouddrive mount` comando

> [!NOTE]
> Se estiver a montar uma nova partilha de ficheiros, é criada uma nova imagem de utilizador para o seu `$Home` diretório. A `$Home` sua imagem anterior é mantida na sua partilha de ficheiros anterior.

Executar `clouddrive mount` o comando com os seguintes parâmetros:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Para ver mais `clouddrive mount -h`detalhes, corra, como mostrado aqui:

![Executando o comando 'clouddrive mount'](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Desmonte a clouddrive
Podes desmontar uma parte de ficheiro montada na Cloud Shell a qualquer momento. Uma vez que a Cloud Shell necessita de uma partilha de ficheiromontada para ser usada, será solicitado a criar e montar outra partilha de ficheiros na próxima sessão.

1. Execute `clouddrive unmount`.
2. Reconheça e confirme as indicações.

A sua parte do ficheiro continuará a existir a menos que a pague manualmente. A Cloud Shell deixará de procurar esta partilha de ficheiros nas sessões subsequentes. Para ver mais `clouddrive unmount -h`detalhes, corra, como mostrado aqui:

![Executando o comando 'clouddrive unmount'](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Embora executar este comando não apague quaisquer recursos, eliminando manualmente um grupo de recursos, uma conta `$Home` de armazenamento ou uma partilha de ficheiros que esteja mapeada para a Cloud Shell apaga a sua imagem de disco de diretório e quaisquer ficheiros na sua partilha de ficheiros. Esta ação não pode ser desfeita.
## <a name="powershell-specific-commands"></a>Comandos específicos powerShell

### <a name="list-clouddrive-azure-file-shares"></a>List `clouddrive` Azure ações de ficheiros
O `Get-CloudDrive` cmdlet recupera as informações de partilha de `clouddrive` ficheiros Azure atualmente montadas pela Cloud Shell. <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Desmontar`clouddrive`
Podes desmontar uma partilha de ficheiros Azure que está montada na Cloud Shell a qualquer momento. Se a parte do ficheiro Azure tiver sido removida, será solicitado a criar e montar uma nova partilha de ficheiros Azure na próxima sessão.

O `Dismount-CloudDrive` cmdlet desmonta uma parte de ficheiro Azure da conta de armazenamento corrente. Desmontar `clouddrive` as terminações da sessão atual. O utilizador será solicitado a criar e montar uma nova partilha de ficheiros Azure durante a próxima sessão.
![Execução Desmontagem-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Nota: Se precisar de definir uma função num ficheiro e chamá-la a partir dos cmdlets PowerShell, então o operador de pontos deve ser incluído. Por exemplo:. .\MyFunctions.ps1

## <a name="next-steps"></a>Passos seguintes
[Cloud Shell Quickstart](quickstart.md) <br>
[Saiba mais sobre o armazenamento do Microsoft Azure Files](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Saiba mais sobre etiquetas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

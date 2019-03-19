---
title: Início Rápido do Azure – Criar cópia de segurança de VMs com a CLI do Azure
description: Saiba como criar cópias de segurança das suas máquinas virtuais com a CLI do Azure
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8a7c722944aacaca9780939a1726c6e47be79be1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112046"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Criar uma cópia de segurança de uma máquina virtual no Azure com a CLI
A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. O Azure Backup cria pontos de recuperação que podem ser armazenados em cofres de recuperação georredundantes. Este artigo mostra em detalhe como criar cópias de segurança de máquinas virtuais (VMs) no Azure com a CLI do Azure. Também pode realizar estes passos com o [Azure PowerShell](quick-backup-vm-powershell.md) ou no [portal do Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma nova, pode [criá-la com a CLI do Azure](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar a CLI localmente, tem de executar a CLI do Azure versão 2.0.18 ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação
Um cofre dos Serviços de Recuperação é um contentor lógico que armazena os dados da cópia de segurança de todos os recursos protegidos, como as VMs do Azure. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Especifique o mesmo grupo de recursos e a mesma localização da VM que quer proteger. Se utilizou o [início rápido da VM](../virtual-machines/linux/quick-create-cli.md), então criou:

- um grupo de recursos com o nome *myResourceGroup*,
- uma VM com o nome *myVM*,
- recursos na localização *eastus*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Por predefinição, o cofre dos Serviços de Recuperação está definido para Armazenamento georredundante. O armazenamento georredundante garante que os dados das cópias de segurança são replicados numa região do Azure secundária, que está situada a centenas de quilómetros de distância da região principal.


## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure
Crie uma política de proteção para definir: quando é executada uma tarefa de cópia de segurança e durante quanto tempo os pontos de recuperação são armazenados. A política de proteção predefinida executa uma tarefa de cópia de segurança todos os dias e mantém os pontos de recuperação durante 30 dias. Pode utilizar estes valores da política predefinida para proteger rapidamente a sua VM. Para ativar a proteção da cópia de segurança de uma VM, utilize [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Especifique o grupo de recursos e a VM a proteger e, em seguida, a política a utilizar:

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Se a VM não se encontrar no mesmo grupo de recursos do cofre, myResourceGroup referir-se-á ao grupo de recursos em que o cofre foi criado. Em vez do nome da VM, forneça o ID da VM, conforme indicado abaixo.

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança
Para iniciar uma cópia de segurança agora em vez de aguardar até que a política predefinida execute a tarefa na hora agendada, utilize [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now). Esta primeira tarefa de cópia de segurança cria um ponto de recuperação completo. Todas as tarefas de cópia de segurança que se seguem a esta cópia de segurança inicial criam pontos de recuperação incrementais. Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Os parâmetros seguintes são utilizados para criar a cópia de segurança da VM:

- `--container-name` é o nome da VM
- `--item-name` é o nome da VM
- O valor `--retain-until` deve ser definido como a última data disponível, no formato de data em UTC (**dd-mm-aaaa**), até à qual pretende que o ponto de recuperação esteja disponível.

O exemplo seguinte cria uma cópia de segurança da VM com o nome *myVM* e define a expiração do ponto de recuperação como 18 de outubro de 2017:

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança
Para monitorizar o estado das tarefas de criação de cópias de segurança, utilize [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A saída é semelhante ao exemplo seguinte, o qual mostra que a tarefa de criação da cópia de segurança está *InProgress* (Em curso):

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *Estado* da tarefa de cópia de segurança mostrar *Concluído*, a VM estará protegida com os Serviços de Recuperação e tem armazenado um ponto de recuperação completo.


## <a name="clean-up-deployment"></a>Limpar a implementação
Quando já não precisar, pode desativar a proteção na VM, remover os pontos de restauro e o cofre dos Serviços de Recuperação e, em seguida, eliminar o grupo de recursos e os recursos da VM associados. Se tiver utilizado uma VM já existente, pode ignorar o último comando [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) para manter o grupo de recursos e a VM.

Se pretender experimentar um tutorial de Cópia de Segurança que explica como restaurar dados para a VM, aceda a [Passos seguintes](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial. Para saber mais sobre o Azure Backup e os Serviços de Recuperação, prossiga para os tutoriais.

> [!div class="nextstepaction"]
> [Back up multiple Azure VMs](./tutorial-backup-vm-at-scale.md)(Fazer uma cópia de segurança de várias VMs do Azure)

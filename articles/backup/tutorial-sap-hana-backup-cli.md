---
title: Tutorial - SAP HANA DB backup em Azure usando Azure CLI
description: Neste tutorial, aprenda a apoiar as bases de dados SAP HANA que estão a funcionar num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure utilizando o Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba06ef876f30dc51e04fe7491d491621f5d8e21b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710605"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Ressarça as bases de dados SAP HANA num Azure VM utilizando O Azure CLI

O Azure CLI é utilizado para criar e gerir recursos Azure a partir da Linha de Comando ou através de scripts. Esta documentação detalha como fazer backup de uma base de dados SAP HANA e desencadear backups a pedido - todos usando Azure CLI. Também pode executar estes passos utilizando o [portal Azure](./backup-azure-sap-hana-database.md).

Este documento pressupõe que já tem uma base de dados SAP HANA instalada num Azure VM. (Também pode [criar um VM utilizando o Azure CLI](../virtual-machines/linux/quick-create-cli.md)). No final deste tutorial, será capaz de:

> [!div class="checklist"]
>
> * Criar um cofre dos Serviços de Recuperação 
> * Registe a instância SAP HANA e descubra a base de dados(s) nele
> * Ativar a cópia de segurança numa base de dados SAP HANA
> * Desencadear uma cópia de segurança a pedido

Confira os [cenários que apoiamos atualmente](./sap-hana-backup-support-matrix.md#scenario-support) para a SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.30 ou posterior do CLI Azure. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é um recipiente lógico que armazena os dados de backup para cada recurso protegido, como VMs Azure ou cargas de trabalho em funcionamento em VMs Azure - como bases de dados SQL ou HANA. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Especifique o mesmo grupo de recursos e a mesma localização da VM que quer proteger. Aprenda a criar um VM utilizando o Azure CLI com este [arranque rápido VM](../virtual-machines/linux/quick-create-cli.md).

Para este tutorial, vamos usar o seguinte:

* um grupo de recursos chamado *saphanaResourceGroup*
* um VM chamado *saphanaVM*
* recursos na localização *westus2.*

Vamos criar um cofre chamado *SaphanaVault.*

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Por predefinição, o cofre dos Serviços de Recuperação está definido para Armazenamento georredundante. Geo-Redundant armazenamento garante que os seus dados de reserva são replicados para uma região secundária de Azure que fica a centenas de milhas da região primária. Se a definição de redundância de armazenamento precisar de ser modificada, utilize o cmdlet [conjunto de propriedades de backup do cofre az](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) backup.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Para ver se o seu cofre foi criado com sucesso, use o cmdlet [da lista de cofre suplente az.](/cli/azure/backup/vault#az-backup-vault-list) Verá a seguinte resposta:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registe-se e proteja a instância SAP HANA

Para que a instância SAP HANA (o VM com SAP HANA instalado nele) seja descoberta pelos serviços Azure, deve ser executado um [script pré-registo](https://aka.ms/scriptforpermsonhana) na máquina SAP HANA. Certifique-se de que todos os [pré-requisitos](./tutorial-backup-sap-hana-db.md#prerequisites) são cumpridos antes de executar o script. Para saber mais sobre o que o script faz, consulte a secção [O que o script de pré-registo faz.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

Uma vez que o script é executado, a instância SAP HANA pode ser registrada com o cofre dos Serviços de Recuperação que criamos anteriormente. Para registar a ocorrência, utilize o cmdlet do [registo do contentor de reserva az.](/cli/azure/backup/container#az-backup-container-register) *VMResourceId* é o ID de recurso do VM que criou para instalar o SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Se o VM não estiver no mesmo grupo de recursos que o cofre, então o *SaphanaResourceGroup* refere-se ao grupo de recursos onde o cofre foi criado.

O registo da instância SAP HANA descobre automaticamente todas as suas bases de dados atuais. No entanto, para descobrir quaisquer novas bases de dados que possam ser adicionadas no futuro consulte as [novas bases de dados descobertas adicionadas à](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) secção de instância SAP HANA registada.

Para verificar se a instância SAP HANA está registada com sucesso no seu cofre, utilize o cmdlet [da lista de contentores de reserva az.](/cli/azure/backup/container#az-backup-container-list) Verá a seguinte resposta:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A coluna "nome" na saída acima refere-se ao nome do recipiente. Este nome do recipiente será utilizado nas próximas secções para ativar as cópias de segurança e desencadeá-las. Que, neste caso, é *vMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Ativar a cópia de segurança na base de dados SAP HANA

O cmdlet [da lista de artigos protegidos por cópias de segurança az](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) lista todas as bases de dados descobertas no caso SAP HANA que registou no passo anterior.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Deve encontrar a base de dados que pretende fazer nesta lista, que será a seguinte:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Como pode ver pela saída acima, o SID do sistema SAP HANA é HXE. Neste tutorial, vamos configurar a cópia de segurança para a base *de dados saphanadatabase;hxe;hxe* que reside no servidor *hxehost.*

Para proteger e configurar a cópia de segurança numa base de dados, uma de cada vez, usamos o [cmdlet de proteção de backup az-for-azurewl.](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) Forneça o nome da apólice que pretende utilizar. Para criar uma política utilizando o CLI, utilize a [política de backup az criar](/cli/azure/backup/policy#az-backup-policy-create) cmdlet. Para este tutorial, vamos usar a política da *sapahanaPolicy.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Pode verificar se a configuração de backup acima está completa usando o cmdlet [da lista de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-list) A saída será exibida da seguinte forma:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

O cmdlet [da lista de trabalho de backup da AZ](/cli/azure/backup/job#az-backup-job-list) lista todos os trabalhos de backup (agendados ou a pedido) que tenham executado ou estejam atualmente a funcionar na base de dados protegida, além de outras operações como registo, configuração de backup e eliminação de dados de backup.

>[!NOTE]
>O Azure Backup não se ajusta automaticamente às alterações de horário de verão ao fazer o backup de uma base de dados SAP HANA em funcionamento num VM Azure.
>
>Modifique a apólice manualmente, se necessário.

## <a name="trigger-an-on-demand-backup"></a>Desencadear uma cópia de segurança a pedido

Enquanto a secção acima detalha como configurar uma cópia de segurança programada, esta secção fala em desencadear um backup a pedido. Para isso, usamos o [reforço de proteção az-backup-agora](/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet.

>[!NOTE]
> A política de retenção de um backup a pedido é determinada pela política de retenção subjacente à base de dados.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A saída será exibida da seguinte forma:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado do trabalho usando o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

>[!NOTE]
>As cópias de segurança de registo são automaticamente ativadas e geridas pela SAP HANA internamente.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a restaurar uma base de dados SAP HANA em Azure VM usando CLI, continue ao tutorial – [Restaurar uma base de dados SAP HANA em Azure VM usando CLI](tutorial-sap-hana-restore-cli.md)

* Para saber como fazer backup de uma base de dados SAP HANA em funcionamento em Azure VM usando o portal Azure, consulte [uma base de dados SAP HANA em VMs Azure](./backup-azure-sap-hana-database.md)

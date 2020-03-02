---
title: Tutorial - Backup SAP HANA DB em Azure usando CLI
description: Neste tutorial, aprenda a apoiar as bases de dados SAP HANA em execução num Azure VM para um cofre dos Serviços de Recuperação de Backup Azure usando o Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206627"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Back up Bases de dados SAP HANA em um Azure VM usando Azure CLI

O Azure CLI é utilizado para criar e gerir os recursos azure a partir da Linha de Comando ou através de scripts. Esta documentação detalha como fazer backup numa base de dados SAP HANA e desencadear cópias de segurança a pedido - todas utilizando o Azure CLI. Também pode executar estes passos utilizando o [portal Azure.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

Este documento pressupõe que já tem uma base de dados SAP HANA instalada num VM Azure. (Também pode [criar um VM utilizando o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). No final deste tutorial, poderá:

> [!div class="checklist"]
>
> * Criar um cofre dos serviços de recuperação
> * Registe a instância SAP HANA e descubra a (s) base de dados no mesmo
> * Ativar cópia de segurança numa base de dados SAP HANA
> * Desencadear um reforço a pedido

Confira os [cenários que atualmente apoiamos](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) para o SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e utilizar o CLI localmente, tem de executar a versão Azure CLI xx.xxx.x ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Um cofre de Serviços de Recuperação é um recipiente lógico que armazena os dados de backup para cada recurso protegido, como VMs Azure ou cargas de trabalho em funcionamento em VMs Azure - como bases de dados SQL ou HANA. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Especifique o mesmo grupo de recursos e a mesma localização da VM que quer proteger. Aprenda a criar um VM utilizando o Azure CLI com este [quickstart VM](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

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

Por predefinição, o cofre dos Serviços de Recuperação está definido para Armazenamento georredundante. O armazenamento georredundante garante que os dados das cópias de segurança são replicados numa região do Azure secundária, que está situada a centenas de quilómetros de distância da região principal. Se a definição de redundância de armazenamento tiver de ser modificada, utilize o conjunto de propriedades de backup do [cofre de reserva az.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Para ver se o seu cofre foi criado com sucesso, use a lista de [cofres de reserva az](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) cmdlet. Verá a seguinte resposta:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registe-se e proteja a instância SAP HANA

Para que o caso SAP HANA (o VM com SAP HANA instalado nele) seja descoberto pelos serviços Azure, um [script de pré-registo](https://aka.ms/scriptforpermsonhana) deve ser executado na máquina SAP HANA. Certifique-se de que todos os [pré-requisitos](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) são cumpridos antes de executar o script. Para saber mais sobre o que o script faz, consulte o [what the pre-registration script does](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) section.

Uma vez que o script é executado, a instância SAP HANA pode ser registada com o cofre de serviços de recuperação que criamos anteriormente. Para registar a instância, utilize o registo de recipientes de [reserva az](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) cmdlet. *VMResourceId* é o ID de recurso do VM que criou para instalar o SAP HANA.

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

Registar a instância SAP HANA descobre automaticamente todas as suas bases de dados atuais. No entanto, para descobrir quaisquer novas bases de dados que possam ser adicionadas no futuro, consulte as novas bases de [dados descobertas adicionadas à secção de instâncias SAP HANA registada.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Para verificar se a instância SAP HANA está registada com sucesso no seu cofre, utilize a lista de recipientes de [reserva az](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) cmdlet. Verá a seguinte resposta:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A coluna "nome" na saída acima refere-se ao nome do recipiente. Este nome do recipiente será utilizado nas secções seguintes para ativar as cópias de segurança e desencadeá-las. Que neste caso, é *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Ativar cópia de segurança na base de dados SAP HANA

A lista de itens protegidos de [reserva az](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) lista todas as bases de dados descobertas na instância SAP HANA que registou no passo anterior.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Deve encontrar a base de dados que pretende fazer parte desta lista, que será a seguinte:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Como pode ver pela saída acima, o SID do sistema SAP HANA é HXE. Neste tutorial, configuraremos a cópia de segurança para a base de *dados saphanadatabase;hxe;hxe que* reside no servidor *hxehost.*

Para proteger e configurar a cópia de segurança numa base de dados, uma de cada vez, usamos o [az de proteção de reserva activa-para-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) cmdlet. Forneça o nome da política que pretende utilizar. Para criar uma política utilizando o CLI, utilize a política de [backup az criar](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet. Para este tutorial, vamos usar a política *de sapahana.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Pode verificar se a configuração de backup acima está completa utilizando a lista de trabalho de [reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet. A saída será exibida da seguinte forma:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

A lista de trabalho de [backup az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet lista todos os trabalhos de backup (programado ou a pedido) que tenham executado ou estejam atualmente em funcionamento na base de dados protegida, além de outras operações como registo, configuração de cópia de segurança, eliminação de dados de backup, etc.

## <a name="trigger-an-on-demand-backup"></a>Desencadear um reforço a pedido

Embora a secção acima detalhe como configurar uma cópia de segurança agendada, esta secção fala sobre desencadear uma cópia de segurança a pedido. Para isso, usamos a proteção de [reserva az agora](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet.

>[!NOTE]
> A política de retenção de um apoio a pedido é determinada pela política de retenção subjacente à base de dados.

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

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado de trabalho usando o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

>[!NOTE]
>Além de agendar uma cópia de segurança completa ou diferencial, também podem ser ativadas manualmente. As cópias de segurança de registo são automaticamente acionadas e geridas pela SAP HANA internamente.
>
> As cópias de segurança incrementais não são atualmente suportadas pela Azure Backup.

## <a name="next-steps"></a>Passos seguintes

* Para aprender como restaurar uma base de dados SAP HANA em Azure VM utilizando cli, continue para o tutorial – [Restaurar uma base de dados SAP HANA em Azure VM usando cli](tutorial-sap-hana-restore-cli.md)

* Para saber como fazer backup de uma base de dados SAP HANA em execução no Portal Azure VM utilizando o portal Azure, consulte a Backup uma base de [dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

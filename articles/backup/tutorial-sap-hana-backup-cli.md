---
title: Tutorial-backup do SAP HANA DB no Azure usando a CLI
description: Neste tutorial, saiba como fazer backup de SAP HANA bancos de dados em execução em uma VM do Azure para um cofre dos serviços de recuperação de backup do Azure usando CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6d35b6ebfc6f47f5775c24407b645b97112387c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472353"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: fazer backup de bancos de dados SAP HANA em uma VM do Azure usando CLI do Azure

CLI do Azure é usado para criar e gerenciar recursos do Azure na linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como fazer backup de um banco de dados SAP HANA e disparar backups sob demanda-todos usando CLI do Azure. Você também pode executar essas etapas usando o [portal do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

Este documento pressupõe que você já tem um banco de dados SAP HANA instalado em uma VM do Azure. (Você também pode [criar uma VM usando CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)). Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Criar um cofre dos serviços de recuperação
> * Registrar SAP HANA instância e descobrir banco (s) de dados nele
> * Habilitar backup em um banco de dados SAP HANA
> * Disparar um backup sob demanda

Confira os [cenários aos quais atualmente damos suporte](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) para SAP Hana.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, você deve executar CLI do Azure versão XX. xxx. x ou posterior. Para encontrar a versão da CLI, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos serviços de recuperação

Um cofre dos serviços de recuperação é um contêiner lógico que armazena os dados de backup para cada recurso protegido, como VMs do Azure ou cargas de trabalho em execução em VMs do Azure, como bancos de dados SQL ou HANA. Quando a tarefa de cópia de segurança de um recurso protegido é executada, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Crie um cofre dos Serviços de Recuperação com [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Especifique o mesmo grupo de recursos e a mesma localização da VM que quer proteger. Saiba como criar uma VM usando CLI do Azure com este guia de [início rápido de VM](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Para este tutorial, usaremos o seguinte:

* um grupo de recursos chamado *saphanaResourceGroup*
* uma VM chamada *saphanaVM*
* recursos no local *westus2* .

Vamos criar um cofre chamado *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Por predefinição, o cofre dos Serviços de Recuperação está definido para Armazenamento georredundante. O armazenamento georredundante garante que os dados das cópias de segurança são replicados numa região do Azure secundária, que está situada a centenas de quilómetros de distância da região principal. Se a configuração de redundância de armazenamento precisar ser modificada, use o cmdlet [AZ backup Vault backup-Properties Set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Para ver se o cofre foi criado com êxito, use o cmdlet [AZ backup Vault List](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) . Você verá a seguinte resposta:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrar e proteger a instância de SAP HANA

Para a instância de SAP HANA (a VM com SAP HANA instalado) a ser descoberta pelos serviços do Azure, um [script de pré-registro](https://aka.ms/scriptforpermsonhana) deve ser executado no computador SAP Hana. Verifique se todos os [pré-requisitos](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) foram atendidos antes de executar o script. Para saber mais sobre o que o script faz, consulte a seção [Configurando permissões](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#setting-up-permissions) .

Depois que o script for executado, a instância SAP HANA poderá ser registrada com o cofre dos serviços de recuperação que criamos anteriormente. Para registrar a instância, use o cmdlet [AZ backup container Register](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) . *VMResourceId* é a ID de recurso da VM que você criou para instalar SAP Hana.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Se a VM não estiver no mesmo grupo de recursos que o cofre, *saphanaResourceGroup* se refere ao grupo de recursos em que o cofre foi criado.

O registro da instância de SAP HANA descobre automaticamente todos os seus bancos de dados atuais. No entanto, para descobrir quaisquer novos bancos de dados que possam ser adicionados no futuro, consulte a seção [descobrindo novos bancos de dados adicionados à instância do SAP Hana registrada](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) .

Para verificar se a instância de SAP HANA foi registrada com êxito com o cofre, use o cmdlet [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) . Você verá a seguinte resposta:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A coluna "nome" na saída acima refere-se ao nome do contêiner. Esse nome de contêiner será usado nas próximas seções para habilitar os backups e dispará-los. Que, nesse caso, é *VMAppContainer; Computação; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Habilitar backup no banco de dados SAP HANA

O cmdlet [AZ backup protegíveis-item List](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) lista todos os bancos de dados descobertos na instância de SAP Hana que você registrou na etapa anterior.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Você deve encontrar o banco de dados do qual deseja fazer backup nesta lista, que terá a seguinte aparência:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Como você pode ver na saída acima, o SID do sistema de SAP HANA é HXE. Neste tutorial, configuraremos o backup para o banco de dados *saphanadatabase; hxe; hxe* que reside no servidor *hxehost* .

Para proteger e configurar o backup em um banco de dados, um de cada vez, usamos o cmdlet [AZ backup Protection enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) . Forneça o nome da política que você deseja usar. Para criar uma política usando a CLI, use o cmdlet [AZ backup Policy Create](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) . Para este tutorial, vamos usar a política *sapahanaPolicy* .

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Você pode verificar se a configuração de backup acima está concluída usando o cmdlet [AZ backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . A saída será exibida da seguinte maneira:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

O cmdlet [AZ backup List lista](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) todos os trabalhos de backup (agendados ou sob demanda) que foram executados ou estão atualmente em execução no banco de dados protegido, além de outras operações como registrar, configurar backup, excluir backup, etc.

## <a name="trigger-an-on-demand-backup"></a>Disparar um backup sob demanda

Embora a seção acima detalha como configurar um backup agendado, esta seção fala sobre o disparo de um backup sob demanda. Para fazer isso, usamos o cmdlet [AZ backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) .

>[!NOTE]
> A política de retenção de um backup sob demanda é determinada pela política de retenção subjacente para o banco de dados.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A saída será exibida da seguinte maneira:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

>[!NOTE]
>Além de agendar um backup completo ou diferencial, eles também podem ser disparados manualmente. Os backups de log são automaticamente disparados e gerenciados pelo SAP HANA internamente.
>
> Atualmente, não há suporte para backups incrementais no backup do Azure.

## <a name="next-steps"></a>Passos seguintes

* Para saber como restaurar um banco de dados SAP HANA na VM do Azure usando a CLI, prossiga para o tutorial – [restaurar um banco de dados de SAP Hana na VM do Azure usando a CLI](tutorial-sap-hana-restore-cli.md)

* Para saber como fazer backup de um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, consulte fazer [backup de um SAP Hana em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

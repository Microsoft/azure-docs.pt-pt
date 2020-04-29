---
title: 'Tutorial: Gerir sap HANA DB apoiado usando CLI'
description: Neste tutorial, aprenda a gerir bases de dados SAP HANA apoiadas em um Azure VM usando o Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79238742"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Gerir bases de dados SAP HANA num Azure VM utilizando o Azure CLI

O Azure CLI é utilizado para criar e gerir os recursos azure a partir da Linha de Comando ou através de scripts. Esta documentação detalha como gerir uma base de dados SAP HANA com reserva no Azure VM - todos utilizando o Azure CLI. Também pode executar estes passos utilizando [o portal Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

Utilize a [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos CLI.

No final deste tutorial, poderá:

> [!div class="checklist"]
>
> * Monitorizar backup e restaurar postos de trabalho
> * Proteja novas bases de dados adicionadas a um caso SAP HANA
> * Mudar a política
> * Parar proteção
> * Retomar a proteção

Se usou uma base de [dados SAP HANA em Azure usando o CLI](tutorial-sap-hana-backup-cli.md) para fazer o backup na sua base de dados SAP HANA, então está a utilizar os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* recipiente protegido chamado *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* base de dados/item com nome *de saphanadatabase;hxe;hxe*
* recursos na região *de Westus2*

O Azure CLI facilita a gestão de uma base de dados SAP HANA em funcionamento num VM Azure que é apoiado usando o Azure Backup. Este tutorial detalha cada uma das operações de gestão.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorizar backup e restaurar postos de trabalho

Para monitorizar os trabalhos concluídos ou atualmente em funcionamento (backup ou restauro), utilize a lista de trabalho de [backup az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet. O CLI também permite [suspender um emprego atualmente em funcionamento](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) ou [esperar até que um trabalho termine](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A saída será semelhante à seguinte:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Mudar a política

Para alterar a política subjacente à configuração de backup SAP HANA, utilize o conjunto de definição de definição de definição de [backup az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) O parâmetro de nome neste cmdlet refere-se ao item de reserva cuja política queremos mudar. Para este tutorial, vamos substituir a política da nossa base de dados SAP HANA *saphanadatabase;hxe;hxe* por uma nova política *newsaphanaPolicy*. Novas políticas podem ser criadas usando a política de [backup az criar](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Proteja novas bases de dados adicionadas a um caso SAP HANA

[Registar uma instância SAP HANA com um cofre](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) de serviços de recuperação descobre automaticamente todas as bases de dados deste caso.

No entanto, nos casos em que novas bases de dados são adicionadas à instância SAP HANA mais tarde, utilize o [item protectable de reserva az inicializar](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) cmdlet. Este cmdlet descobre as novas bases de dados adicionadas.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Em seguida, utilize a lista de [itens protegidos](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) de az para listar todas as bases de dados que foram descobertas na sua instância SAP HANA. Esta lista, no entanto, exclui as bases de dados em que a cópia de segurança já foi configurada. Uma vez descoberta a base de dados a ser apoiada, consulte a cópia de segurança ativa na base de [dados SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

A nova base de dados que pretende apoiar aparecerá nesta lista, que será a seguinte:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Parar a proteção para uma base de dados SAP HANA

Pode parar de proteger uma base de dados SAP HANA de várias maneiras:

* Pare todos os futuros trabalhos de backup e elimine todos os pontos de recuperação.
* Pare todos os futuros trabalhos de reserva e deixe os pontos de recuperação intactos.

Se optar por deixar pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, todas as podas devem parar na proteção stop com dados de retenção.
* Será cobrado pela instância protegida e pelo armazenamento consumido.
* Se eliminar uma fonte de dados sem parar cópias de segurança, novas cópias de segurança falharão.

Vamos ver cada uma das formas de parar a proteção com mais detalhes.

### <a name="stop-protection-with-retain-data"></a>Parar a proteção com reter dados

Para parar a proteção com os dados de retenção, utilize a proteção de [reserva az desativação](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o show de trabalho de [reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retain-data"></a>Parar a proteção sem reter dados

Para parar a proteção sem reter dados, utilize a proteção de [reserva az desativação](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o show de trabalho de [reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="resume-protection"></a>Retomar a proteção

Quando parar a proteção da base de dados SAP HANA com dados de retenção, poderá posteriormente retomar a proteção. Se não reter os dados de reserva, não poderá retomar a proteção.

Para retomar a proteção, utilize o currículo de proteção de [reserva az](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o show de trabalho de [reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Passos seguintes

* Para saber como fazer backup uma base de dados SAP HANA em funcionamento no Azure VM utilizando o portal Azure, consulte as bases de [dados Backup SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Para saber como gerir uma base de dados SAP HANA apoiada em Azure VM utilizando o portal Azure, consulte as bases de [dados Manage Backed Up SAP HANA no Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)

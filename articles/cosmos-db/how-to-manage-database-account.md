---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir os recursos DB da Azure Cosmos utilizando os modelos do portal Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 76e8167a5d0dc83a5cc1b238d3015594278e344d
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116697"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Azure Cosmos

Este artigo descreve como gerir várias tarefas numa conta do Azure Cosmos com o portal do Azure, o Azure PowerShell, a CLI do Azure e os modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>CLI do Azure

Por favor, consulte [Criar uma conta Azure Cosmos DB com o Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Por favor, consulte [Criar uma conta Azure Cosmos DB com a PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Modelo Azure Resource Manager

Por favor, consulte [a conta Create Azure Cosmos DB com modelos](manage-sql-with-resource-manager.md) de Gestor de Recursos Azure

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replicate globalmente.**

1. Para adicionar regiões, selecione os hexágonos no mapa com o **+** rótulo que corresponde à região(s) desejada. Alternativamente, para adicionar uma região, selecione a opção **região + Adicionar** e escolha uma região a partir do menu suspenso.

1. Para remover regiões, limpe uma ou mais regiões do mapa selecionando os hexágonos azuis com marcas de verificação. Ou selecione o ícone "wastebasket" ao 🗑 lado da região do lado direito.

1. Para guardar as suas alterações, selecione **OK**.

   ![Adicionar ou remover menu de regiões](./media/how-to-manage-database-account/add-region.png)

No modo de escrita de região única, não pode remover a região de escrita. Deve efetuar ativação pós-falha para uma região diferente para poder eliminar a região de escrita atual.

No modo de escrita de várias regiões, pode adicionar ou remover qualquer região desde que tenha, pelo menos, uma região.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>CLI do Azure

Consulte [Adicionar ou remover regiões com Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Consulte [Adicionar ou remover regiões com PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configurar várias regiões de escrita

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra o separador **Replicate Data Globalmente** e selecione **Enable** para ativar as escritas de várias regiões. Depois de permitir a escrita em várias regiões, todas as regiões de leitura que tem atualmente por conta tornar-se-ão regiões de leitura e escrita.

![Conta Azure Cosmos configura imagem multi-master](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>CLI do Azure

Por favor, consulte [Permitir regiões de múltiplas escritas com O CLI Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Por favor, consulte [Permitir regiões de múltiplas escritas com PowerShell](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de single-master para multi-master, implementando o modelo de Gestor de Recursos usado para criar a conta e definição `enableMultipleWriteLocations: true` . O seguinte modelo de Gestor de Recursos Azure é um modelo mínimo nu que irá implementar uma conta Azure Cosmos para a API SQL com duas regiões e múltiplas localizações de escrita habilitadas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Ativar a ativação pós-falha automática para a conta do Azure Cosmos

A opção de failover automática permite que a Azure Cosmos DB falhe na região com a maior prioridade de failover sem qualquer ação do utilizador caso uma região fique indisponível. Quando a falha automática é ativada, a prioridade da região pode ser modificada. A conta deve ter duas ou mais regiões para permitir a falha automática.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. A partir da sua conta Azure Cosmos, abra os **dados Replicate globalmente.**

2. Na parte superior do painel, selecione **Falha Automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **de falha automática,** certifique-se de que **o ativar** a falha automática está ligado a **.** 

4. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>CLI do Azure

Consulte [Enable falha automática com Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Por favor, consulte [Enable falha automática com PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de ativação pós-falha para a conta do Azure Cosmos

Depois de uma conta Cosmos estar configurada para falha automática, a prioridade de failover para as regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de escrita (prioridade de falha de zero) quando a conta está configurada para falha automática. Para alterar a região de escrita, deve desativar a falha automática e fazer uma falha manual.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. A partir da sua conta Azure Cosmos, abra os **dados Replicate globalmente.**

2. Na parte superior do painel, selecione **Falha Automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **de falha automática,** certifique-se de que **o ativar** a falha automática está ligado a **.**

4. Para modificar a prioridade de failover, arraste as regiões de leitura através dos três pontos do lado esquerdo da linha que aparecem quando paira sobre elas.

5. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>CLI do Azure

Por favor, consulte [definir falha de prioridade com Azure CLI](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Por favor, consulte [definir falha de prioridade com powerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Efetuar a ativação pós-falha manual numa conta do Azure Cosmos

> [!IMPORTANT]
> A conta Azure Cosmos deve ser configurada para o failover manual para que esta operação tenha sucesso.

O processo de realização de uma falha manual envolve a alteração da região de escrita da conta (failover priority = 0) para outra região configurada para a conta.

> [!NOTE]
> As contas multi-master não podem ser reprovadas manualmente. Para aplicações que utilizem o Azure Cosmos SDK, o SDK detetará quando uma região fica indisponível e, em seguida, redireciona automaticamente para a região mais próxima se utilizar API multi-homing no SDK.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replicate globalmente.**

2. Na parte superior do menu, selecione **Failover Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **Failover Manual,** selecione a sua nova região de escrita. Selecione a caixa de verificação para indicar que entende que esta opção muda a sua região de escrita.

4. Para acionar a falha, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>CLI do Azure

Por favor, consulte [o trigger manual failover com O ClI Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Por favor, consulte [a falha do manual do Gatilho com a PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações e exemplos sobre como gerir a conta Azure Cosmos, bem como bases de dados e contentores, leia os seguintes artigos:

* [Gerir o Azure Cosmos DB usando o Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md) (Gerir o Azure Cosmos DB com a CLI do Azure)

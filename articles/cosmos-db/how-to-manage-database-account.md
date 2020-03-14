---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir os recursos DB da Azure Cosmos utilizando os modelos do portal Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247387"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Azure Cosmos

Este artigo descreve como gerir várias tarefas numa conta do Azure Cosmos com o portal do Azure, o Azure PowerShell, a CLI do Azure e os modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>CLI do Azure

Por favor, consulte [Criar uma conta Azure Cosmos DB com o Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Por favor, consulte [Criar uma conta Azure Cosmos DB com powershell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Modelo de Gestor de Recursos Azure

Este modelo de Gestor de Recursos Azure criará uma conta Azure Cosmos para a SQL API configurada com duas regiões e opções para selecionar o nível de consistência, falha automática e multi-master. Para implementar este modelo, clique em Deploy para Azure na página readme, [Create Azure Cosmos account](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replicate globalmente.**

1. Para adicionar regiões, selecione os hexágonos no mapa com o rótulo **+** que corresponda à região(s) desejada. Alternativamente, para adicionar uma região, selecione a opção **região + Adicionar** e escolha uma região a partir do menu suspenso.

1. Para remover regiões, limpe uma ou mais regiões do mapa selecionando os hexágonos azuis com marcas de verificação. Ou selecione o ícone "wastebasket" (🗑) ao lado da região do lado direito.

1. Para guardar as suas alterações, selecione **OK**.

   ![Adicionar ou remover menu de regiões](./media/how-to-manage-database-account/add-region.png)

No modo de escrita de região única, não pode remover a região de escrita. Deve efetuar ativação pós-falha para uma região diferente para poder eliminar a região de escrita atual.

No modo de escrita de várias regiões, pode adicionar ou remover qualquer região desde que tenha, pelo menos, uma região.

### <a id="add-remove-regions-via-cli"></a>CLI do Azure

Consulte [Adicionar ou remover regiões com Azure CLI](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Por favor, consulte [Adicionar ou remover regiões com Powershell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de escrita

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra o separador **Replicate Data Globalmente** e selecione **Enable** para ativar as escritas de várias regiões. Depois de permitir a escrita em várias regiões, todas as regiões de leitura que tem atualmente por conta tornar-se-ão regiões de leitura e escrita.

![Conta Azure Cosmos configura imagem multi-master](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>CLI do Azure

Por favor, consulte [Permitir regiões de múltiplas escritas com O CLI Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Por favor, consulte [Permitir regiões de múltiplas escritas com Powershell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de single-master para multi-master, implementando o modelo de Gestor de Recursos usado para criar a conta e definir `enableMultipleWriteLocations: true`. O seguinte modelo de Gestor de Recursos Azure é um modelo mínimo nu que irá implementar uma conta Azure Cosmos para a API SQL com duas regiões e múltiplas localizações de escrita habilitadas.

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

## <a id="automatic-failover"></a>Ativar falha automática para a sua conta Azure Cosmos

A opção de failover automática permite que a Azure Cosmos DB falhe na região com a maior prioridade de failover sem qualquer ação do utilizador caso uma região fique indisponível. Quando a falha automática é ativada, a prioridade da região pode ser modificada. A conta deve ter duas ou mais regiões para permitir a falha automática.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. A partir da sua conta Azure Cosmos, abra os **dados Replicate globalmente.**

2. Na parte superior do painel, selecione **Falha Automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **de falha automática,** certifique-se de que **o ativar** a falha automática está ligado a **.** 

4. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>CLI do Azure

Consulte [Enable falha automática com Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Por favor, consulte [Enable falha automática com Powershell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de ativação pós-falha para a conta do Azure Cosmos

Depois de uma conta Cosmos estar configurada para falha automática, a prioridade de failover para as regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de escrita (prioridade de falha de zero) quando a conta está configurada para falha automática. Para alterar a região de escrita, deve desativar a falha automática e fazer uma falha manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. A partir da sua conta Azure Cosmos, abra os **dados Replicate globalmente.**

2. Na parte superior do painel, selecione **Falha Automática**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **de falha automática,** certifique-se de que **o ativar** a falha automática está ligado a **.**

4. Para modificar a prioridade de failover, arraste as regiões de leitura através dos três pontos do lado esquerdo da linha que aparecem quando paira sobre elas.

5. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>CLI do Azure

Por favor, consulte [definir falha de prioridade com Azure CLI](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Por favor, consulte [definir falha de prioridade com Powershell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Execute falha manual numa conta azure cosmos

> [!IMPORTANT]
> A conta Azure Cosmos deve ser configurada para o failover manual para que esta operação tenha sucesso.

O processo de realização de uma falha manual envolve a alteração da região de escrita da conta (failover priority = 0) para outra região configurada para a conta.

> [!NOTE]
> As contas multi-master não podem ser reprovadas manualmente. Para aplicações que utilizem o Azure Cosmos SDK, o SDK detetará quando uma região fica indisponível e, em seguida, redireciona automaticamente para a região mais próxima se utilizar API multi-homing no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replicate globalmente.**

2. Na parte superior do menu, selecione **Failover Manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **Failover Manual,** selecione a sua nova região de escrita. Selecione a caixa de verificação para indicar que entende que esta opção muda a sua região de escrita.

4. Para acionar a falha, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>CLI do Azure

Por favor, consulte [o trigger manual failover com O ClI Azure](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Por favor, consulte [o manual do gatilho falhacom a Powershell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações e exemplos sobre como gerir a conta Azure Cosmos, bem como bases de dados e contentores, leia os seguintes artigos:

* [Gerir o Azure Cosmos DB usando o Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md) (Gerir o Azure Cosmos DB com a CLI do Azure)

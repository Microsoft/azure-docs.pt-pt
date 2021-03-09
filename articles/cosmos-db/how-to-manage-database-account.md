---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerir os recursos DB da Azure Cosmos utilizando os modelos do portal Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489757"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como gerir várias tarefas numa conta do Azure Cosmos com o portal do Azure, o Azure PowerShell, a CLI do Azure e os modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portal Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Consulte [a Create a Azure Cosmos DB account with Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Consulte [Criar uma conta DB Azure Cosmos com PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Modelo Azure Resource Manager

Consulte a [conta DB da Create Azure Cosmos com modelos de Gestor de Recursos Azure](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portal Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com).

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replica globalmente.**

1. Para adicionar regiões, selecione os hexágonos no mapa com o **+** rótulo que corresponde à região desejada. Em alternativa, para adicionar uma região, selecione a opção **+ Adicionar região** e escolha uma região a partir do menu suspenso.

1. Para remover regiões, limpe uma ou mais regiões do mapa selecionando os hexágonos azuis com marcas de verificação. Ou selecione o ícone "cesto de lixo" 🗑 () ao lado da região do lado direito.

1. Para guardar as suas alterações, selecione **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Adicione ou remova o menu de regiões":::

No modo de escrita de região única, não pode remover a região de escrita. Deve efetuar ativação pós-falha para uma região diferente para poder eliminar a região de escrita atual.

No modo de escrita de várias regiões, pode adicionar ou remover qualquer região desde que tenha, pelo menos, uma região.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Por favor, consulte [Adicionar ou remover regiões com CLI Azure](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Por favor, consulte [Adicionar ou remover regiões com PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Configurar várias regiões de escrita

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portal Azure

Abra o **separador De Dados Replicar Globalmente** e selecione **Ativar** para permitir as gravações em várias regiões. Depois de permitir a escrita de várias regiões, todas as regiões de leitura que tem atualmente na conta passarão a ser regiões de leitura e escrita.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Conta Azure Cosmos configura multi-região escreve screenshot":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Consulte [Ativar regiões de escrita múltipla com Azure CLI](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Consulte [Ativar regiões de escrita múltipla com PowerShell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de uma única região de escrita para várias regiões de escrita, implantando o modelo de Gestor de Recursos utilizado para criar a conta e a `enableMultipleWriteLocations: true` definição. O modelo seguinte do Azure Resource Manager é um modelo mínimo que irá implementar uma conta Azure Cosmos para API SQL com duas regiões e vários locais de escrita ativados.

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

A opção de failover automática permite que a Azure Cosmos DB falhe na região com a maior prioridade de failover sem nenhuma ação do utilizador caso uma região fique indisponível. Quando a falha automática é ativada, a prioridade da região pode ser modificada. A conta deve ter duas ou mais regiões para permitir a cadência automática.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portal Azure

1. A partir da sua conta Azure Cosmos, abra o painel **de dados replicado globalmente.**

2. Na parte superior do painel, selecione **Automatic Failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No painel **de falha automática,** certifique-se de que **a ativação automática de falha** está definida para **ON**. 

4. Selecione **Guardar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu do portal de ativação pós-falha automática":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Consulte [Ativar a falha automática com o Azure CLI](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Consulte [Ativar a falha automática com o PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de ativação pós-falha para a conta do Azure Cosmos

Depois de uma conta Cosmos ser configurada para o failover automático, a prioridade de failover para as regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de escrita (prioridade de failover de zero) quando a conta está configurada para falha automática. Para alterar a região de escrita, tem de desativar a falha automática e fazer uma falha manual.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portal Azure

1. A partir da sua conta Azure Cosmos, abra o painel **de dados replicado globalmente.**

2. Na parte superior do painel, selecione **Automatic Failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No painel **de falha automática,** certifique-se de que **a ativação automática de falha** está definida para **ON**.

4. Para modificar a prioridade de failover, arraste as regiões de leitura através dos três pontos do lado esquerdo da linha que aparecem quando paira sobre elas.

5. Selecione **Guardar**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu do portal de ativação pós-falha automática":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Consulte [a prioridade de failover do set com o Azure CLI](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Consulte [a prioridade de failover de Definição com PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Efetuar a ativação pós-falha manual numa conta do Azure Cosmos

> [!IMPORTANT]
> A conta Azure Cosmos deve ser configurada para falha manual para que esta operação tenha sucesso.

O processo de realização de um failover manual implica a alteração da região de escrita da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> As contas com várias regiões de escrita não podem ser falhadas manualmente. Para aplicações que usam o Azure Cosmos SDK, o SDK detetará quando uma região fica indisponível e, em seguida, redireciona automaticamente para a região mais próxima.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portal Azure

1. Vá à sua conta Azure Cosmos e abra o menu **de dados Replica globalmente.**

2. No topo do menu, selecione **Manual Failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu Replicar dados globalmente":::

3. No menu **Manual Failover,** selecione a sua nova região de escrita. Selecione a caixa de verificação para indicar que compreende que esta opção altera a sua região de escrita.

4. Para ativar a falha, selecione **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menu do portal de ativação pós-falha manual":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Consulte [o trigger manual failover com Azure CLI](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Consulte [o gatilho de falha manual com PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos sobre como gerir a conta Azure Cosmos, bem como bases de dados e contentores, leia os seguintes artigos:

* [Gerir a Azure Cosmos DB usando a Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md) (Gerir o Azure Cosmos DB com a CLI do Azure)
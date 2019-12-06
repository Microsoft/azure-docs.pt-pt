---
title: Saiba como gerir contas de base de dados no Azure Cosmos DB
description: Saiba como gerenciar recursos de Azure Cosmos DB usando os modelos portal do Azure, PowerShell, CLI e Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873680"
---
# <a name="manage-an-azure-cosmos-account"></a>Gerir uma conta do Azure Cosmos

Este artigo descreve como gerir várias tarefas numa conta do Azure Cosmos com o portal do Azure, o Azure PowerShell, a CLI do Azure e os modelos do Azure Resource Manager.

## <a name="create-an-account"></a>Criar uma conta

### <a id="create-database-account-via-portal"></a>Portal do Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>CLI do Azure

Consulte [criar uma conta de Azure Cosmos DB com CLI do Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Consulte [criar uma conta de Azure Cosmos DB com o PowerShell](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Modelo de Azure Resource Manager

Este modelo de Azure Resource Manager criará uma conta do Azure Cosmos para a API do SQL configurada com duas regiões e opções para selecionar nível de consistência, failover automático e vários mestres. Para implantar esse modelo, clique em implantar no Azure na página Leiame, [criar conta do Azure Cosmos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Adicionar/remover regiões da conta de base de dados

### <a id="add-remove-regions-via-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Acesse sua conta do Azure Cosmos e abra o menu **replicar dados globalmente** .

1. Para adicionar regiões, selecione os hexágonos no mapa com o rótulo de **+** que corresponde às regiões desejadas. Como alternativa, para adicionar uma região, selecione a opção **+ Adicionar região** e escolha uma região no menu suspenso.

1. Para remover regiões, desmarque uma ou mais regiões do mapa selecionando os hexágonos azuis com marcas de seleção. Ou selecione o ícone "lixeira" (🗑) ao lado da região no lado direito.

1. Para salvar as alterações, selecione **OK**.

   ![Menu Adicionar ou remover regiões](./media/how-to-manage-database-account/add-region.png)

Em um modo de gravação de região única, não é possível remover a região de gravação. Você deve fazer failover para uma região diferente antes de excluir a região de gravação atual.

Em um modo de gravação de várias regiões, você pode adicionar ou remover qualquer região, se tiver pelo menos uma região.

### <a id="add-remove-regions-via-cli"></a>CLI do Azure

Consulte [Adicionar ou remover regiões com CLI do Azure](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Consulte [Adicionar ou remover regiões com o PowerShell](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Configurar várias regiões de gravação

### <a id="configure-multiple-write-regions-portal"></a>Portal do Azure

Abra a guia **replicar dados globalmente** e selecione **habilitar** para habilitar gravações em várias regiões. Depois de habilitar as gravações de várias regiões, todas as regiões de leitura que você tem atualmente na conta ficarão regiões de leitura e gravação.

![A conta do Azure Cosmos configura a captura de tela de vários mestres](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>CLI do Azure

Consulte [habilitar regiões de várias gravações com CLI do Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Consulte [habilitar regiões de várias gravações com o PowerShell](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Modelo do Resource Manager

Uma conta pode ser migrada de um mestre único para vários mestres implantando o modelo do Resource Manager usado para criar a conta e a configuração `enableMultipleWriteLocations: true`. O modelo de Azure Resource Manager a seguir é um modelo mínimo que implantará uma conta do Azure Cosmos para a API do SQL com duas regiões e vários locais de gravação habilitados.

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

## <a id="automatic-failover"></a>Habilitar o failover automático para sua conta do Azure Cosmos

A opção de failover automático permite que Azure Cosmos DB failover para a região com a maior prioridade de failover sem ação do usuário se uma região ficar indisponível. Quando o failover automático estiver habilitado, a prioridade da região poderá ser modificada. A conta deve ter duas ou mais regiões para habilitar o failover automático.

### <a id="enable-automatic-failover-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **replicar dados globalmente** .

2. Na parte superior do painel, selecione **failover automático**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **failover automático** , verifique se **habilitar failover automático** está definido como **ativado**. 

4. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>CLI do Azure

Consulte [habilitar failover automático com CLI do Azure](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Consulte [habilitar failover automático com o PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Definir prioridades de failover para sua conta do Azure Cosmos

Depois que uma conta do cosmos é configurada para failover automático, a prioridade de failover para regiões pode ser alterada.

> [!IMPORTANT]
> Não é possível modificar a região de gravação (prioridade de failover zero) quando a conta está configurada para failover automático. Para alterar a região de gravação, você deve desabilitar o failover automático e fazer um failover manual.

### <a id="set-failover-priorities-via-portal"></a>Portal do Azure

1. Na sua conta do Azure Cosmos, abra o painel **replicar dados globalmente** .

2. Na parte superior do painel, selecione **failover automático**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No painel **failover automático** , verifique se **habilitar failover automático** está definido como **ativado**.

4. Para modificar a prioridade do failover, arraste as regiões de leitura por meio dos três pontos no lado esquerdo da linha que aparece quando você passa o mouse sobre elas.

5. Selecione **Guardar**.

   ![Menu do portal de ativação pós-falha automática](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>CLI do Azure

Consulte [definir a prioridade de failover com CLI do Azure](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Consulte [definir a prioridade de failover com o PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Executar failover manual em uma conta do Azure Cosmos

> [!IMPORTANT]
> A conta do Azure Cosmos deve ser configurada para failover manual para que essa operação seja realizada com sucesso.

O processo para executar um failover manual envolve alterar a região de gravação da conta (prioridade de failover = 0) para outra região configurada para a conta.

> [!NOTE]
> Não é possível fazer failover manual das contas de vários mestres. Para aplicativos que usam o SDK do cosmos do Azure, o SDK detectará quando uma região ficar indisponível e, em seguida, redirecionará automaticamente para a região mais próxima se usar a API de hospedagem múltipla no SDK.

### <a id="enable-manual-failover-via-portal"></a>Portal do Azure

1. Acesse sua conta do Azure Cosmos e abra o menu **replicar dados globalmente** .

2. Na parte superior do menu, selecione **failover manual**.

   ![Menu Replicar dados globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. No menu **failover manual** , selecione sua nova região de gravação. Marque a caixa de seleção para indicar que você entende que essa opção altera sua região de gravação.

4. Para disparar o failover, selecione **OK**.

   ![Menu do portal de ativação pós-falha manual](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>CLI do Azure

Consulte [disparar failover manual com CLI do Azure](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Consulte [disparar failover manual com o PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações e exemplos sobre como gerenciar a conta do Azure Cosmos, bem como banco de dados e contêineres, leia os seguintes artigos:

* [Gerenciar Azure Cosmos DB usando Azure PowerShell](manage-with-powershell.md)
* [Manage Azure Cosmos DB using Azure CLI](manage-with-cli.md) (Gerir o Azure Cosmos DB com a CLI do Azure)

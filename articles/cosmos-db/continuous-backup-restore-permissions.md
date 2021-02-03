---
title: Configure permissões para restaurar uma conta DB Azure Cosmos.
description: Saiba como isolar e restringir as permissões de restauro para uma conta de backup contínua a uma função específica ou a um principal. Mostra como atribuir um papel incorporado usando o portal Azure, CLI, ou definir um papel personalizado.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 3614a85a6df2e793a73a2609d6f5762e4dc873fb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527863"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Gerir permissões para restaurar uma conta DB da Azure Cosmos
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

O Azure Cosmos DB permite-lhe isolar e restringir as permissões de restauro para uma conta de backup contínua a um papel específico ou a um principal. O proprietário da conta pode desencadear uma restauração e atribuir uma função a outros principais para realizar a operação de restauro. Estas permissões podem ser aplicadas no âmbito de subscrição ou mais granularmente no âmbito da conta de origem, tal como mostrado na imagem seguinte:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista de funções necessárias para executar a operação de restauro." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

O âmbito é um conjunto de recursos que têm acesso, para aprender mais sobre os âmbitos, ver a documentação do [Azure RBAC.](../role-based-access-control/scope-overview.md) No Azure Cosmos DB, os âmbitos aplicáveis são a conta de subscrição e base de dados de origem para a maioria dos casos de utilização. O principal responsável pelas ações de restauro deve ter permissões de escrita para o grupo de recursos de destino.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Atribuir funções para restauro utilizando o portal Azure

Para efetuar uma restauração, um utilizador ou um principal precisa da permissão para restaurar (que é permissão de "restaurar/ação" ), e permissão para providenciar uma nova conta (que é permissão de "escrever").  Para conceder estas permissões, o proprietário pode atribuir o "CosmosRestoreOperator" e "Cosmos DB Operator" construídos em funções a um principal.

1. Inscreva-se no [portal Azure](https://portal.azure.com/)

1. Navegue para a sua subscrição e vá ao **separador Access Control (IAM)** e selecione **Add**  >  **Fun assignment**

1. No painel de atribuição de **funções Add,** para o campo **Role,** selecione o papel **cosmosRestoreOperator.** Escolha **o utilizador, grupo ou um principal de serviço** para o acesso do Designado **ao** campo e procure o nome de um utilizador ou iD de e-mail, como mostrado na imagem seguinte:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Atribua funções de Operador CosmosRestoreOperator e Cosmos DB." border="true":::

1. **Selecione Guardar** para conceder a "permissão de restauro/ação".

1. Repita o passo 3 com a função **de operador de DB cosmos** para conceder a permissão de escrita. Ao atribuir este papel a partir do portal Azure, concede a permissão de restauro a toda a subscrição.

## <a name="permission-scopes"></a>Âmbitos de permissão

|Âmbito  |Exemplo  |
|---------|---------|
|Subscrição | /subscrições/00000000-0000-0000-0000-0000-0000000000000000000 |
|Grupo de recursos | /subscrições/00000000-0000-0000-0000-0000000000000000/resourceGroups/Exemplo-cosmosdb-rg |
|Recurso de conta restaurador cosmosDB | /subscrições/00000000-0000-0000-0000-0000000000000000/fornecedores/Microsoft.DocumentDB/localizações/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

O recurso de conta restaurador pode ser extraído da saída do `az cosmosdb restorable-database-account list --name <accountname>` comando em CLI ou `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet em PowerShell. O atributo de nome na saída representa o "casoID" da conta restauradora. Para saber mais, consulte o artigo [PowerShell](continuous-backup-restore-powershell.md) ou [CLI.](continuous-backup-restore-command-line.md)

## <a name="permissions"></a>Permissões

As permissões seguintes são necessárias para realizar as diferentes atividades relativas à restauração para contas contínuas de modo de backup:

|Permissão  |Impacto  |Âmbito mínimo  |Âmbito máximo  |
|---------|---------|---------|---------|
|Microsoft.Resources/deployments/valide/action, Microsoft.Resources/deployments/write | Estas permissões são necessárias para a implementação do modelo ARM para criar a conta restaurada. Consulte a permissão de amostra [RestorableAction]() abaixo para saber como definir esta função. | Não aplicável | Não aplicável  |
|Microsoft.DocumentDB/base de dadosAconselhos/escrita | Esta permissão é necessária para restaurar uma conta em um grupo de recursos | Grupo de recursos sob o qual a conta restaurada é criada. | Subscrição sob a qual a conta restaurada é criada |
|Microsoft.DocumentDB/localizações/restrableDatabaseAccounts/restaurar/ação |Esta permissão é exigida no âmbito da conta de conta de base de dados restauradora de origem para permitir a repor as ações a serem realizadas na sua base.  | O recurso "RestorableDatabaseAccount" pertencente à conta de origem que está a ser restaurada. Este valor é também dado pela propriedade "ID" do recurso de conta de base de dados restaurador. Um exemplo de conta restauradora é `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>` | A assinatura que contém a conta de base de dados restauradora. O grupo de recursos não pode ser escolhido como âmbito.  |
|Microsoft.DocumentDB/localizações/restorableDatabaseAccounts/read |Esta permissão é exigida no âmbito da conta de base de dados de origem para listar as contas de base de dados que podem ser restauradas.  | O recurso "RestorableDatabaseAccount" pertencente à conta de origem que está a ser restaurada. Este valor é também dado pela propriedade "ID" do recurso de conta de base de dados restaurador. Um exemplo de conta restauradora é `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| A assinatura que contém a conta de base de dados restauradora. O grupo de recursos não pode ser escolhido como âmbito.  |
|Microsoft.DocumentDB/localizações/restorableDatabaseAccounts/*/read | Esta permissão é exigida no âmbito da conta restauradora de origem para permitir a leitura de recursos restauradores, tais como lista de bases de dados e contentores para uma conta restauradora.  | O recurso "RestorableDatabaseAccount" pertencente à conta de origem que está a ser restaurada. Este valor é também dado pela propriedade "ID" do recurso de conta de base de dados restaurador. Um exemplo de conta restauradora é `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| A assinatura que contém a conta de base de dados restauradora. O grupo de recursos não pode ser escolhido como âmbito. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Cenários de atribuição de funções do Azure CLI para restaurar em diferentes âmbitos

As funções com permissão podem ser atribuídas a diferentes âmbitos para obter controlo granular sobre quem pode realizar a operação de restauro dentro de uma subscrição ou de uma determinada conta.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Atribuir capacidade para restaurar a partir de qualquer conta restauradora numa subscrição

Atribuir o `CosmosRestoreOperator` papel incorporado ao nível de subscrição

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Atribuir capacidade de restauro a partir de uma conta específica

* Atribua um utilizador a agir no grupo de recursos específico. Esta ação é necessária para criar uma nova conta no grupo de recursos.

* Atribua a função incorporada "CosmosRestoreOperator" à conta de base de dados reparadora específica que precisa de ser restaurada. No seguinte comando, o âmbito para a "RestaurarableDatabaseAccount" é recuperado da propriedade "ID" na saída de `az cosmosdb restorable-database-account` (se utilizar O CLI) ou  `Get-AzCosmosDBRestorableDatabaseAccount` (se utilizar o PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Atribua capacidade para restaurar a partir de qualquer conta de origem num grupo de recursos.
Esta operação não está suportada neste momento.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Criação de função personalizada para restaurar a ação com o CLI

O proprietário da subscrição pode fornecer a permissão para restaurar qualquer outra identidade AD AZure. A permissão de restauro baseia-se na ação: "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action", e deve ser incluída na sua permissão de restauro. Há um papel incorporado chamado "CosmosRestoreOperator" que tem este papel incluído. Pode atribuir a permissão usando esta função incorporada ou criar uma função personalizada.

A RestaurarableAction abaixo representa um papel personalizado. Tens de criar explicitamente este papel. O modelo JSON a seguir cria uma função personalizada "RestorableAction" com permissão de restauro:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Em seguida, utilize o seguinte comando de implementação do modelo para criar uma função com a permissão de restauro usando o modelo ARM:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Passos seguintes

* Configure e gere a cópia de segurança contínua utilizando o [portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)

---
title: Controlo de acesso baseado em funções Azure em Azure Cosmos DB
description: Saiba como a Azure Cosmos DB fornece proteção de base de dados com integração de diretório ativo (Azure RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: 8f3e2e96a529ea3347f09729146e73d5517ed759
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692152"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Controlo de acesso baseado em funções Azure em Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Este artigo é sobre controlo de acesso baseado em funções para operações de avião de gestão em Azure Cosmos DB. Se estiver a utilizar operações de plano de [dados, consulte o Azure Cosmos DB RBAC](how-to-setup-rbac.md) para controlo de acesso baseado em funções aplicado às suas operações de plano de dados.

A Azure Cosmos DB fornece um controlo de acesso baseado em funções (Azure RBAC) para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory pode atribuir estas funções Azure a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos. As atribuições de funções são apenas para acesso de avião de controlo, o que inclui o acesso às contas da Azure Cosmos, bases de dados, contentores e ofertas (produção).

## <a name="built-in-roles"></a>Funções incorporadas

Seguem-se os papéis integrados apoiados pela Azure Cosmos DB:

|**Papel incorporado**  |**Descrição**  |
|---------|---------|
|[Colaborador de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerir as contas de DB da Azure Cosmos.|
|[Cosmos DB Leitor de Conta](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler os dados da conta DB da Azure Cosmos.|
|[Operador de backup cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Pode submeter um pedido de restauro para o portal Azure para uma base de dados de backup periódica ou um recipiente. Pode modificar o intervalo de backup e a retenção no portal Azure. Não é possível aceder a nenhum dado ou utilizar o Data Explorer.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | Pode executar ação de restauro para conta DB Azure Cosmos com modo de backup contínuo.|
|[Operador de DB cosmos](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Pode providenciar contas, bases de dados e contentores da Azure Cosmos. Não é possível aceder a nenhum dado ou utilizar o Data Explorer.|

> [!IMPORTANT]
> O suporte do Azure RBAC em Azure Cosmos DB aplica-se apenas às operações de controlo do avião. As operações de planos de dados são protegidas usando chaves primárias ou fichas de recursos. Para saber mais, consulte [acesso seguro aos dados em Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestão de identidade e acesso (IAM)

O painel **de controlo de acesso (IAM)** no portal Azure é utilizado para configurar o controlo de acesso baseado em funções da Azure Cosmos. As funções são aplicadas aos utilizadores, grupos, diretores de serviços e identidades geridas no Ative Directory. Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos. A imagem que se segue mostra a integração do Ative Directory (Azure RBAC) utilizando o controlo de acesso (IAM) no portal Azure:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados.":::

## <a name="custom-roles"></a>Funções personalizadas

Além das funções incorporadas, os utilizadores podem também criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar estas funções aos diretores de serviços em todas as subscrições dentro do seu inquilino Ative Directory. As funções personalizadas fornecem aos utilizadores uma forma de criar definições de função Azure com um conjunto personalizado de operações de fornecedor de recursos. Para saber quais as operações disponíveis para a construção de funções personalizadas para a Azure Cosmos DB ver, [Azure Cosmos DB fornecedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> As funções personalizadas que precisam de aceder aos dados armazenados dentro do Cosmos DB ou utilizar o Data Explorer no portal Azure devem ter `Microsoft.DocumentDB/databaseAccounts/listKeys/*` ação.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Prevenção de alterações dos DB SDKs Azure Cosmos

O fornecedor de recursos DB da Azure Cosmos pode ser bloqueado para evitar alterações nos recursos de um cliente que se conecte utilizando as chaves da conta (ou seja, aplicações que se ligam através do Azure Cosmos SDK). Isto também inclui alterações feitas a partir do portal Azure. Esta funcionalidade pode ser desejável para os utilizadores que pretendam maiores graus de controlo e governação para ambientes de produção. A prevenção de alterações do SDK também permite funcionalidades como bloqueios de recursos e registos de diagnóstico para operações de plano de controlo. Os clientes que se ligarem à Azure Cosmos DB SDK serão impedidos de alterar qualquer propriedade para as contas da Azure Cosmos, bases de dados, contentores e produção. As operações que envolvem a leitura e a escrita de dados para os próprios contentores da Cosmos não são afetadas.

Quando esta funcionalidade está ativada, as alterações a qualquer recurso só podem ser feitas a partir de um utilizador com o papel Azure certo e credenciais de Diretório Azure Ative, incluindo identidades de serviço gerido.

> [!WARNING]
> Ativar esta funcionalidade pode ter impacto na sua aplicação. Certifique-se de que compreende o impacto antes de o ativar.

### <a name="check-list-before-enabling"></a>Verifique a lista antes de ativar

Esta definição evitará quaisquer alterações a qualquer recurso cosmos de qualquer cliente que se conecte utilizando chaves de conta, incluindo qualquer Cosmos DB SDK, quaisquer ferramentas que se conectem através de chaves de conta ou do portal Azure. Para evitar problemas ou erros das aplicações após ativar esta funcionalidade, verifique se as aplicações ou os utilizadores do portal Azure executam alguma das seguintes ações antes de ativar esta funcionalidade, incluindo:

- Qualquer alteração na conta cosmos, incluindo quaisquer propriedades ou adição ou remoção de regiões.

- Criar, eliminar recursos infantis, como bases de dados e contentores. Isto inclui recursos para outras APIs, tais como Cassandra, MongoDB, Gremlin, e recursos de mesa.

- Atualização do rendimento na base de dados ou nos recursos de nível de contentores.

- Modificação das propriedades dos contentores, incluindo a política de índice, TTL e chaves únicas.

- Modificação de procedimentos armazenados, gatilhos ou funções definidas pelo utilizador.

Se as suas aplicações (ou utilizadores através do portal Azure) realizarem alguma destas ações, terão de ser migradas para serem executadas através [de Modelos ARM](./manage-with-templates.md), [PowerShell,](manage-with-powershell.md) [Azure CLI,](manage-with-cli.md)REST ou [Biblioteca de Gestão Azure.](https://github.com/Azure-Samples/cosmos-management-net) Note que a Azure Management está disponível em [vários idiomas.](../index.yml?product=developer-tools#languages-and-tools)

### <a name="set-via-arm-template"></a>Definido via modelo ARM

Para definir esta propriedade usando um modelo ARM, atualize o seu modelo existente ou exporte um novo modelo para a sua implementação atual, em seguida, inclua as `"disableKeyBasedMetadataWriteAccess": true` propriedades para os `databaseAccounts` recursos. Abaixo está um exemplo básico de um modelo de Gestor de Recursos Azure com esta definição de propriedade.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Certifique-se de que inclui as outras propriedades para a sua conta e recursos infantis ao redparear com esta propriedade. Não implemente este modelo como está ou irá redefinir todas as propriedades da sua conta.

### <a name="set-via-azure-cli"></a>Definido via Azure CLI

Para ativar a utilização do Azure CLI, utilize o comando abaixo:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Definido via PowerShell

Para ativar a utilização do Azure PowerShell, utilize o comando abaixo:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Passos seguintes

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md)
- [Funções personalizadas do Azure](../role-based-access-control/custom-roles.md)
- [Operações de fornecedor de recursos DB da Azure Cosmos](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
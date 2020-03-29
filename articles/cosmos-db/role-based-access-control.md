---
title: Controlo de acesso baseado em funções no Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece proteção de base de dados com integração de diretórios Ativos (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445090"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controlo de acesso baseado em funções no Azure Cosmos DB

A Azure Cosmos DB fornece um controlo de acesso baseado em funções (RBAC) para cenários comuns de gestão em Azure Cosmos DB. Um indivíduo que tenha um perfil no Azure Ative Directory pode atribuir estas funções rBAC a utilizadores, grupos, diretores de serviços ou identidades geridas para conceder ou negar o acesso a recursos e operações em recursos DB da Azure Cosmos. As atribuições de funções são orientadas apenas para o acesso ao plano de controlo, que inclui acesso a contas, bases de dados, contentores e ofertas da Azure Cosmos (entrada).

## <a name="built-in-roles"></a>Funções incorporadas

Seguem-se as funções incorporadas apoiadas pela Azure Cosmos DB:

|**Papel incorporado**  |**Descrição**  |
|---------|---------|
|[Contribuinte de Conta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerir as contas da Azure Cosmos DB.|
|[Leitor de Conta Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler os dados da conta Azure Cosmos DB.|
|[Operador de backup Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Pode submeter o pedido de restauro de uma base de dados Azure Cosmos ou um recipiente.|
|[Operador DB cosmos](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Pode fornecer contas, bases de dados e contentores da Azure Cosmos, mas não consegue aceder às chaves que são necessárias para aceder aos dados.|

> [!IMPORTANT]
> O suporte rBAC no Azure Cosmos DB aplica-se apenas ao controlo das operações de aviões. As operações de avião de dados são asseguradas utilizando chaves-me-mestre ou fichas de recursos. Para saber mais, consulte [Acesso seguro aos dados em Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestão de identidade e acesso (IAM)

O painel de controlo de **acesso (IAM)** no portal Azure é utilizado para configurar o controlo de acesso baseado em papéis nos recursos da Azure Cosmos. As funções são aplicadas a utilizadores, grupos, diretores de serviços e identidades geridas no Diretório Ativo. Você pode usar papéis incorporados ou papéis personalizados para indivíduos e grupos. A seguinte imagem mostra integração de Diretório Ativo (RBAC) utilizando o controlo de acesso (IAM) no portal Azure:

![Controlo de acesso (IAM) no portal Azure - demonstrando segurança na base de dados](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Funções personalizadas

Além das funções incorporadas, os utilizadores também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar estas funções aos principais de serviço em todas as subscrições dentro do seu inquilino Ative Directory. As funções personalizadas fornecem aos utilizadores uma forma de criar definições de função RBAC com um conjunto personalizado de operações de fornecedor de recursos. Para saber que operações estão disponíveis para construir funções personalizadas para a Azure Cosmos DB ver, operações de fornecedor de [recursos Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Prevenção de alterações da Cosmos SDK

O fornecedor de recursos da Cosmos pode ser bloqueado para evitar quaisquer alterações nos recursos, incluindo a conta Cosmos, bases de dados, contentores e entrada de qualquer cliente que ligue através de chaves de conta (ou seja, aplicações que liguem através da Cosmos SDK). Quando definido, as alterações a qualquer recurso devem ser de um utilizador com a função e credenciais RBAC adequadas. Esta capacidade é `disableKeyBasedMetadataWriteAccess` definida com o valor imobiliário no fornecedor de recursos da Cosmos. Um exemplo de um modelo de Gestor de Recursos Azure com esta definição de propriedade está abaixo.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
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

## <a name="next-steps"></a>Passos seguintes

- [O que é o controlo de acesso baseado em papéis (RBAC) para os recursos do Azure](../role-based-access-control/overview.md)
- [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Operações de prestadorde de recursos Da Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

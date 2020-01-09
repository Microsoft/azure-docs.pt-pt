---
title: Controle de acesso baseado em função no Azure Cosmos DB
description: Saiba como Azure Cosmos DB fornece proteção de banco de dados com RBAC (integração do Active Directory).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445090"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controle de acesso baseado em função no Azure Cosmos DB

O Azure Cosmos DB fornece RBAC (controle de acesso baseado em função) interno para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory pode atribuir essas funções de RBAC a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos. As atribuições de função têm como escopo o acesso ao plano de controle, que inclui acesso a contas, bancos de dados, contêineres e ofertas (taxa de transferência) do Azure Cosmos.

## <a name="built-in-roles"></a>Funções incorporadas

A seguir estão as funções internas com suporte pelo Azure Cosmos DB:

|**Função interna**  |**Descrição**  |
|---------|---------|
|[Colaborador de conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas de Azure Cosmos DB.|
|[Cosmos DB leitor de conta](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler Azure Cosmos DB dados da conta.|
|[Operador de backup Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Pode enviar solicitação de restauração para um banco de dados Cosmos do Azure ou um contêiner.|
|[Operador de Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|O pode provisionar contas, bancos de dados e contêineres do Azure Cosmos, mas não pode acessar as chaves necessárias para acessar os dados.|

> [!IMPORTANT]
> O suporte a RBAC no Azure Cosmos DB se aplica somente às operações do plano de controle. As operações do plano de dados são protegidas usando chaves mestras ou tokens de recurso. Para saber mais, confira [proteger o acesso aos dados no Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestão de identidades e acessos (IAM)

O painel de **controle de acesso (iam)** no portal do Azure é usado para configurar o controle de acesso baseado em função nos recursos de Cosmos do Azure. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos. A captura de tela a seguir mostra o RBAC (integração de Active Directory) usando IAM (controle de acesso) no portal do Azure:

![IAM (controle de acesso) no portal do Azure-demonstrando a segurança do banco de dados](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Funções personalizadas

Além das funções internas, os usuários também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar essas funções a entidades de serviço em todas as assinaturas em seu locatário Active Directory. As funções personalizadas fornecem aos usuários uma maneira de criar definições de função RBAC com um conjunto personalizado de operações de provedor de recursos. Para saber quais operações estão disponíveis para a criação de funções personalizadas para Azure Cosmos DB consulte, [Azure Cosmos DB operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Impedindo alterações do SDK do cosmos

O provedor de recursos Cosmos pode ser bloqueado para evitar qualquer alteração nos recursos, incluindo conta do cosmos, bancos de dados, contêineres e taxa de transferência de qualquer cliente que se conecte por meio de chaves de conta (ou seja, aplicativos que se conectam via SDK do cosmos). Quando definido, as alterações em qualquer recurso devem ser de um usuário com a função e as credenciais de RBAC adequadas. Esse recurso é definido com `disableKeyBasedMetadataWriteAccess` valor da propriedade no provedor de recursos Cosmos. Veja abaixo um exemplo de um modelo de Azure Resource Manager com essa configuração de propriedade.

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

- [O que é o RBAC (controle de acesso baseado em função) para recursos do Azure](../role-based-access-control/overview.md)
- [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Operações do provedor de recursos Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

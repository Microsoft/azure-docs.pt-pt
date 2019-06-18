---
title: Controlo de acesso baseado em funções no Azure Cosmos DB com a integração do Azure Active Directory
description: Saiba como o Azure Cosmos DB fornece a proteção de base de dados com integração do Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243503"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Controlo de acesso baseado em funções no Azure Cosmos DB

O Azure Cosmos DB fornece controlo de acesso interno baseado em funções (RBAC) para cenários comuns de gestão no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory pode atribuir estas funções RBAC a utilizadores, grupos, os principais de serviço ou geridos identidades para conceder ou negar o acesso a recursos e operações em recursos do Azure Cosmos DB. Atribuições de funções estão no âmbito de plano de controlo de acesso apenas, que inclui acesso a contas do Cosmos do Azure, bases de dados, contentores e ofereça (débito).

## <a name="built-in-roles"></a>Funções incorporadas

Seguem-se as funções incorporadas suportadas pelo Azure Cosmos DB:

|**Função incorporada**  |**Descrição**  |
|---------|---------|
|[Contribuinte de contas do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Pode gerir contas do Azure Cosmos DB.  |
|[Leitor de conta do cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Pode ler os dados da conta do Azure Cosmos DB.        |
|[Operador de cópia de segurança de cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Pode submeter o pedido de restauro de uma base de dados do Cosmos do Azure ou um contentor.       |
|[Operador de cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Pode aprovisionar contas do Azure Cosmos, bases de dados e contentores, mas não é possível aceder às chaves que são necessárias para aceder aos dados.         |

> [!IMPORTANT]
> Suporte RBAC do Azure Cosmos DB aplica-se apenas operações do painel de controle. Operações do plano de dados estão protegidas com o mestre de chaves ou tokens de recursos. Para obter mais informações, consulte [proteger o acesso a dados no Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Gestão de identidades e acessos (IAM)

O **controlo de acesso (IAM)** painel no portal do Azure é utilizado para configurar o controlo de acesso baseado em funções nos recursos do Azure Cosmos. As funções são aplicadas aos utilizadores, grupos, os principais de serviço e identidades geridas no Active Directory. Pode utilizar funções incorporadas ou funções personalizadas para indivíduos e grupos. Captura de ecrã seguinte mostra a integração do Active Directory (RBAC) com o controlo de acesso (IAM) no portal do Azure:

![Controlo de acesso (IAM) no portal do Azure - segurança da base de dados de demonstração](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Funções personalizadas

Para além das funções incorporadas, os utilizadores também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar essas funções para principais de serviço em todas as subscrições no respetivo inquilino do Active Directory. Funções personalizadas fornecem aos utilizadores uma forma de criar definições de funções do RBAC com um conjunto personalizado de recursos de operações do fornecedor. Para saber quais operações estão disponíveis para criar funções personalizadas para Azure Cosmos DB, consulte [operações de fornecedor de recursos do Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Passos Seguintes

- [O que é o controlo de acesso baseado em funções (RBAC) para recursos do Azure](../role-based-access-control/overview.md)
- [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)
- [Operações de fornecedor de recursos do Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

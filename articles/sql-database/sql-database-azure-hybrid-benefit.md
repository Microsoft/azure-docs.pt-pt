---
title: Benefício Híbrido do Azure
description: Utilize as licenças sQL Server existentes para descontos na Base de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945626"
---
# <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

No nível de cálculo provisionado do modelo de compra baseado em vCore, pode trocar as suas licenças existentes por tarifas descontadas na Base de Dados SQL utilizando o [Benefício Híbrido Azure para o Servidor SQL](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício Azure permite-lhe economizar até 30% ou mesmo mais na Base de Dados Azure SQL utilizando as suas licenças SQL Server no local com Garantia de Software. Utilize a Calculadora de Benefícios Híbridos Azure utilizando o link mencionado anteriormente para valores corretos. 

> [!NOTE]
> A mudança para O Benefício Híbrido Azure não requer qualquer tempo de inatividade.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Escolha um modelo de licença

Com o Azure Hybrid Benefit, pode optar por pagar apenas pela infraestrutura Azure subjacente utilizando a licença SQL Server existente para o próprio motor de base de dados SQL (preços base Compute), ou pode pagar tanto pela infraestrutura subjacente como pela licença SQL Server (preço incluído na licença).

Pode escolher ou alterar o seu modelo de licenciamento utilizando o portal Azure ou utilizando uma das seguintes APIs:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para definir ou atualizar o tipo de licença utilizando o PowerShell:

- [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para definir ou atualizar o tipo de licença utilizando o Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi criar](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi atualização](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[API REST](#tab/rest)

Para definir ou atualizar o tipo de licença utilizando a API REST:

- [Bases de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate)
- [Bases de dados - Atualização](/rest/api/sql/databases/update)
- [Instâncias Geridas - Criar ou Atualizar](/rest/api/sql/managedinstances/createorupdate)
- [Instâncias Geridas - Atualização](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Passos seguintes

- Para escolher entre as opções de implementação da Base de Dados SQL, consulte [Escolha a opção de implementação certa no Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Para uma comparação das funcionalidades da Base de Dados SQL, consulte as funcionalidades da Base de [Dados Azure SQL](sql-database-features.md).

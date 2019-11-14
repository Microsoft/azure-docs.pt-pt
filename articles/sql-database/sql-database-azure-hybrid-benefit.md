---
title: Benefício Híbrido do Azure
description: Use licenças de SQL Server existentes para descontos do banco de dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 4aab699ff0141b648066fae0de445c4467be7509
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048399"
---
# <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Na camada de computação provisionada do modelo de compra baseado em vCore, você pode trocar suas licenças existentes por tarifas com desconto no banco de dados SQL usando [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Esse benefício do Azure permite que você economize até 30% no banco de dados SQL do Azure usando suas licenças de SQL Server locais com o Software Assurance. 

> [!NOTE]
> Alterar para Benefício Híbrido do Azure não requer nenhum tempo de inatividade.

![refere](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Escolher um modelo de licença

Com Benefício Híbrido do Azure, você pode optar por pagar apenas pela infraestrutura subjacente do Azure usando sua licença de SQL Server existente para o próprio mecanismo de banco de dados SQL (preço de computação base) ou pode pagar pela infraestrutura subjacente e pelo SQL Server licença (preço incluído na licença).

Você pode escolher ou alterar seu modelo de licenciamento usando o portal do Azure ou usando uma das seguintes APIs:

- Para definir ou atualizar o tipo de licença usando o PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Para definir ou atualizar o tipo de licença usando o CLI do Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Para definir ou atualizar o tipo de licença usando a API REST:

  - [Bancos de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bancos de dados-atualização](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Instâncias gerenciadas – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Instâncias gerenciadas-atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="next-steps"></a>Passos seguintes

- Para escolher entre as opções de implantação do banco de dados SQL, consulte [escolher a opção de implantação correta no SQL do Azure](sql-database-paas-vs-sql-server-iaas.md).
- Para obter uma comparação dos recursos do banco de dados SQL, consulte [recursos do banco de dados SQL do Azure](sql-database-features.md).

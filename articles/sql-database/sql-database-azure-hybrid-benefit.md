---
title: Banco de dados SQL do Azure-Benefício Híbrido do Azure | Microsoft Docs
description: Use licenças de SQL Server existentes para descontos do banco de dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: cb5caf65a3083cfb5700432573cfddcfb7c6385f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515892"
---
# <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Na camada de computação provisionada do modelo de compra baseado em vCore, você pode trocar suas licenças existentes por tarifas com desconto no banco de dados SQL usando [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Esse benefício do Azure permite que você economize até 30% no banco de dados SQL do Azure usando suas licenças de SQL Server locais com o Software Assurance.

![Refere](./media/sql-database-service-tiers/pricing.png)


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
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [atualização do AZ SQL mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Para definir ou atualizar o tipo de licença usando a API REST:

  - [Bancos de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bancos de dados-atualização](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Instâncias gerenciadas – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Instâncias gerenciadas-atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>Passos seguintes

- Para escolher entre as opções de implantação do banco de dados SQL, consulte [escolher a opção de implantação correta no SQL do Azure](sql-database-paas-vs-sql-server-iaas.md).
- Para obter uma comparação dos recursos do banco de dados SQL, consulte [recursos do banco de dados SQL do Azure](sql-database-features.md).

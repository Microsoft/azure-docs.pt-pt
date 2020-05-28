---
title: Benefício Híbrido do Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Utilize as licenças de Servidor SQL existentes para a Base de Dados Azure SQL e descontos de Instância Gerida SQL.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=4
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d57eb2718f79e9243426a59245abc44c98dc6249
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118612"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Benefício Híbrido Azure - Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

No nível de cálculo provisionado do modelo de compra baseado em vCore, pode trocar as suas licenças existentes por tarifas descontadas na Base de Dados Azure SQL e na Instância Gerida Azure SQL utilizando o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício Azure permite-lhe economizar até 30% ou mesmo mais na Base de Dados SQL & SQL Managed Instance, utilizando as suas licenças SQL Server no local com Garantia de Software. A página [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) tem uma calculadora para ajudar a determinar a poupança.  Note que o Benefício Híbrido Azure não se aplica ao Servidor de Base de Dados Azure SQL.

> [!NOTE]
> A mudança para O Benefício Híbrido Azure não requer qualquer tempo de inatividade.

![preços](./media/azure-hybrid-benefit/pricing.png)

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


### <a name="azure-hybrid-benefit-questions"></a>Perguntas de Benefício Híbrido Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existem direitos de dupla utilização com o Azure Hybrid Benefit para o Servidor SQL?

Tem 180 dias de direitos de dupla utilização da licença para garantir que as migrações estão a funcionar sem problemas. Após esse período de 180 dias, só pode utilizar a licença SQL Server na nuvem na Base de Dados SQL. Já não tens direitos de uso duplo no local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como é que o Azure Hybrid Benefit para o SQL Server difere da mobilidade da licença?

Oferecemos benefícios de mobilidade de licença para clientes SQL Server com Garantia de Software. Isto permite a reatribuição das suas licenças para os servidores partilhados de um parceiro. Pode utilizar este benefício no Azure IaaS e no AWS EC2.

O Benefício Híbrido Azure para o Servidor SQL difere da mobilidade da licença em duas áreas-chave:

- Proporciona benefícios económicos para a deslocação de cargas de trabalho altamente virtualizadas para o Azure. Os clientes da SQL Server Enterprise Edition podem obter quatro núcleos em Azure no General Purpose SKU para cada núcleo que possuam no local para aplicações altamente virtualizadas. A mobilidade de licenças não permite quaisquer benefícios especiais para o deslocamento de cargas de trabalho virtualizadas para a nuvem.
- Fornece um destino PaaS no Azure (SQL Database managed instance) que é altamente compatível com o SQL Server no local.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido Azure para o Servidor SQL?

Os clientes da Base de Dados SQL têm os seguintes direitos associados ao Benefício Híbrido Azure para o Servidor SQL:

|Pegada de licença|O que é que o Azure Hybrid Benefit para o SQL Server te dá?|
|---|---|
|SQL Server Enterprise Edition clientes principais com SA|<li>Pode pagar a taxa base em propósito geral ou SKU crítico de negócios</li><br><li>1 núcleo no local = 4 núcleos em Fins Gerais SKU</li><br><li>1 núcleo no local = 1 núcleo em Business Critical SKU</li>|
|Clientes centrais da SQL Server Standard Edition com SA|<li>Pode pagar a taxa base apenas em Fins Gerais SKU</li><br><li>1 núcleo no local = 1 núcleo em Fins Gerais SKU</li>|
|||


## <a name="next-steps"></a>Próximos passos

- Para obter ajuda na escolha de uma opção de implementação Azure SQL, consulte [Escolha a opção de implementação certa no Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Para uma comparação das funcionalidades SQL Database e SQL Managed Instance, consulte a Base de [Dados SQL & funcionalidades de Instância Gerida SQL](database/features-comparison.md).

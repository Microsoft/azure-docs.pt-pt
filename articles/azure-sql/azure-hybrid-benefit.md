---
title: Benefício Híbrido do Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Utilize as licenças existentes do SQL Server para descontos Azure SQL Database e SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4, devx-track-azurecli
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 11/13/2019
ms.openlocfilehash: 872b36eae9142473885885d5bf5ad3fa10ae5ccb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841602"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit - Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

No nível de computação previsto do modelo de compra baseado em vCore, pode trocar as licenças existentes por tarifas com desconto na Base de Dados Azure SQL e na Azure SQL Managed Instance utilizando [o Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício Azure permite-lhe economizar até 30% ou até mais na Base de Dados SQL & SQL Managed Instance utilizando as suas licenças SQL Server com Software Assurance. A página [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) tem uma calculadora para ajudar a determinar a poupança.  Note que o Benefício Híbrido Azure não se aplica ao servidor Azure SQL Database sem servidor.

> [!NOTE]
> Mudar para Azure Hybrid Benefit não requer qualquer tempo de inatividade.

![estrutura de preços vcore](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Escolha um modelo de licença

Com o Azure Hybrid Benefit, pode optar por pagar apenas pela infraestrutura Azure subjacente utilizando a licença existente do SQL Server para o próprio motor de base de dados SQL Server (preço base compute), ou pode pagar tanto pela infraestrutura subjacente como pela licença SQL Server (preço incluído na licença).

Pode escolher ou alterar o seu modelo de licenciamento no portal Azure: 
- Para novas bases de dados, durante a criação, selecione a **base de dados Configure** no separador **Básicos** e selecione a opção de economizar dinheiro.
- Para as bases de dados existentes, selecione **Configurar** no menu **Definições** e selecione a opção de economizar dinheiro.

Também pode configurar uma base de dados nova ou existente utilizando uma das seguintes APIs:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para definir ou atualizar o tipo de licença utilizando o PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
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
- [Casos geridos - Criar ou Atualizar](/rest/api/sql/managedinstances/createorupdate)
- [Casos Geridos - Atualização](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Perguntas de Benefício Híbrido Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existem direitos de dupla utilização com benefícios híbridos Azure para o SQL Server?

Tem 180 dias de direitos de dupla utilização da licença para garantir que as migrações estão a funcionar sem problemas. Após esse período de 180 dias, só pode utilizar a licença SQL Server na nuvem na Base de Dados SQL. Já não tem direitos de dupla utilização no local e na nuvem.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como é que o Benefício Híbrido Azure para o SQL Server difere da mobilidade das licenças?

Oferecemos benefícios de mobilidade de licença a clientes do SQL Server com Software Assurance. Isto permite a reatribuição das suas licenças aos servidores partilhados de um parceiro. Você pode usar este benefício em Azure IaaS e AWS EC2.

O Benefício Híbrido Azure para o SQL Server difere da mobilidade de licenças em duas áreas-chave:

- Proporciona benefícios económicos para a deslocação de cargas de trabalho altamente virtualizadas para Azure. Os clientes da SQL Server Enterprise Edition podem obter quatro núcleos em Azure no SKU de Finalidade Geral para cada núcleo que possuam no local para aplicações altamente virtualizadas. A mobilidade de licenças não permite benefícios especiais de custos para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece um destino PaaS em Azure (SQL Managed Instance) que é altamente compatível com o SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quais são os direitos específicos do Benefício Híbrido Azure para o SQL Server?

Os clientes da SQL Database têm os seguintes direitos associados ao Azure Hybrid Benefit para o SQL Server:

|Pegada de licença|O que é que o Azure Hybrid Benefit for SQL Server te dá?|
|---|---|
|SQL Server Enterprise Edition clientes centrais com SA|<li>Pode pagar taxa base em Propósito Geral ou SKU crítico de negócios</li><br><li>1 núcleo no local = 4 núcleos no SKU de Finalidade Geral</li><br><li>1 núcleo nas instalações = 1 núcleo no Business Critical SKU</li>|
|Sql Server Standard Edition clientes centrais com SA|<li>Pode pagar taxa base apenas no SKU de Finalidade Geral</li><br><li>1 núcleo nas instalações = 1 núcleo no SKU de Finalidade Geral</li>|
|||


## <a name="next-steps"></a>Passos seguintes

- Para obter ajuda na escolha de uma opção de implementação Azure SQL, consulte [Escolha a opção de implementação certa em Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Para uma comparação das funcionalidades SQL Database e SQL Managed Instance, consulte a [Base de Dados SQL & as funcionalidades sql Managed Instance](database/features-comparison.md).

---
title: Configure O tempo de execução de integração Azure-SSIS para a falha da base de dados SQL
description: Este artigo descreve como configurar o Tempo de Integração Azure-SSIS com geo-replicação de base de dados Azure SQL e falha para a base de dados SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188727"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configure o tempo de execução de integração Azure-SSIS com geo-replicação e failover da Base de Dados Azure SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como configurar o Tempo de Integração Azure-SSIS com geo-replicação de base de dados Azure SQL para a base de dados SSISDB. Quando ocorre uma falha, pode garantir que o IR Azure-SSIS continue a trabalhar com a base de dados secundária.

Para obter mais informações sobre geo-replicação e failover para A Base de Dados SQL, consulte [Visão Geral: Geo-replicação ativa e grupos de auto-failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Falha no IR azure-SSIS com instância gerida pela Base de Dados Azure SQL

### <a name="prerequisites"></a>Pré-requisitos
1. Execute abaixo o comando no SSISDB em instância primária. Este passo é adicionar uma nova senha de encriptação.
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. Crie um grupo de failover na Instância Gerida pela Base de Dados Azure SQL.

3. Execute **sp_control_dbmasterkey_password** na instância secundária, usando a nova senha de encriptação.
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>Solução
Quando ocorre uma falha, se pretender utilizar o IR Azure-SSIS existente na região primária:
1. Stop Azure-SSIS IR na região primária.

2. Editar o IR Azure-SSIS com novas informações de região, ponto final e VNET de instância secundária.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Reiniciar o IR Azure-SSIS.

4. Altere o nome do servidor no **ConnectionManager** dos seus pacotes SSIS com o nome do servidor de instância secundária e, em seguida, recoloque estes pacotes e execute.

Se quiser fornecer um novo IR Azure-SSIS na região secundária:
> [!NOTE]
> O passo 4 (criação de IR) tem de ser feito através da PowerShell. O portal Azure reportará um erro afirmando que o SSISDB já existe.
1. Stop Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Criar uma nova ** \<fábrica\> ** de dados chamada new_data_factory_name na nova região. Para mais informações, consulte Criar uma fábrica de dados.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  Para mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure usando powerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo IR Azure-SSIS nomeado ** \<new_integration_runtime_name\> ** na nova região utilizando o Azure PowerShell.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md)

5. Altere o nome do servidor no **ConnectionManager** dos seus pacotes SSIS com o nome do servidor de instância secundária e, em seguida, recoloque estes pacotes e execute.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Falha no IR azure-SSIS com base de dados Azure SQL

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Cenário 1 - O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura

#### <a name="conditions"></a>Condições

Esta secção aplica-se quando as seguintes condições são verdadeiras:

- O IR Azure-SSIS aponta para o ponto final do ouvinte de leitura do grupo failover.

  AND

- O servidor De base de dados SQL *não* está configurado com a regra final do serviço de rede virtual.

#### <a name="solution"></a>Solução

Quando ocorre uma falha, é transparente para o IR Azure-SSIS. O IR Azure-SSIS liga-se automaticamente à nova primária do grupo failover.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Cenário 2 - O IR Azure-SSIS aponta para o ponto final do servidor primário

#### <a name="conditions"></a>Condições

Esta secção aplica-se quando uma das seguintes condições é verdadeira:

- O IR Azure-SSIS está a apontar para o ponto final do servidor primário do grupo failover. Este ponto final muda quando ocorre a falha.

  OU

- O servidor de base de dados Azure SQL está configurado com a regra final do serviço de rede virtual.


#### <a name="solution"></a>Solução

1. Stop Azure-SSIS IR na região primária.

2. Editar informações do Azure-SSIS IR com nova região, ponto final e informação VNET de instância secundária.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Reiniciar o IR Azure-SSIS.

4. Altere o nome do servidor no **ConnectionManager** dos seus pacotes SSIS com o nome do servidor de instância secundária e, em seguida, recoloque estes pacotes e execute.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Cenário 3 - Anexar um SSISDB existente (catálogo SSIS) a um novo IR Azure-SSIS

Quando ocorre um desastre de IR ADF ou Azure-SSIS na região atual, pode fazer com que o seu SSISDB continue a trabalhar com um novo IR Azure-SSIS numa nova região.

#### <a name="solution"></a>Solução

> [!NOTE]
> O passo 4 (criação de IR) tem de ser feito através da PowerShell. O portal Azure reportará um erro afirmando que o SSISDB já existe.

1. Stop Azure-SSIS IR na região primária.

2. Execute o procedimento armazenado para atualizar metadados no SSISDB para aceitar ligações de ** \<new_data_factory_name\> ** e ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Criar uma nova ** \<fábrica\> ** de dados chamada new_data_factory_name na nova região. Para mais informações, consulte Criar uma fábrica de dados.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  Para mais informações sobre este comando PowerShell, consulte Criar uma fábrica de [dados Azure usando powerShell](quickstart-create-data-factory-powershell.md)

4. Crie um novo IR Azure-SSIS nomeado ** \<new_integration_runtime_name\> ** na nova região utilizando o Azure PowerShell.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
```

  Para mais informações sobre este comando PowerShell, consulte [Create the Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md)

5. Altere o nome do servidor no **ConnectionManager** dos seus pacotes SSIS com o nome do servidor de instância secundária e, em seguida, recoloque estes pacotes e execute.


## <a name="next-steps"></a>Passos seguintes

Considere estas outras opções de configuração para o IR Azure-SSIS:

- [Configure o tempo de execução de integração Azure-SSIS para um alto desempenho](configure-azure-ssis-integration-runtime-performance.md)

- [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Personalizar a configuração do runtime de integração do Azure-SSIS)

- [Provision Enterprise Edition para o Tempo de Integração Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

---
title: Aprovisionar o Integration Runtime do Azure-SSIS com o PowerShell | Microsoft Docs
description: Saiba como aprovisionar o runtime de integração do Azure-SSIS no Azure Data Factory com o PowerShell, para que possa implementar e executar pacotes SSIS no Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484767"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Aprovisionar o Integration Runtime do Azure-SSIS no Azure Data Factory com o PowerShell

Este tutorial disponibiliza os passos para o aprovisionamento de um Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) no Azure Data Factory (ADF). Runtime de integração Azure-SSIS suporta a execução de pacotes implementados no catálogo de SSIS (SSISDB) hospedado por servidor/gerida de base de dados do Azure SQL instância (modelo de implementação do projeto) e os que são implementados em sistemas de ficheiros/ficheiro ficheiros de partilhas/do Azure (modelo de implementação do pacote). Depois do IR Azure-SSIS é aprovisionado, em seguida, pode utilizar ferramentas familiares, como o SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) e o comando utilitários da linha, como `dtinstall` / `dtutil` / `dtexec`ao Implemente e execute os seus pacotes no Azure. Neste tutorial, vai executar os seguintes passos:

> [!NOTE]
> Este artigo utiliza o Azure PowerShell para aprovisionar um ir Azure-SSIS. Para utilizar a aplicação de portal/ADF do Azure para aprovisionar um IR Azure-SSIS, veja [Tutorial: Aprovisionar o IR Azure-SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime do Azure-SSIS
> * Iniciar o integration runtime do Azure-SSIS
> * Rever o script completo
> * Implementar pacotes de SSIS

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subscrição do Azure**. Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar. Para obter informações concetuais sobre o IR Azure-SSIS, veja [Azure-SSIS integration runtime overview (Descrição geral do runtime de integração Azure-SSIS)](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Servidor de base de dados SQL do Azure (opcional)** . Se ainda não tiver um servidor de base de dados, crie um no portal do Azure antes de começar. ADF por sua vez criará SSISDB neste servidor de base de dados. Recomendamos que crie o servidor de base de dados na mesma região do Azure que o integration runtime. Esta configuração permite que o runtime de integração escreva registos de execução em SSISDB sem cruzamento entre regiões do Azure. 
    - Com base no servidor de bases de dados selecionado, a SSISDB pode ser criada em seu nome como uma base de dados individual, parte de um conjunto elástico, ou numa Instância Gerida e acessível na rede pública ou ao associar uma rede virtual. Para obter orientações sobre como escolher o tipo de servidor de base de dados para alojar o SSISDB, consulte [comparar o Azure SQL Database única base de dados/elastic pool/gerida instância](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Se utilizar a base de dados do Azure SQL server com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB ou exigir acesso a dados no local, tem de associar o runtime de integração Azure-SSIS a uma rede virtual, consulte [criar Runtime de integração Azure-SSIS numa rede virtual](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme que a definição **Permitir acesso aos serviços do Azure** está ativada para o servidor da base de dados. Não se aplica ao utilizar o servidor de base de dados do Azure SQL com a rede virtual serviço pontos finais/gerida instância numa rede virtual para alojar o SSISDB. Para obter mais informações, veja [Proteger a base de dados SQL do Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Para ativar esta definição com o PowerShell, consulte [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Adicione o endereço IP do computador cliente ou um intervalo de endereços IP que inclua o endereço IP do computador cliente à lista de endereços IP do cliente nas definições de firewall para o servidor de base de dados. Para obter mais informações, veja [Regras de firewall ao nível do servidor da Base de Dados SQL do Azure e ao nível da base de dados](../sql-database/sql-database-firewall-configure.md).
    - Pode ligar ao servidor da base de dados com autenticação de SQL com as suas credenciais de administrador do servidor ou autenticação do Azure Active Directory (AAD) com a identidade gerida para o ADF.  Para esta última opção, tem de adicionar a identidade gerida do ADF a um grupo do AAD com permissões de acesso ao servidor de base de dados, veja [Criar Azure-SSIS IR com a autenticação do AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Confirme que o servidor de base de dados não tem um SSISDB já. O aprovisionamento de um IR Azure-SSIS não suporta a utilização de um SSISDB existente.
- **Azure PowerShell**. Siga as instruções em [como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps), se quiser executar um script do PowerShell para aprovisionar o ir Azure-SSIS.

> [!NOTE]
> - Para obter uma lista de regiões do Azure em que o ADF e IR Azure-SSIS estão atualmente disponíveis, consulte [ADF + IR do SSIS disponibilidade por região](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Iniciar o ISE do Windows PowerShell

Inicie o **ISE do Windows PowerShell** com privilégios administrativos. 

## <a name="create-variables"></a>Criar variáveis

Copie e cole o seguinte script – especifique valores para as variáveis. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Iniciar sessão e selecione a subscrição

Adicione o seguinte código ao script para iniciar sessão e selecione a sua subscrição do Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Validar a ligação ao servidor de base de dados

Adicione o seguinte script para validar o seu servidor de base de dados do Azure SQL. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Para criar uma base de dados do SQL do Azure como parte do script, veja o exemplo seguinte: 

Defina valores para as variáveis que ainda não foram definidos. Por exemplo: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar uma [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizando o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

Se o grupo de recursos já existir, não copie este código ao seu script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados

Execute o comando seguinte para criar uma fábrica de dados:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Criar um integration runtime

Execute os seguintes comandos para criar um runtime de integração Azure-SSIS que execute pacotes de SSIS no Azure.

Se não utilizar SSISDB, pode omitir CatalogServerEndpoint CatalogPricingTier e CatalogAdminCredential parâmetros.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Iniciar o integration runtime

Execute os seguintes comandos para iniciar o runtime de integração Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Excluindo qualquer tempo de configuração personalizada, esse processo deve ser concluído dentro de 5 minutos.
>
> Se usar o SSISDB, o serviço do ADF irá ligar ao seu servidor de base de dados para preparar o SSISDB. 
> 
> Quando aprovisiona um IR Azure-SSIS, acesso redistribuível e pacote de funcionalidades do Azure para SSIS também são instalados. Estes componentes fornecem conectividade aos ficheiros de Excel/acesso e de várias origens de dados do Azure, além das origens de dados já suportadas pelos componentes incorporados. Também pode instalar componentes adicionais, consulte [configuração personalizada para IR Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Script completo

O script do PowerShell nesta secção configura uma instância do Runtime de integração Azure-SSIS que execute pacotes de SSIS. Depois de executar este script com êxito, pode implementar e executar pacotes do SSIS no Azure.

1. Inicie o Windows PowerShell Integrated Scripting Environment (ISE).
2. No ISE, execute o seguinte comando a partir da linha de comandos.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Copie o script do PowerShell nesta secção e cole-o no ISE.
4. Indique os valores adequados para todos os parâmetros no início do script.
5. Execute o script. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorizar e gerir o IR Azure-SSIS

Veja os artigos seguintes para obter detalhes sobre a monitorização e a gestão de IR Azure-SSIS. 

- [O runtime de integração do monitor do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Gerir o IR Azure-SSIS](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Implementar pacotes de SSIS

Se usar o SSISDB, pode implementar os pacotes para o mesmo e execute-os no runtime de integração Azure-SSIS com as ferramentas do SSDT/SSMS que se ligam ao seu servidor de base de dados por meio do seu ponto de extremidade do servidor. Para a base de dados do Azure SQL server/instância gerida com um ponto final público, o formato de ponto final de servidor é `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivamente. Se não utilizar SSISDB, pode implementar os pacotes em partilhas de sistemas/ficheiro de arquivo/Azure arquivos e execute-os sobre como utilizar o IR Azure-SSIS `dtinstall` / `dtutil` / `dtexec` utilitários de linha de comandos. Para obter mais informações, consulte [pacotes do SSIS implementar](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Em ambos os casos, também pode executar os pacotes implementados no runtime de integração Azure-SSIS com a atividade de executar o pacote do SSIS em pipelines ADF, consulte [execução como uma atividade ADF primeira classe de pacotes do SSIS invocar](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Consulte também os seguintes artigos da documentação do SSIS: 

- [Implementar, executar e monitorizar pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ligar ao SSISDB no Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Agendar execuções de pacote no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Criar um integration runtime do Azure-SSIS
> * Iniciar o integration runtime do Azure-SSIS
> * Rever o script completo
> * Implementar pacotes de SSIS

Para saber mais sobre como personalizar o runtime de integração do Azure-SSIS, avance para o seguinte artigo:

> [!div class="nextstepaction"]
>[Personalizar o Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
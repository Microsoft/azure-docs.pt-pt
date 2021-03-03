---
title: Classifique os seus dados Azure SQL usando etiquetas Azure Purview
description: Importe a sua classificação a partir de Azure Purview na sua Base de Dados Azure SQL e Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714906"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Classifique os seus dados Azure SQL usando etiquetas Azure Purview
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Este documento descreve como adicionar etiquetas Azure Purview na sua Base de Dados Azure SQL e Azure Synapse Analytics (anteriormente SQL DW).

## <a name="create-an-application"></a>Criar uma aplicação

1. A partir do portal Azure, abra o seu **Diretório Ativo Azure**.
2. Em **Gestão**, selecione **registo de Aplicação.**
3. Crie uma nova aplicação Azure Ative Directory selecionando **Nova Aplicação.**
4. Introduza um nome para a sua inscrição e **selecione Registar-se.**
5. Após a criação da sua candidatura, abra **certificados & segredos** sob **o Comando**.
6. Crie um novo segredo de cliente selecionando o **segredo do novo cliente** sob os **segredos do Cliente.**
7. Adicione uma descrição ao segredo do seu cliente, selecione um período de validade e selecione **Adicionar**.
8. Mantenha o **Valor** para utilização futura.

   > [!NOTE]
   > Assim que fechar a página, o valor será mascarado. Não conseguirá recuperar o valor do segredo do cliente se voltar à página. Terá que gerar um novo segredo de cliente.

9. Volte para a página geral da sua aplicação recém-criada e copie os seguintes valores para utilização futura:
    1. ID da Aplicação (cliente)
    1. ID do Diretório (inquilino)

## <a name="provide-permissions-to-the-application"></a>Fornecer permissões à aplicação

1. No seu portal Azure, procure **por contas de Purview.**
2. Selecione a conta ' ' onde as bases de dados SQL e sinapse são classificadas.
3. Controlo **de acesso aberto (IAM)**, selecione **Add**.

4. Selecione **Adicionar atribuição de função**.
5. Na secção **'Fun',** procure **o Leitor de Dados de Purga** e selecione-o.
6. Na secção **Seleção,** procure a aplicação que criou anteriormente, selecione-a e acerte **em Guardar**.

## <a name="extract-the-classification-from-azure-purview"></a>Extrair a classificação do Azure Purview

1. Abra a sua conta 'Barriga' e na página Inicial, procure a sua Base de Dados Azure SQL ou Azure Synapse Analytics onde pretende copiar as etiquetas.
2. Copie o nome qualificado em **Propriedades** e guarde-o para uso futuro.
3. Abra a sua concha PowerShell.

4. Copie um dos scripts abaixo de acordo com o seu tipo de ativo SQL (Base de Dados Azure SQL ou Azure Synapse).
5. Preencha os parâmetros com os valores que copiou acima:

   a. $TenantID: secção 1, passo 9
   
   b. $ClientID: secção 1, passo 9
   
   c. $SecretID: secção 1, passo 8
   
   d. $purviewAccountName: secção 2, passo 2
   
   e. $sqlDatabaseName: secção 3, passo 2

6. Copie a saída do script para utilização futura.

### <a name="for-azure-sql-database"></a>Para a base de dados Azure SQL

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Para Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Executar o comando T-SQL no seu ativo SQL

1. Ligue-se à sua Base de Dados Azure SQL ou à sua Sinapse Azure utilizando a sua ferramenta de eleição.
2. Executar o comando T-SQL que copiou da secção anterior.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/).

Para obter mais informações sobre o Azure Purview, consulte [a documentação do Azure Purview](../../purview/index.yml).
---
title: 'Tutorial: Começar com Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL'
description: Este tutorial ensina-lhe como criar um ambiente básico para sempre encriptado com enclaves seguros na Base de Dados Azure SQL e como encriptar dados no local, e emitir consultas confidenciais ricas contra colunas encriptadas usando SQL Server Management Studio (SSMS).
keywords: encriptar dados, encriptação sql, encriptação de base de dados, dados confidenciais, Sempre Criptografados, enclaves seguros, SGX, atestado
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: d9c2bec575f2c7a948f3eb6e65be6a735a3c03e8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733819"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Tutorial: Começar com Sempre Encriptado com enclaves seguros na Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Sempre encriptado com enclaves seguros para Azure SQL Database está atualmente em **pré-visualização pública**.

Este tutorial ensina-lhe como começar com [Always Encrypted com enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) na Base de Dados Azure SQL. Vai mostrar-lhe:

> [!div class="checklist"]
> - Como criar um ambiente para testar e avaliar Sempre Encriptado com enclaves seguros.
> - Como encriptar dados no local e emitir consultas confidenciais ricas contra colunas encriptadas usando o SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer Azure PowerShell e [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Requisitos PowerShell

Consulte [a visão geral do Azure PowerShell](/powershell/azure) para obter informações sobre como instalar e executar a Azure PowerShell. 

Versão mínima dos módulos Az necessários para suportar operações de atestado:

- Az 4.5.0
- Az.Contas 1.9.2
- Az.Attestation 0.1.8

Executar o comando abaixo para verificar a versão instalada de todos os módulos Az:

```powershell
Get-InstalledModule
```

Se as versões não corresponderem ao requisito mínimo, executar o `Update-Module` comando.

A PowerShell Gallery depretou as versões 1.0 e 1.1 da Transport Layer Security (TLS). Recomenda-se tLS 1.2 ou uma versão posterior. Pode receber os seguintes erros se estiver a utilizar uma versão TLS inferior a 1.2:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Para continuar a interagir com a PowerShell Gallery, executar o seguinte comando antes que o Install-Module comandos

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Requisitos SSMS

Consulte [o Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para obter informações sobre como descarregar SSMS.

A versão mínima necessária de SSMS é de 18.8.


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>Passo 1: Criar um servidor e uma base de dados da série DC

 Neste passo, irá criar um novo servidor lógico Azure SQL Database e uma nova base de dados utilizando a configuração de hardware da série DC. Sempre encriptado com enclaves seguros na Base de Dados Azure SQL utiliza enclaves Intel SGX, que são suportados na configuração de hardware da série DC. Para mais informações, consulte [a série DC.](service-tiers-vcore.md#dc-series)

1. Abra uma consola PowerShell e assine em Azure. Se necessário, [mude para a subscrição](/powershell/azure/manage-subscriptions-azureps) que está a usar para este tutorial.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. Crie um grupo de recursos para conter o seu servidor de base de dados. 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > Precisa de criar o seu grupo de recursos numa região que suporte a configuração de hardware da série DC. Para a lista das regiões atualmente apoiadas, consulte [a disponibilidade da série DC](service-tiers-vcore.md#dc-series-1).

3. Criar um servidor de base de dados. Quando solicitado, insira o nome do administrador do servidor e uma palavra-passe.

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. Crie uma regra de firewall do servidor que permite o acesso a partir da gama IP especificada
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. Atribua uma identidade gerida do sistema ao seu servidor. Mais tarde, vai precisar dele para conceder ao seu servidor acesso à Microsoft Azure Atesstation.

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. Recupere uma identificação de objeto da identidade atribuída ao seu servidor. Guarde a identificação do objeto resultante. Vai precisar da identificação numa secção posterior.

  > [!NOTE]
  > Pode levar alguns segundos para que a identidade do sistema gerido recentemente atribuído se propague no Diretório Ativo Azure. Se o guião abaixo devolver um resultado vazio, volte a tentar.

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. Crie uma base de dados da série DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>Passo 2: Configurar um fornecedor de atestado

Neste passo, irá criar e configurar um fornecedor de atestado na Microsoft Azure Attestation. Isto é necessário para atestar o enclave seguro no seu servidor de base de dados.

1. Copie a política de atestação abaixo e guarde a política num ficheiro de texto (txt). Para obter informações sobre a política abaixo, consulte [Criar e configurar um provedor de atestado](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importar as versões necessárias de `Az.Accounts` e `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. Criar um grupo de recursos para o fornecedor de atestado.

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. Criar um fornecedor de atestado. 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. Configure a sua política de atestado.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. Conceda ao seu servidor lógico Azure SQL acesso ao seu fornecedor de atestado. Neste passo, estamos a usar o ID do objeto da identidade de serviço gerida que atribuiu ao seu servidor anteriormente.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. Recupere a URL de atestado.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  Guarde o URL de atestação resultante que aponta para uma política de atestação que configuraste para o enclave SGX. Precisará dela mais tarde. O URL da estação de atestado deve ser assim: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Passo 3: Povoar a sua base de dados

Neste passo, irá criar uma tabela e povoá-la com alguns dados que irá encriptar e consultar mais tarde.

1. Abra o SSMS e ligue-se à base de **dados ContosoHR** no servidor lógico Azure SQL que criou **sem** sempre encriptado ativado na ligação da base de dados.
    1. No diálogo **'Ligar ao Servidor',** especificar o nome do servidor (por exemplo, *myserver123.database.windows.net),* e introduzir o nome de utilizador e a palavra-passe que configuraste anteriormente.
    2. Clique **em Opções >>** e selecione o **separador Propriedades de Ligação.** Certifique-se de que seleciona a base **de dados ContosoHR** (não a base de dados principal padrão). 
    3. Selecione o separador **Always Encrypted.**
    4. Certifique-se de que a caixa de verificação **Enable Always Encrypted (encriptação de colunas)** **não** está selecionada.

        ![Conecte-se sem sempre encriptado](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Clique em **Ligar**.

2. Criar uma nova tabela, chamada **Empregados.**

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Adicione alguns registos de empregados à mesa **dos empregados.**

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Passo 4: Provisão de chaves ativadas pelo enclave

Neste passo, você vai criar uma chave mestre de colunas e uma chave de encriptação de coluna que permitem computaçãos enclave.

1. Utilizando a instância SSMS do passo anterior, no **Object Explorer,** expanda a sua base de dados e navegue para **As**  >  **Teclas Sempre Encriptadas de** Segurança .
1. Disposição de uma nova chave-mestre da coluna ativada pelo enclave:
    1. Clique com o botão direito **Sempre As Teclas encriptadas** e selecione **a Nova Chave Master da Coluna...**.
    2. Selecione o nome principal da coluna: **CMK1**.
    3. Certifique-se de que seleciona a **Loja de Certificados do Windows (Utilizador Atual ou Máquina Local)** ou **o Cofre de Chaves Azure**.
    4. Selecione **Permitir computações de enclave .**
    5. Se selecionou o Azure Key Vault, inscreva-se no Azure e selecione o cofre da chave. Para obter mais informações sobre como criar um cofre chave para Sempre Encriptado, consulte [Gerir os seus cofres chave a partir do portal Azure](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Selecione o seu certificado ou tecla Azure Key Value se já existir, ou clique no botão **'Gerar Certificado'** para criar um novo.
    7. Selecione **OK**.

        ![Permitir cálculos de enclave](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Criar uma nova chave de encriptação de colunas ativada pelo enclave:

    1. Clique em **'Direita' Sempre As Teclas encriptadas** e selecione a nova chave de **encriptação da coluna**.
    2. Introduza um nome para a nova chave de encriptação da coluna: **CEK1**.
    3. No dropdown **da chave master da Coluna,** selecione a chave master da coluna que criou nos passos anteriores.
    4. Selecione **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Passo 5: Criptografe algumas colunas no lugar

Neste passo, irá encriptar os dados armazenados nas colunas **SSN** e **Salary** dentro do enclave do lado do servidor e, em seguida, testar uma consulta SELECT nos dados.

1. Abra uma nova instância SSMS e conecte-se à sua base de dados **com** sempre encriptado ativado para a ligação de base de dados.
    1. Inicie uma nova instância de SSMS.
    2. No diálogo **'Ligar ao Servidor',** especificar o nome do servidor, selecionar um método de autenticação e especificar as suas credenciais.
    3. Clique **em Opções >>** e selecione o **separador Propriedades de Ligação.** Certifique-se de que seleciona a base **de dados ContosoHR** (não a base de dados principal padrão). 
    4. Selecione o separador **Always Encrypted.**
    5. Certifique-se de que a caixa de verificação **Enable Always Encrypted (encriptação de colunas)** está selecionada.
    6. Especifique o url de atestado do enclave que obteve seguindo os passos no [Passo 2: Configurar um provedor de atestado](#step-2-configure-an-attestation-provider). Veja a imagem abaixo.

        ![Conecte-se com atestado](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Selecione **Ligar**.
    8. Se for solicitado para ativar a paraquedização para consultas sempre encriptadas, selecione **Enable**.



1. Utilizando a mesma instância SSMS (com sempre ativado), abra uma nova janela de consulta e criptografe as colunas **SSN** e **Salary** executando as declarações abaixo.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Note a configuração de ALCANCE ALTER DATABASE CLEAR PROCEDURE_CACHE declaração para limpar a cache do plano de consulta para a base de dados no script acima. Depois de ter alterado a tabela, precisa de limpar os planos para todos os lotes e procedimentos armazenados que acedam à tabela para atualizar informações de encriptação de parâmetros. 

1. Para verificar se as colunas **SSN** e **Salary** estão agora encriptadas, abra uma nova janela de consulta na instância SSMS **sem** sempre encriptada para a ligação da base de dados e execute a declaração abaixo. A janela de consulta deve devolver valores encriptados nas colunas **SSN** e **Salary.** Se executar a mesma consulta utilizando a instância SSMS com sempre encriptado ativado, deverá ver os dados desencriptados.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Passo 6: Executar consultas ricas contra colunas encriptadas

Pode fazer consultas ricas contra as colunas encriptadas. Algum processamento de consulta será realizado dentro do enclave do lado do servidor. 

1. No caso SSMS **com** sempre encriptado ativado, certifique-se de que a parametrização para Sempre Encriptada também está ativada.
    1. Selecione **Ferramentas** do menu principal de SSMS.
    2. Selecione **Opções...**.
    3. Navegue para **consulta execução**  >  **SQL Servidor**  >  **Avançado**.
    4. Certifique-se de que **ativar a parametrização para sempre encriptada** é verificada.
    5. Selecione **OK**.
2. Abra uma nova janela de consulta, cole na consulta abaixo e execute. A consulta deve devolver valores e linhas de texto simples que satisfazem os critérios de pesquisa especificados.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Tente novamente a mesma consulta no caso SSMS que não tenha sempre encriptado ativado. Deve ocorrer uma falha.
 
## <a name="next-steps"></a>Próximos passos

Depois de completar este tutorial, pode ir a um dos seguintes tutoriais:
- [Tutorial: Desenvolver uma aplicação .NET usando Sempre Encriptado com enclaves seguros](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Tutorial: Desenvolver uma aplicação quadro .NET usando sempre encriptado com enclaves seguros](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Tutorial: Criar e utilizar índices em colunas ativadas pelo enclave usando encriptação aleatória](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Consulte também

- [Configure e use Sempre Encriptado com enclaves seguros](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
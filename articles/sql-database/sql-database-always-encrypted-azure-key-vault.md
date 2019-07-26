---
title: 'Always Encrypted: Banco de dados SQL-Azure Key Vault | Microsoft Docs'
description: Este artigo mostra como proteger os dados confidenciais em um banco de dados SQL com a criptografia de dado usando o assistente de Always Encrypted no SQL Server Management Studio.
keywords: criptografia de dados, chave de criptografia, criptografia de nuvem
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a833c255e7a4e83c003fbef72367c5e271f380c5
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347223"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Always Encrypted: Proteger dados confidenciais e armazenar chaves de criptografia no Azure Key Vault

Este artigo mostra como proteger os dados confidenciais em um banco de dados SQL com a criptografia de dado usando o [Assistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também inclui instruções que lhe mostrarão como armazenar cada chave de criptografia em Azure Key Vault.

Always Encrypted é uma nova tecnologia de criptografia de dados no banco de dados SQL do Azure e SQL Server que ajuda a proteger dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, e enquanto os dados estão em uso. Always Encrypted garante que os dados confidenciais nunca apareçam como texto não criptografado no sistema de banco de dados. Depois de configurar a criptografia de dados, somente aplicativos cliente ou servidores de aplicativos que têm acesso às chaves podem acessar dados de texto não criptografado. Para obter informações detalhadas, consulte [Always Encrypted (mecanismo de banco de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar o banco de dados para usar Always Encrypted, você criará um aplicativo C# cliente no com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo e saiba como configurar Always Encrypted para um banco de dados SQL do Azure. Neste artigo, você aprenderá a executar as seguintes tarefas:

* Use o assistente de Always Encrypted no SSMS para criar [chaves de Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Crie uma [chave mestra de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Crie uma [CEK (chave de criptografia de coluna)](https://msdn.microsoft.com/library/mt146372.aspx).
* Criar uma tabela de banco de dados e criptografar colunas.
* Crie um aplicativo que insere, seleciona e exibe dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para este tutorial, você precisará de:

* Uma conta e subscrição do Azure. Se você não tiver uma, Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET Framework 4,6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview).

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Habilitar o aplicativo cliente para acessar o serviço do banco de dados SQL
Você deve habilitar seu aplicativo cliente para acessar o serviço do banco de dados SQL Configurando um aplicativo Azure Active Directory (AAD) e copiando a ID e a *chave* do *aplicativo* que você precisará para autenticar seu aplicativo.

Para obter a *ID* e a *chave*do aplicativo, siga as etapas em [criar um aplicativo Azure Active Directory e uma entidade de serviço que possa acessar os recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Criar um cofre de chaves para armazenar suas chaves
Agora que seu aplicativo cliente está configurado e você tem a ID do aplicativo, é hora de criar um cofre de chaves e configurar sua política de acesso para que você e seu aplicativo possam acessar os segredos do cofre (as chaves de Always Encrypted). As *permissões criar*, *obter*, *listar*, *assinar*, *verificar*, *wrapKey*e *unwrapKey* são necessárias para criar uma nova chave mestra de coluna e configurar a criptografia com SQL Server Management Studio.

Você pode criar rapidamente um cofre de chaves executando o script a seguir. Para obter uma explicação detalhada desses cmdlets e mais informações sobre como criar e configurar um cofre de chaves, consulte [o que é Azure Key Vault?](../key-vault/key-vault-overview.md).

```powershell
    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    # Use the same resource group name when creating your SQL Database below
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzAccount
    $subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
    Set-AzContext -SubscriptionId $subscriptionId

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```



## <a name="create-a-blank-sql-database"></a>Criar uma base de dados SQL vazia
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Vá para **criar um recurso** > **bancos** > de**dados SQL Database**.
3. Crie um banco de dados **em branco** chamado **clínica** em um servidor novo ou existente. Para obter instruções detalhadas sobre como criar um banco de dados no portal do Azure, consulte [seu primeiro banco de dados SQL do Azure](sql-database-single-database-get-started.md).
   
    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Você precisará da cadeia de conexão posteriormente no tutorial, portanto, depois de criar o banco de dados, navegue até o novo banco de dados da clínica e copie a cadeia de conexão. Você pode obter a cadeia de conexão a qualquer momento, mas é fácil copiá-la no portal do Azure.

1. Vá para **bancos** > de dados SQL**clínica** > **Mostrar cadeias de conexão de banco de dados**.
2. Copie a cadeia de conexão para **ADO.net**.
   
    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS
Abra o SSMS e conecte-se ao servidor com o banco de dados da clínica.

1. Abra o SQL Server Management Studio. (Vá para **conectar** > **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** se ela não estiver aberta.)
2. Insira o nome do servidor e as credenciais. O nome do servidor pode ser encontrado na folha do banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome completo do servidor, incluindo *Database.Windows.net*.
   
    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Se a janela **nova regra de firewall** abrir, entre no Azure e deixe que o SSMS crie uma nova regra de firewall para você.

## <a name="create-a-table"></a>Criar uma tabela
Nesta seção, você criará uma tabela para manter os dados do paciente. Ele não está inicialmente criptografado--você configurará a criptografia na próxima seção.

1. Expanda **bancos de dados**.
2. Clique com o botão direito do mouse no banco de dados da **clínica** e clique em **nova consulta**.
3. Cole o Transact-SQL (T-SQL) a seguir na nova janela de consulta e **Execute** -o.

```sql
        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografar colunas (configurar Always Encrypted)
O SSMS fornece um assistente que ajuda você a configurar facilmente Always Encrypted Configurando a chave mestra de coluna, a chave de criptografia de coluna e as colunas criptografadas para você.

1. Expandir  > **tabelas**clínicas > de bancos de dados.
2. Clique com o botão direito do mouse na tabela **pacientes** e selecione **criptografar colunas** para abrir o assistente de Always Encrypted:
   
    ![Criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

O assistente de Always Encrypted inclui as seguintes seções: **Seleção de coluna**, **configuração de chave mestra**, **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna
Clique em **Avançar** na página **introdução** para abrir a página **seleção de coluna** . Nessa página, você selecionará quais colunas deseja criptografar, [o tipo de criptografia e qual chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

Criptografar informações de **SSN** e **DataDeNascimento** para cada paciente. A coluna SSN usará a criptografia determinística, que oferece suporte a pesquisas de igualdade, junções e agrupar por. A coluna DataDeNascimento usará a criptografia aleatória, que não oferece suporte a operações.

Defina o **tipo de criptografia** para a coluna SSN como **determinístico** e a coluna DataDeNascimento como **aleatório**. Clique em **Seguinte**.

![Criptografar colunas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave mestra
A página de **configuração de chave mestra** é onde você configura seu CMK e seleciona o provedor de repositório de chaves em que o CMK será armazenado. No momento, você pode armazenar um CMK no repositório de certificados do Windows, Azure Key Vault ou um módulo de segurança de hardware (HSM).

Este tutorial mostra como armazenar suas chaves no Azure Key Vault.

1. Selecione **Azure Key Vault**.
2. Selecione o cofre de chaves desejado na lista suspensa.
3. Clique em **Seguinte**.

![Configuração da chave mestra](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validação
Você pode criptografar as colunas agora ou salvar um script do PowerShell para ser executado mais tarde. Para este tutorial, selecione **continuar para concluir agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo
Verifique se as configurações estão todas corretas e clique em **concluir** para concluir a instalação para Always Encrypted.

![Resumo](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Verificar as ações do assistente
Depois que o assistente for concluído, seu banco de dados será configurado para Always Encrypted. O assistente executou as seguintes ações:

* Criou uma chave mestra de coluna e a armazenou em Azure Key Vault.
* Criou uma chave de criptografia de coluna e a armazenou em Azure Key Vault.
* Configuradas as colunas selecionadas para criptografia. A tabela pacientes atualmente não tem dados, mas todos os dados existentes nas colunas selecionadas agora estão criptografados.

Você pode verificar a criação das chaves no SSMS expandindo a **clínica** > **Security** > **Always Encrypted Keys**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funciona com os dados criptografados
Agora que Always Encrypted está configurado, você pode criar um aplicativo que executa *inserções* e *seleciona* nas colunas criptografadas.  

> [!IMPORTANT]
> Seu aplicativo deve usar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto sem formatação para o servidor com colunas Always Encrypted. Passar valores literais sem usar objetos SqlParameter resultará em uma exceção.
> 
> 

1. Abra o Visual Studio e crie um C# novo **aplicativo de console** (Visual Studio 2015 e anterior) ou **aplicativo de console (.NET Framework)** (Visual Studio 2017 e posterior). Verifique se seu projeto está definido para **.NET Framework 4,6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.
3. Instale os seguintes pacotes NuGet acessando **ferramentas** > **Gerenciador** > de pacotes NuGet**console do Gerenciador de pacotes**.

Execute estas duas linhas de código no console do Gerenciador de pacotes.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifique a cadeia de conexão para habilitar Always Encrypted
Esta seção explica como habilitar Always Encrypted em sua cadeia de conexão de banco de dados.

Para habilitar Always Encrypted, você precisa adicionar a palavra-chave de **configuração de criptografia de coluna** à cadeia de conexão e defini-la como **habilitada**.

Você pode definir isso diretamente na cadeia de conexão ou pode defini-la usando [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar Always Encrypted na cadeia de conexão
Adicione a palavra-chave a seguir à sua cadeia de conexão.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Habilitar Always Encrypted com SqlConnectionStringBuilder
O código a seguir mostra como habilitar Always Encrypted definindo [SqlConnectionStringBuilder. ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [habilitado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registrar o provedor de Azure Key Vault
O código a seguir mostra como registrar o provedor de Azure Key Vault com o driver ADO.NET.

```csharp
    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }
```

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted aplicativo de console de exemplo
Este exemplo demonstra como:

* Modifique a cadeia de conexão para habilitar Always Encrypted.
* Registre Azure Key Vault como o provedor de repositório de chaves do aplicativo.  
* Inserir dados nas colunas criptografadas.
* Selecione um registro filtrando um valor específico em uma coluna criptografada.

Substitua o conteúdo de **Program.cs** pelo código a seguir. Substitua a cadeia de conexão para a variável connectionString global na linha que precede diretamente o método Main com sua cadeia de conexão válida do portal do Azure. Essa é a única alteração que você precisa fazer nesse código.

Execute o aplicativo para ver Always Encrypted em ação.
```CS
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }
```


## <a name="verify-that-the-data-is-encrypted"></a>Verificar se os dados estão criptografados
Você pode verificar rapidamente se os dados reais no servidor são criptografados consultando os dados de pacientes com o SSMS (usando sua conexão atual em que a **configuração de criptografia de coluna** ainda não está habilitada).

Execute a consulta a seguir no banco de dados da clínica.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Você pode ver que as colunas criptografadas não contêm nenhum dado de texto sem formatação.

   ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Para usar o SSMS para acessar os dados de texto sem formatação, primeiro você precisa garantir que o usuário tenha as permissões adequadas para o Azure Key Vault: *Get*, *unwrapKey*e *Verify*. Para obter informações detalhadas, consulte [criar e armazenar chaves mestras de coluna (Always Encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Em seguida, adicione o parâmetro *configuração de criptografia de coluna = habilitado* durante a conexão.

1. No SSMS, clique com o botão direito do mouse no servidor no Pesquisador de **objetos** e escolha Desconectar.
2. Clique em **conectar** > **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e clique em **Opções**.
3. Clique em **parâmetros de conexão adicionais** e digite **configuração de criptografia de coluna = habilitado**.
   
    ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Execute a consulta a seguir no banco de dados da clínica.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Agora você pode ver os dados de texto sem formatação nas colunas criptografadas.
     ![Novo aplicativo de console](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Passos Seguintes
Depois de criar um banco de dados que usa Always Encrypted, talvez você queira fazer o seguinte:

* [Gire e limpe suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migre dados que já estão criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informações relacionadas
* [Always Encrypted (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server criptografia](https://msdn.microsoft.com/library/bb510663.aspx)
* [Assistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog de Always Encrypted](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)


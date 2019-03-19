---
title: 'Sempre encriptados: Base de dados do SQL - Cofre de chaves do Azure | Documentos da Microsoft'
description: Este artigo mostra-lhe como proteger dados confidenciais numa base de dados SQL com a encriptação de dados utilizando o assistente encriptado sempre no SQL Server Management Studio.
keywords: encriptação de dados, a chave de encriptação, a encriptação de cloud
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
ms.openlocfilehash: bcda6ac723101d6a907a10c5163ae1baf0ad2214
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884176"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Sempre encriptados: Proteger dados confidenciais e armazenar chaves de encriptação no Azure Key Vault

Este artigo mostra-lhe como proteger dados confidenciais numa base de dados SQL com a encriptação de dados com o [sempre o Assistente de encriptados](https://msdn.microsoft.com/library/mt459280.aspx) na [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também inclui instruções que mostram como armazenar cada chave de encriptação no Azure Key Vault.

Always Encrypted é uma nova tecnologia de encriptação de dados na base de dados do Azure SQL e o SQL Server, que ajuda a proteger dados confidenciais Inativos no servidor, durante a movimentação entre cliente e servidor, e enquanto os dados estão em utilização. Sempre encriptado garante que os dados confidenciais nunca é apresentada como texto não criptografado dentro do sistema de base de dados. Depois de configurar a encriptação de dados, apenas as aplicações de cliente ou servidores de aplicações que têm acesso às chaves podem aceder a dados de texto sem formatação. Para obter informações detalhadas, consulte [Always Encrypted (motor de base de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar o Always Encrypted, irá criar uma aplicação cliente no c# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos neste artigo e saiba como configurar o Always Encrypted para uma base de dados SQL do Azure. Neste artigo, aprenderá como realizar as seguintes tarefas:

* Utilize o assistente sempre encriptado no SSMS para criar [chaves Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Criar uma [chave mestra de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Criar uma [chave de encriptação de coluna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Criar uma tabela de base de dados e encriptar colunas.
* Crie uma aplicação que insere, seleciona e apresenta os dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para este tutorial, terá de:

* Uma conta e subscrição do Azure. Se não tiver uma, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview).

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Ativar a aplicação de cliente aceder ao serviço de base de dados SQL
Tem de ativar a aplicação de cliente aceder ao serviço de base de dados SQL ao configurar uma aplicação do Azure Active Directory (AAD) e ao copiar o *ID da aplicação* e *chave* que será necessário autenticar a sua aplicação.

Para obter o *ID da aplicação* e *chave*, siga os passos [criar um Azure Active Directory principal de aplicações e serviço que pode aceder a recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Criar um cofre de chaves para armazenar as suas chaves
Agora que a aplicação de cliente está configurada e se tiver o ID da sua aplicação, é hora de criar um cofre de chaves e configurar a sua política de acesso para e a sua aplicação podem aceder aos segredos do cofre (as chaves Always Encrypted). O *crie*, *obter*, *lista*, *início de sessão*, *verificar*, *wrapKey*, e *unwrapKey* permissões são necessárias para criar uma nova chave mestra de coluna e para configurar a encriptação com o SQL Server Management Studio.

Pode criar rapidamente um cofre de chaves executando o seguinte script. Para obter uma explicação detalhada destes cmdlets e obter mais informações sobre como criar e configurar um cofre de chaves, consulte [o que é o Azure Key Vault?](../key-vault/key-vault-overview.md).

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
2. Aceda a **criar um recurso** > **bases de dados** > **base de dados SQL**.
3. Criar uma **em branco** com o nome da base de dados **clínica** num servidor novo ou existente. Para obter instruções detalhadas sobre como criar uma base de dados no portal do Azure, consulte [sua primeira base de dados SQL do Azure](sql-database-single-database-get-started.md).
   
    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Terá da ligação mais tarde no tutorial de cadeias de caracteres, então, depois de criar a base de dados, navegue para a nova base de dados de clínica e copie a cadeia de ligação. Pode obter a cadeia de ligação em qualquer altura, mas é fácil para copiá-la no portal do Azure.

1. Aceda a **bases de dados SQL** > **clínica** > **Mostrar cadeias de ligação de base de dados**.
2. Copie a cadeia de ligação para **ADO.NET**.
   
    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS
Abra o SSMS e ligue ao servidor com a base de dados clínica.

1. Abra o SQL Server Management Studio. (Aceda a **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela se não estiver aberta.)
2. Introduza o nome do servidor e as credenciais. O nome do servidor pode ser encontrado no painel da base de dados do SQL e na cadeia de ligação que copiou anteriormente. Escreva o nome de servidor completo, incluindo *database.windows.net*.
   
    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Se o **nova regra de Firewall** é apresentada a janela, inicie sessão no Azure e permita que o SSMS crie uma nova regra de firewall para.

## <a name="create-a-table"></a>Criar uma tabela
Nesta secção, irá criar uma tabela para conter dados de pacientes. Não é inicialmente encriptados - irá configurar encriptação na próxima seção.

1. Expanda **bases de dados**.
2. Com o botão direito a **clínica** da base de dados e clique em **nova consulta**.
3. Cole o Transact-SQL seguinte (T-SQL) na nova janela de consulta e **Execute** -lo.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Encriptar colunas (configurar Always Encrypted)
SSMS fornece um assistente que o ajuda a configurar facilmente Always Encrypted ao definir a chave mestra de coluna chave de encriptação de coluna e colunas encriptadas para.

1. Expanda **bases de dados** > **clínica** > **tabelas**.
2. Com o botão direito a **pacientes** da tabela e selecione **encriptar colunas** para abrir o assistente sempre encriptado:
   
    ![Encriptar colunas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

O assistente sempre encriptado inclui as secções seguintes: **Seleção da coluna**, **configuração da chave mestra**, **validação**, e **resumo**.

### <a name="column-selection"></a>Seleção de coluna
Clique em **próxima** no **introdução** página para abrir o **seleção de coluna** página. Nesta página, que irá selecionar as colunas que pretende encriptar, [o tipo de encriptação e que chave de encriptação de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) a utilizar.

Encriptar **SSN** e **data de nascimento** informações para cada doente. A coluna SSN irá utilizar encriptação determinista, que oferece suporte a pesquisas de igualdade, associações e agrupar por. A coluna de data de nascimento irá utilizar encriptação aleatória, que não suporta operações.

Definir o **tipo de encriptação** para a coluna SSN **Deterministic** e a coluna de data de nascimento para **Randomized**. Clique em **Seguinte**.

![Encriptar colunas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave mestra
O **configuração da chave mestra** página é onde configurar seu CMK e selecionar o fornecedor de armazenamento de chaves onde será armazenado o CMK. Atualmente, pode armazenar um CMK no arquivo de certificados do Windows, no Azure Key Vault ou um módulo de segurança de hardware (HSM).

Este tutorial mostra como armazenar as suas chaves no Azure Key Vault.

1. Selecione **do Azure Key Vault**.
2. Selecione o Cofre de chaves pretendido na lista pendente.
3. Clique em **Seguinte**.

![Configuração da chave mestra](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validação
Pode encriptar as colunas agora ou guardar um script do PowerShell para executar mais tarde. Neste tutorial, selecione **continuar para terminar agora** e clique em **próxima**.

### <a name="summary"></a>Resumo
Certifique-se de que as definições estão corretas e clique em **concluir** para concluir a configuração para sempre encriptados.

![Resumo](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Certifique-se de ações do Assistente
Depois de concluído o assistente, a base de dados é configurada para sempre encriptados. O assistente efetuar as seguintes ações:

* Criar uma chave mestra de coluna e guardá-la no Azure Key Vault.
* Criar uma chave de encriptação de coluna e guardá-la no Azure Key Vault.
* Configurar as colunas selecionadas para a encriptação. A tabela de pacientes atualmente não tem dados, mas qualquer dado existente nas colunas selecionadas já está encriptado.

Pode verificar a criação de chaves no SSMS, expandindo **clínica** > **segurança** > **sempre as chaves encriptada**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente que funciona com os dados encriptados
Agora que Always Encrypted é configurada, pode criar um aplicativo que realiza *insere* e *seleciona* nas colunas encriptadas.  

> [!IMPORTANT]
> Tem de utilizar o seu aplicativo [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos quando passar dados de texto sem formatação para o servidor com as colunas são sempre encriptados. Transmitir valores literais sem utilizar objetos SqlParameter resultará numa exceção.
> 
> 

1. Abra o Visual Studio e crie uma nova linguagem c# **aplicação de consola** (Visual Studio 2015 e anterior) ou **aplicação de consola (.NET Framework)** (Visual Studio 2017 e posterior). Certifique-se de que o seu projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.
3. Instale os seguintes pacotes de NuGet acedendo a **ferramentas** > **Gestor de pacotes NuGet** > **Package Manager Console**.

Execute estas duas linhas de código na consola do Gestor de pacotes.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de ligação para ativar Always Encrypted
Esta secção explica como ativar o Always Encrypted na sua cadeia de ligação de base de dados.

Para ativar Always Encrypted, tem de adicionar o **definição de encriptação de coluna** palavra-chave para a ligação de cadeias de caracteres e defini-lo como **ativado**.

Pode definir isso diretamente na cadeia de ligação ou pode configurá-lo usando [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na secção seguinte mostra como usar **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar Always Encrypted na cadeia de ligação
Adicione a seguinte palavra-chave para a cadeia de ligação.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Ativar Always encriptada com SqlConnectionStringBuilder
O código seguinte mostra como ativar o Always Encrypted através da definição [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) ao [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registar o fornecedor do Azure Key Vault
O código seguinte mostra como registar o fornecedor do Azure Key Vault com o controlador ADO.NET.

```C#
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

## <a name="always-encrypted-sample-console-application"></a>Sempre encriptado aplicativo de console de exemplo
Este exemplo demonstra como:

* Modificar a cadeia de ligação para ativar Always Encrypted.
* Registe o Azure Key Vault como fornecedor de armazenamento de chaves da aplicação.  
* Inserir dados em colunas criptografadas.
* Selecione um registo ao filtrar para um valor específico numa coluna criptografada.

Substitua o conteúdo do **Program.cs** com o código a seguir. Substitua a cadeia de ligação para a variável global connectionString na linha que precede diretamente o método Main, com sua cadeia de ligação válida a partir do portal do Azure. Esta é a única alteração, que terá de fazer no código.

Execute a aplicação para ver o Always Encrypted em ação.
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


## <a name="verify-that-the-data-is-encrypted"></a>Certifique-se de que os dados são encriptados
Pode verificar rapidamente o que os dados reais no servidor são encriptados ao consultar os dados de doentes com o SSMS (usando a ligação atual onde **definição de encriptação de coluna** ainda não está ativada).

Execute a seguinte consulta na base de dados clínica.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Pode ver que colunas criptografadas não contêm quaisquer dados de texto sem formatação.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Para utilizar o SSMS para acessar os dados de texto sem formatação, tem primeiro de se certificar de que o utilizador tem permissões adequadas para o Azure Key Vault: *Obtenha*, *unwrapKey*, e *verificar*. Para obter informações detalhadas, consulte [criar e Store coluna mestre de chaves (Always Encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Em seguida, adicione a *definição de encriptação de coluna = ativada* parâmetro durante a ligação.

1. No SSMS, clique no seu servidor no **Object Explorer** e escolha **desligar**.
2. Clique em **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela e clique em **opções**.
3. Clique em **parâmetros de ligação adicionais** e escreva **definição de encriptação de coluna = ativada**.
   
    ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Execute a seguinte consulta na base de dados clínica.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Agora, pode ver os dados de texto sem formatação em colunas criptografadas.
     ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Passos Seguintes
Depois de criar uma base de dados que utiliza o Always Encrypted, pode querer fazer o seguinte:

* [Rodar e limpar as suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrar os dados que já são criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informações relacionadas
* [Sempre encriptado (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Sempre encriptado Assistente](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog sempre encriptado](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)


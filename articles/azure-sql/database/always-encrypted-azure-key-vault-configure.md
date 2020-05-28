---
title: Configure sempre encriptado usando cofre de chave Azure
description: Este tutorial mostra-lhe como proteger dados sensíveis numa Base de Dados SQL Azure com encriptação de dados utilizando o Assistente Sempre Encriptado no Estúdio de Gestão de Servidores SQL.
keywords: encriptação de dados, chave de encriptação, encriptação em nuvem
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 8f828d11d5351565c112b7e4b9dccaaef4607056
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047700"
---
# <a name="configure-always-encrypted-using-azure-key-vault"></a>Configure sempre encriptado usando cofre de chave Azure 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo mostra-lhe como proteger dados sensíveis para a sua base de dados em Azure SQL Database ou Azure SQL Managed Instance com encriptação de dados utilizando o [Assistente Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-wizard) no Estúdio de [Gestão de Servidores SQL (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Também inclui instruções que lhe mostrarão como armazenar cada chave de encriptação no Cofre de Chaves Azure.

Sempre Encriptado é uma tecnologia de encriptação de dados que ajuda a proteger dados sensíveis em repouso no servidor, durante o movimento entre o cliente e o servidor, e enquanto os dados estão em uso. Sempre encriptado garante que os dados sensíveis nunca aparecem como texto simples dentro do sistema de base de dados. Depois de configurar a encriptação de dados, apenas aplicações de clientes ou servidores de aplicações que tenham acesso às teclas podem aceder a dados de texto simples. Para obter informações detalhadas, consulte [Sempre Encriptado (Motor de Base de Dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar Sempre Encriptado, criará uma aplicação de cliente em C# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos deste artigo e aprenda a configurar Sempre Encriptado para a sua base de dados em Base de Dados Azure SQL ou SQL Managed Instance. Neste artigo aprenderá a executar as seguintes tarefas:

- Utilize o assistente sempre encriptado no SSMS para criar [teclas sempre encriptadas](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  - Criar uma [chave-mestre de colunas (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  - Criar uma chave de encriptação de [colunas (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Crie uma tabela de bases de dados e criptografe colunas.
- Crie uma aplicação que insere, selecione e mostre dados das colunas encriptadas.

## <a name="prerequisites"></a>Pré-requisitos


- Uma conta e subscrição do Azure. Se não tiver um, inscreva-se para um [julgamento livre.](https://azure.microsoft.com/pricing/free-trial/)
- Uma base de dados em [Azure SQL Database](single-database-create-quickstart.md) ou [Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET Quadro 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Estúdio Visual.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Azure PowerShell](/powershell/azure/overview) ou [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Ativar o acesso à aplicação do cliente

Tem de ativar a sua aplicação de clientes para aceder à Base de Dados SQL ou à SQL Managed Instance, configurando uma aplicação de Diretório Ativo Azure (AAD) e copiando o ID de *aplicação* e *a chave* que necessitará para autenticar a sua aplicação.

Para obter o ID de *aplicação* e *chave,* siga os passos na criação de uma aplicação e diretor de [serviço Azure Ative que possam aceder a recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="create-a-key-vault-to-store-your-keys"></a>Crie um cofre chave para armazenar as suas chaves

Agora que a sua aplicação de cliente está configurada e tem o seu ID de aplicação, é hora de criar um cofre chave e configurar a sua política de acesso para que você e a sua aplicação possam aceder aos segredos do cofre (as chaves Sempre Encriptadas). As permissões de *criação,* *get*, *list*, *sign*, *check,* *wrapKey*e *desembrulhar* As permissões chave são necessárias para criar uma nova chave master de colunas e para configurar encriptação com o SQL Server Management Studio.

Você pode rapidamente criar um cofre chave executando o seguinte script. Para uma explicação detalhada destes comandos e mais informações sobre a criação e configuração de um cofre chave, veja [o que é o Cofre chave Azure?](../../key-vault/general/overview.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *


## <a name="connect-with-ssms"></a>Ligar com o SSMS

Abra o Estúdio Gerido pelo Servidor SQL (SSMS) e ligue-se ao servidor ou gerido com a sua base de dados.

1. Abra o SQL Server Management Studio. (Ir para **ligar**  >  **Motor de base de dados** para abrir a janela Connect to **Server** se não estiver aberta.)

2. Introduza o nome do seu servidor ou nome e credenciais de instância. 

    ![Copiar a cadeia de ligação](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Se a janela **New Firewall Rule** abrir, inscreva-se no Azure e deixe o SSMS criar uma nova regra de firewall para si.

## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para reter os dados do paciente. Não é inicialmente encriptado- irá configurar a encriptação na próxima secção.

1. Expandir **bases de dados**.
2. Clique na base de dados e clique em **Nova Consulta**.
3. Colhe o seguinte Transact-SQL (T-SQL) na **Execute** nova janela de consulta e execute-a.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografe colunas (configure Sempre Encriptado)

O SSMS fornece um assistente que o ajuda a configurar facilmente sempre encriptado, configurando a chave principal da coluna, a chave de encriptação da coluna e as colunas encriptadas para si.

1. Expandir **tabelas clínicas**de bases de  >  **Clinic**  >  **Tables**dados.
2. Clique na tabela **dos Pacientes** e selecione **Colunas de Encriptação** para abrir o assistente sempre encriptado:

    ![Criptografe colunas](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

O assistente sempre encriptado inclui as seguintes secções: **Seleção de Colunas,** **Configuração da Chave Master,** **Validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de Colunas

Clique em **Seguinte** na página **introdução** para abrir a página **de Seleção** de Colunas. Nesta página, irá selecionar quais as colunas que pretende encriptar, o tipo de encriptação e que chave de encriptação da [coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) utilizará.

Criptografe informações **de SSN** e **Data de Nascimento** para cada paciente. A coluna SSN usará encriptação determinística, que suporta as aparências de igualdade, adere e agrupam-se. A coluna BirthDate utilizará encriptação aleatória, que não suporta operações.

Desloque o tipo de **encriptação** para a coluna SSN para **Deterministic** e a coluna BirthDate para **Randomized**. Clique em **Seguinte**.

![Criptografe colunas](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave master

A página **de Configuração da Chave Master** é onde configura o seu CMK e seleciona o fornecedor de loja-chave onde o CMK será armazenado. Atualmente, pode armazenar um CMK na loja de certificados Windows, Azure Key Vault ou num módulo de segurança de hardware (HSM).

Este tutorial mostra como guardar as suas chaves no Cofre de Chaves Azure.

1. Selecione **Cofre de Chave Azure**.
2. Selecione o cofre de chaves desejado da lista de lançamentos.
3. Clique em **Seguinte**.

![Configuração da chave principal](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Validação

Pode encriptar as colunas agora ou guardar um script PowerShell para ser executado mais tarde. Para este tutorial, selecione **Proceder para terminar agora** e clique em **Next**.

### <a name="summary"></a>Resumo

Verifique se as definições estão todas corretas e clique em **Terminar** para completar a configuração para Sempre Encriptado.

![Resumo](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifique as ações do assistente

Depois de terminado o assistente, a sua base de dados está configurada para Sempre Encriptado. O assistente realizou as seguintes ações:

- Criei uma chave-mestre de colunas e guardei-a no Cofre de Chaves Azure.
- Criei uma chave de encriptação de colunas e guardei-a no Cofre de Chaves Azure.
- Configurou as colunas selecionadas para encriptação. A tabela Pacientes atualmente não tem dados, mas quaisquer dados existentes nas colunas selecionadas estão agora encriptados.

Pode verificar a criação das chaves em SSMS, expandindo as chaves **Clinic**  >  **Security**  >  **encriptadas de**segurança clínica .

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente que funcione com os dados encriptados

Agora que o Always Encrypted está configurado, pode construir uma aplicação que executa *inserções* e *seleciona* nas colunas encriptadas.  

> [!IMPORTANT]
> A sua aplicação deve utilizar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto simples para o servidor com colunas Sempre Encriptadas. Passar valores literais sem usar objetos SqlParameter resultará numa exceção.

1. Open Visual Studio e criar uma nova **Aplicação** de Consola C# (Visual Studio 2015 e anterior) ou **App consola (.NET Framework)** (Visual Studio 2017 e mais tarde). Certifique-se de que o seu projeto está definido para **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.
3. Instale os seguintes pacotes NuGet indo para **tools**  >  **NuGet Package Manager**Manager  >  **Consola**.

Executar estas duas linhas de código na consola do Gestor de Pacotes:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifique a sua cadeia de ligação para ativar Sempre Encriptado

Esta secção explica como ativar sempre encriptado na sua cadeia de ligação de base de dados.

Para ativar sempre encriptado, é necessário adicionar a palavra-chave de definição de **encriptação** da coluna à sua cadeia de ligação e defini-la para **Ativada**.

Pode defini-lo diretamente na cadeia de ligação, ou pode defini-lo utilizando [o SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação da amostra na secção seguinte mostra como utilizar o **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar Sempre Encriptado na cadeia de ligação

Adicione a seguinte palavra-chave à sua corda de ligação.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com SqlConnectionStringBuilder

O código que se segue mostra como ativar sempre encriptado, definindo [SqlConnectionStringBuilder.ColumnCryptonSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) to [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registe o fornecedor azure key vault
O código que se segue mostra como registar o fornecedor de cofre sinuoso Azure com o condutor ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Aplicação de consola de amostras sempre encriptada

Esta amostra demonstra como:

- Modifique a sua cadeia de ligação para ativar sempre encriptado.
- Registe o Azure Key Vault como o fornecedor chave da loja da aplicação.  
- Insira os dados nas colunas encriptadas.
- Selecione um registo filtrando por um valor específico numa coluna encriptada.

Substitua o conteúdo do *Program.cs* pelo seguinte código. Substitua a cadeia de ligação para a variável de ligação globalString na linha que precede diretamente o método Principal com a sua cadeia de ligação válida do portal Azure. Esta é a única mudança que precisa fazer a este código.

Execute a aplicação para ver Sempre Encriptado em ação.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Verifique se os dados estão encriptados

Pode verificar rapidamente se os dados reais do servidor estão encriptados consultando os dados dos Pacientes com SSMS (utilizando a sua ligação atual onde a Definição de **Encriptação** da Coluna ainda não está ativada).

Execute a seguinte consulta na base de dados da Clínica.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Pode ver que as colunas encriptadas não contêm dados de texto simples.

   ![Nova aplicação de consola](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Para utilizar o SSMS para aceder aos dados de texto simples, primeiro é necessário garantir que o utilizador tem permissões adequadas para o Cofre de Chaves Azure: *obter*, *desembrulharKey*, e *verificar*. Para obter informações detalhadas, consulte [Criar e Armazenar Chaves Master (Sempre Encriptadas)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Em seguida, adicione o parâmetro de definição de encriptação da *coluna=ativado* durante a sua ligação.

1. No SSMS, clique no seu servidor no **Object Explorer** e escolha **Desligar**.
2. Clique em **ligar**  >  **o motor base** de dados para abrir a janela **Connect to Server** e clicar **em Opções**.
3. Clique em parâmetros de **ligação adicionais** e na definição de encriptação da **coluna do tipo=ativada**.

    ![Nova aplicação de consola](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Execute a seguinte consulta na base de dados da Clínica.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Pode agora ver os dados de texto simples nas colunas encriptadas.
     ![Nova aplicação de consola](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Próximos passos

Depois de configurada a sua base de dados para utilizar Sempre Encriptado, poderá querer fazer o seguinte:

- [Rode e limpe as chaves.](https://msdn.microsoft.com/library/mt607048.aspx)
- [Migrar dados que já estão encriptados com Sempre Encriptado](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informações relacionadas

- [Sempre Encriptado (desenvolvimento do cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Encriptação do Servidor SQL](https://msdn.microsoft.com/library/bb510663.aspx)
- [Sempre encriptado assistente](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog sempre encriptado](https://docs.microsoft.com/archive/blogs/sqlsecurity/always-encrypted-key-metadata)

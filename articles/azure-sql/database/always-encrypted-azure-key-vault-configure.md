---
title: Configure Sempre Encriptado usando o Cofre da Chave Azure
description: Este tutorial mostra-lhe como proteger dados confidenciais numa base de dados na Base de Dados Azure SQL com encriptação de dados utilizando o assistente sempre encriptado no SQL Server Management Studio.
keywords: encriptação de dados, chave de encriptação, encriptação em nuvem
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 26bac8115a64d78ce64bc400f98fb26cb929ba4d
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164504"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Configure Sempre Encriptado usando o Cofre da Chave Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo mostra-lhe como proteger dados sensíveis numa base de dados na Base de Dados Azure SQL com encriptação de dados utilizando o [assistente sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-wizard) no [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Também inclui instruções que lhe mostrarão como armazenar cada chave de encriptação no Cofre de Chaves Azure.

Sempre encriptada é uma tecnologia de encriptação de dados que ajuda a proteger dados sensíveis em repouso no servidor, durante o movimento entre o cliente e o servidor, e enquanto os dados estão em uso. Sempre encriptado garante que os dados sensíveis nunca aparecem como texto simples dentro do sistema de base de dados. Depois de configurar a encriptação de dados, apenas as aplicações do cliente ou servidores de aplicações que tenham acesso às teclas podem aceder a dados de texto simples. Para obter informações detalhadas, consulte [Sempre Encriptado (Motor de Base de Dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar Always Encrypted, criará uma aplicação do cliente em C# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos deste artigo e aprenda a configurar Sempre Encriptado para a sua base de dados na Base de Dados Azure SQL ou na SQL Managed Instance. Neste artigo aprenderá a executar as seguintes tarefas:

- Utilize o assistente sempre encriptado em SSMS para criar [teclas sempre encriptadas](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  - Criar uma [chave-mestre de colunas (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  - Criar uma [chave de encriptação de colunas (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Crie uma tabela de bases de dados e criptografe colunas.
- Crie uma aplicação que insira, selecione e apresente dados a partir das colunas encriptadas.

## <a name="prerequisites"></a>Pré-requisitos


- Uma conta e subscrição do Azure. Se não tiver um, inscreva-se para um [julgamento gratuito.](https://azure.microsoft.com/pricing/free-trial/)
- Uma base de dados na [Base de Dados Azure SQL](single-database-create-quickstart.md) ou [na Azure SQL Gerenciada Instância](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou mais tarde.
- [.NET Quadro 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Estúdio Visual](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) ou [Azure CLI](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Ativar o acesso à aplicação do cliente

Tem de ativar a aplicação do seu cliente para aceder à sua base de dados na Base de Dados SQL, criando uma aplicação Azure Ative Directory (Azure AD) e copiando o ID da *aplicação* e *a chave* que necessitará para autenticar a sua aplicação.

Para obter o ID e *a chave* *da aplicação,* siga os passos para criar uma [aplicação e um diretor de serviço azure ative que possa aceder aos recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="create-a-key-vault-to-store-your-keys"></a>Crie um cofre chave para guardar as chaves

Agora que a sua aplicação cliente está configurada e tem o seu ID de aplicação, é hora de criar um cofre chave e configurar a sua política de acesso para que você e a sua aplicação possam aceder aos segredos do cofre (as chaves Sempre Encriptadas). As permissões *de criar*, *obter*, *obter,* *assinar,* *verificar,* *wrapKey*, e *desembrulhar* as permissões São necessárias para criar uma nova chave principal de coluna e para configurar encriptação com o SQL Server Management Studio.

Pode criar rapidamente um cofre de chaves executando o seguinte script. Para uma explicação detalhada destes comandos e mais informações sobre a criação e configuração de um cofre chave, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

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
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Ligar com o SSMS

Abra o SQL Server Management Studio (SSMS) e ligue-se ao servidor ou seja gerido com a sua base de dados.

1. Abra o SQL Server Management Studio. (Ir para **a Ligação**  >  **Motor de base de dados** para abrir a janela **'Ligar ao Servidor'** se não estiver aberta.)

2. Insira o nome do servidor ou nome de instância e credenciais. 

    ![Copiar a cadeia de ligação](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Se a janela **New Firewall Rule** abrir, inscreva-se no Azure e deixe que o SSMS crie uma nova regra de firewall para si.

## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para conter os dados do paciente. Não está inicialmente encriptado, configurará a encriptação na próxima secção.

1. Expandir **bases de dados.**
2. Clique com o botão direito na base de dados e clique em **Nova Consulta.**
3. Cole o seguinte Transact-SQL (T-SQL) na nova **Execute** janela de consulta e execute-a.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Colunas encriptadas (configurar sempre encriptadas)

O SSMS fornece um assistente que o ajuda a configurar facilmente Sempre Encriptado, configurando a chave principal da coluna, a chave de encriptação da coluna e colunas encriptadas para si.

1. Expandir **tabelas clínicas de bases de**  >  **Clinic**  >  **dados.**
2. Clique com o botão direito na tabela **'Pacientes'** e **selecione Encrypt Columns** para abrir o assistente sempre encriptado:

    ![Screenshot que destaca as Colunas encriptadas... opção menu.](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

O assistente sempre encriptado inclui as seguintes secções: **Seleção de Colunas,** **Configuração de Chave Master,** **Validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de Colunas

Clique em **seguida** na página **Introdução** para abrir a página **de Seleção de Colunas.** Nesta página, irá selecionar quais as colunas que pretende encriptar, [o tipo de encriptação e qual a chave de encriptação da coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) a utilizar.

Criptografe as informações **do SSN** e **do BirthDate** para cada paciente. A coluna SSN usará encriptação determinística, que suporta procuras de igualdade, juntas e grupo por. A coluna BirthDate utilizará encriptação aleatória, que não suporta operações.

Desaprova o **Tipo de Encriptação** para a coluna SSN **para determinista** e a coluna BirthDate **para Randomized**. Clique em **Seguinte**.

![Encriptar colunas](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuração de chave principal

A página **de Configuração da Chave Principal** é onde configura o seu CMK e seleciona o fornecedor de loja chave onde o CMK será armazenado. Atualmente, pode armazenar um CMK na loja de certificados Windows, Azure Key Vault ou um módulo de segurança de hardware (HSM).

Este tutorial mostra como guardar as chaves no Cofre da Chave Azure.

1. Selecione **Azure Key Vault**.
2. Selecione o cofre de teclas pretendido da lista de drop-down.
3. Clique em **Seguinte**.

![Configuração da chave principal](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Validação

Pode encriptar as colunas agora ou guardar um script PowerShell para ser executado mais tarde. Para este tutorial, **selecione Proceder para terminar agora** e clique em **Seguinte**.

### <a name="summary"></a>Resumo

Verifique se as definições estão todas corretas e clique em **Terminar** para completar a configuração para Sempre Encriptado.

![A screenshot mostra a página de resultados com tarefas marcadas como passadas.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifique as ações do feiticeiro

Depois de terminado o assistente, a sua base de dados é configurada para "Always Encrypted". O assistente realizou as seguintes ações:

- Criei uma chave-mestre de colunas e guardou-a no Cofre da Chave Azure.
- Criei uma chave de encriptação de colunas e guardou-a no Cofre da Chave Azure.
- Configurar as colunas selecionadas para encriptação. A tabela Pacientes não tem dados, mas quaisquer dados existentes nas colunas selecionadas estão agora encriptados.

Pode verificar a criação das chaves no SSMS expandindo as chaves sempre **Clinic**  >  **Security**  >  **encriptadas**da Clinic Security .

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente que funcione com os dados encriptados

Agora que está sempre encriptado, pode construir uma aplicação que executa *inserções* e *selecione* nas colunas encriptadas.  

> [!IMPORTANT]
> A sua aplicação deve utilizar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto simples para o servidor com colunas Sempre Encriptadas. Passar valores literais sem utilizar objetos SqlParameter resultará numa exceção.

1. Abra o Visual Studio e crie uma nova **aplicação de consola** C# (Visual Studio 2015 e anterior) ou **App de Consola (.NET Framework)** (Visual Studio 2017 e mais tarde). Certifique-se de que o seu projeto está definido para **.NET Framework 4.6** ou mais tarde.
2. Nomeie o projeto **AlwaysEncryptedConsoleAKVApp** e clique **em OK**.
3. Instale os seguintes pacotes NuGet indo para **a Consola**de Gestor de  >  **Pacotes Do Gestor de Pacotes**Do  >  **NuGet.**

Executar estas duas linhas de código na consola package manager:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifique o seu string de ligação para ativar sempre encriptado

Esta secção explica como ativar sempre encriptado na sua cadeia de ligação de base de dados.

Para ativar sempre encriptado, é necessário adicionar a palavra-chave **de definição de encriptação** da coluna à sua cadeia de ligação e defini-la para **Ativada**.

Pode defini-lo diretamente na cadeia de ligação, ou pode defini-lo utilizando [o SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação da amostra na secção seguinte mostra como utilizar o **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar sempre encriptado na cadeia de ligação

Adicione a seguinte palavra-chave à sua cadeia de ligação.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com SqlConnectionStringBuilder

O código que se segue mostra como ativar sempre encriptado definindo [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) to [Enableed](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registe o fornecedor Azure Key Vault
O código que se segue mostra como registar o fornecedor Azure Key Vault com o ADO.NET condutor.

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

## <a name="always-encrypted-sample-console-application"></a>Aplicação de consola de amostra sempre encriptada

Esta amostra demonstra como:

- Modifique o seu fio de ligação para ativar Sempre Encriptado.
- Registe o Azure Key Vault como o fornecedor chave da loja da aplicação.  
- Insira os dados nas colunas encriptadas.
- Selecione um registo filtrando um valor específico numa coluna encriptada.

Substitua o conteúdo da *Program.cs* pelo seguinte código. Substitua a cadeia de ligação para a variável de ligação global Adceding na linha que precede diretamente o método Principal com a sua cadeia de ligação válida a partir do portal Azure. Esta é a única mudança que precisa fazer a este código.

Execute a aplicação para ver Always Encrypted em ação.

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

Pode verificar rapidamente se os dados reais do servidor são encriptados consultando os dados dos Pacientes com SSMS (utilizando a sua ligação atual onde a **Definição de Encriptação da Coluna** ainda não está ativada).

Execute a seguinte consulta na base de dados da Clínica.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Pode ver que as colunas encriptadas não contêm dados de texto simples.

   ![Screenshot que mostra que as colunas encriptadas não contêm quaisquer dados de texto simples.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Para utilizar o SSMS para aceder aos dados de texto simples, tem primeiro de se certificar de que o utilizador tem permissões adequadas para o Cofre da Chave Azure: *obter,* *desembrulhar aKey,* e *verificar*. Para obter informações detalhadas, consulte [As Teclas Principais da Coluna criar e armazenar (sempre encriptadas)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Em seguida, adicione o *parâmetro de definição de encriptação da coluna=ativado* durante a sua ligação.

1. No SSMS, clique com o botão direito no seu servidor no **Object Explorer** e escolha **Desligar**.
2. Clique **em Connect**Database  >  **Engine** para abrir a janela **'Ligar ao servidor'** e clicar em **Opções**.
3. Clique em **parâmetros de ligação adicionais** e tipo **de definição de encriptação da coluna=ativada**.

    ![Screenshot que mostra o separador Parâmetros de Correção Adicional.](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Execute a seguinte consulta na base de dados da Clínica.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   Agora é possível ver os dados de texto simples nas colunas encriptadas.
   
   ![Nova aplicação para consolas](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Passos seguintes

Depois de a sua base de dados estar configurada para utilizar Sempre Encriptado, pode querer fazer o seguinte:

- [Rode e limpe as chaves.](https://msdn.microsoft.com/library/mt607048.aspx)
- [Migrar dados que já estão encriptados com Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informações relacionadas

- [Sempre encriptado (desenvolvimento do cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Encriptação de dados transparentes](https://msdn.microsoft.com/library/bb934049.aspx)
- [Encriptação do Servidor SQL](https://msdn.microsoft.com/library/bb510663.aspx)
- [Feiticeiro sempre encriptado](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog sempre encriptado](https://docs.microsoft.com/archive/blogs/sqlsecurity/always-encrypted-key-metadata)

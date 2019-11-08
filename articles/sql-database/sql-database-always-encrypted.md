---
title: Always Encrypted-repositório de certificados do Windows
description: Este artigo mostra como proteger dados confidenciais em um banco de dados SQL com criptografia de banco de dado usando o assistente de Always Encrypted no SQL Server Management Studio (SSMS). Ele também mostra como armazenar suas chaves de criptografia no repositório de certificados do Windows.
keywords: criptografar dados, criptografia do SQL, criptografia de banco de dados, informações confidenciais Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822049"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: proteger dados confidenciais e armazenar chaves de criptografia no repositório de certificados do Windows

Este artigo mostra como proteger dados confidenciais em um banco de dados SQL com criptografia de banco de dado usando o [Assistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também mostra como armazenar suas chaves de criptografia no repositório de certificados do Windows.

Always Encrypted é uma nova tecnologia de criptografia de dados no banco de dados SQL do Azure e SQL Server que ajuda a proteger dados confidenciais em repouso no servidor, durante a movimentação entre o cliente e o servidor, e enquanto os dados estão em uso, garantindo que os dados confidenciais nunca apareçam como texto não criptografado dentro do sistema de banco de dados. Depois de criptografar dados, somente aplicativos cliente ou servidores de aplicativos que têm acesso às chaves podem acessar dados de texto não criptografado. Para obter informações detalhadas, consulte [Always Encrypted (mecanismo de banco de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar o banco de dados para usar Always Encrypted, você criará um C# aplicativo cliente no com o Visual Studio para trabalhar com os dados criptografados.

Siga as etapas neste artigo para saber como configurar Always Encrypted para um banco de dados SQL do Azure. Neste artigo, você aprenderá a executar as seguintes tarefas:

* Use o assistente de Always Encrypted no SSMS para criar [chaves de Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Crie uma [chave mestra de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Crie uma [CEK (chave de criptografia de coluna)](https://msdn.microsoft.com/library/mt146372.aspx).
* Criar uma tabela de banco de dados e criptografar colunas.
* Crie um aplicativo que insere, seleciona e exibe dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisará de:

* Uma conta e subscrição do Azure. Se você não tiver uma, Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET Framework 4,6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Criar uma base de dados SQL vazia

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **criar um recurso** > **Data + armazenamento** > banco de dados **SQL**.
3. Crie um banco de dados **em branco** chamado **clínica** em um servidor novo ou existente. Para obter instruções detalhadas sobre como criar um banco de dados no portal do Azure, consulte [seu primeiro banco de dados SQL do Azure](sql-database-single-database-get-started.md).

    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted/create-database.png)

Você precisará da cadeia de conexão posteriormente no tutorial. Depois que o banco de dados for criado, vá para o novo banco de dados da clínica e copie a cadeia de conexão. Você pode obter a cadeia de conexão a qualquer momento, mas é fácil copiá-la quando estiver na portal do Azure.

1. Clique em bancos de dados **SQL** > **clínica** > **Mostrar cadeias de conexão de banco de dados**.
2. Copie a cadeia de conexão para **ADO.net**.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS

Abra o SSMS e conecte-se ao servidor com o banco de dados da clínica.

1. Abra o SQL Server Management Studio. (Clique em **conectar** > **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** se ela não estiver aberta).
2. Insira o nome do servidor e as credenciais. O nome do servidor pode ser encontrado na folha do banco de dados SQL e na cadeia de conexão que você copiou anteriormente. Digite o nome completo do servidor, incluindo *Database.Windows.net*.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/ssms-connect.png)

Se a janela **nova regra de firewall** abrir, entre no Azure e deixe que o SSMS crie uma nova regra de firewall para você.

## <a name="create-a-table"></a>Criar uma tabela

Nesta seção, você criará uma tabela para manter os dados do paciente. Esta será uma tabela normal inicialmente--você configurará a criptografia na próxima seção.

1. Expanda **bancos de dados**.
2. Clique com o botão direito do mouse no banco de dados da **clínica** e clique em **nova consulta**.
3. Cole o Transact-SQL (T-SQL) a seguir na nova janela de consulta e **Execute** -o.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografar colunas (configurar Always Encrypted)

O SSMS fornece um assistente para configurar facilmente Always Encrypted configurando as colunas CMK, CEK e Encrypted para você.

1. Expanda **bancos de dados** > **clínica** > **tabelas**.
2. Clique com o botão direito do mouse na tabela **pacientes** e selecione **criptografar colunas** para abrir o assistente de Always Encrypted:

    ![Criptografar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente de Always Encrypted inclui as seguintes seções **: seleção de coluna**, configuração de **chave mestra** (CMK), **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna

Clique em **Avançar** na página **introdução** para abrir a página **seleção de coluna** . Nessa página, você selecionará quais colunas deseja criptografar, [o tipo de criptografia e qual chave de criptografia de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

Criptografar informações de **SSN** e **DataDeNascimento** para cada paciente. A coluna **ssn** usará a criptografia determinística, que oferece suporte a pesquisas de igualdade, junções e agrupar por. A coluna **DataDeNascimento** usará a criptografia aleatória, que não oferece suporte a operações.

Defina o **tipo de criptografia** para a coluna **SSN** como **determinístico** e a coluna **DataDeNascimento** como **aleatório**. Clique em **Seguinte**.

![Criptografar colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave mestra

A página de **configuração de chave mestra** é onde você configura seu CMK e seleciona o provedor de repositório de chaves em que o CMK será armazenado. No momento, você pode armazenar um CMK no repositório de certificados do Windows, Azure Key Vault ou um módulo de segurança de hardware (HSM). Este tutorial mostra como armazenar suas chaves no repositório de certificados do Windows.

Verifique se **repositório de certificados do Windows** está selecionado e clique em **Avançar**.

![Configuração da chave mestra](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validação

Você pode criptografar as colunas agora ou salvar um script do PowerShell para ser executado mais tarde. Para este tutorial, selecione **continuar para concluir agora** e clique em **Avançar**.

### <a name="summary"></a>Resumo

Verifique se as configurações estão todas corretas e clique em **concluir** para concluir a instalação para Always Encrypted.

![Resumo](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Verificar as ações do assistente

Depois que o assistente for concluído, seu banco de dados será configurado para Always Encrypted. O assistente executou as seguintes ações:

* Criou um CMK.
* Criou um CEK.
* Configuradas as colunas selecionadas para criptografia. Sua tabela **pacientes** não tem dados no momento, mas todos os dados existentes nas colunas selecionadas agora estão criptografados.

Você pode verificar a criação das chaves no SSMS acessando a **clínica** > **Security** > **Always Encrypted Keys**. Agora você pode ver as novas chaves que o assistente gerou para você.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar um aplicativo cliente que funciona com os dados criptografados

Agora que Always Encrypted está configurado, você pode criar um aplicativo que executa *inserções* e *seleciona* nas colunas criptografadas. Para executar o aplicativo de exemplo com êxito, você deve executá-lo no mesmo computador em que executou o assistente de Always Encrypted. Para executar o aplicativo em outro computador, você deve implantar seus Always Encrypted certificados no computador que executa o aplicativo cliente.  

> [!IMPORTANT]
> Seu aplicativo deve usar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto sem formatação para o servidor com colunas Always Encrypted. Passar valores literais sem usar objetos SqlParameter resultará em uma exceção.

1. Abra o Visual Studio e crie um C# novo aplicativo de console. Verifique se seu projeto está definido para **.NET Framework 4,6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleApp** e clique em **OK**.

![Novo aplicativo de console](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifique a cadeia de conexão para habilitar Always Encrypted

Esta seção explica como habilitar Always Encrypted em sua cadeia de conexão de banco de dados. Você modificará o aplicativo de console que acabou de criar na próxima seção, "Always Encrypted aplicativo de console de exemplo".

Para habilitar Always Encrypted, você precisa adicionar a palavra-chave de **configuração de criptografia de coluna** à cadeia de conexão e defini-la como **habilitada**.

Você pode definir isso diretamente na cadeia de conexão ou pode defini-la usando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na próxima seção mostra como usar o **SqlConnectionStringBuilder**.

> [!NOTE]
> Essa é a única alteração necessária em um aplicativo cliente específico para Always Encrypted. Se você tiver um aplicativo existente que armazena sua cadeia de conexão externamente (ou seja, em um arquivo de configuração), poderá habilitar Always Encrypted sem alterar nenhum código.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar Always Encrypted na cadeia de conexão

Adicione a seguinte palavra-chave à sua cadeia de conexão:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Habilitar Always Encrypted com um SqlConnectionStringBuilder

O código a seguir mostra como habilitar Always Encrypted definindo o [SqlConnectionStringBuilder. ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) como [habilitado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted aplicativo de console de exemplo

Este exemplo demonstra como:

* Modifique a cadeia de conexão para habilitar Always Encrypted.
* Inserir dados nas colunas criptografadas.
* Selecione um registro filtrando um valor específico em uma coluna criptografada.

Substitua o conteúdo de **Program.cs** pelo código a seguir. Substitua a cadeia de conexão da variável connectionString global na linha diretamente acima do método Main por sua cadeia de conexão válida do portal do Azure. Essa é a única alteração que você precisa fazer nesse código.

Execute o aplicativo para ver Always Encrypted em ação.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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

Você pode verificar rapidamente se os dados reais no servidor são criptografados consultando os dados de **pacientes** com o SSMS. (Use sua conexão atual onde a configuração de criptografia de coluna ainda não está habilitada.)

Execute a consulta a seguir no banco de dados da clínica.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Você pode ver que as colunas criptografadas não contêm nenhum dado de texto sem formatação.

   ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-encrypted.png)

Para usar o SSMS para acessar os dados de texto sem formatação, você pode adicionar o parâmetro de **configuração de criptografia de coluna = habilitado** para a conexão.

1. No SSMS, clique com o botão direito do mouse em seu servidor no **pesquisador de objetos**e clique em **Desconectar**.
2. Clique em **conectar** > **mecanismo de banco de dados** para abrir a janela **conectar ao servidor** e clique em **Opções**.
3. Clique em **parâmetros de conexão adicionais** e digite **configuração de criptografia de coluna = habilitado**.

    ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Execute a consulta a seguir no banco de dados da **clínica** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora você pode ver os dados de texto sem formatação nas colunas criptografadas.

    ![Novo aplicativo de console](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Se você se conectar com o SSMS (ou qualquer cliente) de um computador diferente, ele não terá acesso às chaves de criptografia e não será capaz de descriptografar os dados.

## <a name="next-steps"></a>Passos seguintes

Depois de criar um banco de dados que usa Always Encrypted, talvez você queira fazer o seguinte:

* Execute este exemplo de um computador diferente. Ele não terá acesso às chaves de criptografia, portanto, não terá acesso aos dados de texto não criptografado e não será executado com êxito.
* [Gire e limpe suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migre dados que já estão criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Implante Always Encrypted certificados em outros computadores cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a seção "fazendo certificados disponíveis para aplicativos e usuários").

## <a name="related-information"></a>Informações relacionadas

* [Always Encrypted (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação de Dados Transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server criptografia](https://msdn.microsoft.com/library/bb510663.aspx)
* [Assistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog de Always Encrypted](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
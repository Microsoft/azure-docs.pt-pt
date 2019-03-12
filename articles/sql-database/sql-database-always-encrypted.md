---
title: 'Sempre encriptados: SQL Database do Azure - arquivo de certificados do Windows | Documentos da Microsoft'
description: Este artigo mostra-lhe como proteger dados confidenciais numa base de dados SQL com a encriptação de base de dados utilizando o assistente encriptado sempre no SQL Server Management Studio (SSMS). Ele também mostra como armazenar as chaves de encriptação no arquivo de certificados do Windows.
keywords: encriptar dados, a criptografia do sql, a encriptação de base de dados, dados confidenciais, Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 03/08/2019
ms.openlocfilehash: 5226ec05af95cf305008968cf945070532274ee5
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726945"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Sempre encriptados: Proteger dados confidenciais e armazenar chaves de encriptação no arquivo de certificados do Windows

Este artigo mostra-lhe como proteger dados confidenciais numa base de dados SQL com a encriptação de base de dados utilizando o [sempre o Assistente de encriptados](https://msdn.microsoft.com/library/mt459280.aspx) na [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Ele também mostra como armazenar as chaves de encriptação no arquivo de certificados do Windows.

Always Encrypted é uma nova tecnologia de encriptação de dados na base de dados do Azure SQL e SQL Server que o ajuda a proteger dados confidenciais Inativos no servidor, durante a movimentação entre cliente e servidor e, enquanto os dados estão em utilização, garantir que os dados confidenciais nunca aparece como texto sem formatação dentro do sistema de base de dados. Depois de criptografar dados, apenas as aplicações de cliente ou servidores de aplicações que têm acesso às chaves podem aceder a dados de texto sem formatação. Para obter informações detalhadas, consulte [Always Encrypted (motor de base de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar o Always Encrypted, irá criar uma aplicação cliente no c# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos neste artigo para saber como configurar o Always Encrypted para uma base de dados SQL do Azure. Neste artigo, aprenderá a realizar as seguintes tarefas:

* Utilize o assistente sempre encriptado no SSMS para criar [chaves de encriptado sempre](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Criar uma [chave mestra de coluna de (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Criar uma [chave de encriptação de coluna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Criar uma tabela de base de dados e encriptar colunas.
* Crie uma aplicação que insere, seleciona e apresenta os dados das colunas criptografadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, terá de:

* Uma conta e subscrição do Azure. Se não tiver uma, inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Criar uma base de dados SQL vazia

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **criar um recurso** > **dados + armazenamento** > **base de dados SQL**.
3. Criar uma **em branco** com o nome da base de dados **clínica** num servidor novo ou existente. Para obter instruções detalhadas sobre como criar uma base de dados no portal do Azure, consulte [sua primeira base de dados SQL do Azure](sql-database-single-database-get-started.md).

    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted/create-database.png)

Terá da cadeia de ligação mais tarde no tutorial. Depois de criar a base de dados, vá para a nova base de dados de clínica e copie a cadeia de ligação. Pode obter a cadeia de ligação em qualquer altura, mas é fácil copiá-lo quando estiver no portal do Azure.

1. Clique em **bases de dados SQL** > **clínica** > **Mostrar cadeias de ligação de base de dados**.
2. Copie a cadeia de ligação para **ADO.NET**.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS

Abra o SSMS e ligue ao servidor com a base de dados clínica.

1. Abra o SQL Server Management Studio. (Clique em **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela se não estiver aberto).
2. Introduza o nome do servidor e as credenciais. O nome do servidor pode ser encontrado no painel da base de dados do SQL e na cadeia de ligação que copiou anteriormente. Escreva o nome de servidor completa incluindo *database.windows.net*.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/ssms-connect.png)

Se o **nova regra de Firewall** é apresentada a janela, inicie sessão no Azure e permita que o SSMS crie uma nova regra de firewall para.

## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para conter dados de pacientes. Este será inicialmente – uma tabela normal irá configurar a encriptação na próxima seção.

1. Expanda **bases de dados**.
2. Com o botão direito a **clínica** da base de dados e clique em **nova consulta**.
3. Cole o Transact-SQL seguinte (T-SQL) na nova janela de consulta e **Execute** -lo.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Encriptar colunas (configurar Always Encrypted)

SSMS fornece um Assistente para configurar facilmente Always Encrypted ao definir o CMK CEK e colunas encriptadas para.

1. Expanda **bases de dados** > **clínica** > **tabelas**.
2. Com o botão direito a **pacientes** da tabela e selecione **encriptar colunas** para abrir o assistente sempre encriptado:

    ![Encriptar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente sempre encriptado inclui as secções seguintes: **Seleção da coluna**, **configuração da chave mestra** (CMK), **validação**, e **resumo**.

### <a name="column-selection"></a>Seleção de coluna

Clique em **próxima** no **introdução** página para abrir o **seleção de coluna** página. Nesta página, que irá selecionar as colunas que pretende encriptar, [o tipo de encriptação e que chave de encriptação de coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) a utilizar.

Encriptar **SSN** e **data de nascimento** informações para cada doente. O **SSN** coluna irá utilizar encriptação determinista, que oferece suporte a pesquisas de igualdade, associações e agrupar por. O **data de nascimento** coluna irá utilizar encriptação aleatória, que não suporta operações.

Definir o **tipo de encriptação** para o **SSN** coluna para **Deterministic** e a **data de nascimento** coluna para **Randomized** . Clique em **Seguinte**.

![Encriptar colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave mestra

O **configuração da chave mestra** página é onde configurar seu CMK e selecionar o fornecedor de armazenamento de chaves onde será armazenado o CMK. Atualmente, pode armazenar um CMK no arquivo de certificados do Windows, no Azure Key Vault ou um módulo de segurança de hardware (HSM). Este tutorial mostra como armazenar as suas chaves no arquivo de certificados do Windows.

Certifique-se de que **arquivo de certificados do Windows** está selecionada e clique em **próxima**.

![Configuração da chave mestra](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validação

Pode encriptar as colunas agora ou guardar um script do PowerShell para executar mais tarde. Neste tutorial, selecione **continuar para terminar agora** e clique em **próxima**.

### <a name="summary"></a>Resumo

Certifique-se de que as definições estão corretas e clique em **concluir** para concluir a configuração para sempre encriptados.

![Resumo](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Certifique-se de ações do Assistente

Depois de concluído o assistente, a base de dados é configurada para sempre encriptados. O assistente efetuar as seguintes ações:

* Criar um CMK.
* Criar um CEK.
* Configurar as colunas selecionadas para a encriptação. Sua **pacientes** tabela atualmente não tem dados, mas qualquer dado existente nas colunas selecionadas já está encriptado.

Pode verificar a criação de chaves no SSMS, acedendo a **clínica** > **segurança** > **sempre as chaves encriptada**. Agora, pode ver as novas chaves de que o assistente gerado para.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente que funciona com os dados encriptados

Agora que Always Encrypted é configurada, pode criar um aplicativo que realiza *insere* e *seleciona* nas colunas encriptadas. Para executar com êxito a aplicação de exemplo, tem de a executar no mesmo computador em que executou o assistente sempre encriptado. Para executar o aplicativo em outro computador, tem de implementar os certificados são sempre encriptados no computador que executa a aplicação de cliente.  

> [!IMPORTANT]
> Tem de utilizar o seu aplicativo [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos quando passar dados de texto sem formatação para o servidor com as colunas são sempre encriptados. Transmitir valores literais sem utilizar objetos SqlParameter resultará numa exceção.

1. Abra o Visual Studio e crie um novo aplicativo de console do c#. Certifique-se de que o seu projeto está definido como **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleApp** e clique em **OK**.

![Nova aplicação de consola](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de ligação para ativar Always Encrypted

Esta secção explica como ativar o Always Encrypted na sua cadeia de ligação de base de dados. Modificará a aplicação de consola que acabou de criar na próxima seção, "Always Encrypted exemplo de aplicação de consola."

Para ativar Always Encrypted, tem de adicionar o **definição de encriptação de coluna** palavra-chave para a ligação de cadeias de caracteres e defini-lo como **ativado**.

Pode definir isso diretamente na cadeia de ligação ou pode configurá-lo ao utilizar um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). O aplicativo de exemplo na secção seguinte mostra como usar **SqlConnectionStringBuilder**.

> [!NOTE]
> Esta é a única alteração necessária num aplicativo de cliente específico para sempre encriptados. Se tiver uma aplicação existente que armazena a cadeia de ligação externa (ou seja, num arquivo de configuração), poderá ativar Always Encrypted sem alterar nenhum código.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar Always Encrypted na cadeia de ligação

Adicione a seguinte palavra-chave para a cadeia de ligação:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com uma SqlConnectionStringBuilder

O código seguinte mostra como ativar o Always Encrypted, definindo a [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) ao [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Sempre encriptado aplicativo de console de exemplo

Este exemplo demonstra como:

* Modificar a cadeia de ligação para ativar Always Encrypted.
* Inserir dados em colunas criptografadas.
* Selecione um registo ao filtrar para um valor específico numa coluna criptografada.

Substitua o conteúdo do **Program.cs** com o código a seguir. Substitua a cadeia de ligação para a variável global connectionString na linha diretamente acima o método Main sua cadeia de ligação válida a partir do portal do Azure. Esta é a única alteração, que terá de fazer no código.

Execute a aplicação para ver o Always Encrypted em ação.

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

## <a name="verify-that-the-data-is-encrypted"></a>Certifique-se de que os dados são encriptados

Pode verificar rapidamente o que os dados reais no servidor são encriptados através de consultas a **pacientes** dados com o SSMS. (Utilize a ligação atual em que a definição de encriptação de coluna ainda não está ativada.)

Execute a seguinte consulta na base de dados clínica.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode ver que colunas criptografadas não contêm quaisquer dados de texto sem formatação.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)

Para utilizar o SSMS para acessar os dados de texto sem formatação, pode adicionar os **definição de encriptação de coluna = ativada** parâmetro para a ligação.

1. No SSMS, clique no seu servidor no **Object Explorer**e, em seguida, clique em **desligar**.
2. Clique em **Connect** > **motor de base de dados** para abrir o **ligar ao servidor** janela e clique em **opções**.
3. Clique em **parâmetros de ligação adicionais** e escreva **definição de encriptação de coluna = ativada**.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Execute a seguinte consulta **clínica** base de dados.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora, pode ver os dados de texto sem formatação em colunas criptografadas.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Se ligar com o SSMS (ou qualquer cliente) num computador diferente, não terá acesso às chaves de encriptação e não será capaz de descriptografar os dados.

## <a name="next-steps"></a>Passos Seguintes

Depois de criar uma base de dados que utiliza o Always Encrypted, pode querer fazer o seguinte:

* Execute este exemplo num computador diferente. Ele não terá acesso às chaves de encriptação, pelo que não terá acesso aos dados de texto sem formatação e não será executado com êxito.
* [Rodar e limpar as suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrar os dados que já são criptografados com o Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).
* [Implementar certificados Always Encrypted para outras máquinas cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a seção de "Tornar certificados disponíveis para aplicações e utilizadores").

## <a name="related-information"></a>Informações relacionadas

* [Sempre encriptado (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [Criptografia do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Assistente sempre encriptado](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog sempre encriptado](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
---
title: Sempre Encriptado - Loja de certificados Windows
description: Este artigo mostra-lhe como proteger dados sensíveis numa base de dados SQL com encriptação de base de dados utilizando o Assistente Sempre Encriptado no Estúdio de Gestão de Servidores SQL (SSMS). Também mostra como armazenar as suas chaves de encriptação na loja de certificados Windows.
keywords: encriptar dados, encriptação sql, encriptação de base de dados, dados sensíveis, Sempre Encriptado
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822049"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Sempre Encriptado: Proteja dados sensíveis e guarde chaves de encriptação na loja de certificados Windows

Este artigo mostra-lhe como proteger dados sensíveis numa base de dados SQL com encriptação de base de dados utilizando o [Assistente Sempre Encriptado](https://msdn.microsoft.com/library/mt459280.aspx) no Estúdio de [Gestão de Servidores SQL (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Também mostra como armazenar as suas chaves de encriptação na loja de certificados Windows.

Sempre Encriptado é uma nova tecnologia de encriptação de dados na Base de Dados SQL do Azure e no SQL Server que ajuda a proteger dados sensíveis em repouso no servidor, durante o movimento entre o cliente e o servidor, e enquanto os dados estão em uso, garantindo que os dados sensíveis nunca aparecem como texto simples dentro do sistema de base de dados. Depois de encriptar dados, apenas aplicações de clientes ou servidores de aplicações que tenham acesso às teclas podem aceder a dados de texto simples. Para obter informações detalhadas, consulte [Sempre Encriptado (Motor de Base de Dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar Sempre Encriptado, irá criar uma aplicação de cliente em C# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos deste artigo para aprender a configurar Sempre Encriptado para uma base de dados Azure SQL. Neste artigo, aprenderá a executar as seguintes tarefas:

* Utilize o assistente sempre encriptado no SSMS para criar [teclas sempre encriptadas](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Criar uma Chave Master de [Coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Criar uma chave de encriptação de [colunas (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Crie uma tabela de bases de dados e criptografe colunas.
* Crie uma aplicação que insere, selecione e mostre dados das colunas encriptadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, você precisará:

* Uma conta e subscrição do Azure. Se não tiver um, inscreva-se para um [julgamento livre.](https://azure.microsoft.com/pricing/free-trial/)
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
* [.NET Quadro 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
* [Estúdio Visual.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

## <a name="create-a-blank-sql-database"></a>Criar uma base de dados SQL vazia

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique **em Criar um recurso** > **Data + Storage** > **SQL Database**.
3. Crie uma base de dados **em branco** chamada **Clinic** num servidor novo ou existente. Para obter instruções detalhadas sobre a criação de uma base de dados no portal Azure, consulte a sua primeira base de [dados Azure SQL](sql-database-single-database-get-started.md).

    ![Criar uma base de dados vazia](./media/sql-database-always-encrypted/create-database.png)

Vai precisar da corda de ligação mais tarde no tutorial. Depois da base de dados ser criada, vá à nova base de dados da Clínica e copie a cadeia de ligação. Pode obter o fio de ligação a qualquer momento, mas é fácil copiá-lo quando se está no portal Azure.

1. Clique em bases de >  **dados SQL****Clinic** > **Show cadeias de ligação**de base de dados .
2. Copie a cadeia de ligação para **ADO.NET**.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com o SSMS

Abra o SSMS e ligue-se ao servidor com a base de dados da Clínica.

1. Abra o SQL Server Management Studio. (Clique **em ligar** > **o motor de base** de dados para abrir a janela Connect **to Server** se não estiver aberto).
2. Introduza o nome do seu servidor e credenciais. O nome do servidor pode ser encontrado na lâmina de base de dados SQL e na cadeia de ligação que copiou anteriormente. Digite o nome completo do servidor, incluindo *database.windows.net*.

    ![Copiar a cadeia de ligação](./media/sql-database-always-encrypted/ssms-connect.png)

Se a janela **New Firewall Rule** abrir, inscreva-se no Azure e deixe o SSMS criar uma nova regra de firewall para si.

## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para reter os dados do paciente. Esta será uma tabela normal inicialmente- irá configurar a encriptação na próxima secção.

1. Expandir **bases de dados**.
2. Clique na base de dados da **Clínica** e clique em **New Consulta**.
3. Colhe o seguinte Transact-SQL (T-SQL) na **Execute** nova janela de consulta e execute-a.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Criptografe colunas (configure Sempre Encriptado)

O SSMS fornece um assistente para configurar facilmente sempre encriptado, configurando as colunas CMK, CEK e encriptadas para si.

1. Expandir**tabelas** > **clínicas** > de bases de **dados.**
2. Clique na tabela **dos Pacientes** e selecione **Colunas de Encriptação** para abrir o assistente sempre encriptado:

    ![Criptografe colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente sempre encriptado inclui as seguintes secções: **Seleção de Colunas,** **Configuração da Chave Master** (CMK), **Validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de Colunas

Clique em **Seguinte** na página **introdução** para abrir a página **de Seleção** de Colunas. Nesta página, irá selecionar quais as colunas que pretende encriptar, o tipo de encriptação e que chave de encriptação da [coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) utilizará.

Criptografe informações **de SSN** e **Data de Nascimento** para cada paciente. A coluna **SSN** usará encriptação determinística, que suporta as aparências de igualdade, adere e agrupam-se. A coluna **BirthDate** utilizará encriptação aleatória, que não suporta operações.

Desloque o tipo de **encriptação** para a coluna **SSN** para **Deterministic** e a coluna **BirthDate** para **Randomized**. Clique em **Seguinte**.

![Criptografe colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração da chave master

A página **de Configuração da Chave Master** é onde configura o seu CMK e seleciona o fornecedor de loja-chave onde o CMK será armazenado. Atualmente, pode armazenar um CMK na loja de certificados Windows, Azure Key Vault ou num módulo de segurança de hardware (HSM). Este tutorial mostra como guardar as suas chaves na loja de certificados Windows.

Verifique se a loja de **certificados Windows** está selecionada e clique **em Next**.

![Configuração da chave principal](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Validação

Pode encriptar as colunas agora ou guardar um script PowerShell para ser executado mais tarde. Para este tutorial, selecione **Proceder para terminar agora** e clique em **Next**.

### <a name="summary"></a>Resumo

Verifique se as definições estão todas corretas e clique em **Terminar** para completar a configuração para Sempre Encriptado.

![Resumo](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Verifique as ações do assistente

Depois de terminado o assistente, a sua base de dados está configurada para Sempre Encriptado. O assistente realizou as seguintes ações:

* Criei uma CMK.
* Criei um CEK.
* Configurou as colunas selecionadas para encriptação. A tabela de **pacientes** não tem dados, mas quaisquer dados existentes nas colunas selecionadas estão agora encriptados.

Pode verificar a criação das chaves em SSMS indo para a **Clinic** > **Security** > **Always Encrypted Keys**. Agora pode supor as novas teclas que o assistente gerou para si.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente que funcione com os dados encriptados

Agora que o Always Encrypted está configurado, pode construir uma aplicação que executa *inserções* e *seleciona* nas colunas encriptadas. Para executar com sucesso a aplicação da amostra, deve executá-la no mesmo computador onde executou o assistente Sempre Encriptado. Para executar a aplicação em outro computador, deve implementar os seus certificados Sempre Encriptados para o computador que executa a aplicação do cliente.  

> [!IMPORTANT]
> A sua aplicação deve utilizar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) ao passar dados de texto simples para o servidor com colunas Sempre Encriptadas. Passar valores literais sem usar objetos SqlParameter resultará numa exceção.

1. Open Visual Studio e criar uma nova aplicação de consola C#. Certifique-se de que o seu projeto está definido para **.NET Framework 4.6** ou posterior.
2. Nomeie o projeto **AlwaysEncryptedConsoleApp** e clique **em OK**.

![Nova aplicação de consola](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modifique a sua cadeia de ligação para ativar Sempre Encriptado

Esta secção explica como ativar sempre encriptado na sua cadeia de ligação de base de dados. Irá modificar a aplicação de consola que acaba de criar na próxima secção, "Aplicação de consola de amostrasempre encriptada".

Para ativar sempre encriptado, é necessário adicionar a palavra-chave de definição de **encriptação** da coluna à sua cadeia de ligação e defini-la para **Ativada**.

Pode defini-lo diretamente na cadeia de ligação, ou pode defini-lo utilizando um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação da amostra na secção seguinte mostra como utilizar o **SqlConnectionStringBuilder**.

> [!NOTE]
> Esta é a única alteração necessária numa aplicação de cliente específica da Always Encrypted. Se tiver uma aplicação existente que armazene a sua cadeia de ligação externamente (isto é, num ficheiro config), poderá ser capaz de ativar sempre encriptado sem alterar qualquer código.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar Sempre Encriptado na cadeia de ligação

Adicione a seguinte palavra-chave à sua cadeia de ligação:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com um SqlConnectionStringBuilder

O código que se segue mostra como ativar sempre encriptado, definindo o [SqlConnectionStringBuilder.ColumnCryptonSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) to [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Aplicação de consola de amostras sempre encriptada

Esta amostra demonstra como:

* Modifique a sua cadeia de ligação para ativar sempre encriptado.
* Insira os dados nas colunas encriptadas.
* Selecione um registo filtrando por um valor específico numa coluna encriptada.

Substitua o conteúdo do **Program.cs** pelo seguinte código. Substitua a cadeia de ligação para a variável de ligação globalString na linha diretamente acima do método Principal com a sua cadeia de ligação válida do portal Azure. Esta é a única mudança que precisa fazer a este código.

Execute a aplicação para ver Sempre Encriptado em ação.

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

## <a name="verify-that-the-data-is-encrypted"></a>Verifique se os dados estão encriptados

Pode verificar rapidamente que os dados reais do servidor estão encriptados consultando os dados dos **Pacientes** com SSMS. (Utilize a sua ligação atual onde a definição de encriptação da coluna ainda não está ativada.)

Execute a seguinte consulta na base de dados da Clínica.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode ver que as colunas encriptadas não contêm dados de texto simples.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)

Para utilizar o SSMS para aceder aos dados de texto simples, pode adicionar a Definição de Encriptação da **Coluna=parâmetro habilitado** à ligação.

1. No SSMS, clique no seu servidor no **Object Explorer**e, em seguida, clique em **Desligar**.
2. Clique em **ligar** > **o motor base** de dados para abrir a janela Connect to **Server** e, em seguida, clique em **Opções**.
3. Clique em parâmetros de **ligação adicionais** e na definição de encriptação da **coluna do tipo=ativada**.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Execute a seguinte consulta na base de dados da **Clínica.**

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Pode agora ver os dados de texto simples nas colunas encriptadas.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Se se conectar com SSMS (ou qualquer cliente) a partir de um computador diferente, não terá acesso às chaves de encriptação e não será capaz de desencriptar os dados.

## <a name="next-steps"></a>Passos seguintes

Depois de criar uma base de dados que utiliza Sempre Encriptado, poderá querer fazer o seguinte:

* Executa esta amostra de um computador diferente. Não terá acesso às chaves de encriptação, pelo que não terá acesso aos dados de texto simples e não funcionará com sucesso.
* [Rode e limpe as chaves.](https://msdn.microsoft.com/library/mt607048.aspx)
* [Migrar dados que já estão encriptados com Sempre Encriptado](https://msdn.microsoft.com/library/mt621539.aspx).
* [Implementar certificados sempre encriptados para outras máquinas clientes](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a secção "Disponibilizar Certificados às Aplicações e Utilizadores").

## <a name="related-information"></a>Informações relacionadas

* [Sempre Encriptado (desenvolvimento do cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [Encriptação do servidor SQL](https://msdn.microsoft.com/library/bb510663.aspx)
* [Sempre Encriptado Assistente](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog sempre encriptado](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
---
title: 'Tutorial: Conceber a sua primeira base de dados relacional na base de dados do SQL Azure com o SSMS | Documentos da Microsoft'
description: Saiba como criar a sua primeira base de dados relacional numa base de dados na base de dados do SQL Azure com o SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 3ca17ae905fff0911b58a0d336e0899ff385085c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990484"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutorial: Criar uma base de dados relacional numa base de dados na base de dados do SQL Azure com o SSMS

Base de dados SQL do Azure é um relacional da base de dados-como-serviço (DBaaS) no Microsoft Cloud (Azure). Neste tutorial, irá aprender a utilizar o portal do Azure e o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para:

> [!div class="checklist"]
> - Criar uma base de dados com o portal do Azure *
> - Configurar uma regra de firewall do IP de ao nível do servidor com o portal do Azure
> - Ligar à base de dados com o SSMS
> - Criar tabelas com o SSMS
> - Carregar dados em massa com o BCP
> - Consultar dados com o SSMS

* Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> Para efeitos deste tutorial, estamos a utilizar uma base de dados. Também poderia usar um banco de dados agrupado num conjunto elástico ou uma base de dados de instância numa instância gerida. Conectividade a uma instância gerida, veja estes guias de introdução de instância gerida: [Quickstart: Configurar a VM do Azure para ligar a um Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vm.md) e [início rápido: Configurar uma ligação de ponto a site para uma instância de gerida de base de dados do Azure SQL no local](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de que instalou:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (versão mais recente)
- [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (versão mais recente)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Uma base de dados na base de dados do Azure SQL é criado com um conjunto definido de recursos de computação e armazenamento. A base de dados é criada dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e é gerido através de um [servidor de base de dados](sql-database-servers.md).

Siga estes passos para criar uma base de dados em branco.

1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha os **base de dados SQL** de formulário com as informações seguintes, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *yourDatabase* | Para nomes de base de dados válida, veja [identificadores de bases de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Subscrição** | *yourSubscription*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *yourResourceGroup* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Selecionar origem** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique em **servidor** para utilizar um servidor de base de dados existente ou criar e configurar um novo servidor de base de dados. Selecione um servidor existente ou clique em **criar um novo servidor** e preencha a **novo servidor** formulário com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para nomes de início de sessão válido, veja [identificadores de bases de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | A palavra-passe tem de ter, pelo menos, oito carateres e tem de utilizar carateres de três das seguintes categorias: carateres maiúsculos, carateres minúsculos, números e carateres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Pode explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada escalão de serviço.

    Depois de selecionar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **aplicar**.

7. Introduza um **agrupamento** para a base de dados em branco (para este tutorial, utilize o valor predefinido). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que concluiu o **base de dados SQL** de formulário, clique em **criar** para aprovisionar a base de dados. Este passo pode demorar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall do IP ao nível do servidor

O serviço de base de dados SQL cria uma firewall do IP ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas estabeleçam uma ligação ao servidor e a quaisquer bases de dados no servidor, a menos que uma regra de firewall permite que seus IP através da firewall. Para ativar a conectividade externa à sua base de dados individual, primeiro tem de adicionar uma regra de firewall do IP para o seu endereço IP (ou o intervalo de endereços IP). Siga estes passos para criar uma [regra de firewall IP de ao nível do servidor de base de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço de base de dados SQL comunica através da porta 1433. Se estiver a tentar ligar a este serviço a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não é possível ligar à base de dados único, a menos que o administrador abra a porta 1433.

1. Depois de concluída a implementação, clique em **bases de dados SQL** no menu do lado esquerdo e clique em *yourDatabase* sobre o **bases de dados SQL** página. Abre a página de descrição geral da base de dados, mostrando a completamente qualificado **nome do servidor** (tal como *yourserver.database.windows.net*) e oferece opções para configuração adicional.

2. Copie este nome de servidor completamente qualificado para utilizar para ligar ao servidor e bases de dados do SQL Server Management Studio.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

3. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall do IP ao nível do servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Clique em **Adicionar IP de cliente** na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall do IP. Uma regra de firewall do IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Guardar**. Uma regra de firewall do IP ao nível do servidor é criada para o seu endereço IP atual abrindo a porta 1433 no servidor de base de dados SQL.

6. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Agora pode transmitir o seu endereço IP através da firewall do IP. Pode agora ligar à base de dados único com SQL Server Management Studio ou outra ferramenta à sua escolha. Certifique-se de que utilizar a conta de administrador de servidor que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall do IP de base de dados SQL está ativado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

## <a name="connect-to-the-database"></a>Ligar à base de dados

Uso [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma ligação à base de dados única.

1. Abra o SQL Server Management Studio.
2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo de servidor** | Motor de base de dados | Este valor é preciso. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Por exemplo, *yourserver.database.windows.net*. |
   | **Autenticação** | Autenticação do SQL Server | Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Início de sessão** | A conta de administrador do servidor | A conta que especificou quando criou o servidor. |
   | **Palavra-passe** | A palavra-passe da sua conta de administrador do servidor | A palavra-passe que especificou quando criou o servidor. |

   ![ligar ao servidor](./media/sql-database-design-first-database/connect.png)

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na **ligar à base de dados** , digite *yourDatabase* para ligar a esta base de dados.

    ![ligar à base de dados no servidor](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Clique em **Ligar**. O **Object Explorer** janela é aberta no SSMS.

5. Na **Object Explorer**, expanda **bases de dados** e, em seguida, expanda *yourDatabase* para ver os objetos na base de dados de exemplo.

   ![objetos da base de dados](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Criar tabelas na base de dados

Crie um esquema de base de dados com quatro tabelas para um sistema de gestão de estudantes para universidades que utilizam o [Transact-SQL](/sql/t-sql/language-reference):

- Pessoa
- Curso
- Estudante
- Crédito

O diagrama seguinte mostra como estas tabelas estão relacionadas entre si. Algumas destas tabelas referenciam colunas noutras tabelas. Por exemplo, o *estudante* referências de tabela a *PersonId* coluna do *pessoa* tabela. Estude o diagrama para compreender a forma como as tabelas neste tutorial estão relacionadas entre si. Para uma visão detalhada de como criar tabelas de base de dados efetivas, veja [Criar tabelas de base de dados efetivas](https://msdn.microsoft.com/library/cc505842.aspx). Para obter informações sobre como selecionar tipos de dados, veja [Tipos de dados](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Também pode utilizar o [estruturador de tabelas no SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) para criar e estruturar as suas tabelas.

![Relações de tabelas](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Na **Object Explorer**, clique com botão direito *yourDatabase* e selecione **nova consulta**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.

2. Na janela da consulta, execute a consulta seguinte para criar quatro tabelas na base de dados:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Criar tabelas](./media/sql-database-design-first-database/create-tables.png)

3. Expanda a **tabelas** no nó *yourDatabase* no **Object Explorer** para ver as tabelas que criou.

   ![ssms tables-created](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas

1. Criar uma pasta denominada *sampleData* na pasta transferências para armazenar dados de exemplo para a base de dados.

2. Com o botão direito as ligações seguintes e guarde-as para o *sampleData* pasta.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra uma janela de linha de comandos e navegue para o *sampleData* pasta.

4. Execute os seguintes comandos para inserir dados de exemplo nas tabelas ao substituir os valores para *servidor*, *base de dados*, *utilizador*, e *depalavra-passe* com os valores para o seu ambiente.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Carregou dados de exemplo para as tabelas que criou anteriormente.

## <a name="query-data"></a>Consultar dados

Execute as seguintes consultas para obter informações das tabelas de base de dados. Ver [consultas SQL escrever](https://technet.microsoft.com/library/bb264565.aspx) para saber mais sobre como escrever consultas SQL. A primeira consulta associa as quatro tabelas para localizar os estudantes ensinados por "Dominick Pope", que tem uma classificação superior a 75%. A segunda consulta associa as quatro tabelas e localiza os cursos em que "Noe Coleman se" inscreveu.

1. Numa janela de consulta do SQL Server Management Studio, execute a seguinte consulta:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. Na janela de consulta, execute a seguinte consulta:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu muitas tarefas de base de dados básica. Aprendeu a:

> [!div class="checklist"]
> - Criar uma base de dados
> - Configurar uma regra de firewall do IP ao nível do servidor
> - Ligar à base de dados com o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Criar tabelas
> - Carregar dados em massa
> - Consultar os dados

Avance para o tutorial seguinte para aprender a criar uma base de dados com o Visual Studio e C#.

> [!div class="nextstepaction"]
> [Criar uma base de dados relacional numa base de dados na base de dados do Azure SQL C# e o ADO.NET](sql-database-design-first-database-csharp.md)

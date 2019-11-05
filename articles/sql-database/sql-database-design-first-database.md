---
title: 'Tutorial: criar seu primeiro banco de dados relacional no banco de dados SQL do Azure usando o SSMS | Microsoft Docs'
description: Aprenda a projetar seu primeiro banco de dados relacional em um único banco de dados no banco de dados SQL do Azure usando SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: 18e7e75b259475b9e360dc3441ed83ccb577e557
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492041"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutorial: criar um banco de dados relacional em um banco de dados individual no banco de dados SQL do Azure usando o SSMS

O banco de dados SQL do Azure é um DBaaS (banco de dados como serviço) relacional no Microsoft Cloud (Azure). Neste tutorial, irá aprender a utilizar o portal do Azure e o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para:

> [!div class="checklist"]
> - Criar um banco de dados individual usando o portal do Azure *
> - Configurar uma regra de firewall de IP no nível de servidor usando o portal do Azure
> - Ligar à base de dados com o SSMS
> - Criar tabelas com o SSMS
> - Carregar dados em massa com o BCP
> - Consultar dados com o SSMS

\* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O módulo Microsoft Learn a seguir ajuda você a aprender gratuitamente como [desenvolver e configurar um aplicativo ASP.NET que consulta um banco de dados SQL do Azure](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de um banco de dados simples.
> [!NOTE]
> Para fins deste tutorial, estamos usando um único banco de dados. Você também pode usar um banco de dados em pool em um pool elástico ou um banco de dados de instância em uma instância gerenciada. Para obter conectividade com uma instância gerenciada, consulte estes guias de início rápido da instância gerenciada: [início rápido: configurar a VM do Azure para se conectar a um instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-configure-vm.md) e [início rápido: configurar uma conexão ponto a site com um banco de dados SQL do Azure Instância Gerenciada do local](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você instalou o:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (versão mais recente)
- [Bcp e sqlcmd](https://www.microsoft.com/download/details.aspx?id=36433) (versão mais recente)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Um banco de dados individual no banco de dados SQL do Azure é criado com um conjunto definido de recursos de computação e armazenamento. O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e é gerenciado usando um [servidor de banco de dados](sql-database-servers.md).

Siga estas etapas para criar um banco de dados individual em branco.

1. No menu portal do Azure ou na **Home** Page do, selecione **criar um recurso**.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do **banco de dados SQL** com as seguintes informações, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *yourDatabase* | Para obter nomes de banco de dados válidos, consulte [identificadores de banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Subscrição** | *yourSubscription*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *Seugruporecursos* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Selecionar origem** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique em **servidor** para usar um servidor de banco de dados existente ou criar e configurar um novo servidor de banco de dados. Selecione um servidor existente ou clique em **criar um novo servidor** e preencha o formulário **novo servidor** com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/naming-conventions) (Atribuição de nomes de regras e restrições). |
    | **Início de sessão de administrador do servidor** | Qualquer nome válido | Para obter nomes de logon válidos, consulte [identificadores de banco de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | Sua senha deve ter pelo menos oito caracteres e deve usar caracteres de três das seguintes categorias: caracteres maiúsculos, caracteres minúsculos, números e caracteres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Você pode explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada camada de serviço.

    Depois de selecionar a camada de serviço, o número de DTUs ou vCores e a quantidade de armazenamento, clique em **aplicar**.

7. Insira um **agrupamento** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que você concluiu o formulário do **banco de dados SQL** , clique em **criar** para provisionar o banco de dados individual. Esta etapa pode levar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall de IP no nível de servidor

O serviço do banco de dados SQL cria um firewall IP no nível do servidor. Esse Firewall impede que aplicativos e ferramentas externos se conectem ao servidor e a qualquer banco de dados no servidor, a menos que uma regra de firewall permita seu IP por meio do firewall. Para habilitar a conectividade externa ao seu banco de dados individual, você deve primeiro adicionar uma regra de firewall IP para seu endereço IP (ou intervalo de endereços IP). Siga estas etapas para criar uma [regra de firewall de IP no nível de servidor do banco de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço do banco de dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar a esse serviço de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 pode não ser permitido pelo firewall da sua rede. Nesse caso, você não poderá se conectar ao banco de dados individual, a menos que o administrador Abra a porta 1433.

1. Após a conclusão da implantação, selecione **bancos de dados SQL** no menu portal do Azure ou procure e selecione bancos de *dados SQL* em qualquer página.  

1. Selecione *yourDatabase* na página **bancos de dados SQL** . A página Visão geral do seu banco de dados é aberta, mostrando o nome totalmente qualificado do **servidor** (como `contosodatabaseserver01.database.windows.net`) e fornece opções para configuração adicional.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

1. Copie esse nome de servidor totalmente qualificado para usar para se conectar ao seu servidor e bancos de dados do SQL Server Management Studio.

1. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall de IP de nível de servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP atual a uma nova regra de firewall de IP. Uma regra de firewall IP pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

1. Clique em **Guardar**. Uma regra de firewall de IP no nível de servidor é criada para o endereço IP atual abrindo a porta 1433 no servidor do banco de dados SQL.

1. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

Seu endereço IP agora pode passar pelo firewall de IP. Agora você pode se conectar ao seu banco de dados individual usando SQL Server Management Studio ou outra ferramenta de sua escolha. Certifique-se de usar a conta de administrador do servidor que você criou anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso por meio do firewall de IP do banco de dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

## <a name="connect-to-the-database"></a>Ligue-se à base de dados

Use [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com o banco de dados individual.

1. Abra o SQL Server Management Studio.
2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo de servidor** | Motor de base de dados | Este valor é preciso. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Por exemplo, *yourserver.Database.Windows.net*. |
   | **Autenticação** | Autenticação do SQL Server | A autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Início de sessão** | A conta de administrador do servidor | A conta que especificou quando criou o servidor. |
   | **Palavra-passe** | A palavra-passe da sua conta de administrador do servidor | A senha que você especificou quando criou o servidor. |

   ![ligar ao servidor](./media/sql-database-design-first-database/connect.png)

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na seção **conectar ao banco de dados** , insira *yourDatabase* para se conectar a este banco de dados.

    ![ligar à base de dados no servidor](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Clique em **Ligar**. A janela pesquisador de **objetos** é aberta no SSMS.

5. No Pesquisador de **objetos**, expanda **bancos** de dados e, em seguida, expanda *yourDatabase* para exibir os objetos no banco de dados de exemplo.

   ![objetos da base de dados](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Criar tabelas em seu banco de dados

Crie um esquema de base de dados com quatro tabelas para um sistema de gestão de estudantes para universidades que utilizam o [Transact-SQL](/sql/t-sql/language-reference):

- Pessoa
- Curso
- Estudante
- Crédito

O diagrama seguinte mostra como estas tabelas estão relacionadas entre si. Algumas destas tabelas referenciam colunas noutras tabelas. Por exemplo, a tabela *Student* faz referência à coluna *PersonID* da tabela *Person* . Estude o diagrama para compreender a forma como as tabelas neste tutorial estão relacionadas entre si. Para uma visão detalhada de como criar tabelas de base de dados efetivas, veja [Criar tabelas de base de dados efetivas](https://msdn.microsoft.com/library/cc505842.aspx). Para obter informações sobre como selecionar tipos de dados, veja [Tipos de dados](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Também pode utilizar o [estruturador de tabelas no SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) para criar e estruturar as suas tabelas.

![Relações de tabelas](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. No Pesquisador de **objetos**, clique com o botão direito do mouse em *YourDatabase* e selecione **nova consulta**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.

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

3. Expanda o nó **tabelas** em *yourDatabase* no Pesquisador de **objetos** para ver as tabelas que você criou.

   ![ssms tables-created](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas

1. Crie uma pasta chamada *SampleData* em sua pasta downloads para armazenar dados de exemplo para seu banco de dado.

2. Clique com o botão direito do mouse nos links a seguir e salve-os na pasta *SampleData* .

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra uma janela de prompt de comando e navegue até a pasta *SampleData* .

4. Execute os comandos a seguir para inserir dados de exemplo nas tabelas que substituem os valores do *servidor, do* *banco*de dado, do *usuário*e da *senha* pelos valores do seu ambiente.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Carregou dados de exemplo para as tabelas que criou anteriormente.

## <a name="query-data"></a>Consultar dados

Execute as seguintes consultas para obter informações das tabelas de base de dados. Consulte [escrever consultas SQL](https://technet.microsoft.com/library/bb264565.aspx) para saber mais sobre como escrever consultas SQL. A primeira consulta une todas as quatro tabelas para localizar os alunos ensinados por ' Dominick Papa ' que têm uma taxa superior a 75%. A segunda consulta une todas as quatro tabelas e localiza os cursos nos quais ' noe Coleman ' já se inscreveu.

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

2. Em uma janela de consulta, execute a seguinte consulta:

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu muitas tarefas básicas de banco de dados. Aprendeu a:

> [!div class="checklist"]
> - Criar um banco de dados individual
> - Configurar uma regra de firewall de IP no nível de servidor
> - Ligar à base de dados com o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Criar tabelas
> - Carregar dados em massa
> - Consultar os dados

Avance para o tutorial seguinte para aprender a criar uma base de dados com o Visual Studio e C#.

> [!div class="nextstepaction"]
> [Criar um banco de dados relacional em um único banco de dados C# no banco de dados SQL do Azure e ADO.net](sql-database-design-first-database-csharp.md)

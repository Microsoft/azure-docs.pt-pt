---
title: 'Tutorial: Desenhe a sua primeira base de dados relacional usando SSMS'
description: Aprenda a desenhar a sua primeira base de dados relacional numa única base de dados em Azure SQL Database utilizando o SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: 9764c4bc794eb8d133270b762fa2bca30a056fea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75459623"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Tutorial: Conceber uma base de dados relacional numa única base de dados dentro da Base de Dados Azure SQL utilizando SSMS

A base de dados Azure SQL é uma base de dados relacional como um serviço (DBaaS) no Microsoft Cloud (Azure). Neste tutorial, irá aprender a utilizar o portal do Azure e o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) para:

> [!div class="checklist"]
> - Criar uma única base de dados utilizando o portal Azure*
> - Configurar uma regra de firewall IP ao nível do servidor utilizando o portal Azure
> - Ligar à base de dados com o SSMS
> - Criar tabelas com o SSMS
> - Carregar dados em massa com o BCP
> - Dados de consulta com SSMS

*Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!TIP]
> O seguinte módulo Microsoft Learn ajuda-o a aprender gratuitamente como [desenvolver e configurar uma aplicação ASP.NET que questiona uma Base de Dados Azure SQL](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), incluindo a criação de uma simples base de dados.
> [!NOTE]
> Para efeitos deste tutorial, estamos a usar uma única base de dados. Também pode utilizar uma base de dados agrinada numa piscina elástica ou numa base de dados de instâncias num caso gerido. Para a conectividade com um caso gerido, consulte estes quickstarts de instância geridos: [Quickstart: Configure Azure VM para ligar a uma Instância gerida](sql-database-managed-instance-configure-vm.md) por base de dados Azure SQL e [Quickstart: Configure uma ligação ponto-a-local a uma base de dados Azure SQL Gerida instância a partir do local](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, certifique-se de que instalou:

- [Estúdio de Gestão de Servidores SQL](https://msdn.microsoft.com/library/ms174173.aspx) (versão mais recente)
- [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (versão mais recente)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Criar uma base de dados única em branco

Uma única base de dados na Base de Dados Azure SQL é criada com um conjunto definido de recursos de computação e armazenamento. A base de dados é criada dentro de um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) e é gerida através de um servidor de base de [dados](sql-database-servers.md).

Siga estes passos para criar uma base de dados única em branco.

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
2. Na página **Nova**, selecione **Bases de Dados** na secção de Microsoft Azure Marketplace e, em seguida, clique em **Base de Dados SQL** na secção **Em Destaque**.

   ![create empty-database](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário de Base de **Dados SQL** com as seguintes informações, conforme mostrado na imagem anterior:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome da base de dados** | *sua Base de Dados* | Para obter nomes de base de dados [válidos, consulte os identificadores da Base de Dados](/sql/relational-databases/databases/database-identifiers). |
    | **Subscrição** | *sua Subscrição*  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *seuGrupo de Recursos* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
    | **Selecione fonte** | Base de dados vazia | Especifica que deve ser criada uma base de dados vazia. |

4. Clique no **Server** para utilizar um servidor de base de dados existente ou criar e configurar um novo servidor de base de dados. Selecione um servidor existente ou clique **em Criar um novo servidor** e preencher o formulário do Novo **servidor** com as seguintes informações:

    | Definição       | Valor sugerido | Descrição |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nome do servidor** | Qualquer nome globalmente exclusivo | Para nomes de servidores válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
    | **Login de administrador do servidor** | Qualquer nome válido | Para obter nomes válidos de login, consulte [identificadores de base de dados](/sql/relational-databases/databases/database-identifiers). |
    | **Palavra-passe** | Qualquer palavra-passe válida | A sua palavra-passe deve ter pelo menos oito caracteres e deve utilizar caracteres de três das seguintes categorias: caracteres maiúsculos, caracteres minúsculos, números e caracteres não alfanuméricos. |
    | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |

    ![criar servidor de base de dados](./media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.
6. Clique em **Escalão de preço** para especificar o escalão de serviço, o número de DTUs ou vCores e a quantidade de armazenamento. Poderá explorar as opções para o número de DTUs/vCores e armazenamento que está disponível para cada nível de serviço.

    Depois de selecionar o nível de serviço, o número de DTUs ou vCores, e a quantidade de armazenamento, clique **em Aplicar**.

7. Introduza uma **Colagem** para a base de dados em branco (para este tutorial, utilize o valor predefinido). Para obter mais informações sobre agrupamentos, veja [Agrupamentos](/sql/t-sql/statements/collations)

8. Agora que completou o formulário de Base de **Dados SQL,** clique em **Criar** para fornecer a base de dados única. Este passo pode demorar alguns minutos.

9. Na barra de ferramentas, clique em **Notificações** para monitorizar o processo de implementação.

   ![notificação](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Criar uma regra de firewall IP ao nível do servidor

O serviço de base de dados SQL cria uma firewall IP ao nível do servidor. Esta firewall impede que aplicações e ferramentas externas se conectem ao servidor e a quaisquer bases de dados no servidor, a menos que uma regra de firewall permita o seu IP através da firewall. Para permitir a conectividade externa à sua única base de dados, tem primeiro de adicionar uma regra de firewall IP para o seu endereço IP (ou intervalo de endereço IP). Siga estes passos para criar uma regra de firewall IP de nível de servidor de base [de dados SQL](sql-database-firewall-configure.md).

> [!IMPORTANT]
> O serviço de base de dados SQL comunica sobre a porta 1433. Se estiver a tentar ligar-se a este serviço a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não poderá ligar-se à sua única base de dados a não ser que o seu administrador abra a porta 1433.

1. Após a implementação estar concluída, selecione bases de **dados SQL** do menu do portal Azure ou procure e selecione bases de *dados SQL* de qualquer página.  

1. *Selecione a sua Base de Dados* na página de bases de dados **SQL.** A página de visão geral da sua base de dados `contosodatabaseserver01.database.windows.net`abre-se, mostrando-lhe o nome do **Servidor** totalmente qualificado (como) e fornece opções para uma configuração posterior.

   ![nome do servidor](./media/sql-database-design-first-database/server-name.png)

1. Copie este nome de servidor totalmente qualificado para utilização para ligar ao seu servidor e bases de dados do Estúdio de Gestão de Servidores SQL.

1. Clique em **Definir firewall do servidor** na barra de ferramentas. É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

   ![regra de firewall IP de nível servidor](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Clique em **Adicionar IP** do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall IP. Uma regra de firewall IP pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP.

1. Clique em **Guardar**. Uma regra de firewall IP de nível de servidor foi criada para a sua porta de abertura de endereço IP atual 1433 no servidor Base de Dados SQL.

1. Clique em **OK** e, em seguida, feche a página **Definições da firewall**.

O seu endereço IP pode agora passar pela firewall IP. Agora pode ligar-se à sua única base de dados utilizando o SQL Server Management Studio ou outra ferramenta à sua escolha. Certifique-se de que utiliza a conta de administração do servidor que criou anteriormente.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall IP da Base de Dados SQL está ativado para todos os serviços Azure. Clique em **DESATIVAR** nesta página para desativar todos os serviços do Azure.

## <a name="connect-to-the-database"></a>Ligue-se à base de dados

Utilize o [Estúdio de Gestão de Servidores SQL](/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma ligação à sua única base de dados.

1. Abra o SQL Server Management Studio.
2. Na caixa de dialogo **Ligar ao Servidor**, introduza as seguintes informações:

   | Definição       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Tipo de servidor** | Motor de base de dados | Este valor é preciso. |
   | **Nome do servidor** | O nome de servidor completamente qualificado | Por exemplo, *yourserver.database.windows.net.* |
   | **Autenticação** | Autenticação do SQL Server | A autenticação SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | **Início de sessão** | A conta de administrador do servidor | A conta que especificou quando criou o servidor. |
   | **Palavra-passe** | A palavra-passe da sua conta de administrador do servidor | A palavra-passe que especificou quando criou o servidor. |

   ![ligar ao servidor](./media/sql-database-design-first-database/connect.png)

3. Clique em **Opções** na caixa de diálogo **Ligar ao servidor**. Na secção **Connect to database,** introduza *a sua Base de Dados* para ligar a esta base de dados.

    ![ligar à base de dados no servidor](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Clique em **Ligar**. A janela **do Object Explorer** abre-se em SSMS.

5. No **Object Explorer,** expanda **as Bases de Dados** e, em seguida, expanda a sua Base de *Dados* para visualizar os objetos na base de dados da amostra.

   ![objetos da base de dados](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Crie tabelas na sua base de dados

Crie um esquema de base de dados com quatro tabelas para um sistema de gestão de estudantes para universidades que utilizam o [Transact-SQL](/sql/t-sql/language-reference):

- Pessoa
- Curso
- Estudante
- Crédito

O diagrama seguinte mostra como estas tabelas estão relacionadas entre si. Algumas destas tabelas referenciam colunas noutras tabelas. Por exemplo, a tabela *Do Estudante* refere a coluna *PessoaId* da tabela *Pessoa.* Estude o diagrama para compreender a forma como as tabelas neste tutorial estão relacionadas entre si. Para uma visão detalhada de como criar tabelas de base de dados efetivas, veja [Criar tabelas de base de dados efetivas](https://msdn.microsoft.com/library/cc505842.aspx). Para obter informações sobre como selecionar tipos de dados, veja [Tipos de dados](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Também pode utilizar o [estruturador de tabelas no SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) para criar e estruturar as suas tabelas.

![Relações de tabelas](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. No **Object Explorer,** clique à direita *na base* de dados e selecione **New Query**. É aberta uma janela de consulta em branco que está ligada à sua base de dados.

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

3. Expanda o nó **tabela** sintetizado sob a *sua Base de Dados* no Explorador de **Objetos** para ver as tabelas que criou.

   ![ssms tables-created](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Carregar dados para as tabelas

1. Crie uma pasta chamada *sampleData* na pasta Downloads para armazenar dados de amostra para a sua base de dados.

2. Clique na direita nos seguintes links e guarde-os na pasta *SampleData.*

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra uma janela de solicitação de comando e navegue para a pasta *sampleData.*

4. Execute os seguintes comandos para inserir os dados da amostra nas tabelas substituindo os valores para *servidor,* base de *dados,* *utilizador*e *palavra-passe* pelos valores para o seu ambiente.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Carregou dados de exemplo para as tabelas que criou anteriormente.

## <a name="query-data"></a>Consultar dados

Execute as seguintes consultas para obter informações das tabelas de base de dados. Consulte [as consultas SQL](https://technet.microsoft.com/library/bb264565.aspx) para saber mais sobre a escrita de consultas SQL. A primeira consulta junta-se às quatro mesas para encontrar os alunos ensinados por 'Dominick Pope' que têm uma nota superior a 75%. A segunda consulta junta-se às quatro mesas e encontra os cursos em que 'Noe Coleman' já se matriculou.

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

2. Numa janela de consulta, execute a seguinte consulta:

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

Neste tutorial, aprendeu muitas tarefas básicas de base de dados. Aprendeu a:

> [!div class="checklist"]
> - Criar uma única base de dados
> - Configurar uma regra de firewall IP de nível de servidor
> - Ligar à base de dados com o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)
> - Criar tabelas
> - Carregar dados em massa
> - Consultar os dados

Avance para o tutorial seguinte para aprender a criar uma base de dados com o Visual Studio e C#.

> [!div class="nextstepaction"]
> [Desenhe uma base de dados relacional numa única base de dados dentro da Base de Dados Azure SQL C# e ADO.NET](sql-database-design-first-database-csharp.md)

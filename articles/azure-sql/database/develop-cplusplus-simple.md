---
title: Ligue-se à Base de Dados SQL utilizando C e C++
description: Utilize o código da amostra neste início rápido para construir uma aplicação moderna com C++ e apoiada por uma poderosa base de dados relacional na nuvem com base de dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 62e3eb73b165a190e9234470471bd699141e8a5f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050493"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ligue-se à Base de Dados SQL utilizando C e C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este post destina-se a desenvolvedores C e C++ que tentam ligar-se à Base de Dados Azure SQL. É dividido em secções para que possa saltar para a secção que melhor capta o seu interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o tutorial C/C++

Certifique-se de que tem os itens seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Estúdio Visual.](https://www.visualstudio.com/downloads/) Tem de instalar os componentes linguísticos C++ para construir e executar esta amostra.
* [Desenvolvimento visual do Estúdio Linux.](https://docs.microsoft.com/cpp/linux/?view=vs-2019) Se estiver a desenvolver-se no Linux, também deve instalar a extensão Visual Studio Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Base de Dados Azure SQL e Servidor SQL em máquinas virtuais

A Base de Dados Azure SQL foi construída no Microsoft SQL Server e foi concebida para fornecer um serviço de alta disponibilidade, desempenho e escalável. Existem muitos benefícios em utilizar o Azure SQL sobre a sua base de dados proprietária que funciona nas instalações. Com o Azure SQL não tem de instalar, configurar, manter ou gerir a sua base de dados, mas apenas o conteúdo e a estrutura da sua base de dados. Coisas típicas com as quais nos preocupamos com bases de dados como tolerância a falhas e redundância estão todas incorporadas.

O Azure tem atualmente duas opções para hospedar cargas de trabalho do servidor SQL: Base de Dados Azure SQL, base de dados como serviço e servidor SQL em Máquinas Virtuais (VM). Não entraremos em detalhes sobre as diferenças entre estes dois, exceto que a Base de Dados Azure SQL é a sua melhor aposta para novas aplicações baseadas na nuvem para tirar partido da poupança de custos e otimização de desempenho que os serviços na nuvem fornecem. Se estiver a considerar migrar ou alargar as suas aplicações no local para a nuvem, o servidor SQL na máquina virtual Azure pode funcionar melhor para si. Para manter as coisas simples para este artigo, vamos criar uma Base de Dados Azure SQL.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Tecnologias de acesso a dados: ODBC e OLE DB

A ligação à Base de Dados Azure SQL não é diferente e atualmente existem duas formas de se conectar em bases de dados: ODBC (conectividade Open Database) e OLE DB (Base de dados de ligação e incorporação de objetos). Nos últimos anos, a Microsoft tem alinhado com a [ODBC para o acesso a dados relacionais nativos.](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/) ODBC é relativamente simples, e também muito mais rápido que OLE DB. A única ressalva aqui é que a ODBC usa uma Antiga API estilo C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Passo 1: Criar a sua Base de Dados Azure SQL

Veja a [página de início](single-database-create-quickstart.md) para aprender a criar uma base de dados de amostras.  Em alternativa, pode seguir este [pequeno vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar uma Base de Dados Azure SQL utilizando o portal Azure.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Passo 2: Obter corda de ligação

Depois de ter sido disponibilizada a sua Base de Dados Azure SQL, é necessário efetuar as seguintes etapas para determinar as informações de ligação e adicionar o SEU CLIENTE IP para acesso à firewall.

No [portal Azure,](https://portal.azure.com/)vá à sua cadeia de ligação Azure SQL Database ODBC utilizando as cadeias de **ligação** de base de dados Show listadas como parte da secção de visão geral para a sua base de dados:

![Cadeia de conexão ODBC](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo da cadeia **ODBC (Inclui Node.js) [autenticação SQL].** Usamos esta corda mais tarde para ligar do nosso intérprete de linha de comando C++ ODBC. Esta cadeia fornece detalhes como o controlador, servidor e outros parâmetros de ligação à base de dados.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Passo 3: Adicione o seu IP à firewall

Vá à secção de firewall do seu servidor e adicione o seu [cliente IP à firewall usando estes passos](firewall-configure.md) para garantir que podemos estabelecer uma ligação bem sucedida:

![Adicione o seu Janela IP](./media/develop-cplusplus-simple/ip.png)

Neste ponto, configurou a sua Base de Dados Azure SQL e está pronto para se ligar a partir do seu código C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Passo 4: Ligação a partir de uma aplicação Windows C/C++

Pode ligar-se facilmente à sua Base de [Dados Azure SQL utilizando o ODBC no Windows utilizando esta amostra](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) que se constrói com o Visual Studio. A amostra implementa um intérprete de linha de comando ODBC que pode ser usado para ligar à nossa Base de Dados Azure SQL. Esta amostra retira um ficheiro de nome de origem de base de dados (DSN) como argumento de linha de comando ou a cadeia de ligação verbosa que copiámos anteriormente do portal Azure. Traga a página de propriedade para este projeto e cola a cadeia de ligação como um argumento de comando como mostrado aqui:

![Arquivo de adereços DSN](./media/develop-cplusplus-simple/props.png)

Certifique-se de que fornece os dados de autenticação certos para a sua base de dados como parte dessa cadeia de ligação à base de dados.

Lance a aplicação para construí-la. Deve ver a seguinte janela validando uma ligação bem sucedida. Pode até executar alguns comandos SQL básicos, como **criar tabela** para validar a conectividade da sua base de dados:

![Comandos SQL](./media/develop-cplusplus-simple/sqlcommands.png)

Em alternativa, pode criar um ficheiro DSN utilizando o assistente que é lançado quando não são fornecidos argumentos de comando. Recomendamos que experimente esta opção também. Pode utilizar este ficheiro DSN para automatização e proteger as definições de autenticação:

![Criar ficheiro DSN](./media/develop-cplusplus-simple/datasource.png)

Parabéns! Já se ligou com sucesso ao Azure SQL utilizando C++ e ODBC no Windows. Pode continuar a ler para fazer o mesmo para a plataforma Linux também.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Passo 5: Ligação a partir de uma aplicação Linux C/C++

Caso ainda não tenha ouvido a notícia, o Visual Studio permite-lhe agora desenvolver também a aplicação C++ Linux. Pode ler sobre este novo cenário no [visual C++ para](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) o blog Linux Development. Para construir para o Linux, precisa de uma máquina remota onde o seu distro Linux esteja a funcionar. Se não tiver uma disponível, pode configurar uma rapidamente utilizando [máquinas Virtuais Linux Azure](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para este tutorial, assumamos que tem uma distribuição Ubuntu 16.04 Linux. Os passos aqui também devem aplicar-se a Ubuntu 15.10, Red Hat 6 e Red Hat 7.

Os seguintes passos instalam as bibliotecas necessárias para a SQL e o ODBC para a sua disção:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Inicie o Visual Studio. No âmbito das Ferramentas -> Opções -> Cross Platform -> Connection Manager, adicione uma ligação à sua caixa Linux:

![Opções de Ferramentas](./media/develop-cplusplus-simple/tools.png)

Após a criação da ligação sobre o SSH, crie um modelo de projeto Vazio (Linux):

![Novo modelo de projeto](./media/develop-cplusplus-simple/template.png)

Em seguida, pode adicionar um [novo ficheiro c fonte e substituí-lo por este conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Utilizando o ODBC APIs SQLAllocHandle, SQLSetConnectAttr e SQLDriverConnect, deverá ser capaz de inicializar e estabelecer uma ligação à sua base de dados.
Tal como acontece com a amostra do Windows ODBC, é necessário substituir a chamada SQLDriverConnect com os detalhes dos parâmetros de cadeia de ligação da base de dados copiados anteriormente do portal Azure.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A última coisa a fazer antes de compilar é adicionar **odbc** como uma dependência da biblioteca:

![Adicionar o ODBC como uma biblioteca de entrada](./media/develop-cplusplus-simple/lib.png)

Para lançar a sua aplicação, traga a consola Linux a partir do menu **Debug:**

![Consola Linux](./media/develop-cplusplus-simple/linuxconsole.png)

Se a sua ligação foi bem sucedida, deve agora ver o nome atual da base de dados impresso na Consola Linux:

![Saída da janela da consola de Linux](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Completou com sucesso o tutorial e pode agora ligar-se à sua Base de Dados Azure SQL a partir de C++ nas plataformas Windows e Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Obtenha a solução tutorial Completa C/C++

Pode encontrar a solução GetStarted que contém todas as amostras deste artigo no GitHub:

* [Amostra do Windows ODBC C++](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Descarregue a amostra ODBC do Windows C++ para ligar ao Azure SQL
* [Amostra de Linux ODBC C++](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Descarregue a amostra Linux C++ ODBC para ligar ao Azure SQL

## <a name="next-steps"></a>Próximos passos

* Reveja a visão geral do desenvolvimento da base de [dados SQL](develop-overview.md)
* Mais informações sobre a [Referência da API da ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais

* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md)
* Explore todas as [capacidades da Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)

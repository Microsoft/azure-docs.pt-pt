---
title: Conectar-se ao banco de dados SQL usando C eC++
description: Use o código de exemplo neste início rápido para criar um aplicativo moderno com C++ o e apoiado por um poderoso banco de dados relacional na nuvem com o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: fb6094ec418d2b212759bddd2c4d49c7e6193849
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690698"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Conectar-se ao banco de dados SQL usando C eC++

Esta postagem destina-se a C++ C e desenvolvedores tentando se conectar ao BD SQL do Azure. Ele é dividido em seções para que você possa ir para a seção que melhor captura seu interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o CC++ /tutorial

Certifique-se de que tem os itens seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Você deve instalar os C++ componentes de idioma para compilar e executar este exemplo.
* [Desenvolvimento em Linux do Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Se estiver desenvolvendo no Linux, você também deverá instalar a extensão do Linux do Visual Studio.

## <a id="AzureSQL"></a>Banco de dados SQL do Azure e SQL Server em máquinas virtuais
O Azure SQL foi criado em Microsoft SQL Server e foi projetado para fornecer um serviço de alta disponibilidade, de alto desempenho e escalonável. Há muitos benefícios em usar SQL Azure em seu banco de dados proprietário em execução no local. Com SQL Azure você não precisa instalar, configurar, manter ou gerenciar seu banco de dados, mas apenas o conteúdo e a estrutura do banco de dados. As coisas típicas com as quais nos preocupamos com bancos de dados como tolerância a falhas e redundância são todas incorporadas.

Atualmente, o Azure tem duas opções para hospedar cargas de trabalho do SQL Server: banco de dados SQL do Azure, banco de dados como serviço e SQL Server em máquinas virtuais (VM). Não entraremos em detalhes sobre as diferenças entre essas duas, exceto pelo fato de o banco de dados SQL do Azure ser a melhor opção para novos aplicativos baseados em nuvem aproveitarem a economia de custos e a otimização de desempenho que os serviços de nuvem fornecem. Se você estiver pensando em migrar ou estender seus aplicativos locais para a nuvem, o SQL Server na máquina virtual do Azure pode funcionar melhor para você. Para manter as coisas simples para este artigo, vamos criar um banco de dados SQL do Azure.

## <a id="ODBC"></a>Tecnologias de acesso a dados: ODBC e OLE DB
Conectar-se ao banco de dados SQL do Azure não é diferente e atualmente há duas maneiras de se conectar a bancos de dados: ODBC (conectividade de Open Database) e OLE DB (vinculação de objeto e banco de dados de incorporação). Nos últimos anos, a Microsoft se alinhou com o [ODBC para acesso a dados relacionais nativos](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). O ODBC é relativamente simples e também muito mais rápido do que OLE DB. A única limitação aqui é que o ODBC usa uma antiga API de estilo C.

## <a id="Create"></a>Etapa 1: criando seu banco de dados SQL do Azure
Consulte a [página de introdução](sql-database-single-database-get-started.md) para saber como criar um banco de dados de exemplo.  Como alternativa, você pode seguir este [pequeno vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar um banco de dados SQL do Azure usando o portal do Azure.

## <a id="ConnectionString"></a>Etapa 2: obter a cadeia de conexão
Depois que o banco de dados SQL do Azure tiver sido provisionado, você precisará executar as etapas a seguir para determinar as informações de conexão e adicionar o IP do cliente para acesso ao firewall.

Em [portal do Azure](https://portal.azure.com/), vá para a cadeia de conexão ODBC do banco de dados SQL do Azure usando as **cadeias de conexão mostrar banco de dados** listadas como parte da seção de visão geral do seu banco de dados:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo da cadeia de caracteres **ODBC (inclui node. js) [autenticação SQL]** . Usamos essa cadeia de caracteres mais tarde para se C++ conectar de nosso interpretador de linha de comando ODBC. Essa cadeia de caracteres fornece detalhes como o driver, o servidor e outros parâmetros de conexão de banco de dados.

## <a id="Firewall"></a>Etapa 3: adicionar seu IP ao firewall
Vá para a seção Firewall do servidor de banco de dados e adicione o [IP do cliente ao firewall usando estas etapas](sql-database-configure-firewall-settings.md) para garantir que possamos estabelecer uma conexão bem-sucedida:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Neste ponto, você configurou seu BD SQL do Azure e está pronto para se conectar do C++ seu código.

## <a id="Windows"></a>Etapa 4: conectando de um aplicativo CC++ /Windows
Você pode se conectar facilmente ao seu [banco de BD SQL do Azure usando ODBC no Windows usando este exemplo](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) que se baseia no Visual Studio. O exemplo implementa um interpretador de linha de comando ODBC que pode ser usado para se conectar ao nosso banco de BD SQL do Azure. Este exemplo usa um arquivo de nome de origem do banco de dados (DSN) como um argumento de linha de comando ou a cadeia de conexão detalhada que copiamos anteriormente do portal do Azure. Ative a página de propriedades deste projeto e cole a cadeia de conexão como um argumento de comando, conforme mostrado aqui:

![Propsfile DSN](./media/sql-database-develop-cplusplus-simple/props.png)

Certifique-se de fornecer os detalhes de autenticação corretos para seu banco de dados como parte da cadeia de conexão do banco de dados.

Inicie o aplicativo para compilá-lo. Você deve ver a janela a seguir Validando uma conexão bem-sucedida. Você pode até mesmo executar alguns comandos básicos do SQL como **criar tabela** para validar a conectividade do banco de dados:

![Comandos SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Como alternativa, você pode criar um arquivo DSN usando o assistente que é iniciado quando nenhum argumento de comando é fornecido. Recomendamos que você experimente essa opção também. Você pode usar esse arquivo DSN para automação e proteger suas configurações de autenticação:

![Criar arquivo DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Parabéns! Agora você se conectou com êxito ao SQL do Azure C++ usando o e o ODBC no Windows. Você pode continuar lendo para fazer o mesmo para a plataforma Linux também.

## <a id="Linux"></a>Etapa 5: conectando de um aplicativo LinuxC++ C/
Caso você ainda não tenha ouvido as notícias, o Visual Studio agora também permite que C++ você desenvolva o aplicativo Linux. Você pode ler sobre esse novo cenário no blog [de C++ desenvolvimento do Visual para Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . Para compilar para o Linux, você precisa de um computador remoto no qual seu distribuição do Linux esteja em execução. Se você não tiver um disponível, poderá configurar um rapidamente usando [máquinas virtuais do Azure do Linux](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para este tutorial, vamos supor que você tenha uma distribuição do Ubuntu 16, 4 Linux configurada. As etapas aqui também devem se aplicar ao Ubuntu 15,10, Red Hat 6 e Red Hat 7.

As etapas a seguir instalam as bibliotecas necessárias para o SQL e ODBC para seu distribuição:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Inicie o Visual Studio. Em Ferramentas-> Opções-> Gerenciador de conexões > a plataforma cruzada, adicione uma conexão à sua caixa do Linux:

![Opções de ferramentas](./media/sql-database-develop-cplusplus-simple/tools.png)

Depois que a conexão via SSH for estabelecida, crie um modelo de projeto vazio (Linux):

![Novo modelo de projeto](./media/sql-database-develop-cplusplus-simple/template.png)

Em seguida, você pode adicionar um [novo arquivo de origem C e substituí-lo por esse conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Usando as APIs de ODBC SQLAllocHandle, SQLSetConnectAttr e SQLDriverConnect, você deve ser capaz de inicializar e estabelecer uma conexão com o banco de dados.
Assim como com o exemplo de ODBC do Windows, você precisa substituir a chamada SQLDriverConnect pelos detalhes dos parâmetros de cadeia de conexão de banco de dados copiados do portal do Azure anteriormente.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A última coisa a fazer antes de Compilar é adicionar o **ODBC** como uma dependência de biblioteca:

![Adicionando o ODBC como uma biblioteca de entrada](./media/sql-database-develop-cplusplus-simple/lib.png)

Para iniciar seu aplicativo, abra o console do Linux no menu **depurar** :

![Console do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Se a conexão tiver sido bem-sucedida, você verá agora o nome do banco de dados atual impresso no console do Linux:

![Saída da janela do console do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Você concluiu com êxito o tutorial e agora pode se conectar ao seu banco de BD C++ SQL do Azure em plataformas Windows e Linux.

## <a id="GetSolution"></a>Obter a solução completa deC++ C/tutorial
Você pode encontrar a solução getstarted que contém todos os exemplos neste artigo no GitHub:

* [Exemplo C++ de ODBC do Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), baixe C++ o exemplo ODBC do Windows para se conectar ao SQL do Azure
* [Exemplo C++ de ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29)do Linux, baixe C++ o exemplo ODBC do Linux para se conectar ao SQL do Azure

## <a name="next-steps"></a>Passos seguintes
* Examine a [visão geral do desenvolvimento do banco de dados SQL](sql-database-develop-overview.md)
* Mais informações sobre a [referência da API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todas as [capacidades da Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)


---
title: Ligar à base de dados SQL com C e C++ | Documentos da Microsoft
description: Utilize o código de exemplo neste início rápido para criar um moderno de aplicação com o C++ e apoiado por uma poderosa base de dados relacional na cloud com a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 00a3904bd78f3bb76266c726af28582770b23921
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316494"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Ligar à base de dados SQL com C e C++

Esta mensagem é destinada a desenvolvedores de C e C++ tentando se conectar a BD SQL do Azure. Ele é dividido em secções para que possa para a secção que captura melhor o seu interesse.

## <a name="prerequisites-for-the-cc-tutorial"></a>Pré-requisitos para o tutorial de C/C++

Certifique-se de que tem os itens seguintes:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Tem de instalar os componentes de linguagem C++ para compilar e executar este exemplo.
* [Desenvolvimento do Visual Studio Linux](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Se estiver a desenvolver no Linux, também tem de instalar a extensão do Visual Studio Linux.

## <a id="AzureSQL"></a>Base de dados SQL e SQL Server em máquinas virtuais do Azure
SQL do Azure baseia-se no Microsoft SQL Server e foi concebido para fornecer uma elevada disponibilidade, eficazes e serviço dimensionável. Há muitos benefícios a utilizar o SQL Azure durante a sua base de dados proprietário em execução no local. Com o SQL Azure não tem de instalar, configurar, manter ou gerir a sua base de dados, mas apenas o conteúdo e a estrutura da sua base de dados. Coisas típicas que nos preocupamos com bancos de dados, como a tolerância a falhas e redundância estão incorporadas.

O Azure tem atualmente duas opções para alojar cargas de trabalho do SQL server: SQL database do Azure, base de dados como um serviço e o SQL server em máquinas virtuais (VM). Não Abordaremos em detalhes sobre as diferenças entre estes dois exceto pelo fato de base de dados SQL do Azure é sua melhor opção para novas aplicações com base na cloud tirar partido das reduções de custos e otimização de desempenho que os serviços em nuvem proporcionam. Se estiver a considerar migrar ou expandir as suas aplicações no local para a cloud, o SQL server em máquinas virtuais do Azure pode funcionar melhor para. Para simplificar as coisas para este artigo, vamos criar uma base de dados SQL do Azure.

## <a id="ODBC"></a>Tecnologias de acesso de dados: ODBC e OLE DB
Ligar-se a BD SQL do Azure não é diferente e, atualmente, existem duas formas de ligar a bases de dados: ODBC (Open Database connectivity) e OLE DB (objeto de ligação e incorporar da base de dados). Nos últimos anos, a Microsoft tem alinhado com [ODBC para acesso a dados relacionais](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC é relativamente simples e também muito mais rápida do que o OLE DB. A única limitação aqui é que o ODBC a usar uma antiga API em estilo C.

## <a id="Create"></a>Passo 1:  Criar base de dados SQL do Azure
Consulte a [página de introdução](sql-database-single-database-get-started.md) para saber como criar uma base de dados de exemplo.  Em alternativa, pode seguir esta [breve vídeo de dois minutos](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) para criar uma base de dados SQL do Azure no portal do Azure.

## <a id="ConnectionString"></a>Passo 2:  Obter cadeia de ligação
Depois de ter sido aprovisionado a sua base de dados SQL do Azure, terá de realizar os passos seguintes para determinar as informações de ligação e adicione o IP de cliente para acesso de firewall.

Na [portal do Azure](https://portal.azure.com/), aceda à sua base de dados SQL do Azure cadeia de ligação de ODBC, utilizando o **Mostrar cadeias de ligação de base de dados** listado como parte da seção de visão geral da base de dados:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Copie o conteúdo dos **ODBC (inclui node. js) [autenticação do SQL]** cadeia de caracteres. Usamos essa cadeia de caracteres mais tarde para ligar a partir de nossa interpretador de linha de comandos do ODBC do C++. Esta cadeia fornece detalhes como o driver, servidor e outros parâmetros de ligação de base de dados.

## <a id="Firewall"></a>Passo 3:  Adicionar o seu IP à firewall
Vá para a secção de firewall para o seu servidor de base de dados e adicione sua [IP de cliente para o firewall utiliza estes passos](sql-database-configure-firewall-settings.md) para se certificar de que podemos estabelecer uma ligação com êxito:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

Neste ponto, tiver configurado a sua BD SQL do Azure e está pronto para ligar a partir do código C++.

## <a id="Windows"></a>Passo 4: Ligar a partir de um aplicativo do Windows. o C/C++
Pode ligar facilmente a sua [BD SQL do Azure com o ODBC no Windows com este exemplo](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) que baseia-se com o Visual Studio. O exemplo implementa um interpretador de linha de comandos do ODBC que pode ser utilizado para ligar ao nosso BD SQL do Azure. Este exemplo utiliza a um arquivo de ficheiro (DSN) do nome de origem da base de dados como um argumento da linha de comandos ou a cadeia de ligação verboso copiou anteriormente no portal do Azure. Abra a página de propriedades para este projeto e cole a cadeia de ligação como um argumento de comando, como mostrado aqui:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Certifique-se de que fornecer os detalhes de autenticação correta para sua base de dados como parte dessa cadeia de ligação de base de dados.

Inicie o aplicativo para criá-lo. Deverá ver a seguinte janela validar uma ligação com êxito. Pode até executar alguns comandos SQL básicos, como **criar tabela** para validar a conectividade da base de dados:

![Comandos SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Em alternativa, pode criar um ficheiro DSN com o assistente que é iniciado quando são fornecidos sem argumentos de comando. Recomendamos que experimente esta opção também. Pode usar esse arquivo DSN para automação e a proteger as suas definições de autenticação:

![Criar ficheiro DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Parabéns! Agora ligou com êxito para o Azure SQL com o C++ e ODBC no Windows. Pode continuar a ler este artigo para fazer o mesmo para também a plataforma Linux.

## <a id="Linux"></a>Passo 5: Ligar a partir de um aplicativo Linux C/C++
Caso ainda não conhece a notícia ainda, o Visual Studio agora permite-lhe desenvolver aplicações do Linux de C++ também. Pode ler sobre este cenário de novo no [Visual C++ para desenvolvimento no Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) blog. Para criar para Linux, tem de um computador remoto onde sua distribuição de Linux está em execução. Se não tiver uma disponível, pode definir um rapidamente usando [máquinas virtuais do Linux do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para este tutorial, vamos assuma que tem uma distribuição de Linux do Ubuntu 16.04 configurar. Os passos aqui também devem aplicar Ubuntu 15.10, Red Hat 6 e 7 do Red Hat.

Os seguintes passos instalar as bibliotecas necessárias para SQL e ODBC para sua distribuição:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Inicie o Visual Studio. Em Ferramentas -> Opções -> Cross Platform -> Gestor de ligações, adicionar uma ligação à sua caixa de Linux:

![Opções de ferramentas](./media/sql-database-develop-cplusplus-simple/tools.png)

Uma vez estabelecido ligação através de SSH, crie um modelo de projeto vazio (Linux):

![Novo modelo de projeto](./media/sql-database-develop-cplusplus-simple/template.png)

Em seguida, pode adicionar um [C novo ficheiro de origem e substituí-lo com este conteúdo](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Usando o SQLAllocHandle do ODBC APIs, SQLSetConnectAttr e SQLDriverConnect, deve ser capaz de inicializar e estabelecer uma ligação à base de dados.
Como com o exemplo de ODBC do Windows, terá de substituir a chamada de SQLDriverConnect com os detalhes de seus parâmetros de cadeia de ligação de base de dados copiados anteriormente a partir do portal do Azure.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

A última coisa a fazer antes de compilar consiste em Adicionar **odbc** como uma dependência de biblioteca:

![A adição de ODBC como uma biblioteca de entrada](./media/sql-database-develop-cplusplus-simple/lib.png)

Para iniciar a aplicação, abra a consola de Linux a partir da **depurar** menu:

![Consola do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Se a ligação foi concluída com êxito, deverá ver agora o nome de base de dados atual, impresso na consola do Linux:

![Saída da janela da consola do Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Parabéns! Concluiu com êxito o tutorial e pode agora ligar ao seu Azure SQL DB do C++ em plataformas Windows e Linux.

## <a id="GetSolution"></a>Obter a solução completa do tutorial C/C++
Pode encontrar a solução GetStarted que contém todos os exemplos neste artigo no GitHub:

* [Exemplo de ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), transfira o exemplo de ODBC de C++ do Windows para ligar ao SQL do Azure
* [Exemplo de ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), transfira o exemplo de ODBC de C++ do Linux para ligar ao SQL do Azure

## <a name="next-steps"></a>Passos Seguintes
* Reveja o [descrição geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md)
* Obter mais informações sobre o [referência da API de ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de Estrutura para Aplicações de SaaS Multi-inquilinos com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todas as [capacidades da Base de Dados SQL](https://azure.microsoft.com/services/sql-database/)


---
title: Portas para além do 1433 para a base de dados SQL | Documentos da Microsoft
description: Ligações de cliente a partir do ADO.NET para a base de dados do Azure SQL podem ignorar o proxy e interagem diretamente com a base de dados com as portas que não sejam 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: b6fbb71a827c90abd1fac58d7975ab2f7b2a5674
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55560894"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas para além do 1433 para ADO.NET 4.5

Este tópico descreve o comportamento de ligação de base de dados do Azure SQL para clientes que utilizam o ADO.NET 4.5 ou posterior. 

> [!IMPORTANT]
> Para obter informações sobre a arquitetura de conectividade, consulte [arquitetura de conectividade da base de dados do Azure SQL](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Exteriores vs dentro

Para as ligações à base de dados do Azure SQL, podemos devem primeiro solicitar se o seu programa cliente executa *fora* ou *dentro* o limite de cloud do Azure. As subsecções abordam dois cenários comuns.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* Cliente é executado no computador de secretária

A porta 1433 é a porta única que tem de estar aberta no computador de secretária que aloja a aplicação de cliente da base de dados SQL.

#### <a name="inside-client-runs-on-azure"></a>*Dentro de:* Cliente é executado no Azure

Quando o cliente é executado dentro do limite do cloud do Azure, ele usa o que podemos chamar um *rota direta* para interagir com o servidor de base de dados SQL. Após uma conexão é estabelecida, ainda mais as interações entre o cliente e a base de dados não envolvem nenhum Gateway de base de dados SQL do Azure.

A sequência é o seguinte:

1. ADO.NET 4.5 (ou posterior) inicia uma breve interação com a cloud do Azure e recebe um número de porta dinamicamente identificados.
   
   * É o número da porta dinamicamente identificados no intervalo de 11000 11999 ou 14000 14999.
2. ADO.NET, em seguida, liga para o servidor de base de dados SQL diretamente, sem middleware entre.
3. As consultas são enviadas diretamente para a base de dados e os resultados são retornados diretamente ao cliente.

Certifique-se de que a porta de intervalos de 11000 11999 e 14000-14999 no seu computador de cliente do Azure permanecem disponíveis para ADO.NET 4.5 interações de cliente com a base de dados SQL.

* Em particular, as portas no intervalo tem de ser gratuitas a qualquer bloqueadores de saída.
* Na sua VM do Azure, o **Firewall do Windows com segurança avançada** controla as definições de porta.
  
  * Pode utilizar o [interface do usuário da firewall](https://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra para que especifique a **TCP** como o protocolo, juntamente com um intervalo de portas com a sintaxe **11000 11999**.

## <a name="version-clarifications"></a>Esclarecimentos de versão
Esta secção esclarece os monikers que se referem às versões do produto. Ele também apresenta algumas emparelhamentos de versões entre produtos.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 suporta o protocolo TDS 7.3, mas não 7.4.
* ADO.NET 4.5 e posterior suporta o protocolo TDS 7.4.

#### <a name="odbc"></a>ODBC
* Microsoft SQL Server ODBC 11 ou superior

#### <a name="jdbc"></a>JDBC
* Microsoft SQL Server JDBC 4.2 ou superior (JDBC 4.0, na verdade, suporta TDS 7.4 mas não implementa "redirecionamento")


## <a name="related-links"></a>Ligações relacionadas
* ADO.NET 4.6 foi lançado no dia 20 de Julho de 2015. Um anúncio do blogue da equipa do .NET está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 foi lançado em 15 de Agosto de 2012. Um anúncio do blogue da equipa do .NET está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx). 
  * Uma mensagem de blogue sobre ADO.NET 4.5.1 está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).

* ® Controlador Microsoft ODBC 17 para SQL Server® - Windows, Linux e macOS https://www.microsoft.com/download/details.aspx?id=56567

* Ligar à base de dados SQL do Azure V12 através de redirecionamento https://blogs.msdn.microsoft.com/sqlcat/2016/09/08/connect-to-azure-sql-database-v12-via-redirection/

* [Lista de versões do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Descrição geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md)
* [Firewall de base de dados SQL do Azure](sql-database-firewall-configure.md)
* [How to: Configure firewall settings on SQL Database (Como: configurar as definições da firewall na Base de Dados SQL)](sql-database-configure-firewall-settings.md)



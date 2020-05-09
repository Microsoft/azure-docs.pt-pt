---
title: Portos além de 1433
description: As ligações com o cliente de ADO.NET à Base de Dados Azure SQL podem contornar o proxy e interagir diretamente com a base de dados utilizando portas que não 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: 34e9b40b0b6989edacd72eb23d357e2a84b6f8d8
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891082"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas para além da porta 1433 para ADO.NET 4.5

Este tópico descreve o comportamento de ligação da Base de Dados Azure SQL para clientes que usam ADO.NET 4.5 ou uma versão posterior.

> [!IMPORTANT]
> Para obter informações sobre arquitetura de conectividade, consulte a arquitetura de [conectividade Azure SQL Database.](sql-database-connectivity-architecture.md)
>

## <a name="outside-vs-inside"></a>Fora vs dentro

Para ligações à Base de Dados Azure SQL, temos primeiro de perguntar se o seu programa de clientes funciona *fora* ou *dentro* do limite da nuvem Azure. As subsecções discutem dois cenários comuns.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Lá fora:* Cliente corre no seu computador de secretária

A porta 1433 é a única porta que deve estar aberta no seu computador de secretária que acolhe a sua aplicação de cliente SQL Database.

### <a name="inside-client-runs-on-azure"></a>*Dentro:* Cliente corre no Azure

Quando o seu cliente corre dentro do limite da nuvem Azure, ele usa o que podemos chamar de *uma rota direta* para interagir com o servidor de base de dados SQL. Após a estabelecer uma ligação, outras interações entre o cliente e a base de dados não envolvem gateway de base de dados Azure SQL.

A sequência é a seguinte:

1. ADO.NET 4.5 (ou mais tarde) inicia uma breve interação com a nuvem Azure, e recebe um número de porta dinamicamente identificado.

   * O número de porta dinamicamente identificado está na gama 11000-11999.
2. ADO.NET então liga-se diretamente ao servidor de base de dados SQL, sem intermediários no meio.
3. As consultas são enviadas diretamente para a base de dados, e os resultados são devolvidos diretamente ao cliente.

Certifique-se de que as gamas de porta seleção 11000-11999 na sua máquina de cliente Azure ficam disponíveis para ADO.NET 4.5 interações com o cliente com a Base de Dados SQL.

* Em particular, as portas do intervalo devem estar isentas de quaisquer outros bloqueadores de saída.
* No seu Azure VM, o **Windows Firewall com Advanced Security** controla as definições da porta.
  
  * Pode utilizar a [interface de utilizador da firewall](https://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra para a qual especifica o protocolo **TCP** juntamente com uma gama de portas com a sintaxe como **11000-11999**.

## <a name="version-clarifications"></a>Esclarecimentos de versão

Esta secção clarifica os apelidos que se referem às versões do produto. Também enumera alguns pares de versões entre produtos.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 suporta o protocolo TDS 7.3, mas não 7.4.
* ADO.NET 4.5 e posteriormente apoia o protocolo TDS 7.4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 ou superior

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 ou superior (JDBC 4.0 suporta realmente TDS 7.4, mas não implementa "redirection")

## <a name="related-links"></a>Ligações relacionadas

* ADO.NET 4,6 foi lançado a 20 de julho de 2015. Um anúncio de blog da equipa .NET está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/)
* ADO.NET 4,5 foi lançado a 15 de agosto de 2012. Um anúncio de blog da equipa .NET está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/)
  * Uma publicação de blog sobre ADO.NET 4.5.1 está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/)

* Microsoft® ODBC Driver 17 para SQL Server® - Windows, Linux, & macOShttps://www.microsoft.com/download/details.aspx?id=56567

* Ligue-se à Base de Dados Azure SQL V12 via Redirectionhttps://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista de versão de protocolo TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Visão geral do desenvolvimento da base de dados SQL](sql-database-develop-overview.md)
* [Firewall de base de dados Azure SQL](sql-database-firewall-configure.md)
* [Como configurar as definições da firewall na Base de Dados SQL (How to: Configure firewall settings on SQL Database)](sql-database-configure-firewall-settings.md)



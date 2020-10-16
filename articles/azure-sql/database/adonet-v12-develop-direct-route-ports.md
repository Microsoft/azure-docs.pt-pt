---
title: Portos além de 1433
description: As ligações do cliente de ADO.NET a Azure SQL Database podem contornar o proxy e interagir diretamente com a base de dados utilizando portas que não 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444876"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas para além da porta 1433 para ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este tópico descreve o comportamento de ligação Azure SQL Database para clientes que usam ADO.NET 4.5 ou uma versão posterior.

> [!IMPORTANT]
> Para obter informações sobre arquitetura de conectividade, consulte [a arquitetura de conectividade Azure SQL Database](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Fora vs dentro

Para ligações à Base de Dados Azure SQL, temos primeiro de perguntar se o seu programa de clientes funciona *fora* ou *dentro* do limite da nuvem Azure. As subsecções discutem dois cenários comuns.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* O cliente funciona no seu computador de secretária

A porta 1433 é a única porta que deve estar aberta no seu computador de secretária que acolhe a sua aplicação de cliente SQL Database.

### <a name="inside-client-runs-on-azure"></a>*No interior:* Cliente corre em Azure

Quando o seu cliente corre dentro do limite da nuvem Azure, ele usa o que podemos chamar de uma *rota direta* para interagir com a Base de Dados SQL. Após a criação de uma ligação, as interações adicionais entre o cliente e a base de dados não envolvem nenhum Gateway de base de dados Azure SQL.

A sequência é a seguinte:

1. ADO.NET 4.5 (ou posterior) inicia uma breve interação com a nuvem Azure e recebe um número de porta identificado dinamicamente.

   * O número de porta identificado dinamicamente está no intervalo de 11000-11999.
2. ADO.NET liga-se diretamente à BASE de Dados SQL, sem middleware no meio.
3. As consultas são enviadas diretamente para a base de dados, e os resultados são devolvidos diretamente ao cliente.

Certifique-se de que as gamas portuárias de 11000-11999 na sua máquina cliente Azure ficam disponíveis para ADO.NET interações de clientes de 4,5 com base de dados SQL.

* Em particular, as portas do alcance devem estar isentas de quaisquer outros bloqueadores de saída.
* No seu Azure VM, o **Windows Firewall com Segurança Avançada** controla as definições da porta.
  
  * Pode utilizar a [interface de utilizador da firewall](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) para adicionar uma regra para a qual especifique o protocolo **TCP** juntamente com uma gama de portas com a sintaxe como **11000-11999**.

## <a name="version-clarifications"></a>Esclarecimentos de versão

Esta secção esclarece os nomes que se referem às versões do produto. Também enumera alguns pares de versões entre produtos.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 suporta o protocolo TDS 7.3, mas não 7.4.
* ADO.NET 4.5 e mais tarde suporta o protocolo TDS 7.4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 ou superior

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 ou superior (JDBC 4.0 realmente suporta TDS 7.4 mas não implementa "redirecionamento")

## <a name="related-links"></a>Ligações relacionadas

* ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um anúncio de blog da equipa .NET está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/)
* ADO.NET 4,5 foi lançado em 15 de agosto de 2012. Um anúncio de blog da equipa .NET está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/)
  * Uma publicação de blog sobre ADO.NET 4.5.1 está disponível [aqui.](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/)

* Microsoft ODBC Driver 17 para SQL Server https://aka.ms/downloadmsodbcsql

* Ligue-se à Base de Dados V12 do Azure SQL através da reorientação https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista de versão de protocolo TDS](https://www.freetds.org/)
* [Visão geral do desenvolvimento da base de dados SQL](develop-overview.md)
* [Firewall de base de dados Azure SQL](firewall-configure.md)

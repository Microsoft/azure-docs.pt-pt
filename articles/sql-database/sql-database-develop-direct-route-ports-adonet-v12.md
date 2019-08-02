---
title: Portas além de 1433 para o banco de dados SQL | Microsoft Docs
description: As conexões de cliente do ADO.NET para o banco de dados SQL do Azure podem ignorar o proxy e interagir diretamente com o banco de dados usando portas diferentes de 1433.
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
ms.openlocfilehash: a39cfd1981041c807a91a08c198378d238f0846e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568907"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Portas além de 1433 para ADO.NET 4,5

Este tópico descreve o comportamento de conexão do banco de dados SQL do Azure para clientes que usam o ADO.NET 4,5 ou uma versão posterior.

> [!IMPORTANT]
> Para obter informações sobre a arquitetura de conectividade, consulte [arquitetura de conectividade do banco de dados SQL do Azure](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Fora do vs Inside

Para conexões com o banco de dados SQL do Azure, primeiro devemos perguntar se o programa cliente é executado *fora* ou *dentro* do limite de nuvem do Azure. As subseções discutem dois cenários comuns.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Exterior* O cliente é executado no computador desktop

A porta 1433 é a única porta que deve ser aberta no computador desktop que hospeda o aplicativo cliente do banco de dados SQL.

### <a name="inside-client-runs-on-azure"></a>*Neste* O cliente é executado no Azure

Quando o cliente é executado dentro do limite de nuvem do Azure, ele usa o que podemos chamar de uma *rota direta* para interagir com o servidor do banco de dados SQL. Depois que uma conexão é estabelecida, outras interações entre o cliente e o banco de dados não envolvem nenhum gateway de banco de dados SQL do Azure.

A sequência é a seguinte:

1. O ADO.NET 4,5 (ou posterior) inicia uma breve interação com a nuvem do Azure e recebe um número de porta identificado dinamicamente.

   * O número da porta identificada dinamicamente está no intervalo de 11000-11999.
2. Em seguida, o ADO.NET se conecta diretamente ao servidor do banco de dados SQL, sem intermediários entre eles.
3. As consultas são enviadas diretamente ao banco de dados e os resultados são retornados diretamente ao cliente.

Verifique se os intervalos de porta de 11000-11999 no computador cliente do Azure estão disponíveis para interações de cliente do ADO.NET 4,5 com o banco de dados SQL.

* Em particular, as portas no intervalo devem ser livres de qualquer outro bloqueador de saída.
* Na sua VM do Azure, o **Firewall do Windows com segurança avançada** controla as configurações de porta.
  
  * Você pode usar a [interface do usuário do firewall](https://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra para a qual você especifica o protocolo **TCP** junto com um intervalo de portas com a sintaxe como **11000-11999**.

## <a name="version-clarifications"></a>Esclarecimentos sobre a versão

Esta seção esclarece os monikers que se referem a versões do produto. Ele também lista alguns emparelhamentos de versões entre produtos.

### <a name="adonet"></a>ADO.NET

* O ADO.NET 4,0 dá suporte ao protocolo TDS 7,3, mas não ao 7,4.
* O ADO.NET 4,5 e posterior dá suporte ao protocolo TDS 7,4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 ou superior

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 ou superior (o JDBC 4,0 realmente dá suporte ao TDS 7,4, mas não implementa "redirecionamento")

## <a name="related-links"></a>Ligações relacionadas

* O ADO.NET 4,6 foi lançado em 20 de julho de 2015. Um comunicado de blog da equipe do .NET está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* O ADO.NET 4,5 foi lançado em 15 de agosto de 2012. Um comunicado de blog da equipe do .NET está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Uma postagem de blog sobre o ADO.NET 4.5.1 está disponível [aqui](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 for SQL Server®-Windows, Linux & macOS https://www.microsoft.com/download/details.aspx?id=56567

* Conectar-se ao banco de dados SQL do Azure V12 via redirecionamento https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lista de versões do protocolo TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Visão geral do desenvolvimento do banco de dados SQL](sql-database-develop-overview.md)
* [Firewall do banco de dados SQL do Azure](sql-database-firewall-configure.md)
* [How to: Configure firewall settings on SQL Database (Como: configurar as definições da firewall na Base de Dados SQL)](sql-database-configure-firewall-settings.md)



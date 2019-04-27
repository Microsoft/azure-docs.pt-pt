---
title: Resolver problemas comuns de ligação para a Base de Dados SQL do Azure
description: Passos para identificar e resolver erros comuns de ligação de base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d278fd6ed06b58db052154e632e565de36853e77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331439"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Resolver problemas de ligação à base de dados do Azure SQL

Quando a ligação à base de dados do Azure SQL falhar, receberá [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que o ajuda a resolver problemas de conectividade da base de dados do Azure SQL. Ele introduz [as causas comuns](#cause) dos problemas de ligação, recomenda [uma ferramenta de resolução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda a identidade do problema e fornece passos de resolução de problemas para resolver [transitório erros](#troubleshoot-transient-errors) e [persistentes ou não transitórias erros](#troubleshoot-persistent-errors). 

Se encontrar os problemas de ligação, tente os passos de resolução de problemas que são descritos neste artigo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa

Problemas de ligação podem ser causados por qualquer um dos seguintes:

* Falha ao aplicar as práticas recomendadas e diretrizes de design durante o processo de design do aplicativo.  Ver [descrição geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md) para começar a utilizar.
* Reconfiguração de base de dados SQL do Azure
* Definições de firewall
* Tempo limite de ligação
* Informações de início de sessão incorretas
* Limite máximo foi atingido no alguns recursos de base de dados do Azure SQL

Em geral, os problemas de ligação à base de dados do Azure SQL podem ser classificados da seguinte forma:

* [Erros transitórios (com vida curta ou intermitentes)](#troubleshoot-transient-errors)
* [Erros de persistentes ou não transitórias (erros que recorrer regularmente)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Experimente a resolução de problemas para problemas de conectividade da base de dados do Azure SQL

Se ocorrer um erro de ligação específica, tente [essa ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que irá ajudá-lo a rapidamente identidade e resolver o problema.

## <a name="troubleshoot-transient-errors"></a>Resolver problemas de erros transitórios

Quando um aplicativo se conecta a uma base de dados SQL do Azure, recebe a seguinte mensagem de erro:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Esta mensagem de erro é normalmente transitória (com vida curta).
> 
> 

Este erro ocorre quando a base de dados está a ser movido (ou reconfiguradas) e seu aplicativo perde a respetiva ligação à base de dados. Os eventos de reconfiguração de base de dados ocorrem devido a um evento planeado (por exemplo, uma atualização de software) ou um evento não planeado (por exemplo, uma falha de processo ou balanceamento de carga). A maioria dos eventos de reconfiguração são geralmente curta duração e devem ser concluídos em menos de 60 segundos no máximo. No entanto, esses eventos, ocasionalmente, podem demorar mais tempo a concluir, por exemplo, quando uma transação grande faz com que uma recuperação de longa execução.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas de conectividade transitório

1. Verifique os [Dashboard de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer falhas conhecidas que ocorreram durante o tempo durante o qual o erro foi comunicado pelo aplicativo.
2. Aplicações que se ligam a um serviço cloud como base de dados do Azure SQL deve esperar que os eventos de reconfiguração periódica e implementar novamente a lógica para lidar com esses erros, em vez de analisar como erros de aplicações aos utilizadores. Reveja a [erros transitórios](sql-database-connectivity-issues.md) secção e as práticas recomendadas e diretrizes de design em [descrição geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md) estratégias de repetição para obter mais informações e geral. Em seguida, veja exemplos de código em [bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md) para obter informações específicas.
3. Como seus limites de recursos se aproxima de uma base de dados, ele pode parecer ser um problema de conectividade transitório. Ver [limites de recursos](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se continuar a problemas de conectividade, ou se a duração para os quais seu aplicativo encontrar o erro exceder 60 segundos, ou se observar várias ocorrências do erro num determinado dia, ficheiro um pedido de suporte do Azure ao selecionar **obter suportar**sobre o [suporte do Azure](https://azure.microsoft.com/support/options) site.

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes
Se a aplicação de forma permanente falhar ligar à base de dados do Azure SQL, normalmente indica um problema com um dos seguintes:

* Configuração da firewall. A firewall de base de dados ou do lado do cliente do SQL do Azure está a bloquear ligações à base de dados do Azure SQL.
* Reconfiguração no lado do cliente de rede: por exemplo, um novo endereço IP ou um servidor proxy.
* Erro de utilizador: por exemplo, estar mal escrito parâmetros de ligação, como o nome do servidor na cadeia de ligação.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos para resolver problemas de conectividade persistente
1. Configurar [regras de firewall](sql-database-configure-firewall-settings.md) para permitir o endereço IP cliente. Para fins de testes temporárias, configure uma regra de firewall através de 0.0.0.0 como o intervalo de endereços IP inicial e 255.255.255.255 como o intervalo de endereços IP final. Esta ação irá abrir o servidor para todos os endereços IP. Se isso resolve o problema de conectividade, remover esta regra e criar uma regra de firewall para um endereço IP adequadamente limitado ou intervalo de endereços. 
2. Em todas as firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para ligações de saída. Revisão [configurar a Firewall do Windows para permitir o acesso do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) e [híbrida identidade portas e protocolos necessários](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) para ponteiros adicionais relacionados com portas adicionais que precisa abrir para o Azure Autenticação do Active Directory.
3. Certifique-se de que a cadeia de ligação e outras definições de ligação. Consulte a secção de cadeia de ligação no [tópico de problemas de conectividade](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Verifique o estado de funcionamento do serviço no dashboard. Se acha que há uma falha regional, consulte [recuperar a partir de um período de indisponibilidade](sql-database-disaster-recovery.md) para obter passos para recuperar para uma nova região.

## <a name="next-steps"></a>Passos Seguintes
* [Pesquisar a documentação no Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Ver as atualizações mais recentes para o serviço de base de dados do Azure SQL](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Descrição geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md)
* [Orientações gerais de processamento de falhas transitórias](../best-practices-retry-general.md)
* [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md)


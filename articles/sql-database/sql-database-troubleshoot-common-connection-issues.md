---
title: Solucionar problemas comuns de conexão
description: Etapas para identificar e resolver erros comuns de conexão para o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: dc58e495256bff9521eb6567736700f5ffcd6e4f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822462"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Solucionar problemas de conexão com o banco de dados SQL do Azure

Quando a conexão com o banco de dados SQL do Azure falha, você recebe [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que ajuda você a solucionar problemas de conectividade do banco de dados SQL do Azure. Ele apresenta [as causas comuns](#cause) de problemas de conexão, recomenda [uma ferramenta de solução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda você a identificar o problema e fornece etapas de solução de problemas para resolver [erros transitórios](#troubleshoot-transient-errors) e [erros persistentes ou não transitórios ](#troubleshoot-persistent-errors). 

Se você encontrar os problemas de conexão, tente as etapas de solução de problemas descritas neste artigo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa

Problemas de conexão podem ser causados por qualquer um dos seguintes:

* Falha ao aplicar as práticas recomendadas e diretrizes de design durante o processo de design do aplicativo.  Consulte [visão geral do desenvolvimento de banco de dados SQL](sql-database-develop-overview.md) para começar.
* Reconfiguração do banco de dados SQL do Azure
* Definições de firewall
* Tempo limite da conexão
* Informações de logon incorretas
* Limite máximo atingido em alguns recursos do banco de dados SQL do Azure

Geralmente, os problemas de conexão com o banco de dados SQL do Azure podem ser classificados da seguinte maneira:

* [Erros transitórios (curta duração ou intermitente)](#troubleshoot-transient-errors)
* [Erros persistentes ou não transitórios (erros que periodicamente são recorrentes)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Experimente a solução de problemas de conectividade do banco de dados SQL do Azure

Se você encontrar um erro de conexão específico, tente [essa ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que o ajudará a identificar e resolver rapidamente o problema.

## <a name="troubleshoot-transient-errors"></a>Solucionar erros transitórios

Quando um aplicativo se conecta a um banco de dados SQL do Azure, você recebe a seguinte mensagem de erro:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Essa mensagem de erro é normalmente transitória (vida curta).
> 
> 

Esse erro ocorre quando o banco de dados está sendo movido (ou reconfigurado) e seu aplicativo perde sua conexão com o banco de dados. Os eventos de reconfiguração de banco de dados ocorrem devido a um evento planejado (por exemplo, uma atualização de software) ou a um evento não planejado (por exemplo, uma falha de processo ou balanceamento de carga). A maioria dos eventos de reconfiguração geralmente é de curta duração e deve ser concluída em menos de 60 segundos. No entanto, esses eventos podem levar mais tempo para terminar, como quando uma transação grande causa uma recuperação de execução longa.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade transitórios

1. Verifique o [painel de serviço do Microsoft Azure](https://azure.microsoft.com/status) para quaisquer interrupções conhecidas que ocorreram durante o tempo durante o qual os erros foram relatados pelo aplicativo.
2. Os aplicativos que se conectam a um serviço de nuvem como o banco de dados SQL do Azure devem esperar eventos de reconfiguração periódicos e implementar a lógica de repetição para lidar com esses erros em vez de identificando-los como erros de aplicativo para os usuários Examine a seção [erros transitórios](sql-database-connectivity-issues.md) e as práticas recomendadas e diretrizes de design em [visão geral do desenvolvimento do banco de dados SQL](sql-database-develop-overview.md) para obter mais informações e estratégias de repetição geral. Em seguida, consulte exemplos de código em [bibliotecas de conexões para o banco de dados SQL e SQL Server](sql-database-libraries.md) para obter informações específicas.
3. Como um banco de dados se aproxima de seus limites de recursos, pode parecer ser um problema de conectividade transitório. Consulte [limites de recursos](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Se os problemas de conectividade continuarem, ou se a duração para a qual seu aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, faça uma solicitação de suporte do Azure selecionando **obter suporte** no [Azure ](https://azure.microsoft.com/support/options)Site de suporte.

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes
Se o aplicativo falhar de forma persistente ao se conectar ao banco de dados SQL do Azure, isso geralmente indica um problema com um dos seguintes:

* Configuração do firewall. O banco de dados SQL do Azure ou o Firewall do lado do cliente está bloqueando conexões com o banco de dados SQL do Azure.
* Reconfiguração de rede no lado do cliente: por exemplo, um novo endereço IP ou um servidor proxy.
* Erro do usuário: por exemplo, parâmetros de conexão digitados, como o nome do servidor na cadeia de conexão.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver problemas de conectividade persistente
1. Configure [as regras de firewall](sql-database-configure-firewall-settings.md) para permitir o endereço IP do cliente. Para fins de teste temporários, configure uma regra de firewall usando 0.0.0.0 como o intervalo de endereços IP inicial e usando 255.255.255.255 como o intervalo de endereços IP final. Isso abrirá o servidor para todos os endereços IP. Se isso resolver o problema de conectividade, remova essa regra e crie uma regra de firewall para um endereço IP ou intervalo de endereços apropriado limitado. 
2. Em todos os firewalls entre o cliente e a Internet, verifique se a porta 1433 está aberta para conexões de saída. Examine [Configurar o Firewall do Windows para permitir acesso SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) e [portas e protocolos necessários de identidade híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) para obter ponteiros adicionais relacionados a portas adicionais que você precisa abrir para a autenticação Azure Active Directory.
3. Verifique a cadeia de conexão e outras configurações de conexão. Consulte a seção cadeia de conexão no [tópico problemas de conectividade](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Verifique a integridade do serviço no painel. Se você considerar que há uma interrupção regional, consulte [recuperação de uma interrupção](sql-database-disaster-recovery.md) para obter as etapas para recuperar em uma nova região.

## <a name="next-steps"></a>Passos seguintes
* [Pesquise a documentação em Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Exibir as atualizações mais recentes para o serviço de banco de dados SQL do Azure](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Visão geral do desenvolvimento do banco de dados SQL](sql-database-develop-overview.md)
* [Diretrizes gerais de tratamento de falhas transitórias](../best-practices-retry-general.md)
* [Bibliotecas de conexão para o banco de dados SQL e SQL Server](sql-database-libraries.md)


---
title: Resolver problemas de ligação à base de dados do Azure para MariaDB | Documentos da Microsoft
description: Saiba como resolver problemas de ligação à base de dados do Azure para MariaDB.
keywords: ligação de mariadb, cadeia de ligação, problemas de conectividade, erro transitório, erro de ligação
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: f5c5a980a99bac3bf02eaaf9c072861ca753d449
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860454"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Resolver problemas de ligação à base de dados do Azure para MariaDB

Problemas de ligação podem ser causados por uma variedade de coisas, inclusive:

* Definições de firewall
* Tempo limite de ligação
* Informações de início de sessão incorretas
* Limite máximo foi atingido num banco de dados do Azure para MariaDB recursos
* Problemas com a infraestrutura do serviço
* A ser executada no serviço de manutenção
* A alocação de computação do servidor é alterada ao dimensionar o número de vCores ou para mover para um escalão de serviço diferentes

Em geral, os problemas de ligação à base de dados do Azure para MariaDB podem ser classificados da seguinte forma:

* Erros transitórios (com vida curta ou intermitentes)
* Erros de persistentes ou não transitórias (erros que recorrer regularmente)

## <a name="troubleshoot-transient-errors"></a>Resolver problemas de erros transitórios

Erros transitórios ocorrem quando é efetuada a manutenção, o sistema detetar um erro com o hardware ou software ou altera o escalão de serviço ou vCores do seu servidor. A base de dados do Azure para MariaDB serviço tem elevada disponibilidade incorporada e foi concebido para atenuar esses tipos de problemas automaticamente. No entanto, seu aplicativo perde a conexão com o servidor durante um curto período de tempo de normalmente, menos de 60 segundos no máximo. Alguns eventos, ocasionalmente, podem demorar mais tempo a atenuar, por exemplo, quando uma transação grande faz com que uma recuperação de longa execução.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas de conectividade transitório

1. Verifique os [Dashboard de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer falhas conhecidas que ocorreram durante a hora em que o erro foi comunicado pelo aplicativo.
2. Aplicações que se ligam a um serviço cloud como base de dados do Azure para MariaDB deve esperar erros transitórios e implementar novamente a lógica para lidar com esses erros, em vez de analisar como erros de aplicações aos utilizadores. Revisão [tratamento de erros de conectividade transitória da base de dados do Azure para MariaDB](concepts-connectivity.md) para melhores práticas e diretrizes de design para a manipulação de erros transitórios.
3. À medida que um servidor aproxima seus limites de recursos, podem parecer erros seja problema de conectividade transitório. Ver [limitações na base de dados do Azure para MariaDB](concepts-limits.md).
4. Se continuar a problemas de conectividade, ou se a duração para os quais seu aplicativo encontrar o erro exceder 60 segundos, ou se observar várias ocorrências do erro num determinado dia, ficheiro um pedido de suporte do Azure ao selecionar **obter suportar**sobre o [suporte do Azure](https://azure.microsoft.com/support/options) site.

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes

Se a aplicação de forma permanente falhar ligar à base de dados do Azure para MariaDB, normalmente indica um problema com um dos seguintes:

* Configuração da firewall: A base de dados do Azure para MariaDB firewall do servidor ou do lado do cliente está a bloquear ligações.
* Reconfiguração da rede no lado do cliente: Foi adicionado um novo endereço IP ou um servidor proxy.
* Erro de utilizador: Por exemplo, pode ter escrito incorretamente parâmetros de ligação, como o nome do servidor na cadeia de ligação ou um em falta  *\@servername* sufixo no nome do utilizador.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos para resolver problemas de conectividade persistente

1. Configurar [regras de firewall](howto-manage-firewall-portal.md) para permitir o endereço IP cliente. Temporários testes apenas para fins, configure uma regra de firewall com 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço IP final a utilizar. Esta ação irá abrir o servidor para todos os endereços IP. Se isso resolve o problema de conectividade, remover esta regra e criar uma regra de firewall para um endereço IP adequadamente limitado ou intervalo de endereços.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 3306 está aberta para ligações de saída.
3. Certifique-se de que a cadeia de ligação e outras definições de ligação. Revisão [como ligar aplicações à base de dados do Azure para MariaDB](howto-connection-string.md).
4. Verifique o estado de funcionamento do serviço no dashboard. Se acha que há uma falha regional, consulte [descrição geral da continuidade do negócio com a base de dados do Azure para MariaDB](concepts-business-continuity.md) para obter passos para recuperar para uma nova região.

## <a name="next-steps"></a>Passos Seguintes

* [Tratamento de erros de conectividade transitória da base de dados do Azure para MariaDB](concepts-connectivity.md)

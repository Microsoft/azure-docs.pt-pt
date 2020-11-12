---
title: Problemas de conexão resolução de problemas - Base de Dados Azure para MariaDB
description: Saiba como resolver problemas de ligação à Base de Dados Azure para MariaDB, incluindo erros transitórios que requerem retrações, problemas de firewall e interrupções.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b9a670472529dccd51cbfa71e385151bc63cff9b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539384"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Resolver problemas de ligação ao Azure Database for MariaDB

Os problemas de ligação podem ser causados por uma variedade de coisas, incluindo:

* Definições de firewall
* Tempo de intervalo de ligação
* Informações incorretas de login
* Limite máximo atingido em algumas Bases de Dados Azure para recursos MariaDB
* Problemas com a infraestrutura do serviço
* Manutenção a ser realizada no serviço
* A atribuição de cálculo do servidor é alterada escalando o número de vCores ou movendo-se para um nível de serviço diferente

Geralmente, os problemas de ligação à Base de Dados Azure para MariaDB podem ser classificados da seguinte forma:

* Erros transitórios (de curta duração ou intermitente)
* Erros persistentes ou não transitórios (erros que se repetem regularmente)

## <a name="troubleshoot-transient-errors"></a>Erros transitórios de resolução de problemas

Erros transitórios ocorrem quando a manutenção é realizada, o sistema encontra um erro com o hardware ou software, ou altera o nível de vCores ou de serviço do seu servidor. A Base de Dados Azure para o serviço MariaDB tem uma elevada disponibilidade incorporada e foi concebida para mitigar este tipo de problemas automaticamente. No entanto, a sua aplicação perde a ligação ao servidor por um curto período de tempo de menos de 60 segundos, no máximo. Alguns eventos podem ocasionalmente demorar mais tempo a mitigar, como quando uma grande transação causa uma recuperação prolongada.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Medidas para resolver problemas transitórios de conectividade

1. Verifique o Painel de Serviços do [Microsoft Azure](https://azure.microsoft.com/status) para saber se ocorreram falhas conhecidas durante o período em que os erros foram reportados pela aplicação.
2. As aplicações que se ligam a um serviço na nuvem, como o Azure Database for MariaDB, devem esperar erros transitórios e implementar lógica de repetição para lidar com estes erros em vez de os surgir como erros de aplicação para os utilizadores. Rever [o manuseamento de erros de conectividade transitórios para a Base de Dados Azure para MariaDB](concepts-connectivity.md) para as melhores práticas e orientações de conceção para lidar com erros transitórios.
3. À medida que um servidor se aproxima dos seus limites de recursos, os erros podem parecer ser problemas transitórios de conectividade. Ver [Limitações na Base de Dados Azure para MariaDB](concepts-limits.md).
4. Se os problemas de conectividade continuarem, ou se a duração durante a qual a sua aplicação se encontra exceder 60 segundos ou se vir múltiplas ocorrências do erro num determinado dia, arquive um pedido de suporte Azure selecionando **Obter Suporte** no site de [Suporte Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes

Se a aplicação não ligar persistentemente à Base de Dados Azure para o MariaDB, geralmente indica um problema com um dos seguintes:

* Configuração de firewall: A Base de Dados Azure para servidor MariaDB ou firewall do lado do cliente está a bloquear ligações.
* Reconfiguração da rede no lado do cliente: Foi adicionado um novo endereço IP ou um servidor proxy.
* Erro do utilizador: Por exemplo, pode ter parâmetros de ligação mal definidos, como o nome do servidor na cadeia de ligação ou um sufixo de nome de *\@ servidor* em falta no nome de utilizador.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Medidas para resolver problemas de conectividade persistentes

1. Configurar [regras de firewall](howto-manage-firewall-portal.md) para permitir o endereço IP do cliente. Apenas para testes temporários, crie uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando o endereço IP inicial e utilizando o endereço IP inicial 255.255.255.255 como o endereço IP final. Isto abrirá o servidor a todos os endereços IP. Se isto resolver o seu problema de conectividade, remova esta regra e crie uma regra de firewall para um endereço IP ou intervalo de endereços devidamente limitado.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 3306 está aberta para ligações de saída.
3. Verifique a sua cadeia de ligação e outras definições de ligação. Rever [Como ligar as aplicações à Base de Dados Azure para MariaDB](howto-connection-string.md).
4. Verifique a saúde do serviço no painel de instrumentos. Se você acha que há uma paralisação regional, consulte [a visão geral da continuidade do negócio com a Azure Database for MariaDB](concepts-business-continuity.md) para passos para recuperar para uma nova região.

## <a name="next-steps"></a>Passos seguintes

* [Tratamento de erros de conectividade transitórios para a Base de Dados Azure para MariaDB](concepts-connectivity.md)

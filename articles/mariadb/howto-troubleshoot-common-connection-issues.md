---
title: Problemas de ligação - Base de Dados Azure para MariaDB
description: Aprenda a resolver problemas de ligação à Base de Dados Azure para o MariaDB, incluindo erros transitórios que requerem tentativas, problemas de firewall e interrupções.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 521853fd361007159d7e497eb7e7c1f3d200731c
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101455"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Resolver problemas de ligação ao Azure Database for MariaDB

Os problemas de ligação podem ser causados por uma variedade de coisas, incluindo:

* Definições de firewall
* Tempo de ligação
* Informações de login incorretas
* Limite máximo atingido em alguma base de dados Azure para recursos MariaDB
* Problemas com a infraestrutura do serviço
* Manutenção a ser realizada no serviço
* A atribuição computacional do servidor é alterada escalando o número de vCores ou movendo-se para um nível de serviço diferente

Geralmente, as questões de ligação à Base de Dados Azure para MariaDB podem ser classificadas da seguinte forma:

* Erros transitórios (de curta duração ou intermitentes)
* Erros persistentes ou não transitórios (erros que se repetem regularmente)

## <a name="troubleshoot-transient-errors"></a>Erros transitórios de resolução de problemas

Erros transitórios ocorrem quando a manutenção é realizada, o sistema encontra um erro com o hardware ou software, ou muda o vCores ou o nível de serviço do seu servidor. A Base de Dados Azure para o serviço MariaDB tem uma elevada disponibilidade incorporada e foi concebida para mitigar este tipo de problemas automaticamente. No entanto, a sua aplicação perde a ligação ao servidor por um curto período de tempo de normalmente menos de 60 segundos no máximo. Alguns eventos podem ocasionalmente demorar mais tempo a atenuar, como quando uma grande transação causa uma recuperação a longo prazo.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas transitórios de conectividade

1. Verifique se o [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) está a verificar quaisquer falhas conhecidas que ocorreram durante o período em que os erros foram reportados pela aplicação.
2. As aplicações que se ligam a um serviço na nuvem, como a Base de Dados Azure para o MariaDB, devem esperar erros transitórios e implementar a lógica de retry para lidar com estes erros em vez de os surcar como erros de aplicação para os utilizadores. Reveja o tratamento de erros transitórios de conectividade para a Base de [Dados Azure para O MariaDB](concepts-connectivity.md) para as melhores práticas e orientações de conceção para lidar com erros transitórios.
3. À medida que um servidor se aproxima dos seus limites de recursos, os erros podem parecer um problema de conectividade transitório. Consulte [limitações na Base de Dados Azure para MariaDB.](concepts-limits.md)
4. Se os problemas de conectividade continuarem, ou se a duração para a qual a sua aplicação se encontra o erro exceder 60 segundos ou se vir múltiplas ocorrências do erro num determinado dia, faça um pedido de suporte Azure selecionando **o Suporte** get no site do [Suporte Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Erros persistentes de resolução de problemas

Se a aplicação não conseguir ligar persistentemente à Base de Dados Azure para o MariaDB, normalmente indica um problema com um dos seguintes:

* Configuração da firewall: A Base de Dados Azure para servidor MariaDB ou firewall do lado do cliente está bloqueando ligações.
* Reconfiguração da rede no lado do cliente: Foi adicionado um novo endereço IP ou um servidor proxy.
* Erro do utilizador: Por exemplo, pode ter parâmetros de ligação enevoados, como o nome do servidor na cadeia de ligação ou um sufixo de nome de * \@servidor* em falta no nome do utilizador.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos para resolver problemas persistentes de conectividade

1. Configurar regras de [firewall](howto-manage-firewall-portal.md) para permitir o endereço IP do cliente. Apenas para efeitos de testes temporários, criar uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando 255.255.255.255 como endereço IP final. Isto abrirá o servidor a todos os endereços IP. Se isto resolver o seu problema de conectividade, remova esta regra e crie uma regra de firewall para um endereço IP ou endereço adequadamente limitado.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 3306 está aberta para ligações de saída.
3. Verifique a sua cadeia de ligação e outras definições de ligação. Reveja como ligar aplicações à Base de [Dados Azure para MariaDB](howto-connection-string.md).
4. Verifique a saúde do serviço no painel de instrumentos. Se acha que há uma paragem regional, consulte a visão geral da continuidade dos negócios com a Base de [Dados Azure para a MariaDB](concepts-business-continuity.md) para obter medidas para recuperar para uma nova região.

## <a name="next-steps"></a>Passos seguintes

* [Tratamento de erros transitórios de conectividade para a Base de Dados Azure para MariaDB](concepts-connectivity.md)

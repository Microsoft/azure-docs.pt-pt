---
title: Conexões de resolução de problemas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba como resolver problemas de ligação à Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
keywords: conexão pós-quadrado,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295622"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Problemas de conexão de resolução de problemas na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Os problemas de ligação podem ser causados por várias coisas, tais como:

* Definições de firewall
* Tempo de intervalo de ligação
* Sinal incorreto na informação
* Limite de ligação atingido para o grupo de servidor
* Problemas com a infraestrutura do serviço
* Manutenção de serviços
* O nó coordenador falhando no novo hardware

Geralmente, os problemas de ligação à Hiperescala (Citus) podem ser classificados da seguinte forma:

* Erros transitórios (de curta duração ou intermitente)
* Erros persistentes ou não transitórios (erros que se repetem regularmente)

## <a name="troubleshoot-transient-errors"></a>Resolver erros transitórios

Erros transitórios ocorrem por uma série de razões. As atualizações mais comuns incluem manutenção do sistema, erro com hardware ou software e atualizações de node vCore do coordenador.

Permitir uma elevada disponibilidade para os nós do grupo de servidores Hyperscale (Citus) pode atenuar este tipo de problemas automaticamente. No entanto, a sua aplicação ainda deve estar preparada para perder a sua ligação brevemente. Outros eventos também podem demorar mais tempo a mitigar, como quando uma grande transação causa uma recuperação prolongada.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Medidas para resolver problemas transitórios de conectividade

1. Consulte o Painel de Serviços do [Microsoft Azure](https://azure.microsoft.com/status) para saber se ocorreram falhas conhecidas durante o período em que a aplicação reportava erros.
2. As aplicações que se ligam a um serviço de nuvem como Hyperscale (Citus) devem esperar erros transitórios e reagir graciosamente. Por exemplo, as aplicações devem implementar uma lógica de reação para lidar com estes erros em vez de os surgirem como erros de aplicação para os utilizadores.
3. À medida que o grupo de servidor se aproxima dos seus limites de recursos, os erros podem parecer problemas transitórios de conectividade. Aumentar a RAM do nó, ou adicionar nós de trabalhadores e reequilibrar dados pode ajudar.
4. Se os problemas de conectividade continuarem, ou durarem mais de 60 segundos, ou acontecerem mais de uma vez por dia, arquive um pedido de suporte Azure selecionando **Get Support** no site de [Suporte Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes

Se a aplicação não se ligar persistentemente à Hiperescala (Citus), as causas mais comuns são a configuração errada da firewall ou o erro do utilizador.

* Configuração da firewall do nó coordenador: Certifique-se de que a firewall do servidor Hyperscale (Citus) está configurada para permitir ligações do seu cliente, incluindo servidores de procuração e gateways.
* Configuração da firewall do cliente: A firewall do seu cliente deve permitir ligações ao servidor de base de dados. Algumas firewalls requerem permitir não só a aplicação pelo nome, mas permitir os endereços IP e portas do servidor.
* Erro do utilizador: Verifique novamente a cadeia de ligação. Pode ter definido mal parâmetros como o nome do servidor. Você pode encontrar cadeias de conexão para várias estruturas linguísticas e psql no portal Azure. Aceda à página **de cordas De Ligação** no seu grupo de servidor Hyperscale (Citus). Tenha também em mente que os clusters Hyperscale (Citus) têm apenas uma base de dados e o seu nome predefinido é **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Medidas para resolver problemas de conectividade persistentes

1. Configurar [regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Apenas para testes temporários, crie uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando o endereço IP inicial e utilizando o endereço IP inicial 255.255.255.255 como o endereço IP final. Esta regra abre o servidor para todos os endereços IP. Se a regra resolver o seu problema de conectividade, remova-o e crie uma regra de firewall para um endereço IP ou intervalo de endereços devidamente limitado.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 5432 está aberta para ligações de saída.
3. Verifique a sua cadeia de ligação e outras definições de ligação.
4. Verifique a saúde do serviço no painel de instrumentos.

## <a name="next-steps"></a>Passos seguintes

* Conheça os conceitos de regras de [Firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](concepts-hyperscale-firewall-rules.md)
* Ver como gerir as regras de [firewall para a base de dados Azure para PostgreSQL - Hiperescala (Citus)](howto-hyperscale-manage-firewall-using-portal.md)

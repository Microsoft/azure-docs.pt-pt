---
title: Ligações de resolução de problemas - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Saiba como resolver problemas de ligação à Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
keywords: conexão pós-gresql,cadeia de ligação,problemas de conectividade,erro transitório,erro de ligação
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: abcc979bb36fe0acb663a511875a1e186c2c0739
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583961"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Problemas de ligação à Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Os problemas de ligação podem ser causados por várias coisas, tais como:

* Definições de firewall
* Tempo de ligação
* Sinal incorreto na informação
* Limite de ligação atingido para o grupo de servidores
* Problemas com a infraestrutura do serviço
* Manutenção de serviços
* O nó coordenador falhando com novo hardware

Geralmente, as questões de ligação à Hiperescala podem ser classificadas da seguinte forma:

* Erros transitórios (de curta duração ou intermitentes)
* Erros persistentes ou não transitórios (erros que se repetem regularmente)

## <a name="troubleshoot-transient-errors"></a>Erros transitórios de resolução de problemas

Erros transitórios ocorrem por uma série de razões. O mais comum inclui manutenção do sistema, erro com hardware ou software, e upgrades vCore do nó coordenador.

Permitir uma elevada disponibilidade para nós de grupo de servidores hyperscale pode atenuar este tipo de problemas automaticamente. No entanto, a sua aplicação deve ainda estar preparada para perder a sua ligação brevemente. Também outros eventos podem demorar mais tempo a atenuar, como quando uma grande transação causa uma recuperação a longo prazo.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas transitórios de conectividade

1. Verifique se o [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) está a verificar quaisquer falhas conhecidas que ocorreram durante o período em que a aplicação reportava erros.
2. As aplicações que se ligam a um serviço na nuvem como o Hyperscale (Citus) devem esperar erros transitórios e reagir graciosamente. Por exemplo, as aplicações devem implementar a lógica de retry para lidar com estes erros em vez de os surcar como erros de aplicação para os utilizadores.
3. À medida que o grupo de servidorse se aproxima dos seus limites de recursos, os erros podem parecer problemas de conectividade transitórios. Aumentar a RAM do nó, ou adicionar nós de trabalhador e reequilibrar dados pode ajudar.
4. Se os problemas de conectividade continuarem, ou durarem mais de 60 segundos, ou acontecerem mais de uma vez por dia, preencha um pedido de suporte Azure selecionando **o Get Support** no site de Suporte Do [Azure.](https://azure.microsoft.com/support/options)

## <a name="troubleshoot-persistent-errors"></a>Erros persistentes de resolução de problemas

Se a aplicação não conseguir ligar persistentemente à Hiperescala (Citus), as causas mais comuns são a configuração errada da firewall ou o erro do utilizador.

* Configuração de firewall do nó coordenador: Certifique-se de que a firewall do servidor Hyperscale está configurada para permitir ligações do seu cliente, incluindo servidores proxy e gateways.
* Configuração da firewall do cliente: A firewall do seu cliente deve permitir ligações ao seu servidor de base de dados. Algumas firewalls requerem permitir não só a aplicação pelo nome, mas permitir os endereços IP e portas do servidor.
* Erro do utilizador: Verifique duas vezes a cadeia de ligação. Podeter ter enevoado parâmetros como o nome do servidor. Pode encontrar cordas de ligação para vários quadros linguísticos e psql no portal Azure. Vá à página de **cordas de Ligação** no seu grupo de servidores Hyperscale (Citus). Tenha também em mente que os clusters de Hiperescala (Citus) têm apenas uma base de dados e o seu nome predefinido é **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos para resolver problemas persistentes de conectividade

1. Configurar regras de [firewall](howto-hyperscale-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Apenas para efeitos de testes temporários, criar uma regra de firewall utilizando 0.0.0.0 como endereço IP inicial e utilizando 255.255.255.255 como endereço IP final. Esta regra abre o servidor a todos os endereços IP. Se a regra resolver o seu problema de conectividade, remova-o e crie uma regra de firewall para um endereço IP ou endereço adequadamente limitado.
2. Em todas as firewalls entre o cliente e a internet, certifique-se de que a porta 5432 está aberta para ligações de saída.
3. Verifique a sua cadeia de ligação e outras definições de ligação.
4. Verifique a saúde do serviço no painel de instrumentos.

## <a name="next-steps"></a>Passos seguintes

* Conheça os conceitos de regras de [Firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)](concepts-hyperscale-firewall-rules.md)
* Veja como gerir as regras de firewall para a Base de [Dados Azure para PostgreSQL - Hiperescala (Citus)](howto-hyperscale-manage-firewall-using-portal.md)

---
title: Regras de firewall - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve as regras de firewall para Azure Database for PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90902091"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regras de firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
A azure Database for PostgreSQL server firewall impede todo o acesso ao seu nó coordenador de Hiperescala (Citus) até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem que os clientes acedam ao nó coordenador da Hyperscale (Citus), ou seja, a todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando o portal Azure. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados ao nó coordenador é bloqueado por padrão pela firewall. Para começar a utilizar o seu servidor a partir de outro computador, tem de especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar quais os endereços IP que variam a partir da Internet para permitir. O acesso ao próprio site do portal Azure não é afetado pelas regras do firewall.
As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados PostgreSQL, como mostra o seguinte diagrama:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Exemplo de fluxo de como a firewall funciona":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Ligação a partir da Internet e de Azure

Uma firewall do grupo de servidor Hyperscale (Citus) controla quem pode ligar-se ao nó coordenador do grupo. A firewall determina o acesso consultando uma lista de regras configuráveis. Cada regra é um endereço IP, ou intervalo de endereços, que são permitidos dentro

Quando a firewall bloqueia as ligações, pode causar erros de aplicação. A utilização do controlador JDBC PostgreSQL, por exemplo, levanta um erro como este:

> cution java.util.concurrent.Exe: java.lang.RuntimeException: org.postgresql.util.PSQLExcepção: FATAL: sem entrada pg \_ hba.conf para anfitrião "123.45.67,890", utilizador "citus", base de dados "citus", SSL

Consulte [criar e gerir as regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber como as regras são definidas.

## <a name="troubleshooting-the-database-server-firewall"></a>Resolução de problemas na firewall do servidor de base de dados
Quando o acesso ao serviço Microsoft Azure Database for PostgreSQL - Hyperscale (Citus) não se comporta como espera, considere estes pontos:

* **As alterações à lista de autorizações ainda não produziram efeitos:** Pode haver um atraso de cinco minutos para que as alterações à configuração de firewall hyperscale (Citus) entrem em vigor.

* **O utilizador não é autorizado ou foi utilizada uma palavra-passe incorreta:** Se um utilizador não tiver permissões no servidor ou a palavra-passe utilizada estiver incorreta, a ligação ao servidor é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentarem ligar-se ao seu servidor; cada cliente deve ainda fornecer as credenciais de segurança necessárias.

Por exemplo, usando um cliente JDBC, pode aparecer o seguinte erro.
> cution java.util.concurrent.ExeCutionExcepção: java.lang.RuntimeExcepção: org.postgresql.util.PSQLExcepção: FATAL: a autenticação da palavra-passe falhou para o utilizador "yourusername"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

* Peça ao seu Fornecedor de Serviços de Internet (ISP) para obter o intervalo de endereços IP atribuído aos seus computadores clientes que acedam ao nó coordenador hyperscale (Citus) e, em seguida, adicione o intervalo de endereço IP como regra de firewall.

* Obtenha endereço IP estático em vez dos computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras de firewall ao nível do servidor e de base de dados, consulte:
* [Criar e gerir a Base de Dados Azure para regras de firewall postgreSQL utilizando o portal Azure](howto-hyperscale-manage-firewall-using-portal.md)

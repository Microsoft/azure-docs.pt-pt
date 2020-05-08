---
title: Regras de firewall - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve as regras de firewall para a Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 127840738d8fb8db00a7efc0b0d8fe393138488d
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580924"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regras de firewall na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
A Base de Dados Azure para firewall do servidor PostgreSQL impede todo o acesso ao seu nó coordenador de Hiperescala (Citus) até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem aos clientes aceder ao seu nó coordenador de Hiperescala (Citus), isto é, todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando o portal Azure. Para criar regras de firewall ao nível do servidor, deve ser o proprietário da subscrição ou um colaborador de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados do nó do seu coordenador é bloqueado por predefinição da firewall. Para começar a utilizar o seu servidor a partir de outro computador, é necessário especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar qual o endereço IP que vai da Internet para permitir. O acesso ao próprio portal Azure não é afetado pelas regras de firewall.
As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados PostgreSQL, como mostra o seguinte diagrama:

![Fluxo de exemplo de como a firewall funciona](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Ligação a partir da Internet e do Azure

Um grupo de firewall de grupo de hiperescala (Citus) que pode ligar-se ao nó coordenador do grupo. A firewall determina o acesso consultando uma lista de regras configuráveis. Cada regra é um endereço IP, ou um intervalo de endereços, que são permitidos dentro

Quando a firewall bloqueia as ligações, pode causar erros de aplicação. A utilização do condutor Do P.R. PostgreSQL JDBC, por exemplo, levanta um erro como este:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: NO\_pg hba.conf entry for host "123.45.67.890", user "citus", base de dados "citus", SSL

Consulte [criar e gerir as regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber como as regras são definidas.

## <a name="troubleshooting-the-database-server-firewall"></a>Resolução de problemas na firewall do servidor de base de dados
Quando o acesso à Base de Dados Microsoft Azure para o serviço PostgreSQL - Hyperscale (Citus) não se comportar como espera, considere estes pontos:

* **As alterações à lista de autorizações ainda não produziram efeito:** Pode haver até um atraso de cinco minutos para que as alterações na configuração de firewall hyperscale (Citus) façam efeito.

* **O utilizador não está autorizado ou foi utilizada uma senha incorreta:** Se um utilizador não tiver permissões no servidor ou se a palavra-passe utilizada estiver incorreta, a ligação ao servidor é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentar ligar-se ao seu servidor; cada cliente deve ainda fornecer as credenciais de segurança necessárias.

Por exemplo, utilizando um cliente JDBC, pode aparecer o seguinte erro.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: a autenticação de senha falhou para o utilizador "yourusername"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

* Peça ao seu Fornecedor de Serviços de Internet (ISP) a gama de endereços IP atribuída aos seus computadores clientes que acedam ao nó coordenador de Hiperescala (Citus) e adicione a gama de endereços IP como regra de firewall.

* Obtenha endereço IP estático em vez dos seus computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras de firewall ao nível do servidor e de nível de base de dados, consulte:
* [Crie e gerea Base de Dados Azure para regras de firewall PostgreSQL usando o portal Azure](howto-hyperscale-manage-firewall-using-portal.md)

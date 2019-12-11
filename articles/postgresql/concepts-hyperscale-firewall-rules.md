---
title: Regras de firewall-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Este artigo descreve as regras de firewall para o banco de dados do Azure para PostgreSQL-Citus (hiperescala).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975572"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
O firewall de servidor do banco de dados do Azure para PostgreSQL impede todo o acesso ao nó do coordenador de hiperescala (Citus) até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível do servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem seu nó de coordenador de hiperescala (Citus), ou seja, todos os bancos de dados dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas usando o portal do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todos os acessos ao banco de dados ao seu nó de coordenador são bloqueados pelo firewall por padrão. Para começar a usar o servidor de outro computador, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso ao site portal do Azure em si não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar pela primeira vez pelo firewall antes que possam acessar seu banco de dados PostgreSQL, conforme mostrado no diagrama a seguir:

![Fluxo de exemplo de como o firewall funciona](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Conectando da Internet e do Azure

Um firewall de grupo de servidores de hiperescala (Citus) controla quem pode se conectar ao nó de coordenador do grupo. O firewall determina o acesso consultando uma lista configurável de regras. Cada regra é um endereço IP ou um intervalo de endereços que são permitidos no.

Quando o firewall bloqueia conexões, ele pode causar erros de aplicativo. O uso do driver JDBC do PostgreSQL, por exemplo, gera um erro como este:

> Java. util. Concurrent. Execuexception: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: FATAL: no PG\_entrada de HBA. conf para o host "123.45.67.890", usuário "citus", banco de dados "citus", SSL

Consulte [criar e gerenciar regras de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber como as regras são definidas.

## <a name="troubleshooting-the-database-server-firewall"></a>Solucionando problemas do firewall do servidor de banco de dados
Quando o acesso ao banco de dados de Microsoft Azure para PostgreSQL-Citus (serviço de hiperescala) não se comportar conforme o esperado, considere estes pontos:

* **As alterações na lista de permissões ainda não entraram em vigor:** Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall de hiperescala (Citus) entrem em vigor.

* **O usuário não está autorizado ou uma senha incorreta foi usada:** Se um usuário não tiver permissões no servidor ou a senha usada estiver incorreta, a conexão com o servidor será negada. A criação de uma configuração de firewall apenas fornece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deve fornecer as credenciais de segurança necessárias.

Por exemplo, usando um cliente JDBC, o erro a seguir pode aparecer.
> Java. util. Concurrent. Execuexception: Java. lang. RuntimeException: org. PostgreSQL. util. PSQLException: FATAL: falha na autenticação de senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

* Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos seus computadores cliente que acessam o nó de coordenador de hiperescala (Citus) e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obtenha o endereçamento IP estático em vez de seus computadores cliente e, em seguida, adicione o endereço IP estático como uma regra de firewall.

## <a name="next-steps"></a>Passos seguintes
Para obter artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](howto-hyperscale-manage-firewall-using-portal.md)

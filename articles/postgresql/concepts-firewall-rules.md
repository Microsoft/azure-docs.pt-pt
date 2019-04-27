---
title: Base de dados do Azure para as regras de firewall do servidor PostgreSQL
description: Este artigo descreve as regras de firewall da base de dados do Azure para o servidor PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 793a68ee829d87a8433d5bdd77fe7b43e2f3af19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560252"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Base de dados do Azure para as regras de firewall do servidor PostgreSQL
Base de dados do Azure para o firewall do servidor PostgreSQL impede todo o acesso ao seu servidor de base de dados até especificar que computadores têm permissão. A firewall concede acesso ao servidor com base no endereço IP de origem de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem que os clientes acedam a sua base de dados do Azure completa para o servidor PostgreSQL, ou seja, todas as bases de dados no mesmo servidor lógico. Regras de firewall ao nível do servidor podem ser configuradas utilizando o portal do Azure ou utilizar comandos da CLI do Azure. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte da subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Por predefinição, todos os acessos de base de dados para a base de dados do Azure para o servidor PostgreSQL estão bloqueado pela firewall. Para começar a utilizar o servidor a partir de outro computador, terá de especificar uma ou mais regras de firewall ao nível do servidor para ativar o acesso ao seu servidor. Utilize as regras de firewall para especificar qual IP intervalos de endereços da Internet para permitir. Acesso ao site de portal do Azure em si não é afetado pelas regras de firewall.
Tentativas de ligação de Internet e do Azure tem primeiro de passar pela firewall antes de chegarem a base de dados do PostgreSQL, conforme mostrado no diagrama seguinte:

![Fluxo de exemplo de como funciona a firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
Regras de firewall ao nível do servidor são aplicadas a todas as bases de dados do mesmo banco de dados do Azure para o servidor PostgreSQL. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação.
Se o endereço IP do pedido não estiver dentro dos intervalos especificados em qualquer uma das regras de firewall ao nível do servidor, o pedido de ligação falha.
Por exemplo, se seu aplicativo se conecta com o controlador JDBC para o PostgreSQL, pode encontrar este erro ao tentar ligar quando a firewall está a bloquear a ligação.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: não pg\_hba.conf de entrada para o anfitrião "123.45.67.890", o utilizador "adminuser", base de dados "postgresql", SSL

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que aplicações do Azure ligar à base de dados do Azure para o servidor PostgreSQL, ligações do Azure tem de estar ativadas. Por exemplo, para alojar uma aplicação de aplicações Web do Azure ou um aplicativo que é executado numa VM do Azure ou para ligar a partir de um gateway de gestão de dados do Azure Data Factory. Os recursos não é necessário estar na mesma rede Virtual (VNet) ou grupo de recursos para a regra de firewall para permitir que essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem dois métodos para permitir que esses tipos de ligações. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir o **permitir o acesso aos serviços do Azure** a opção de **ON** no portal do **segurança da ligação** painel e clique em **guardar**. Se a tentativa de ligação não for permitida, o pedido não chega a base de dados do Azure para o servidor PostgreSQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Para além do portal do Azure, as regras de firewall podem ser geridas programaticamente com a CLI do Azure.
Consulte também [criar e gerir a base de dados do Azure para as regras de firewall do PostgreSQL com a CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>O firewall do servidor de base de dados de resolução de problemas
Ao acesso à base de dados do Microsoft Azure para o serviço de servidor PostgreSQL não tem um comportamento conforme o esperado, considere os seguintes pontos:

* **Alterações à lista de permissões não foram ainda aplicadas:** Pode ser tanto quanto o atraso de cinco minutos para que as alterações à base de dados do Azure para a configuração de firewall do servidor PostgreSQL entrem em vigor.

* **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:** Se um início de sessão não tem permissões na base de dados do Azure para o servidor PostgreSQL ou a palavra-passe utilizada estiver incorreta, a ligação à base de dados do Azure para o servidor PostgreSQL é negada. Apenas criando uma definição de firewall fornece aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem ainda de fornecer as credenciais de segurança necessário.

Por exemplo, utilizar um cliente JDBC, pode aparecer o erro seguinte.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha de autenticação de palavra-passe do utilizador "yourusername"

* **Endereço IP dinâmico:** Se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das seguintes soluções:

* Peça ao seu fornecedor de serviços de Internet (ISP) para o intervalo de endereços IP atribuído a computadores cliente que acedem a base de dados do Azure para o servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Obtenha o endereçamento IP estático em vez disso, para os computadores cliente e, em seguida, adicione o endereço IP estático como uma regra de firewall.

## <a name="next-steps"></a>Passos Seguintes
Para artigos sobre a criação de regras de firewall ao nível do servidor e ao nível da base de dados, consulte:
* [Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](howto-manage-firewall-using-cli.md)

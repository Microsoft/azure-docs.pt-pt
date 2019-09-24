---
title: Regras de firewall no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve as regras de firewall para o banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: a48e9e2583afbde584987e5a1ac61da9734058d1
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200129"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regras de firewall no banco de dados do Azure para PostgreSQL-servidor único
O firewall de servidor do banco de dados do Azure para PostgreSQL impede todo o acesso ao servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível do servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem todo o seu banco de dados do Azure para o servidor PostgreSQL, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas usando o portal do Azure ou usando comandos CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso ao banco de dados para o servidor do banco de dados do Azure para PostgreSQL é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso ao site portal do Azure em si não é afetado pelas regras de firewall.
As tentativas de conexão da Internet e do Azure devem passar pela primeira vez pelo firewall antes que possam acessar seu banco de dados PostgreSQL, conforme mostrado no diagrama a seguir:

![Fluxo de exemplo de como o firewall funciona](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall no nível de servidor aplicam-se a todos os bancos de dados no mesmo servidor do Azure para PostgreSQL. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação.
Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em qualquer uma das regras de firewall no nível de servidor, a solicitação de conexão falhará.
Por exemplo, se seu aplicativo se conectar com o driver JDBC para PostgreSQL, você poderá encontrar esse erro ao tentar se conectar quando o firewall estiver bloqueando a conexão.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: nenhuma entrada\_de HBA de PG. conf para o host "123.45.67.890", usuário "adminuser", banco de dados "PostgreSQL", SSL

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao seu banco de dados do Azure para o servidor PostgreSQL, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o servidor de banco de dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal do Azure, as regras de firewall podem ser gerenciadas programaticamente usando CLI do Azure.
Consulte também [criar e gerenciar regras de firewall do banco de dados do Azure para PostgreSQL usando CLI do Azure](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao banco de dados do Microsoft Azure para o serviço do servidor PostgreSQL não se comportar conforme o esperado:

* **As alterações na lista de permissões ainda não entraram em vigor:** As alterações à configuração da firewall do Servidor da Base de Dados do Azure para PostgreSQL podem demorar até cinco minutos a serem aplicadas.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor do banco de dados do Azure para PostgreSQL ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor PostgreSQL será negada. A criação de uma configuração de firewall apenas fornece aos clientes uma oportunidade de tentar se conectar ao servidor; cada cliente ainda deve fornecer as credenciais de segurança necessárias.

   Por exemplo, usando um cliente JDBC, o erro a seguir pode aparecer.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: falha na autenticação de senha para o usuário "seunomedeusuário"

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores cliente que acessam o servidor do banco de dados do Azure para PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Obtenha o endereçamento IP estático em vez de seus computadores cliente e, em seguida, adicione o endereço IP estático como uma regra de firewall.

* **O IP do servidor parece ser público:** As conexões com o banco de dados do Azure para servidor PostgreSQL são roteadas por meio de um gateway do Azure acessível publicamente. No entanto, o IP do servidor real é protegido pelo firewall. Para obter mais informações, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passos seguintes
Para obter artigos sobre como criar regras de firewall no nível de servidor e de banco de dados, consulte:
* [Criar e gerir a base de dados do Azure para as regras de firewall de PostgreSQL no portal do Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerir a base de dados do Azure PostgreSQL das regras de firewall ao utilizar a CLI do Azure](howto-manage-firewall-using-cli.md)

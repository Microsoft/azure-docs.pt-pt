---
title: Banco de dados do Azure para regras de firewall do servidor MariaDB
description: Descreve as regras de firewall para o banco de dados do Azure para MariaDB Server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 94efc87cdea8e1d9b63361d4652b63909c6ce3e7
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203607"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Banco de dados do Azure para regras de firewall do servidor MariaDB
Os firewalls impedem todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.

Para configurar um firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível do servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem todo o banco de dados do Azure para o servidor MariaDB, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas usando os comandos portal do Azure ou CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso de banco de dados ao banco de dados do Azure para MariaDB Server é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso ao site portal do Azure em si não é afetado pelas regras de firewall.

As tentativas de conexão da Internet e do Azure devem passar pela primeira vez pelo firewall antes que possam acessar o banco de dados do Azure para o banco de dados MariaDB, conforme mostrado no diagrama a seguir:

![Fluxo de exemplo de como o firewall funciona](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall no nível de servidor se aplicam a todos os bancos de dados no banco de dados do Azure para o servidor MariaDB.

Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível de servidor, a conexão será concedida.

Se o endereço IP da solicitação estiver fora dos intervalos especificados em qualquer uma das regras de firewall no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao banco de dados do Azure para MariaDB Server, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o banco de dados do Azure para o servidor MariaDB.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal do Azure, as regras de firewall podem ser gerenciadas programaticamente usando o CLI do Azure. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao banco de dados do Microsoft Azure para o serviço do servidor MariaDB não se comportar conforme o esperado:

* **As alterações na lista de permissões ainda não entraram em vigor:** As alterações à configuração da firewall do Servidor do Azure Database for MariaDB podem demorar até cinco minutos a serem aplicadas.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor de banco de dados do Azure para MariaDB ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor MariaDB será negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores cliente que acessam o banco de dados do Azure para MariaDB Server e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

* **O IP do servidor parece ser público:** As conexões com o banco de dados do Azure para o servidor MariaDB são roteadas por meio de um gateway do Azure acessível publicamente. No entanto, o IP do servidor real é protegido pelo firewall. Para obter mais informações, visite o [artigo arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passos seguintes
- [Criar e gerenciar as regras de firewall do banco de dados do Azure para MariaDB usando o portal do Azure](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->

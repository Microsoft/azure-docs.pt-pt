---
title: Base de dados do Azure para MariaDB regras de firewall de servidor
description: Descreve as regras de firewall da base de dados do Azure para MariaDB server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6fb9099ebfe884fc6eee58882ee23e46ba550e13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734443"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Base de dados do Azure para MariaDB regras de firewall de servidor
As firewalls impedem todo o acesso ao seu servidor de base de dados até especificar que computadores têm permissão. A firewall concede acesso ao servidor com base no endereço IP de origem de cada pedido.

Para configurar uma firewall, crie regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem que os clientes acedam a sua base de dados do Azure completo para o servidor de MariaDB, ou seja, todas as bases de dados no mesmo servidor lógico. Regras de firewall ao nível do servidor podem ser configuradas com o portal do Azure ou comandos da CLI do Azure. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte da subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todos os acessos de base de dados para a base de dados do Azure para MariaDB server está bloqueada pelo firewall por predefinição. Para começar a utilizar o servidor a partir de outro computador, terá de especificar uma ou mais regras de firewall ao nível do servidor para ativar o acesso ao seu servidor. Utilize as regras de firewall para especificar qual IP intervalos de endereços da Internet para permitir. Acesso ao site de portal do Azure em si não é afetado pelas regras de firewall.

Tentativas de ligação de Internet e do Azure tem primeiro de passar pela firewall antes de chegarem a base de dados do Azure para MariaDB base de dados, conforme mostrado no diagrama seguinte:

![Fluxo de exemplo de como funciona a firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
Regras de firewall ao nível do servidor aplicam-se a todas as bases de dados na base de dados do Azure para MariaDB server.

Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, em seguida, é concedida ligação.

Se o endereço IP do pedido está fora dos intervalos especificados em qualquer uma das regras de firewall ao nível da base de dados ou ao nível do servidor, em seguida, o pedido de ligação falha.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que aplicações do Azure ligar à base de dados do Azure para MariaDB server, tem de estar ativadas ligações do Azure. Por exemplo, para alojar uma aplicação de aplicações Web do Azure ou um aplicativo que é executado numa VM do Azure ou para ligar a partir de um gateway de gestão de dados do Azure Data Factory. Os recursos não é necessário estar na mesma rede Virtual (VNet) ou grupo de recursos para a regra de firewall para permitir que essas ligações. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Existem dois métodos para permitir que esses tipos de ligações. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Em alternativa, pode definir o **permitir o acesso aos serviços do Azure** a opção de **ON** no portal do **segurança da ligação** painel e clique em **guardar**. Se a tentativa de ligação não for permitida, o pedido não chega a base de dados do Azure para MariaDB server.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Para além do portal do Azure, as regras de firewall podem ser geridas programaticamente com a CLI do Azure. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-the-database-firewall"></a>Resolver problemas da firewall de base de dados
Ao acesso à base de dados do Microsoft Azure para o serviço de servidor MariaDB não tem um comportamento conforme esperado, considere os seguintes pontos:

* **Alterações à lista de permissões não foram ainda aplicadas:** Pode ser tanto quanto o atraso de cinco minutos para que as alterações à base de dados do Azure para a configuração de firewall do servidor de MariaDB entrem em vigor.

* **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:** Se um início de sessão não tem permissões na base de dados do Azure para MariaDB server ou a palavra-passe utilizada estiver incorreta, a ligação à base de dados do Azure para MariaDB server é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das seguintes soluções:

* Peça ao seu fornecedor de serviços de Internet (ISP) para o intervalo de endereços IP atribuído a computadores cliente que acedem a base de dados do Azure para MariaDB server e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

* Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

## <a name="next-steps"></a>Passos Seguintes
- [Criar e gerir a base de dados do Azure para MariaDB regras de firewall com o portal do Azure](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->

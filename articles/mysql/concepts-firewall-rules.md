---
title: Banco de dados do Azure para as regras de firewall do servidor MySQL
description: Saiba mais sobre como usar regras de firewall para habilitar conexões com o servidor de banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 1d75b9e7d997b0c62c7e235187907f0556318efe
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970413"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Banco de dados do Azure para as regras de firewall do servidor MySQL
Os firewalls impedem todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação.

Para configurar um firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível do servidor.

**Regras de firewall:** Essas regras permitem que os clientes acessem todo o seu banco de dados do Azure para o servidor MySQL, ou seja, todos os bancos dentro do mesmo servidor lógico. As regras de firewall no nível de servidor podem ser configuradas usando os comandos portal do Azure ou CLI do Azure. Para criar regras de firewall no nível de servidor, você deve ser o proprietário da assinatura ou um colaborador da assinatura.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo acesso ao banco de dados para o servidor do banco de dados do Azure para MySQL é bloqueado por padrão pelo firewall. Para começar a usar o servidor de outro computador, você precisa especificar uma ou mais regras de firewall no nível de servidor para habilitar o acesso ao servidor. Use as regras de firewall para especificar quais intervalos de endereços IP da Internet permitir. O acesso ao site portal do Azure em si não é afetado pelas regras de firewall.

As tentativas de conexão da Internet e do Azure devem passar pela primeira vez pelo firewall antes que possam acessar seu banco de dados do Azure para MySQL, conforme mostrado no diagrama a seguir:

![Fluxo de exemplo de como o firewall funciona](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall no nível de servidor se aplicam a todos os bancos de dados no servidor do Azure para MySQL.

Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall no nível de servidor, a conexão será concedida.

Se o endereço IP da solicitação estiver fora dos intervalos especificados em qualquer uma das regras de firewall no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Para permitir que os aplicativos do Azure se conectem ao seu banco de dados do Azure para o servidor MySQL, as conexões do Azure devem ser habilitadas. Por exemplo, para hospedar um aplicativo de aplicativos Web do Azure, ou um aplicativo que é executado em uma VM do Azure, ou para se conectar de um Azure Data Factory gateway de gerenciamento de dados. Os recursos não precisam estar na mesma rede virtual (VNet) ou grupo de recursos para que a regra de firewall habilite essas conexões. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Há alguns métodos para habilitar esses tipos de conexões. Uma definição de firewall com o endereço de início e de fim igual a 0.0.0.0 indica que estas ligações são permitidas. Como alternativa, você pode definir a opção **permitir acesso aos serviços do Azure** como ativado no portal no painel **segurança de conexão** e clicar **em** **salvar**. Se a tentativa de conexão não for permitida, a solicitação não alcançará o banco de dados do Azure para o servidor MySQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal do Azure, as regras de firewall podem ser gerenciadas programaticamente usando o CLI do Azure. Consulte também [criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao banco de dados do Microsoft Azure para o serviço do servidor MySQL não se comportar conforme o esperado:

* **As alterações na lista de permissões ainda não entraram em vigor:** As alterações à configuração da firewall da Base de Dados do Azure para Servidor MySQL podem demorar até cinco minutos a serem aplicadas.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor do banco de dados do Azure para MySQL ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor MySQL será negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores cliente que acessam o servidor do banco de dados do Azure para MySQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

* **O IP do servidor parece ser público:** As ligações ao servidor da Base de Dados do Azure para MySQL são encaminhadas através de um gateway do Azure acessível publicamente. No entanto, o IP real do servidor está protegido pela firewall. Para obter mais informações, veja o [artigo sobre a arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o CLI do Azure](./howto-manage-firewall-using-cli.md)

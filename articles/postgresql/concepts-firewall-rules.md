---
title: Regras de firewall - Azure Database for PostgreSQL - Single Server
description: Este artigo descreve como usar regras de firewall para ligar à Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76157275"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único
A azure Database for PostgreSQL server firewall impede todo o acesso ao seu servidor de base de dados até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem que os clientes acedam a toda a sua Base de Dados Azure para o Servidor PostgreSQL, ou seja, todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando o portal Azure ou utilizando comandos Azure CLI. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados à base de dados do seu servidor PostgreSQL está bloqueado por padrão. Para começar a utilizar o seu servidor a partir de outro computador, tem de especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar quais os endereços IP que variam a partir da Internet para permitir. O acesso ao próprio site do portal Azure não é afetado pelas regras do firewall.
As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados PostgreSQL, como mostra o seguinte diagrama:

![Exemplo de fluxo de como a firewall funciona](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall ao nível do servidor aplicam-se a todas as bases de dados da mesma Base de Dados Azure para servidor PostgreSQL. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação.
Se o endereço IP do pedido não estiver dentro dos intervalos especificados em nenhuma das regras de firewall ao nível do servidor, o pedido de ligação falha.
Por exemplo, se a sua aplicação se ligar ao controlador JDBC para PostgreSQL, poderá encontrar este erro ao tentar ligar-se quando a firewall estiver a bloquear a ligação.
> cution java.util.concurrent.ExeExceção: java.lang.RuntimeException: org.postgresql.util.PSQLExcepção: FATAL: no pg \_ hba.conf entrada para anfitrião "123.45.67.890", utilizador "adminuser", base de dados "postgresql", SSL

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Recomenda-se que encontre o endereço IP de saída de qualquer aplicação ou serviço e permita explicitamente o acesso a esses endereços ou intervalos IP individuais. Por exemplo, pode encontrar o endereço IP de saída de um Serviço de Aplicações Azure ou utilizar um IP público ligado a uma máquina virtual ou outro recurso (ver abaixo para obter informações sobre a ligação com o IP privado de uma máquina virtual sobre os pontos finais de serviço). 

Se um endereço IP de saída fixo não estiver disponível para o seu serviço Azure, pode considerar ativar ligações a partir de todos os endereços IP do datacenter Azure. Esta definição pode ser ativada a partir do portal Azure, definindo a opção **De acesso a Serviços Azure** para **ON** a partir do painel de segurança **De Ligação** e batendo **Save**. A partir do CLI Azure, uma definição de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor PostgreSQL.

> [!IMPORTANT]
> A opção **de acesso a serviços Azure** configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configure Permitir o acesso aos serviços da Azure no portal](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ligação a partir de um VNet
Para ligar de forma segura à sua Base de Dados Azure para servidor PostgreSQL a partir de um VNet, considere utilizar [pontos finais de serviço VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal Azure, as regras de firewall podem ser geridas programáticamente usando O Azure CLI.
Consulte também [criar e gerir a Base de Dados Azure para regras de firewall postgresQL utilizando O Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Problemas de firewall de resolução de problemas
Considere os seguintes pontos quando o acesso à Base de Dados do Microsoft Azure para o serviço PostgreSQL Server não se comporta como espera:

* **As alterações à lista de autorizações ainda não produziram efeitos:** Pode haver um atraso de cinco minutos para que as alterações na Base de Dados Azure para a configuração de firewall do Servidor PostgreSQL produzam efeitos.

* **O login não é autorizado ou foi utilizada uma palavra-passe incorreta:** Se um login não tiver permissões na Base de Dados Azure para o servidor PostgreSQL ou a palavra-passe utilizada estiver incorretamente, a ligação à Base de Dados Azure para o servidor PostgreSQL é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentarem ligar-se ao seu servidor; cada cliente deve ainda fornecer as credenciais de segurança necessárias.

   Por exemplo, usando um cliente JDBC, pode aparecer o seguinte erro.
   > cution java.util.concurrent.ExeCutionExcepção: java.lang.RuntimeExcepção: org.postgresql.util.PSQLExcepção: FATAL: a autenticação da palavra-passe falhou para o utilizador "yourusername"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) para o intervalo de endereços IP atribuído aos seus computadores clientes que acedam à Base de Dados Azure para o Servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como regra de firewall.

   * Obtenha endereço IP estático em vez dos computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.

* **O IP do servidor parece ser público:** As ligações à Base de Dados Azure para o servidor PostgreSQL são encaminhada através de um gateway Azure acessível ao público. No entanto, o IP real do servidor está protegido pela firewall. Para obter mais informações, veja o [artigo sobre a arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Próximos passos
Para artigos sobre a criação de regras de firewall ao nível do servidor e de base de dados, consulte:
* [Criar e gerir a Base de Dados Azure para regras de firewall postgreSQL utilizando o portal Azure](howto-manage-firewall-using-portal.md)
* [Criar e gerir a Base de Dados de Azure para regras de firewall postgresQL usando Azure CLI](howto-manage-firewall-using-cli.md)
- [Pontos finais de serviço VNet na Base de Dados Azure para PostgreSQL](./concepts-data-access-and-security-vnet.md)

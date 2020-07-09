---
title: Regras de firewall - Azure Database for MySQL
description: Saiba como utilizar as regras de firewall para permitir ligações à sua Base de Dados Azure para o servidor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76155201"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Base de dados Azure para regras de firewall de servidor MySQL
As firewalls impedem todo o acesso ao servidor da base de dados até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.

Para configurar uma firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem que os clientes acedam a toda a sua Base de Dados Azure para o servidor MySQL, ou seja, todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando o portal Azure ou os comandos Azure CLI. Para criar regras de firewall ao nível do servidor, tem de ser o proprietário da subscrição ou um contribuinte de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados à base de dados do seu servidor MySQL está bloqueado por defeito pela firewall. Para começar a utilizar o seu servidor a partir de outro computador, tem de especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar quais os endereços IP que variam a partir da Internet para permitir. O acesso ao próprio site do portal Azure não é afetado pelas regras do firewall.

As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados Azure para a base de dados MySQL, como mostra o seguinte diagrama:

![Exemplo de fluxo de como a firewall funciona](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall ao nível do servidor aplicam-se a todas as bases de dados da Base de Dados Azure para o servidor MySQL.

Se o endereço IP do pedido estiver dentro de uma das gamas especificadas nas regras de firewall ao nível do servidor, então a ligação é concedida.

Se o endereço IP do pedido estiver fora dos intervalos especificados em qualquer uma das regras de firewall de nível de base de dados ou de nível de servidor, então o pedido de ligação falha.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Recomenda-se que encontre o endereço IP de saída de qualquer aplicação ou serviço e permita explicitamente o acesso a esses endereços ou intervalos IP individuais. Por exemplo, pode encontrar o endereço IP de saída de um Serviço de Aplicações Azure ou utilizar um IP público ligado a uma máquina virtual ou outro recurso (ver abaixo para obter informações sobre a ligação com o IP privado de uma máquina virtual sobre os pontos finais de serviço). 

Se um endereço IP de saída fixo não estiver disponível para o seu serviço Azure, pode considerar ativar ligações a partir de todos os endereços IP do datacenter Azure. Esta definição pode ser ativada a partir do portal Azure, definindo a opção **De acesso a Serviços Azure** para **ON** a partir do painel de segurança **De Ligação** e batendo **Save**. A partir do CLI Azure, uma definição de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor MySQL.

> [!IMPORTANT]
> A opção **de acesso a serviços Azure** configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configure Permitir o acesso aos serviços da Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ligação a partir de um VNet
Para ligar de forma segura à sua Base de Dados Azure para o servidor MySQL a partir de um VNet, considere utilizar [pontos finais de serviço VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal Azure, as regras de firewall podem ser geridas programáticamente utilizando o CLI Azure. Consulte também [criar e gerir a Base de Dados Azure para as regras de firewall mySQL utilizando o Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Problemas de firewall de resolução de problemas
Considere os seguintes pontos quando o acesso à Base de Dados do Microsoft Azure para o serviço de servidores MySQL não se comporta como esperado:

* **As alterações à lista de autorizações ainda não produziram efeitos:** Pode haver um atraso de cinco minutos para que as alterações na Base de Dados Azure para a configuração de firewall do MySQL Server produzam efeitos.

* **O login não é autorizado ou foi utilizada uma palavra-passe incorreta:** Se um login não tiver permissões na Base de Dados Azure para o servidor MySQL ou a palavra-passe utilizada estiver incorretamente, a ligação à Base de Dados Azure para o servidor MySQL é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se tiver uma ligação à Internet com endereço IP dinâmico e estiver com dificuldades em passar pela firewall, pode experimentar uma das seguintes soluções:

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) para o intervalo de endereços IP atribuído aos seus computadores clientes que acedam à Base de Dados Azure para o servidor MySQL e, em seguida, adicione o intervalo de endereço IP como regra de firewall.

   * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

* **O IP do servidor parece ser público:** As ligações à Base de Dados Azure para o servidor MySQL são encaminhada através de um gateway Azure acessível ao público. No entanto, o IP real do servidor está protegido pela firewall. Para obter mais informações, veja o [artigo sobre a arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Próximos passos

* [Criar e gerir a Base de Dados Azure para as regras de firewall do MySQL utilizando o portal Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerir a Base de Dados Azure para as regras de firewall mySQL usando O Azure CLI](./howto-manage-firewall-using-cli.md)
- [Pontos finais de serviço VNet na Base de Dados Azure para o MySQL](./concepts-data-access-and-security-vnet.md)

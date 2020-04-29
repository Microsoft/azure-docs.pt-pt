---
title: Regras da Firewall - Base de Dados Azure para MariaDB
description: Saiba utilizar regras de firewall para permitir ligações à sua Base de Dados Azure para servidor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 743e3f50d747993250399493d97fc2becab19319
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79532047"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Base de Dados Azure para regras de firewall do servidor MariaDB
As firewalls impedem qualquer acesso ao seu servidor de base de dados até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.

Para configurar uma firewall, crie regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem aos clientes aceder a toda a sua Base de Dados Azure para servidor MariaDB, ou seja, todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando os comandos do portal Azure ou do Azure CLI. Para criar regras de firewall ao nível do servidor, deve ser o proprietário da subscrição ou um colaborador de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados da sua Base de Dados Azure para servidor MariaDB está bloqueado por predefinição pela firewall. Para começar a utilizar o seu servidor a partir de outro computador, é necessário especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar qual o endereço IP que vai da Internet para permitir. O acesso ao próprio portal Azure não é afetado pelas regras de firewall.

As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados Do Azure para a base de dados MariaDB, como mostra o seguinte diagrama:

![Fluxo de exemplo de como a firewall funciona](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall ao nível do servidor aplicam-se a todas as bases de dados da Base de Dados Azure para o servidor MariaDB.

Se o endereço IP do pedido estiver dentro de uma das gamas especificadas nas regras de firewall ao nível do servidor, então a ligação é concedida.

Se o endereço IP do pedido estiver fora dos intervalos especificados em qualquer uma das regras de firewall ao nível da base de dados ou do servidor, então o pedido de ligação falha.

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Recomenda-se que encontre o endereço IP de saída de qualquer aplicação ou serviço e permita explicitamente o acesso a esses endereços ou gamas IP individuais. Por exemplo, pode encontrar o endereço IP de saída de um Serviço de Aplicações Azure ou utilizar um IP público ligado a uma máquina virtual ou outro recurso (ver abaixo para obter informações sobre a ligação com o IP privado de uma máquina virtual sobre pontos finais de serviço). 

Se não estiver disponível um endereço IP de saída fixo para o seu serviço Azure, pode considerar ativar ligações de todos os endereços IP do Centro de Dados Azure. Esta definição pode ser ativada a partir do portal Azure, definindo a opção **permitir o acesso aos serviços Azure** para **ON** a partir do painel de segurança de **ligação** e bater **save**. A partir do Azure CLI, uma definição de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor MariaDB.

> [!IMPORTANT]
> A opção **permitir o acesso aos serviços Azure** configura a firewall para permitir todas as ligações do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configure Permitir o acesso aos serviços do Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ligação a partir de uma VNet
Para ligar de forma segura à sua Base de Dados Azure para o servidor MariaDB a partir de um VNet, considere utilizar [pontos finais](./concepts-data-access-security-vnet.md)de serviço VNet . 

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal Azure, as regras de firewall podem ser geridas programáticamente utilizando o Azure CLI. 

Consulte também criar e gerir a Base de Dados Azure para regras de [firewall MariaDB utilizando o Azure CLI](./howto-manage-firewall-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Problemas de resolução de problemas com problemas de firewall
Considere os seguintes pontos quando o acesso à Base de Dados Microsoft Azure para o serviço de servidor MariaDB não se comporta como esperado:

* **As alterações à lista de autorizações ainda não produziram efeito:** Pode haver até um atraso de cinco minutos para que as alterações na Base de Dados Azure para a configuração da firewall do Servidor MariaDB entrem em vigor.

* **O login não está autorizado ou foi utilizada uma senha incorreta:** Se um login não tiver permissões na Base de Dados Azure para o servidor MariaDB ou se a palavra-passe utilizada estiver incorreta, a ligação à Base de Dados Azure para o servidor MariaDB é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se tiver uma ligação à Internet com endereços IP dinâmicos e estiver com dificuldades em passar pela firewall, pode experimentar uma das seguintes soluções:

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) a gama de endereços IP atribuída aos seus computadores clientes que acedam à Base de Dados Azure para o servidor MariaDB e, em seguida, adicione a gama de endereços IP como regra de firewall.

   * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

* **O IP do servidor parece ser público:** As ligações à Base de Dados Azure para servidor MariaDB são direcionadas através de um portal Azure acessível ao público. No entanto, o IP real do servidor está protegido pela firewall. Para mais informações, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md) 

## <a name="next-steps"></a>Passos seguintes
- [Crie e gerea Base de Dados Azure para regras de firewall MariaDB utilizando o portal Azure](./howto-manage-firewall-portal.md)
- [Crie e gerea Base de Dados Azure para regras de firewall MariaDB usando o Azure CLI](./howto-manage-firewall-cli.md)
- [Pontos finais do serviço VNet na Base de Dados Azure para MariaDB](./concepts-data-access-security-vnet.md)

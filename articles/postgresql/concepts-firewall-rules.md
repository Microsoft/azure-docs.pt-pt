---
title: Regras de firewall - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como usar regras de firewall para ligar à Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76157275"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regras de firewall na Base de Dados Azure para PostgreSQL - Servidor Único
A Base de Dados Azure para firewall do servidor PostgreSQL impede todo o acesso ao seu servidor de base de dados até especificar quais os computadores que têm permissão. A firewall concede acesso ao servidor com base no endereço IP originário de cada pedido.
Para configurar a firewall, crie as regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall ao nível do servidor.

**Regras de firewall:** Estas regras permitem aos clientes aceder a toda a sua Base de Dados Azure para o PostgreSQL Server, isto é, todas as bases de dados dentro do mesmo servidor lógico. As regras de firewall ao nível do servidor podem ser configuradas utilizando o portal Azure ou utilizando comandos Azure CLI. Para criar regras de firewall ao nível do servidor, deve ser o proprietário da subscrição ou um colaborador de subscrição.

## <a name="firewall-overview"></a>Descrição geral das firewalls
Todo o acesso à base de dados da sua Base de Dados Azure para o servidor PostgreSQL está bloqueado por predefinição. Para começar a utilizar o seu servidor a partir de outro computador, é necessário especificar uma ou mais regras de firewall ao nível do servidor para permitir o acesso ao seu servidor. Utilize as regras de firewall para especificar qual o endereço IP que vai da Internet para permitir. O acesso ao próprio portal Azure não é afetado pelas regras de firewall.
As tentativas de ligação da Internet e do Azure devem primeiro passar pela firewall antes de poderem chegar à base de dados PostgreSQL, como mostra o seguinte diagrama:

![Fluxo de exemplo de como a firewall funciona](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet
As regras de firewall ao nível do servidor aplicam-se a todas as bases de dados da mesma Base de Dados Azure para o servidor PostgreSQL. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida ligação.
Se o endereço IP do pedido não estiver dentro dos intervalos especificados em nenhuma das regras de firewall ao nível do servidor, o pedido de ligação falha.
Por exemplo, se a sua aplicação se ligar ao controlador JDBC para o PostgreSQL, poderá encontrar este erro a tentar ligar-se quando a firewall estiver a bloquear a ligação.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: NO\_pg hba.conf entry for host "123.45.67.890", user "adminuser", base de dados "postgresql", SSL

## <a name="connecting-from-azure"></a>Ligar a partir do Azure
Recomenda-se que encontre o endereço IP de saída de qualquer aplicação ou serviço e permita explicitamente o acesso a esses endereços ou gamas IP individuais. Por exemplo, pode encontrar o endereço IP de saída de um Serviço de Aplicações Azure ou utilizar um IP público ligado a uma máquina virtual ou outro recurso (ver abaixo para obter informações sobre a ligação com o IP privado de uma máquina virtual sobre pontos finais de serviço). 

Se não estiver disponível um endereço IP de saída fixo para o seu serviço Azure, pode considerar ativar ligações de todos os endereços IP do Centro de Dados Azure. Esta definição pode ser ativada a partir do portal Azure, definindo a opção **permitir o acesso aos serviços Azure** para **ON** a partir do painel de segurança de **ligação** e bater **save**. A partir do Azure CLI, uma definição de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de ligação não for permitida, o pedido não chega à Base de Dados Azure para o servidor PostgreSQL.

> [!IMPORTANT]
> A opção **permitir o acesso aos serviços Azure** configura a firewall para permitir todas as ligações do Azure, incluindo ligações a partir das subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configure Permitir o acesso aos serviços do Azure no portal](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Ligação a partir de uma VNet
Para ligar de forma segura à sua Base de Dados Azure para o servidor PostgreSQL a partir de um VNet, considere utilizar [pontos finais](./concepts-data-access-and-security-vnet.md)do serviço VNet . 

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal Azure, as regras de firewall podem ser geridas programáticamente utilizando o Azure CLI.
Consulte também Criar e gerir a Base de Dados Azure para regras de [firewall PostgreSQL usando o Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Problemas de resolução de problemas com problemas de firewall
Considere os seguintes pontos no acesso à Base de Dados Microsoft Azure para o serviço PostgreSQL Server não se comporta como espera:

* **As alterações à lista de autorizações ainda não produziram efeito:** Pode haver até um atraso de cinco minutos para alterações na Base de Dados Azure para a configuração da firewall do Servidor PostgreSQL para produzir efeito.

* **O login não está autorizado ou foi utilizada uma senha incorreta:** Se um login não tiver permissões na Base de Dados Azure para o servidor PostgreSQL ou a palavra-passe utilizada estiver incorreta, a ligação à Base de Dados Azure para servidor PostgreSQL é negada. A criação de uma definição de firewall apenas proporciona aos clientes a oportunidade de tentar ligar-se ao seu servidor; cada cliente deve ainda fornecer as credenciais de segurança necessárias.

   Por exemplo, utilizando um cliente JDBC, pode aparecer o seguinte erro.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: a autenticação de senha falhou para o utilizador "yourusername"

* **Endereço IP dinâmico:** se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das soluções seguintes.

   * Peça ao seu Fornecedor de Serviços de Internet (ISP) a gama de endereços IP atribuída aos seus computadores clientes que acedam à Base de Dados Azure para o Servidor PostgreSQL e, em seguida, adicione a gama de endereços IP como regra de firewall.

   * Obtenha endereço IP estático em vez dos seus computadores clientes e, em seguida, adicione o endereço IP estático como regra de firewall.

* **O IP do servidor parece ser público:** As ligações à Base de Dados Azure para servidor PostgreSQL são direcionadas através de um portal Azure acessível ao público. No entanto, o IP real do servidor está protegido pela firewall. Para mais informações, visite o [artigo arquitetura de conectividade.](concepts-connectivity-architecture.md) 

## <a name="next-steps"></a>Passos seguintes
Para artigos sobre a criação de regras de firewall ao nível do servidor e de nível de base de dados, consulte:
* [Crie e gerea Base de Dados Azure para regras de firewall PostgreSQL usando o portal Azure](howto-manage-firewall-using-portal.md)
* [Crie e gerea Base de Dados Azure para regras de firewall PostgreSQL usando o Azure CLI](howto-manage-firewall-using-cli.md)
- [Pontos finais do serviço VNet na Base de Dados Azure para PostgreSQL](./concepts-data-access-and-security-vnet.md)

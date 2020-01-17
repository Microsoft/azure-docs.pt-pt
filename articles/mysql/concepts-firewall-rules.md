---
title: Regras de firewall-banco de dados do Azure para MySQL
description: Saiba mais sobre como usar regras de firewall para habilitar conexões com o servidor de banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155201"
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
É recomendável que você localize o endereço IP de saída de qualquer aplicativo ou serviço e permita explicitamente o acesso a esses endereços IP ou intervalos individuais. Por exemplo, você pode encontrar o endereço IP de saída de um serviço de Azure App ou usar um IP público vinculado a uma máquina virtual ou outro recurso (consulte abaixo para obter informações sobre como se conectar com o IP privado de uma máquina virtual nos pontos de extremidade de serviço). 

Se um endereço IP de saída fixo não estiver disponível para seu serviço do Azure, você poderá considerar a possibilidade de habilitar conexões de todos os endereços IP do datacenter do Azure. Essa configuração pode ser habilitada no portal do Azure definindo a opção **permitir acesso aos serviços do Azure** como **ativado** no painel **segurança de conexão** e ao pressionar **salvar**. No CLI do Azure, uma configuração de regra de firewall com endereço inicial e final igual a 0.0.0.0 faz o equivalente. Se a tentativa de conexão não for permitida, a solicitação não alcançará o banco de dados do Azure para o servidor MySQL.

> [!IMPORTANT]
> A opção **permitir acesso aos serviços do Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.
> 

![Configurar permitir acesso aos serviços do Azure no portal](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Conectando de uma VNet
Para se conectar com segurança ao banco de dados do Azure para servidor MySQL de uma VNet, considere o uso de [pontos de extremidade de serviço de VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gerir regras de firewall programaticamente
Além do portal do Azure, as regras de firewall podem ser gerenciadas programaticamente usando o CLI do Azure. Consulte também [criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando CLI do Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Solucionando problemas de firewall
Considere os seguintes pontos quando o acesso ao banco de dados do Microsoft Azure para o serviço do servidor MySQL não se comportar conforme o esperado:

* **As alterações na lista de permissões ainda não entraram em vigor:** Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall do banco de dados do Azure para MySQL tenham efeito.

* **O logon não está autorizado ou uma senha incorreta foi usada:** Se um logon não tiver permissões no servidor do banco de dados do Azure para MySQL ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor MySQL será negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias.

* **Endereço IP dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores cliente que acessam o servidor do banco de dados do Azure para MySQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

   * Em alternativa, obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP como regras de firewall.

* O **IP do servidor parece ser público:** As conexões com o banco de dados do Azure para servidor MySQL são roteadas por meio de um gateway do Azure acessível publicamente. No entanto, o IP real do servidor está protegido pela firewall. Para obter mais informações, veja o [artigo sobre a arquitetura de conectividade](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passos seguintes

* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o portal do Azure](./howto-manage-firewall-using-portal.md)
* [Criar e gerenciar regras de firewall do banco de dados do Azure para MySQL usando o CLI do Azure](./howto-manage-firewall-using-cli.md)
- [Pontos de extremidade de serviço de VNet no banco de dados do Azure para MySQL](./concepts-data-access-and-security-vnet.md)

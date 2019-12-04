---
title: Solucionar problemas de conexão-banco de dados do Azure para MySQL
description: Saiba como solucionar problemas de conexão com o banco de dados do Azure para MySQL, incluindo erros transitórios que exigem novas tentativas, problemas de firewall e interrupções.
keywords: conexão do MySQL, Cadeia de conexão, problemas de conectividade, erro transitório, erro de conexão
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: troubleshooting
ms.date: 12/02/2019
ms.openlocfilehash: 8c1c521488270ff0bfe3b306470345bc2cd885ce
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773535"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Solucionar problemas de conexão com o banco de dados do Azure para MySQL

Problemas de conexão podem ser causados por uma variedade de coisas, incluindo:

* Definições de firewall
* Tempo limite da conexão
* Informações de logon incorretas
* Limite máximo atingido em alguns recursos do banco de dados do Azure para MySQL
* Problemas com a infraestrutura do serviço
* Manutenção sendo executada no serviço
* A alocação de computação do servidor é alterada com o dimensionamento do número de vCores ou a mudança para uma camada de serviço diferente

Geralmente, os problemas de conexão com o banco de dados do Azure para MySQL podem ser classificados da seguinte maneira:

* Erros transitórios (curta duração ou intermitente)
* Erros persistentes ou não transitórios (erros que periodicamente são recorrentes)

## <a name="troubleshoot-transient-errors"></a>Solucionar erros transitórios

Erros transitórios ocorrem quando a manutenção é executada, o sistema encontra um erro com o hardware ou software, ou você altera o vCores ou a camada de serviço do seu servidor. O serviço de banco de dados do Azure para MySQL tem alta disponibilidade interna e foi projetado para atenuar esses tipos de problemas automaticamente. No entanto, seu aplicativo perde sua conexão com o servidor por um curto período de tempo, em geral, menos de 60 segundos. Algumas vezes, alguns eventos podem levar mais tempo para reduzir, como quando uma transação grande causa uma recuperação de longa execução.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Etapas para resolver problemas de conectividade transitórios

1. Verifique o [painel de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer interrupção conhecida que tenha ocorrido durante o tempo em que os erros foram relatados pelo aplicativo.
2. Os aplicativos que se conectam a um serviço de nuvem como o banco de dados do Azure para MySQL devem esperar erros transitórios e implementar a lógica de repetição para lidar com esses erros em vez de identificando-los como erros de aplicativo para os usuários. Examine o [tratamento de erros de conectividade transitórios para o banco de dados do Azure para MySQL](concepts-connectivity.md) para obter práticas recomendadas e diretrizes de design para lidar com erros transitórios.
3. Como um servidor se aproxima de seus limites de recursos, os erros podem parecer um problema de conectividade transitório. Confira [limitações no banco de dados do Azure para MySQL](concepts-limits.md).
4. Se os problemas de conectividade continuarem, ou se a duração para a qual seu aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, faça uma solicitação de suporte do Azure selecionando **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Solucionar erros persistentes

Se o aplicativo falhar de maneira persistente ao se conectar ao banco de dados do Azure para MySQL, isso geralmente indica um problema com um dos seguintes:

* Configuração de firewall do servidor: Verifique se o Firewall do servidor do banco de dados do Azure para MySQL está configurado para permitir conexões do cliente, incluindo servidores proxy e gateways.
* Configuração de firewall do cliente: o firewall em seu cliente deve permitir conexões com o servidor de banco de dados. Endereços IP e portas do servidor que não podem ser permitidos, bem como nomes de aplicativos, como MySQL em alguns firewalls.
* Erro do usuário: você pode ter parâmetros de conexão digitados, como o nome do servidor na cadeia de conexão ou um sufixo ausente do *\@ServerName* no nome de usuário.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Etapas para resolver problemas de conectividade persistente

1. Configure [as regras de firewall](howto-manage-firewall-using-portal.md) para permitir o endereço IP do cliente. Somente para fins de teste temporários, configure uma regra de firewall usando 0.0.0.0 como o endereço IP inicial e usando 255.255.255.255 como o endereço IP final. Isso abrirá o servidor para todos os endereços IP. Se isso resolver o problema de conectividade, remova essa regra e crie uma regra de firewall para um endereço IP ou intervalo de endereços apropriado limitado.
2. Em todos os firewalls entre o cliente e a Internet, verifique se a porta 3306 está aberta para conexões de saída.
3. Verifique a cadeia de conexão e outras configurações de conexão. Examine [como conectar aplicativos ao banco de dados do Azure para MySQL](howto-connection-string.md).
4. Verifique a integridade do serviço no painel. Se você considerar que há uma interrupção regional, consulte [visão geral da continuidade de negócios com o banco de dados do Azure para MySQL](concepts-business-continuity.md) para obter as etapas para recuperar em uma nova região.

## <a name="next-steps"></a>Passos seguintes

* [Tratamento de erros de conectividade transitórios para o banco de dados do Azure para MySQL](concepts-connectivity.md)
